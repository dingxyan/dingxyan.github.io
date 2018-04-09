---
layout: post
title:  原生js+nodejs 实现ajax及jsonp
categories: 前端知识积累
tags: js nodejs ajax jsonp
---
### 客户端实现原理
1. ajax

ajax是一种实现客户端与服务端数据交流的方式，可以实现页面局部刷新。这种实现方式被大家所熟知，这里不再赘述。然而这种方式不允许跨域请求。

2. jsonp

在前端页面中，我们发现通过script标签可以引入文件，而且是没有域限制的，因此可以从这个角度来实现从其他域获取信息，其实现步骤如下：

**步骤一**：创建script标签；
```
var script = document.createElement("script");
```
**步骤二**：获取请求url，将请求的参数拼接到url，例如
```
var url = "http://localhost:8888/test?id=1&name=2";
```
在这里，需要注意的是，在将参数拼接到url的时候，需要将参数编码。

**步骤三**：在url中添加回调函数
```
var url = "http://localhost:8888/test?id=1&name=2&callback=jsonpCallback";
```
**步骤四**：将url写入到script的src属性，并且将script添加到页面
```
script.setAttribute('src', url);
var head = document.getElementsByTagName('head')[0]; 
head.appendChild(script);
```
这里需要注意几点：jsonpCallback必须是全局方法；url与页面必须不在同一个域，否则callback方法不会执行。

将script添加到页面之后，url中的callback方法会立即执行。

**步骤五**：处理回调
```
window.jsonpCallback = function(data){
	head.removeChild(script);//删除临时添加的script标签
	//获取到的data即为请求到的数据，这里可以处理请求结果
	return data;
}
```
从上述步骤也可以看出，jsonp请求一般都是get请求，而在jQuery中，将jsonp封装成了类似于ajax请求，并且默认把所有jsonp请求type转为get。
### 服务端实现原理
**步骤一**：引入必备模块
```
//引入必备模块
var http = require("http");
var fs = require("fs")
var expressLess = require('express-less');
var express = require('express');
var app = express();
var mysql = require('mysql');
```
**步骤二**：处理请求（这里以get请求为例）
```
//处理get请求
app.get("*", function(req, res, next) {
    var query = req.originalUrl;
    var jsonpCallback = req.query.callback;

    query =  query.substring(1, query.length).split('?')[0];
    query = '/'+query;

    var table='';//数据库表名称
    var query='';//数据库检索信息
    queryAllData(table,query,function(result,err){
    	var sendData;
        if(err){
            sendData = result;
        }else if(result.content){
            sendData = result.content.toString();
        }else{
            sendData = '{"errno":"10001", "errmsg":"访问页面或者接口不存在！"}';
        }
        //返回信息到客户端
        !jsonpCallback && res.send(sendData);//json
        jsonpCallback && res.send(jsonpCallback + "(" + JSON.stringify(sendData) + ")");//jsonp
    });

});
```
获取到请求信息，从数据库检索信息，根据jsonpCallback判断是否是jsonp请求；如果不是jsonp，那么直接将获取到的信息返回给客户端；如果是jsonp，那么需要先将jsonp回调方法跟数据拼接成为字符串，再返回到客户端。

### 完整示例

前端代码：
```
Send({
	type: 'get',
	url: 'http://localhost:8888/test',
	dataType: 'jsonp',
	async: true,
	jsonpCallback: 'jsonp',
	data: {
		"id":1,
		"name":"名称"
	},
	success: function(data){
		console.log(data)
	}
});

function Send(params){
	var me = this;
	var params = params||{};
	if(params.dataType=='json'){
		//json
		var obj;
		if (window.XMLHttpRequest){
			obj=new XMLHttpRequest();//IE7+, Firefox, Chrome, Opera, Safari
		}else{
			obj=new ActiveXObject("Microsoft.XMLHTTP");//IE6, IE5
		}
		if(params.type=='get'){
			// get请求
                        var data = formatSendData(params.data);
			obj.open(params.type, params.url+'?'+data, params.async);
			obj.send();
		}else if(params.type=='post'){
			// post请求
			obj.open(params.type, params.url, params.async);
			obj.send(params.data);
		}
		obj.onreadystatechange=function(){
			if (obj.readyState==4 && obj.status==200){
				params.success && params.success.call(me, obj.responseText)
			}
		}
		
	}else if(params.dataType=='jsonp'){
		//jsonp
		var jsonpCallbackName = params.jsonpCallback || "jsonpCallbac";
		var data = formatSendData(params.data);
		var url = params.url + '?'+ data + 'callback=' + jsonpCallbackName;
		var script = document.createElement("script");
		script.setAttribute('src', url);
		var head = document.getElementsByTagName('head')[0]; 
		head.appendChild(script);

		window[jsonpCallbackName] = function(data){
			head.removeChild(script);
			params.success && params.success.call(me, data);
			return data;
		}
	}
}
function formatSendData(data){
	var str = '';
	for(var key in data){
		str += (encodeURIComponent(key)+'='+encodeURIComponent(data[key]))+'&';
	}
	return str;
}
```

服务端代码：
```
//引入必备模块
var http = require("http");
var fs = require("fs")
var expressLess = require('express-less');
var express = require('express');
var app = express();
var mysql = require('mysql');

var connection = null;

function connect(){
    connection = mysql.createConnection({
        host: '',//主机
        user: '',//用户名
        password: '',//密码
        database: ''//数据库名
    });
    connection.connect();
}

//处理get请求
app.get("*", function(req, res, next) {
    var query = req.originalUrl;
    var jsonpCallback = req.query.callback;

    query =  query.substring(1, query.length).split('?')[0];
    query = '/'+query;

    var table='';//数据库表名称
    var query='';//数据库检索信息
    queryAllData(table,query,function(result,err){
    	var sendData;
        if(err){
            sendData = result;
        }else if(result.content){
            sendData = result.content.toString();
        }else{
            sendData = '{"errno":"10001", "errmsg":"访问页面或者接口不存在！"}';
        }

        //返回信息到客户端
        !jsonpCallback && res.send(sendData);//json
        jsonpCallback && res.send(jsonpCallback + "(" + JSON.stringify(sendData) + ")");//jsonp
    });

});
function queryAllData(table, query, callback){
    connect();//与数据库建立连接
    var sql = '';//sql语句
    connection.query.call(connection, sql,function(err, rows, fields) {
        var result = null;
        if(err){
            result = '{"errno":"10001", "errmsg":"' + err.toString() + '"}';
        }else{
            var result = rows[0] || {};
            connection.end();//断开与数据库的连接
        }
        //执行回调
        callback && callback.call(connection, result, err, rows, fields);
    });
}
```

