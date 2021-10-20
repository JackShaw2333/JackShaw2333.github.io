---
title: HTTP首部字段
date: 2019-12-27 16:33:32
categories: [network, http]
tags: 
- http
- network
---

# 引言

部分摘取自书籍《图解HTTP》

# 4种Http首部字段类型

1. 通用首部字段（General Header Fields）
2. 请求首部字段（Request Header Fields）
3. 响应首部字段（Response Header Fields）
4. 实体首部字段（Entity Header Fields）

<!--more-->

# Http/1.1通用首部字段

## Cache-Control

操作缓存的工作机制。

指令的参数可选，多个指令通过“,”分隔。

### public

> Cache-Control: public

明确表明其他用户也可利用缓存。

### private

响应只以特定的用户作为对象，与`public`指令的行为相反。

### no-cache

为了防止从缓存中返回过期的资源

客户端：表示客户端不接收缓存过的响应。缓存服务器必须把客户端请求转发给源服务器。

服务器：缓存服务器不能对资源进行缓存。源服务器以后也不再对缓存服务器请求中提出的资源有效性进行确认，禁止其对响应资源进行缓存操作。

由服务器返回的响应中，若报文首部字段`Cache-Control`对`no-cache`指定了参数，如

> Cache-Control: no-cache=Location

那么客户端在接收到这个指定了参数值的首部字段对应的响应报文后，就不能使用缓存。

### no-store

暗示请求或响应中包含机密信息。因此缓存不能在本地存储请求或响应的任一部分。

### s-maxage

与`max-age`的功能相同，不同点在于`s-maxage`只适用于供多位用户使用的公共缓存服务器。对于向同一用户重复返回响应的服务器来说，这个指令没有任何作用。

当使用s-maxage指令后，忽略`Expires`首部字段和`max-age`指令的处理。

### max-age

**客户端**请求包含此指令时，若缓存资源的缓存时间小于指定时间，客户端就接受缓存的资源。若`max-age=0`，缓存服务器通常要将请求转发给源服务器。

**服务器**返回的响应包含此指令时，缓存服务器不再对资源有效性作确认，`max-age`数值表示资源保存为缓存的最长时间。

HTTP/1.1版本的缓存服务器在遇到同时存在`Expires`首部字段时，优先处理`max-age`指令，忽略`Expire`字段。而HTTP/1.0版本则相反。

### min-fresh

要求缓存服务器返回至少还未过指定时间的缓存资源。

### max-stale

此时若缓存资源过期也照常接收。

若未指定参数值，无论多久客户端都会接收响应；

若指定参数，则在过期后，若仍处于`max-stale`指定的时间内，仍旧被客户端接收。

### only-if-cached

客户端仅在缓存服务器本地缓存了目标资源的情况下才要求其返回。

若本地缓存无响应，返回状态码`504 Gateway Timeout`

### must-revalidate

代理向源服务器再次验证即将返回的响应缓存目前是否仍然有效。

若代理无法联通源服务器再次获得有效资源时，返回状态码`504 Gateway Timeout`。

此外，`must-revalidate`会忽略`max-stale`。

### proxy-revalidate

要求所有的缓存服务器在返回响应前，必须再次验证缓存的有效性。

### no-transform

规定无论在请求或响应中，缓存不能改变实体主体的媒体类型。可防止缓存代理压缩图片等类似操作。

## Connection

具备以下两个功能：

1. 控制不再转发给代理的首部字段
2. 管理持久连接

### 控制不再转发给代理的首部字段

> Connection: 不再转发的首部字段名

此即`hop-by-hop`首部。

### 管理持久连接

HTTP/1.1版本默认连接为持久连接。当服务器端想明确断开连接时，指定`Connection`字段值为`Close`。

> Connection: close

之前的版本默认非持久连接，若想持久连接，须指定`Keep-Alive`

> Connection: Keep-Alive

## Date

表明创建HTTP报文的日期和时间。

## Pragma

历史遗留字段，作为与HTTP/1.0向后兼容而定义。

规范的形式唯一，为

> Pragma: no-cache

属于通用首部字段，但只在客户端发送的请求中。客户端会要求所有的中间服务器不返回缓存的资源。

为保证所有版本有效，发送的请求一般同时含有如下两个字段：

> Cache-Control: no-cache
> Pragma: no-cache

## Trailer

事先说明报文主体后记录了哪些首部字段。可用于HTTP/1.1版本分块传输编码时。

栗子：

> HTTP/1.1 200 OK
> Date: blahblahblah
> Content-Type: text/html
> ...
> Trailer: Expires
> ...（报文主体）
> 0
> Expires: blahblahblah

## Transfer-Encoding

规定了传输报文主体时采用的编码方式。仅对分块传输编码有效。

> Transfer-Encoding: chunked

HTTP/1.1的传输编码方式仅对分块传输编码有效。

## Upgrade

检测HTTP协议及其他协议能否用更高的版本进行通信，参数值可为一个完全不同的通信协议。

需额外指定`Connection: Upgrade`。

对首部有`Upgrade`的请求，服务器可用`101 Switch Protocols`作为响应返回。

## Via

追踪客户端与服务器之间的请求和响应报文的传输路径。

报文经过代理或网关时，会先在首部字段`Via`中附加该服务器的信息，然后再进行转发。

`Via`还可避免请求回环发生，所以必须在经过代理时附加该首部字段内容。

栗子：

> GET / HTTP/1.1
> Via: 1.0 gw.hackr.jp (Squid/3.1),
> 1.1 a1.example.com (Squid/2.7)


## Warning

告知用户与缓存相关的问题警告。

# 请求首部字段


