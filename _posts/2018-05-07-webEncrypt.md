---
layout: post
title:  "常见web加密方式"
date:   2018-05-07 15:16
categories: web安全
permalink: /archivers/enc
---
## 对称加密

采用单钥密码系统的加密方法，同一个密钥可以同时用作信息的加密和解密，这种加密方法称为对称加密，也称为单密钥加密。

<div class="mermaid">
sequenceDiagram
    participant 加密
    Note left of 加密:原始数据 明天回家
    participant 解密
    Note right of 解密:原始数据 明天回家
    加密->解密: 密钥
    Note over 加密,解密: 加密密文 jaskfhjdshf
</div>

在对称加密算法中常用的算法有：DES、3DES、TDEA、Blowfish、RC2、RC4、RC5、IDEA、SKIPJACK、AES等。

**DES3加密**

[DES3.js](https://github.com/jqing1968/tools/blob/master/DES3.js)
```js
    var str="123"
    var key="abcd"
    console.log(DES3.encrypt(key,str));//XupdZhIkd5k=
    console.log(DES3.decrypt(key,"XupdZhIkd5k="));//123
```

## MD5加密

MD5消息摘要算法（英语：MD5 Message-Digest Algorithm），一种被广泛使用的密码散列函数，可以产生出一个128位（16字节）的散列值（hash value），用于确保信息传输完整一致，利用MD5算法来进行文件校验的方案被大量应用到软件下载站、论坛数据库、系统文件安全等方面。

[MD5.js](https://github.com/jqing1968/tools/blob/master/md5.js)
```js
    var str="123"
    console.log(hex_md5(str));
    // 加盐 这个盐要足够长足够复杂足够咸
    var salt="khadaffdfasfka**ssss!%$$^&";
    console.log(hex_md5(str)+salt);
```

## base54编码

Base64是网络上最常见的用于传输8Bit字节码的编码方式之一，Base64就是一种基于64个可打印字符来表示二进制数据的方法。

[base64.js](https://github.com/jqing1968/tools/blob/master/base64.js)
```js
    var str=123
    var bs=new Base64()
    console.log(bs.encode(str));//MTIz
    // 解密
    var str1="MTIz"
    console.log(bs.decode(str1));//123
```

## 非对称加密

非对称加密算法需要两个密钥：公开密钥（publickey）和私有密钥（privatekey）。公开密钥与私有密钥是一对，如果用公开密钥对数据进行加密，只有用对应的私有密钥才能解密；如果用私有密钥对数据进行加密，那么只有用对应的公开密钥才能解密

**公钥** :用于向外发布，任何人都能获取，
**私钥** :要自己保存，切勿给别人

**情况1**：公钥用于【加密】， 私钥用于【解密】

如果加密密钥是公开的，这用于客户给私钥所有者上传加密的数据，这被称作为公开密钥加密(狭义)。
例如，网络银行的客户发给银行网站的账户操作的加密数据。HTTPS 等。

**情况2**：公钥用于【解密】，私钥用于【加密】

如果解密密钥是公开的，用私钥加密的信息，可以用公钥对其解密，用于客户验证持有私钥一方发布的数据或文件是完整准确的，接收者由此可知这条信息确实来自于拥有私钥的某人，这被称作数字签名，公钥的形式就是数字证书。例如，从网上下载的安装程序，一般都带有程序制作者的数字签名，可以证明该程序的确是该作者（公司）发布的而不是第三方伪造的且未被篡改过（身份认证/验证）。

**SSH免密码登录**

