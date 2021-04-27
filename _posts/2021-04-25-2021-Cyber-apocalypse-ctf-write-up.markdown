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
> One of our agents managed to store some valuable information in an air-gapped hardware password manage and delete any trace of them in our network before it got compromised by the invaders but the device got damaged during transportation and its OLED screen broke. We need help to recover the information stored in it!

這題給了一張圖和sal的file。題目也說了OLE screen broke，看來就是要把那個OLE screen復原出來了(這不是Forensics嗎?)。那張圖是那個device和logic anaylzer是怎連的。由此知道:  
CH0 -> DIN 應該是data  
CH1 -> CLK  
CH2 -> CS  
CH3/4是什麼也沒所謂了 :P  

Analyzers用下面的setting
![images](/images/HTBCTF2021/Screenshot 2021-04-26 143823.png)
之後把mosi的hex都記下來，很懶的我把他都掉去image2cpp後轉成圖，就看到flag了
![images](/images/HTBCTF2021/Screenshot 2021-04-26 154936.png)
![images](/images/HTBCTF2021/Screenshot 2021-04-26 154953.png)
`CHTB{013d_h4ck1n9_f7w!2^25#}`

## [Hardware] Hidden
這題給了一個firmware(ELF file)跟sal file。  
sal裡有encoded的data。用Logic，baud rate是62500，就會找到已encode了的data
```
\xCB\x80\x80\x80\x85\x80\x80\x80\xC2\x80\x80\x80\xCB\x80\x80\x80\\xCD\x80\x80\x80\xCB\x80\x80\x80\xC4\x80\x80\x80\xCD\x80\x80\x80\\x89\x80\x80\x80\x8F\x80\x80\x80\xC4\x80\x80\x80\x89\x80\x80\x80\\x89\x80\x80\x80\x8A\x80\x80\x80\x8C\x80\x80\x80\xC2\x80\x80\x80\\xC2\x80\x80\x80\x8A\x80\x80\x80\x86\x80\x80\x80\xF0\x80\x80\x80\\x8C\x80\x80\x80\xCE\x80\x80\x80\x83\x80\x80\x80\x8A\x80\x80\x80\\xF0\x80\x80\x80\x8F\x80\x80\x80\xC1\x80\x80\x80\x83\x80\x80\x80\\x8A\x80\x80\x80\xF0\x80\x80\x80\xC1\x80\x80\x80\xC7\x80\x80\x80\\xCB\x80\x80\x80\xF0\x80\x80\x80\x89\x80\x80\x80\x8A\x80\x80\x80\\x8A\x80\x80\x80\x85\x80\x80\x80\x85\x80\x80\x80\x86\x80\x80\x80\\x8A\x80\x80\x80\x8A\x80\x80\x80\x8F\x80\x80\x80\xC1\x80\x80\x80\\xC7\x80\x80\x80\x83\x80\x80\x80\x85\x80\x80\x80\xC1\x80\x80\x80\\x8F\x80\x80\x80\xC7\x80\x80\x80\xC1\x80\x80\x80\x8A\x80\x80\x80\\xCE\x80\x80\x80\xF0\x80\x80\x80\x83\x80\x80\x80\xC7\x80\x80\x80\\xC8\x80\x80\x80\xC8\x80\x80\x80\xC1\x80\x80\x80\xC1\x80\x80\x80\\xC7\x80\x80\x80\x85\x80\x80\x80\xC4\x80\x80\x80\x89\x80\x80\x80\\x8F\x80\x80\x80\xC1\x80\x80\x80\xC4\x80\x80\x80\x8A\x80\x80\x80\\xF0\x80\x80\x80\xC8\x80\x80\x80\xC8\x80\x80\x80\x85\x80\x80\x80\\xC2\x80\x80\x80\xC1\x80\x80\x80\x85\x80\x80\x80\xF0\x80\x80\x80\\xC1\x80\x80\x80\x83\x80\x80\x80\x85\x80\x80\x80\xC7\x80\x80\x80\
```

