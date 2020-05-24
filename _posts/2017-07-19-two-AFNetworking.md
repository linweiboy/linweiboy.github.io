---
title: AFNetworking3.0/NSURLSession的理解
description: 作为一个iOS开发者来说，AFNetworking是我很熟悉的一个开源库了，所以对它的了解有必要知道更多些.
categories:
 - iOS
tags:
---

> 很多时候，AFNetworking都是目前iOS开发者网络库中的不二选择。Github上2W+的star数足见其流行程度。而从iOS7.0开始，苹果推出了新的网络库继承者NSURLSession后，AFNetworking也毫不犹豫地加入了对其的支持。3.0+更加只是提供了NSURLSession的支持。

<!-- more -->

## AFNetworking常用的写法

我们使用AFNetworking的时候，可能会有很多的朋友都会采用以下的写法：
```
AFHTTPSessionManager *sessionManager = [AFHTTPSessionManager manager];
    sessionManager.requestSerializer     = [AFHTTPRequestSerializer serializer];
    sessionManager.responseSerializer    = [AFHTTPResponseSerializer serializer];
    [sessionManager GET:urlString
             parameters:parameters
               progress:progressBlock
                success:successHandler
                failure:failureHandler];
```
大概可以描述一下这个过程，每次开启一个网络请求时，首先新建一个AFHTTPSessionManager，然后将相关的requestSerializer和reponseSerializer赋值；最后发起相应的GET/POST等请求。

而如果是直接采用NSURLSession来请求网络呢，我们则经常会采用以下的写法：
```
NSURLSession *session =  [NSURLSession 
    sessionWithConfiguration:
    [NSURLSessionConfiguration defaultSessionConfiguration]
     delegate:nil
      delegateQueue:[NSOperationQueue mainQueue]];
      
      NSURLSessionDataTask *dataTask = [session dataTaskWithRequest:request
      completionHandler:completionHandler];
      
    [dataTask resume];
```
这个过程其实和上面的基本一致。新建一个Session，然后新建task，激活task，完成网络请求。

那么现在问题来了。为什么每次都需要新建一个SessionManager/Session？如果在多个Task请求的情况下，如果采取一个共享的SessionManager/Session是否可行？如果可行，与之前每次新建SessionManager/Session相比，孰优孰劣？


## 这篇文章的主要目的

本篇文章会告诉您：
1. 为什么要使用NSURLSession而不是NSURLConnection
2. 为什么要用共享的SessionManager/Session，而不是每次都启动一个新的
为什么要选择NSURLSession

NSURLSession在iOS7.0时被Apple提出后，虽然Apple一直对其良好的API设计大力推广，然而其能够达到的效果，似乎一直都和NSURLConnection不相伯仲。

特别是在网络的Dependecy依赖处理上，由于AFNetworking优秀的架构设计，NSURLSession甚至还不如NSURLConnection好用。那么，有什么理由切换到NSURLSession？ 2015年的WWDC似乎告诉了我们答案。

HTTP /2, 2015年5月RFC 7540正式发表的下一代HTTP协议，是1999年来HTTP 1.1发布后的首个更新。相对于前一个版本，HTTP /2以快著称。对相同图片、相同服务器的下载，在不同协议下所需的时间：
>对于HTTP 1.1与HTTP 2加载时间对照（[这是我查了HTTP官方解释得到的](https://http2.akamai.com/demo)）   HTTP 1.1 load time：9.63s    HTTP 2  load time：2.32s

这里我们并不打算展开HTTP /2的原理，有兴趣的同学可以Google之。根据[2015的WWDC Session711](https://developer.apple.com/videos/play/wwdc2015/711/)，我们知道iOS9+，NSURLSession开始正式支持HTTP /2，也就意味着你的网络连接速度也可以有如上图那样的提升。

更人性化更优秀的API设计，HTTP /2的支持，这是否能成为你使用NSURLSession的理由？至少它们成为了说服我的理由。

## 为什么要尽量共享Session，而不是每次新建Session

在回答这个问题以前，我们先来聊聊网络的通讯协议。我们也都知道，HTTP协议是基于TCP协议的。所以在每次的HTTP请求之前，客户端和服务器端，都先需要经过TCP连接的三次握手，即每次请求之前，网络的数据都已经在客户端和服务器端之间来回了三次。如下图：

![TCP三次握手（图片来源网络）](https://image.baidu.com/search/detail?ct=503316480&z=0&ipn=d&word=TCP%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B&step_word=&hs=0&pn=9&spn=0&di=53130&pi=0&rn=1&tn=baiduimagedetail&is=0%2C0&istype=0&ie=utf-8&oe=utf-8&in=&cl=2&lm=-1&st=undefined&cs=480079048%2C3952040209&os=3061573288%2C1237351487&simid=4024651412%2C769276171&adpicid=0&lpn=0&ln=904&fr=&fmq=1590304993091_R&fm=&ic=undefined&s=undefined&hd=undefined&latest=undefined&copyright=undefined&se=&sme=&tab=0&width=undefined&height=undefined&face=undefined&ist=&jit=&cg=&bdtype=0&oriquery=&objurl=http%3A%2F%2Fimg.it610.com%2Fimage%2Finfo5%2F3a481616717741198d6e8afc36f81452.png&fromurl=ippr_z2C%24qAzdH3FAzdH3Fooo_z%26e3Btpm8a_z%26e3Bv54AzdH3Fw6ptvsjAzdH3F9mlbbnn_z%26e3Bip4&gsm=a&rpstart=0&rpnum=0&islist=&querylist=&force=undefined)


关于HTTP的三次握手，我在另一篇博文有介绍，可自行查阅相关了解。

**事实上，苹果的文档中，还对一个服务器最高的TCP并发有相应的描述**：

```
HTTPMaximumConnectionsPerHost  Property
The maximum number of simultaneous connections to make to a given host.

Declaration
SWIFT
    var HTTPMaximumConnectionsPerHost: Int
OBJECTIVE-C
    @property NSInteger HTTPMaximumConnectionsPerHost
Discussion
This property determines the maximum number of simultaneous connections made to each host by tasks within sessions based on this configuration.

This limit is per session, so if you use multiple sessions, your app as a whole may exceed this limit. Additionally, depending on your connection to the Internet, a session may use a lower limit than the one you specify.

The default value is 6 in OS X, or 4 in iOS.

Availability
Available in iOS 7.0 and later.
```

我们可以看到，默认配置下，iOS对于同一个IP服务器的并发最大为4，OS X为6。而如果你没有使用共享的Session，则可能会超过这个数。

因此，如果能用共享的Session，还是用共享的吧。有些许的网络加速，也是一件不错的事情.
