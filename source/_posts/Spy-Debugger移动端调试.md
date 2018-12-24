---
title: Spy-Debugger移动端调试
date: 2018-12-24 23:19:24
categories: 工具
tags: 工具
---

## Spy-Debugger 移动端调试

#### 安装
```shell
npm install spy-debugger -g
npx spy-debugger
```

#### 使用步骤
1.手机和PC保持在同一网络下（比如同时连到一个Wi-Fi下）。
<br>
2.命令行输入spy-debugger，按命令行提示用浏览器打开相应地址。
<br>
3.设置手机的HTTP代理，代理IP地址设置为PC的IP地址，端口为spy-debugger的启动端口(默认端口：9888)。
Android设置代理步骤：设置 - WLAN - 长按选中网络 - 修改网络 - 高级 - 代理设置 - 手动。
iOS设置代理步骤：设置 - 无线局域网 - 选中网络 - HTTP代理手动。
<br>
4.手机安装证书。注：手机必须先设置完代理后再通过(非微信)手机浏览器访问https://github.com/wuchangming/spy-debugger/blob/master/demo/img/QRCodeForCert.png
安装证书。
<br>
5.用手机访问你要调试的页面即可。
