---
layout: post
title:  2021 Cyber Apocalypse CTF Write up
date:   2021-04-25 13:45:37 +0800
categories: CTF
---

このCTFはHack The Boxさんが2021年4月19日から23日まで、5日のCTFです。今回はチームBlack Bauhiniaのみなさんと一緒に遊びました。16800 ptsで14位でした。解けたの54問の中で、わたしは4問は自力解いた、2問はMystizさんと協力で解けました。

***

## [Hardware] Serial Logs
題目給了一個sal的file。先用saleae的Logic開起來，把file掉上去，在Analyzers上用Async Serial，把Bit Rate set為115200，按Save
![images](/images/HTBCTF2021/Screenshot-from-2021-04-25-15-35-39.png)
用115200是因為115200是常用的baud rate(bit rate)
用Async Serial是因為他看上去很像UART的東西
就會看到Date已經是人能讀的東西 
```
[LOG] Connection from 4b1186d29d6b97f290844407273044e5202ddf8922163077b4a82615fdb22376
[LOG] Connection from ebd967f3ed47d5410160d3ee603884a32b426d5f3a84212697290c922407d45e
[LOG] Connection from ab290d3a380f04c2f0db98f42d5b7adea2bd0723fa38e0621fb3d7c1c2808284
...
[ERR] Noise detected in channel. Swithcing baud to backup value
\xEE\x1E\xEC~\x9E\x10\xF2\x1E|`~\x1C\xEE|\x9E\x1C\x0E\x1C\x1Eb\x9C\x8E|\x1E|\x1C\x8C\x9C\x8Er\x1E|\x8E\x0C\x9E|p\xE0|\x0E\x10\x1E\x1C\xE2p\x9C\x9Cpp\xE2p\x8E\x1C\x1C\x1C\xE2pp|\x8E\x90|\x8E\x80p\x8E\x8C|\x8E\x8Epp\x8E\x90|\xE2\x92p\x1C\x80p\x1C\x10\x1E\x9C\x1C\x1C||\x8C|\x1C\x82|\x0E\x8Epp\x8E\x9E|p\x90|\xE0\xE0|\x1C\x9Cpp\x92p\x1C\x10\x1Ep\x8C|\x1C\x8Ep\x8E\x9Cp\x02\xEE\x1E\xEC~\x9E\x10\xF2\x1E|`~\x1C\xEE|\x9E\x1C\x0E\x1C\x1Eb\
...
```
但是後半還有讀不到的東西。而且都寫了`baud to backup value`，要找新的baud rate了
量一個由0去1的長度，在logic是ΔT，再計回每秒send多少bit，所以是:
![images](/images/HTBCTF2021/Screenshot from 2021-04-25 18-26-54.png)
baud rate = 1/ΔT
這個大約是13\*10^-6s，baud rate大約是78000
調了baudrate再看到最後就會找到flag了
![images](/images/HTBCTF2021/Screenshot from 2021-04-25 18-35-32.png)
`CHTB{wh47?!_f23qu3ncy_h0pp1n9_1n_4_532141_p2070c01?!!!52}`

## [Hardware] Compromised
這題跟Serial Logs的做法差不多，也是在Logic就能找到flag。
這次的sal file在channel 0跟1都有signal。
仔細看的話channel 1都在重覆同一種patten，所以把他調去I2C，Channel 0用SDA，Channel 1用SCL。就會在Data看到是人能讀的東西
![images](/images/HTBCTF2021/Screenshot from 2021-04-25 19-55-29.png)
之後把`write to 0x2C ack data`的Hex記下來再轉ASCII就找到flag
![images](/images/HTBCTF2021/Screenshot from 2021-04-25 20-04-56.png)
`CHTB{nu11_732m1n47025_c4n_8234k_4_532141_5y573m!@52)#@%}`

## [Hardware] Secure
解掉Compromised之後，就好像感覺到他會怎放flag。所以這次也是跟Serial Logs和Compromised一樣，而這次用還未用的SPI(也因為這次有4個channel啦)


## [Hardware] Off the grid


## [Hardware] Hidden


## [Forensics] Low Energy Crypto