再看看firmware是什麼東西吧
```bash
$ file firmware            
firmware: ELF 32-bit LSB executable, ARM, EABI5 version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-armhf.so.3, for GNU/Linux 3.2.0, BuildID[sha1]=572cbaa2da38724b97a40ec009c2e5b4ca452be4, not stripped
```
```bash
$ binwalk ~/Desktop/firmware

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             ELF, 32-bit LSB executable, ARM, version 1 (SYSV)
6057          0x17A9          Unix path: /usr/lib/gcc/arm-linux-gnueabihf/8/../../../arm-linux-gnueabihf/crt1.o
```
むむむ，還以為會是什麼filesystem。ELF的話就掉去Ghidra了。掉去Ghidra會看到有比較在意的是main和key_generator  
main()
```c
undefined4 main(int param_1,int param_2)

{
  byte bVar1;
  uint uVar2;
  int iVar3;
  undefined4 uVar4;
  int aiStack596 [100];
  byte abStack196 [52];
  undefined4 local_90;
  undefined4 uStack140;
  undefined4 uStack136;
  undefined4 uStack132;
  undefined4 local_80;
  undefined4 uStack124;
  undefined4 uStack120;
  undefined4 uStack116;
  undefined4 local_70;
  undefined4 uStack108;
  undefined4 uStack104;
  undefined4 uStack100;
  undefined2 local_60;
  termios local_5c;
  ssize_t local_20;
  int local_1c;
  int local_18;
  int local_14;
  
  local_90 = 0x42485443;
  uStack140 = 0x4141417b;
  uStack136 = 0x41414141;
  uStack132 = 0x41414141;
  local_80 = 0x41414141;
  uStack124 = 0x41414141;
  uStack120 = 0x41414141;
  uStack116 = 0x41414141;
  local_70 = 0x41414141;
  uStack108 = 0x41414141;
  uStack104 = 0x41414141;
  uStack100 = 0x41414141;
  local_60 = 0x7d41;
  local_14 = 0;
  while (local_14 < 0x32) {
    bVar1 = *(byte *)((int)&local_90 + local_14);
    uVar2 = key_generator();
    abStack196[local_14] = (byte)uVar2 ^ bVar1;
    local_14 = local_14 + 1;
  }
  if (param_1 == 1) {
    uVar4 = 0xffffffff;
  }
  else {
    printf("[!] sending data to %s\n",*(undefined4 *)(param_2 + 4));
    local_1c = open(*(char **)(param_2 + 4),0x902);
    if (local_1c == -1) {
      perror(*(char **)(param_2 + 4));
      uVar4 = 0xffffffff;
    }
    else {
      iVar3 = tcgetattr(local_1c,&local_5c);
      if (iVar3 < 0) {
        perror("[x] configuration error");
        uVar4 = 0xffffffff;
      }
      else {
        local_5c.c_iflag = 0;
        local_5c.c_oflag = 0;
        local_5c.c_lflag = 0;
        local_5c.c_cc[6] = '\0';
        local_5c.c_cc[5] = '\0';
        local_5c.c_cflag = 0x1141;
        tcsetattr(local_1c,0,&local_5c);
        local_18 = 0;
        local_14 = 0;
        while (local_14 < 0x32) {
          aiStack596[local_18] = (abStack196[local_14] >> 4) + 1;
          aiStack596[local_18 + 1] = (abStack196[local_14] & 0xf) + 1;
          local_18 = local_18 + 2;
          local_14 = local_14 + 1;
        }
        local_14 = 0;
        while (local_14 < 1) {
          local_20 = write(local_1c,aiStack596,400);
          if (local_20 < 0) {
            perror("[x] write error");
            return 0xffffffff;
          }
          local_14 = local_14 + 1;
        }
        close(local_1c);
        uVar4 = 0;
      }
    }
  }
  return uVar4;
}
```
key_generator()
```c
uint key_generator(void)

{
  next_in_seq = next_in_seq * 0x303577d + 0x145a;
  return next_in_seq % 0xff;
}
```
連按`next_in_seq`就會跳到`next_in_seq`。`next_in_seq`是`0x2E9D3h`
![images](/images/HTBCTF2021/Screenshot 2021-04-26 172832.png)

我也只能做到這而已。接下來都已經是Mystizさん在解了w (`next_in_seq`也是他找出來的)  
我把找到的都掉給Mystizさん，他很快就寫了個script出來了。跟據hoifanrdさん說法，是個XOR。我也只能明這麼多了w  
再我不停調baud rate之後就解到flag了  

## [Forensics] Low Energy Crypto
這題後半也是Mystizさん在解，因為我真的不會Crypto。  
這題結構是把public key跟cipher都隱藏BLE的communication裡，所以就是要在大海找public key跟ciphertext  
要在BLE的packet中找東西，先用`btatt`把會有data傳送的ATT filter出來  
![images](/images/HTBCTF2021/Screenshot 2021-04-27 101004.png)
看看那些packet時看到有`BEGIN PUBLIC KEY`的東西
![images](/images/HTBCTF2021/Screenshot 2021-04-27 101358.png)
再把上下的packet的data都拿出來。(不知為什麼我總是copy錯內容給Mystizさん，最後他brute force正確的key出來w) 
```
-----BEGIN PUBLIC KEY-----
MGowDQYJKoZIhvcNAQEBBQADWQAwVgJBAKKPHxnmkWVC4fje7KMbWZf07zR10D0m
B9fjj4tlGekPOW+f8JGzgYJRWboekcnZfiQrLRhA3REn1lUKkRAnUqAkCEQDL/3Li
4l+RI2g0FqJvf3ff
-----END PUBLIC KEY-----
```
還有ciphertext
![images](/images/HTBCTF2021/Screenshot 2021-04-27 103221.png)
之後就是TWTさん和Mystizさん在解了。  
TWTさん說法: 用 public key factorize 之後解 cipher text  
Mystizさん: 不知怎由 public key 拿到 private key -> 解密 -> ?? -> get flag  
之前都是一個人玩CTF，多人起就是這樣吧w  

