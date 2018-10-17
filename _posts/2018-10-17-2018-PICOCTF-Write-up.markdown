---
layout: post
title:  2018 PICOCTF Write up
date:   2018-10-17 07:51:37 -0400
categories: CTF
---
一人でPICOCTFに参加しました、competitionはScore 7760で終了しましだ。
![progress](/images/pico2018/progress.JPG)

以下是窩的Progress
是窩第二次的CTF吧

個人主要是做Forensics和Web Exploitation 題目

General Skills 大多都是A氏幫我解掉的

所以這邊的write-up會多是Web Exploitation那方面的

以下是窩解的題目

***

## [Web] Inspect Me (Points: 125)

F12之後再去Network那裡看每一個file，再把下面comment的組起來
GET FLAG

## [Web] Client Side is Still Bad (Points: 150)

view-source之後把verify()裡的東西組起來
![ClientSideisStillBad](/images/pico2018/ClientSideisStillBad150.JPG)
GET FLAG


## [Web] Logon (Points: 150)

在login裡什麼也不輸入的情況下Sign In，Login之後他會跟你說 No flag for you
![Logon2](/images/pico2018/Logon150_2.JPG)
這時候用Edit the cookie之類的東西，把admin裡的false改成true，再F5
![Logon3](/images/pico2018/Logon150_3.JPG)
GET FLAG


## [Web] Irish Name Repo (Points: 200)

先不說他的解法好不好玩，但窩頗喜歡他的內容w

由Support裡能得知 這題目是SQL injection
去Login Page，在username做SQL injection登入
```
admin' --
```
![IrishNameRepo200_2](/images/pico2018/IrishNameRepo200_2.JPG)
GET FLAG

## [Web] Mr. Robots (Points: 200)

題目名稱已經是提示。進去網站之後在URL加上
```
/robots.txt
```
之後裡面的Disallow的html就是放著Flag

## [Web] No Login (Points: 200)

這題是令窩很火的一題，窩還在decode那個cookie是什麼鬼東西，但原來是不用想太多。
窩被這題玩了兩天，Sinon氏五分鐘也不用就把Flag找出來了_(:□ 」∠)_

開一個叫`admin`的cookie，他的value也是`admin`再按Flag就GET FLAG了。
把窩兩天還回來


## [Web] Secret Agent (Points: 200)

這個就是用chrome UA Spoofer來改成Googlebot User Agent後，再按FLAG就行了

## [Web] Button (Points: 250)

開Inspect，改HTML
```HTML
<form action="button2.php" method="POST">
<input type="submit" value="PUSH ME! I am your only hope!"/>
</form>
```
再按那個PUSH ME! 就有FLAG了

## [Web] The Vault (Points: 250)

對對，這題多加了filter。我和Sinon氏也很87地在Username試SQL injection
但其實這裡沒有在password做filtering，直接在Password裡`1' or 1=1 --`之後就GET FLAG

## [Web] Artisinal Handcrafted HTTP 3 (Points: 300)

這題可以說是不錯的練習，做完會對Header更熟
但要輸入的東西
先nc過去那個再過了那個CAPTCHA
```
GET / HTTP/1.1
Host: flag.local
```
之後就能看到source code得知到他有/login
```
GET /login HTTP/1.1
Host: flag.local
```
這個login system是 `method="POST"`，所以要用POST的方式來用GIVEN的account來登入
```
POST /login HTTP/1.1
Host: flag.local
Content-Type: application/x-www-form-urlencoded;charset=utf-8
content-length: 40

user=realbusinessuser&pass=potoooooooo
```
他會回一個cookie，再把cookie也放進Request裡
```
GET / HTTP/1.1
Host: flag.local
Cookie: xxxxxxx
```
之後就會回FLAG了

## [Web] Flaskcards (Points: 350)

這題在題目就會知道Flask，可是我不會這東西w
之後H氏跟我說了這是Template injection

先在網站Register，Username跟Password隨心吧。Login之後去create card，在question裡輸入`{{config}}`，answer也是隨心就好。
然後去List Cards，就會看到一堆文字，找一下之會看到FLAG了
![flaskcards350_2](/images/pico2018/flaskcards350_2.JPG)

## [Web] Help Me Reset 2 (Points: 600)

窩有99.9%相信窩的解法不是正常的解法
Inspect會看到 `<!--Proudly maintained by crandall-->`
那個user每次也會不同。之後去Log In 的 Forgot your password?。 輸入了剛給的username之後要你回答安全問題的東西，好像錯了兩次就要reload重來。
窩在這邊還算是正確地做吧。窩在這邊看到一個`<link rel="javascript" href="/static/js/reset.js">`，但窩不能直接去看到那個`/static/js/reset.js`，去看的話會看到以下的
```
Not Found
The requested URL was not found on the server. If you entered the URL manually please check your spelling and try again.
```
所以窩在想，是不是directory出問題。用了dirb來把掃掃看。
![HelpMeReset2_600_1](/images/pico2018/HelpMeReset2_600_1.jpg)

嗯...沒見過的URL欸，進去看看吧！
![HelpMeReset2_600_2](/images/pico2018/HelpMeReset2_600_2.jpg)
是FLAG啊！！！

---
以上是我解掉的Web，其他就再等我有空吧