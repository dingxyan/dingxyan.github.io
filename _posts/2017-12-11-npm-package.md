---
layout: post
title:  npm package.json
categories: npm
tags:   npm
---

### 要求，严格的json格式

```
{
	"name": "name",//*必填，要求与项目名称一致，不允许空格和大写，要求小写字母，可以有短横线和下划线，但不能以横线或者下划线开头；当你的项目作为一个包发布后，其他项目引入的时候，引用名称便是该name
	"version": "1.0.0",//*必填，固定三级格式
	"main":"index.js",//程序的入口文件；当你的项目作为一个包发布后，其他项目引入的时候，默认引用该文件
	"description":"",//描述
	"keywords":"",//关键词，帮助检索
	"homepage":"",//项目主页链接
	"bugs":{
		"url" : "https://github.com/owner/project/issues",
		"email" : "project@hostname.com"
	},//bug链接或者邮箱，当用户遇到问题后，方便与开发者联系
	"dependencies":{//程序运行依赖的包
	},
	"devDependencies":{//开发过程中需要依赖的包
	},
	"peerDependencies":{
	},
	"license":"",
	"bin": {//在路径中安装可执行文件，类似于配置全局变量；例如karma，这样设置后，局部安装karma的时候，会在node_modules/.bin 目录下创建链接，局部可以直接使用karma命令
	    "karma": "./bin/karma"
	},
	"files":[//描述一组文件，项目所包含的一组文件
	],
	"repository" :{ //代码地址
		"type" : "git",
		"url" : ""
	},
	"scripts":{//脚本
	},
	"contributors":{},
	"author":{},
	"engines":{}//node版本
}
```

.npmignore  .gitignore

"module" 参数 "jsnext:main"
 直接引入，以main为主，rollup webpack2 以module优先读取


