---
title: 微信公众号在ios授权登录时，无法重写向问题？
date: 2021-02-21 12:56:07
tags: 微信小程序
---



Https://open.weixin.qq.com/connect/oauth2/authorize？在IOS中，访问微信授权的链接
Appid=APPID&redirect_uri=REDIRECT_URI&response_type=code&scope=SCOPE&state=STA
按照道理是会回调到重定向_uri_的，我写了我自己域名下的一个接口来接收
应该返回的代码，获取授权后会跳转到这里，可是IOS下授权后卡在自动登录那个界面，无法跳转，
进不到后台去.使用android和web开发者工具都是没问题的
解决方案
已经完成了，搞了2天，需要适配到IOS的时候，重定向_uri一定要使用http不能使用https。同时android也能使用http的。Android也能使用的