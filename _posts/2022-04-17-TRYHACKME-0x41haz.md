---
title: 0x41haz writeup
published: true
categories: writeups
tags: TryHackMe
challenge: https://tryhackme.com/room/0x41haz
---
**Challenge link** => [https://tryhackme.com/room/0x41haz](https://tryhackme.com/room/0x41haz)

Ah reverse engineering. Everybody's favorite area. Today, I'll try a new *easy* challenge from **0x41haz**

**0x41haz's [twitter](https://twitter.com/0x41haz)**

---
Alright so let's get started by downloading the file from tryhackme

```sh
└╼root$ls
total 16K
-rw-r--r-- 1 gal1leo gal1leo 15K Apr 17 21:32 0x41haz.0x41haz
```
```sh
┌[Gal1leoHackingStation]─[21:48-17/04]─[/home/gal1leo/TryHackMe/0x41haz]
└╼root$file 0x41haz.0x41haz

0x41haz.0x41haz: ELF 64-bit MSB *unknown arch 0x3e00* (SYSV)
```

Now that we have the file on our machine and we confirmed that it's a binary, how about we give it permission to be executed, and run it.

```bash
┌[Gal1leoHackingStation]─[21:37-17/04]─[/home/gal1leo/TryHackMe/0x41haz]
└╼root$chmod +x 0x41haz.0x41haz

┌[Gal1leoHackingStation]─[21:37-17/04]─[/home/gal1leo/TryHackMe/0x41haz]
└╼root$./0x41haz.0x41haz 

=======================
Hey , Can You Crackme ?
=======================
It's jus a simple binary 

Tell Me the Password :
[JOE]
Is it correct , I don't think so.
```

Tried running `r2`, `readelf`, `gdb` but if I had read the description of the challenge, I would've seen that the file is obfuscated in some way.

I tried searching for the output from the `file` command on google and I found this website `https://pentester.blog/?p=247`. It describes an obfuscation technique, so I tried reversing it by patching the 6th byte to 0x01. You can get more information about how this obfuscation is performed by following the link above.

![](/assets/images/2022-04-17_21-56.png)

saved the file and opened it in r2

```c
┌[Gal1leoHackingStation]─[22:00-17/04]─[/home/gal1leo/TryHackMe/0x41haz]
└╼root$r2 0x41haz.0x41haz 

[0x00001080]> aaa
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze function calls (aac)
[x] Analyze len bytes of instructions for references (aar)
[x] Finding and parsing C++ vtables (avrr)
[x] Type matching analysis for all functions (aaft)
[x] Propagate noreturn information (aanr)
[x] Use -AA or aaaa to perform additional experimental analysis.
[0x00001080]>s main
[0x00001165]> 
```
Here I'm using `aaa` to analyze all of the functions the program may have. I changed to the main function `(s main)` and now we’re going to switch into radare2's graph mode. This will help us see how the program flows and see where the checks for these passwords are being done at. To go into graph mode we use the command `VV`.

![](/assets/images/2022-04-17_22-23.png)
![](/assets/images/2022-04-17_22-23_1.png)
![](/assets/images/2022-04-17_22-23_2.png)

After staring at these instructions for a while, I dedcuted that the password was just a concatenation of the commented strings in the assembly code. By merging them, we get the right password.

![](/assets/images/2022-04-17_22-30.png)

