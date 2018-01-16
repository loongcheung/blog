---
layout: post
title:  "AJAX请求时响应成功执行error的问题"
date:   2017-08-01 16:22:41 +0800
categories: 前端
tags: 跨域
---

有时候我们在请求后台的时候明明已经得的了后台的相应但是没有进入success而是进入了error代码快中，处理的方法有以下几种：

* 将`dataType:jsonp`或者`dataType：json`改为`dataType:text`，这种处理方法的依据是后台返回的数据并不是json格式，这个时候我们就要将其转换为普通文本文档处理

* 如果上述方法无效，那就是后台设置的jsonp跨域函数不是callback，而是自定义的，我遇到的就是后台写为jsoncallback。后台的代码一般如下(php):

```php
    <?php
     
    $data = ".......";
    $callback = $_GET['callback']; //callback为默认，我遇到就是jsoncallback
    echo $callback.'('.json_encode($data).')';
    exit;
     
    ?>
```
这个时候我们就将添加`jsonp:callback`(这里要和后台一致，默认为callback)，因为如果前端不添加这个字段就等于默认传callback，而后台获取不到自己定义的jsoncallback。这样就可以处理这个问题。

