---
layout: post
title:  "将AJAX封装为fetch"
date:   2017-08-07 20:29:41 +0800
categories: 前端
tags: ajax
---

在日常开发中我们可能需要使用ajax来异步请求后台数据，但我们不想将`jquery`或者`zepto`全部引用进来。在vue或者react框架开发中
我们一般都是使用`axios`这个异步请求库，此库虽好，但我们完全可以自己封装一个简单可以满足自己日常需求的ajax库。

* 传统ajax封装

```javascript
//fetch.js
export default fetch = (obj = {}) => {
    let obj = obj;
    obj.type = obj.type || 'post';
    obj.url = obj.url || '';
    obj.async = obj.async || true;
    obj.data = obj.data || null;
    obj.success = obj.success || function () {};
    obj.error = obj.error || function () {};
    let xmlHttp = null;
    if (XMLHttpRequest) {
        xmlHttp = new XMLHttpRequest();
    }else {
        xmlHttp = new ActiveXObject('Microsoft.XMLHTTP');
    }
    let params = [];
    for (let key in obj.data) {
        params.push(key+'='+obj.data[key]);
    }
    let postData = params.join('&');
    if (obj.type.toUpperCase() === 'POST') {
        xmlHttp.open(obj.type,obj.url,obj.async);
        xmlHttp.setRequestHeader('Content-type','application/x-www-form=urlencoded;charset=utf-8');
        xmlHttp.send(postData);
    }else if (obj.type.toUpperCase() === 'GET') {
        xmlHttp.open(obj.type,obj.url+'?'+postData,obj.async);
        xmlHttp.send(null);
    }
    xmlHttp.onreadystatechange = () => {
        if (xmlHttp.readyState == 4 && xmlHttp.status == 200){
            obj.success(xmlHttp.responseText);
        }else {
            obj.error(xmlHttp.responseText);
        }
    };
}

//使用方法
import fetch from 'fetch.js';
fetch({
   type: 'post',
   url: 'baidu',
   async: 'true',//可以不写
   data: {
       'data1': 1
   },
   success: function(res) {
     console.log(res);
   },
   error: function(err) {
     console.log(err);
   }
});
```
这个库只是简单讲xmlHttp对象进行了封装，方便使用。但在支持fetch API的浏览器中还不是最优的。

* 进一步对AJAX封装

```javascript
//fetch.js
export default async(type = 'GET', url = '', data = {}, method = 'fetch') => {
	type = type.toUpperCase();

	if (type == 'GET') {
		let dataStr = ''; //数据拼接字符串
		Object.keys(data).forEach(key => {
			dataStr += key + '=' + data[key] + '&';
		});
		if (dataStr !== '')
		{
			dataStr = dataStr.substr(0, dataStr.lastIndexOf('&'));
			url = url + '?' + dataStr;
		}
	}

	if (window.fetch && method == 'fetch') {//对支持fetch的浏览器使用
		let requestConfig = {
			credentials: 'include',//跨域发送cookie
			method: type,
			headers: {
				'Accept': 'application/json',
				'Content-Type': 'application/json'
			},
			mode: "cors",
			cache: "force-cache"
		};

		if (type == 'POST') {
			Object.defineProperty(requestConfig, 'body', {
				value: JSON.stringify(data)
			});
		}

		try {
			let response = await fetch(url, requestConfig);
			let responseJson = await response.json();
		} catch (error) {
			throw new Error(error)
		}
		return responseJson
	} else {
		let requestObj;
		if (window.XMLHttpRequest) {
			requestObj = new XMLHttpRequest();
		} else {
			requestObj = new ActiveXObject;
		}

		let sendData = '';
		if (type == 'POST') {
			sendData = JSON.stringify(data);
		}

		requestObj.open(type, url, true);
		requestObj.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
		requestObj.send(sendData);

		requestObj.onreadystatechange = () => {
			if (requestObj.readyState == 4) {
				if (requestObj.status == 200) {
					let obj = requestObj.response;
					if (typeof obj !== 'object') {
						obj = JSON.parse(obj);
					}
					return obj;
				} else {
					throw new Error(requestObj);
				}
			}
		}
	}
}

//使用方法
import fetch from 'fetch.js';
let getData = () => fetch('post','baidu',{
    data1: 1
});
getData().then((res)=> {
    console.log(res)
},(err)=>{
    console.log(err)
});
```
上面的fetch库遵循es6最新的async编程，它会每次返回promise对象，这样使得异步不会进行深度的嵌套。