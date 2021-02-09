<p align="center">
  <img width="500" src="https://user-images.githubusercontent.com/43796175/107217985-ff302900-69dc-11eb-999b-b1da568d1ff9.jpg" alt="DEVEL: Write Up">
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

---

# 04: Exploitation

---

# 05: Post-Exploitation

