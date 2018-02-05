---
title: JSEncrypt长文本分段加解密
date: 2018-02-05 13:54:30
categories: 前端
tags: RSA
---

## 需求出发点 
在进行RSA加密中，由于RSA的特性，一个1024位的密钥只能加密117位字节数据，当数据量超过117位字节的时候，程序就会抛出异常，下面就给出如何完成前端RSA分段解密和后端RSA分段解密。js加密库使用的是比较成熟的JSEncrypted，地址是https://github.com/travist/jsencrypt/tree/master/bin 下载相应的js文件。

## 代码开发
对于加密我的方法就是判断字符的特性，然后进行分段处理，循环加密处理。每次加密117bytes，最后合为一体。代码及注释如下：

```javascript
	/**
     * 长文本加密
     * @param {string} string 待加密长文本
     * @returns {string} 加密后的base64编码
     * */
    JSEncrypt.prototype.encryptLong = function (string) {
        var k = this.getKey();
        try {
            var ct = "";
            //RSA每次加密117bytes，需要辅助方法判断字符串截取位置
            //1.获取字符串截取点
            var bytes = new Array();
            bytes.push(0);
            var byteNo = 0;
            var len, c;
            len = string.length;
            var temp = 0;
            for (var i = 0; i < len; i++) {
                c = string.charCodeAt(i);
                if (c >= 0x010000 && c <= 0x10FFFF) {  //特殊字符，如Ř，Ţ
                    byteNo += 4;
                } else if (c >= 0x000800 && c <= 0x00FFFF) { //中文以及标点符号
                    byteNo += 3;
                } else if (c >= 0x000080 && c <= 0x0007FF) { //特殊字符，如È，Ò
                    byteNo += 2;
                } else { // 英文以及标点符号
                    byteNo += 1;
                }
                if ((byteNo % 117) >= 114 || (byteNo % 117) == 0) {
                    if (byteNo - temp >= 114) {
                        bytes.push(i);
                        temp = byteNo;
                    }
                }
            }
            //2.截取字符串并分段加密
            if (bytes.length > 1) {
                for (var i = 0; i < bytes.length - 1; i++) {
                    var str;
                    if (i == 0) {
                        str = string.substring(0, bytes[i + 1] + 1);
                    } else {
                        str = string.substring(bytes[i] + 1, bytes[i + 1] + 1);
                    }
                    var t1 = k.encrypt(str);
                    ct += t1;
                }
                ;
                if (bytes[bytes.length - 1] != string.length - 1) {
                    var lastStr = string.substring(bytes[bytes.length - 1] + 1);
                    ct += k.encrypt(lastStr);
                }
                return hex2b64(ct);
            }
            var t = k.encrypt(string);
            var y = hex2b64(t);
            return y;
        } catch (ex) {
            return false;
        }
    };
```

如果后台是node，那可以使用下面相应的长文本解密方法。其他语言代码逻辑相同。思路就是117bytes的加密，解密使用128bytes位，也就是每128bytes位解密一次，最后拼接在一起。代码及注释如下：

```javascript
 	/**
     * 长文本解密
     * @param {string} string 加密后的base64编码
     * @returns {string} 解密后的原文
     * */
    JSEncrypt.prototype.decryptLong = function (string) {
        var k = this.getKey();
        var maxLength = 128;
        try {
            var string = b64tohex(string);
            var ct = "";
            if (string.length > maxLength * 2) {
                var lt = string.match(/.{1,256}/g);  //128位解密。取256位
                lt.forEach(function (entry) {
                    var t1 = k.decrypt(entry);
                    ct += t1;
                });
                return ct;
            }
            var y = k.decrypt(string);
            return y;
        } catch (ex) {
            return false;
        }
    };
```

## 使用方法

将上面的两段代码加载JSEncrypt库的后面，使用方法就是将原先加解密调用的方法换为此处的两个方法即可。