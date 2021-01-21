![lame](https://user-images.githubusercontent.com/43796175/105421932-e835bc80-5c10-11eb-9ace-f5e0f34407d4.jpg)

Firts of all, i ran a basic NMAP scan:
```
nmap -Pn 10.10.10.3
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-01-21 16:34 -05
Nmap scan report for 10.10.10.3
Host is up (0.16s latency).
Not shown: 996 filtered ports
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
```

