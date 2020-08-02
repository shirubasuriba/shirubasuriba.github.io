---
layout: post
title:  pwn Exercise Writeup
date:   2020-07-29 10:11:34 +0800
categories: CTF
---

わたしはできそこないの人です。周りの人がWebをやるから、わたしはpwnに逃げたが、知識ゼロです。

リアルも使えないかも、わたし何をやってるや。


ここは昔あるのpwn系の問集をやる時の記録です。

***

## [TWMMA CTF 2016] Pwn greeting

首先是file跟checksec

```bash
$ file greeting 
greeting: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.24, BuildID[sha1]=beb85611dbf6f1f3a943cecd99726e5e35065a63, not stripped
$ checksec greeting
[*] '/home/u468/Documents/CTF/pwn/baby/TWMMA_CTF-2016_Pwn-greeting/greeting'
    Arch:     i386-32-little
    RELRO:    No RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      No PIE (0x8048000)

```

先跑了program一次，打了大量的A下去但沒bof的反應。所以用ghidra看他的souce code

![miansource](/images/pwnexercise/greeting/main_source.PNG)

在"nice to meet you"那邊的printf沒有format打下去，所以這題是Format String Attack

```C++
sprint(local_94,"Nice to meet you, %s :)\n", local_54);
printf(local_94);
```
