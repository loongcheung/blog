---
title: 实现一个Markdown实时转HTML预览
date: 2018-02-01 11:16:07
categories: 后端
tags: markdown
---

* ### 需求描述

在后台管理系统写文章的时候，产品希望有个预览模式，左边用markdown语法写，右边能实时预览到最终效果。这样的效果在很多文章类网站都有实践，比如简书。经过分析后，实现的方法就是实时的监听文件的变化，然后转换为HTML，发送给浏览器渲染。

* ### 实现思路

1、首先使用fs模块的watch方法监听MD文件的变化。
<br/>
2、当监听到文件变化后，借助marked包提供的mared方法将md内容转换为html。
<br/>
3、将得到的html以及读入的css文件替换到模板，得到最终要渲染的html。
<br/>
4、最后利用BrowserSync模块实现浏览器自动刷新，即每当改变markdown文件，浏览器中会自动刷新显示。

代码如下：

```javascript
const fs = require('fs');
const path = require('path');
const marked = require('marked');
const browserSync = require('browser-sync');

//接收需要转换的文件路径
const target = path.join(__dirname, process.argv[2] || 'README.md')
//最终生成的html文件的位置
const filename = target.replace(path.extname(target), '.html')
//获取html文件名
const indexpath = path.basename(filename);

//通过browser-sync创建一个文件服务器
browserSync({
    notify: false,
    server: path.dirname(target),//网站根目录
    index: indexpath//默认文档
})

//监视文件变化，可以理解为当文件发生变化（需要保存才能触发文件变化)，interval时间间隔后调用回调函数
fs.watchFile(target, { interval: 200 }, (cur, prev) => {
    // console.log(`current:${cur.size} previous:${prev.size}`);
    //判断文件的最后修改时间是否改变，减少不必要的转换
    if (cur.mtime === prev.mtime) {
        return false;
    }
    fs.readFile(target, 'utf8', (err, content) => {
        if (err) {
            throw err;
        }
        let html = marked(content);
        // console.log(html);
        fs.readFile(path.join(__dirname, 'github.css'), 'utf8', (err, css) => {
            html = template.replace('{{{content}}}', html).replace('{{{styles}}}', css);
            // console.log(html);
            fs.writeFile(filename, html, (err) => {
                if (err) {
                    throw err;
                }
                browserSync.reload(indexpath);
                console.log('updated@' + new Date());
            })
        })
    })
})
const template = `
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Markdown to Html</title>
    <style>{{{styles}}}</style>
</head>
<body>
    <div class='vs'>
    {{{content}}}
    </div>
</body>
</html>`;
```

