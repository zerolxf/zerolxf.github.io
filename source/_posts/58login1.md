---
title: Java 模拟 58登录 （一） 分析
categories:
    - Java
date: 2018-01-17 00:23:36
tags: Java
---


> 实习期间需要做一个模拟58登录然后爬取简历，第一次做项目遇到很多坑
同时网上关于58登录的又没有很多资料，遇到了很多坑，这里记录一下，方便自己以及他人学习

<!-- more -->

源代码放在[Github](https://github.com/zerolxf/Login58)

## 前言

这里是通过手机动态码的方式来登录58, 而非帐号密码, 适用于第三方网站登录使用
原因是模拟密码登录的时候, 当我用其他人账号在我这里登录58的时候就会出现账号登录限制
需要手机动态码验证之后才可以登录，所以决定直接手机动态码的登录。

<!-- more -->

**如果你知道这里的机制或者知道如何避免，请告知我liangxianfeng96@qq.com**

## 分析

###  记录Http Request请求
首先是登录58， 查看登录期间的Http Request请求
![request 请求][1]

其中，connect和clientid对我们模拟登录没有影响
可以发现大致的流程如下
> - 发送Get请求（data？）， 获取 token
-   发送Get请求（getcode？）， 请求发送手机验证码， 同时获得一个tokencode
-   发送Get请求（login？）， 输入手机号，动态码以及其他相关参数来发送登录请求
-   登录成功


###  查看登录Js代码
我们先看下登录的关键js代码，看都需要哪些参数
```JavaScript
            function codeCommitLogin() {
                loginClickLog("from=PC_login_sj_dlqq");
                $("#loginMobileButton").attr("disabled", "disabled");
                var source = $("#source").val();
                var mobile = $("#loginMobile").val();
                var mobilecode = $("#loginMobilecode").val();
                var validatecode = $("#validatecodeMobile").val();
                var token = $("#tokenMobile").val();
                var tokencode = $("#tokencodeMobile").val();
                var path = $("#path").val();
                var rsaModulus = $("#rsaModulusMobile").val();
                var rsaExponent = $("#rsaExponentMobile").val();
                var timesign = (new Date).getTime() + timespan;
                var isremember = $("#isremember_id_new_mobile").prop("checked");
                var fingerprint = "";
                if (typeof fingerPrint != "undefined") {
                    fingerprint = fingerPrint.get()
                }
                if (typeof Fingerprint2 != "undefined") {
                    var fp = new Fingerprint2;
                    var finger2 = fp.get()
                } else {
                    var finger2 = ""
                }
                var p = encryptString(timesign + encodeURIComponent(mobile), rsaExponent, rsaModulus);
                var url = "//passport.58.com/mobile/pc/login?callback=?&mobile=" + p + "&mobilecode=" + mobilecode + "&source=" + source + "&token=" + token + "&tokencode=" + tokencode + "&fingerprint=" + fingerprint + "&isremember=" + isremember + "&finger2=" + finger2 + "&path=" + path;
                $.getJSON(url, function(result) {
                    codeSuccessCommitLoginFunction(result)
                })
            }
```
### 参数分析
> - 这里的话，finger2和fingerprint是唯一确定浏览器的参数，每个浏览器都有不同的值
这里直接从浏览器拷贝，没有影响
-   timeSpan时间戳， 表示的是整个登录的时间+常数1411093327735
-   密文p， 是timesign + encodeURIComponent(mobile)的密文，rsaModulus 和 rsaExponent ，加密参数，58一直不变
-   其他参数直接和request请求一致，或者酌情设置

### 获取token

请求的URL Request
> URL:https://passport.58.com/frontend/data?callback=jQuery18308426383909637498_1515994624659&_=1515994624692

Response
> jQuery18305434571389149673_1515995590691({"code":0,"data":{"remainTime":0,"token":"7pjUnv6fhBONHh7A_4HrN_2rH4is-gC8","rsaModulus":"xxx固定值xxx",
"rsaExponent":"010001","totalTime":60,"mobile":"手机号"},"msg":""})

如上，其中callback参数中jQuery18308426383909637498_1515994624659和_=1515994624692，一个表示回调的函数+时间戳，一个应该是发送请求时候的时间戳，这里可以参考jquery资料

那么这个请求是页面刚加载就发送的请求，从response可以看出主要是获取token参数


### 获取tokencode

Request URL
>https://passport.58.com/mobile/getcode?

参数
![参数][2]
Response
> jQuery18305434571389149673_1515995590693({"code":0,"data":{"exist":0,"tokencode":"GhxN2kozUcU8wsbkzGXbMmst8G4_0sSg"},"msg":"动态码已发送"})

###  发送login请求
Request URL
> https://passport.58.com/mobile/pc/login?

参数
![login参数][3]

Response
> jQuery18305434571389149673_1515995590693({"code":0,"data":{"fingerprint":"84E63543A0E4DF048727E0037314879E66AE767A7311EA8C"},"msg":"成功"})


以上便是整个登录的流程

## 总结
由于是第一次做Java项目，很多都不懂而且前期公司网不好半天才能刷出个界面，然后导致这个功能做了很久.
最初我试着用selenium框架去模拟登录，但是！！！公司网络不行并且我觉得这样效率太慢了就没有进行下去，后面转入Java模拟登录
前期很多时间都放在了去读Js代码，手动模拟添加部分cookie，以及模拟java加密过程，其实这三个后面发现都是在做无用功
然后自己登录成功后发现其他人账号不能登录，又转到手机动态码登录。。。

做这个功能最大的体会就是，做项目先不要深入的去了解某一个环节(这里我花了几天都在读js代码，Java模拟js加密)，应该显示着做出一个最简单的demo，遇到问题再去学习解决

最后这个模拟登录还是很在很多问题，希望有了解的人可以告诉应该怎么做才最好




  [1]: http://oh9ex6wd2.bkt.clouddn.com/58_1.JPG
  [2]: http://oh9ex6wd2.bkt.clouddn.com/58_2.JPG
  [3]: http://oh9ex6wd2.bkt.clouddn.com/58_3.JPG
