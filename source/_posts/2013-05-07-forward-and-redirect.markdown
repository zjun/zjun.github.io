---
layout: post
title: "Forward & Redirect"
date: 2006-09-19 18:33
comments: true
external-url: 
categories: Web开发
---
>使用forward的話，網址列上並不會出現被轉發的目標位址，而且forward是在Web應用程式之內進行，可以訪問Web應用程式的隱藏目錄，像是WEB-INF，然而forward只能在Web應用程式中進行，不能指定至其它的Web應用程式位址。


>使用redirect的話，是要求客戶端瀏覽器重新發出一個指定的請求位址，因此網址列上會出現被重導的目錄位址，重導的請求是由瀏覽器發出，所以不能訪問Web應用程式中的隱藏目錄，像是WEB-INF，然而重導是重新要求一個網頁，所以可以指定至其它的Web應用程式位址。 