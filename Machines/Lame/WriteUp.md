![lame](https://user-images.githubusercontent.com/43796175/105421932-e835bc80-5c10-11eb-9ace-f5e0f34407d4.jpg)

## LAME: Write Up

# 01: Information Gathering

Firts of all, i ran a NMAP scan:

![nmap](https://user-images.githubusercontent.com/43796175/105422199-52e6f800-5c11-11eb-9d5c-f11edeef1217.jpg)

I see the port 22 with "vsftpd 2.3.4" version, which seens to be vulnerable.

# 02: Threat Modeling

I found a metasploit module for exploit this vulnerability:

![ftp](https://user-images.githubusercontent.com/43796175/105422583-f7693a00-5c11-11eb-8f00-ad22f801efd3.jpg)

However, this seems not be vulnerable:

![ftpno](https://user-images.githubusercontent.com/43796175/105424208-f71e6e00-5c14-11eb-8014-84b267df30ef.jpg)

# 03: Vulnerability Analysis

The next step for me is vulnerabilities associated with the version of SAMBA 3.0.20:

![SAMBA](https://user-images.githubusercontent.com/43796175/105424661-bd9a3280-5c15-11eb-8edd-f6beb69bfd63.jpg)

# 04: Exploitation

I set the configuration of the exploit in msfconsole and ran the script gave me a reverse shell like root user:

![sambaok](https://user-images.githubusercontent.com/43796175/105425003-782a3500-5c16-11eb-9da1-6a972aa548cd.jpg)

Finally, got a shell:

![fina](https://user-images.githubusercontent.com/43796175/105425162-d0f9cd80-5c16-11eb-84d9-2aa8c580a198.jpg)
