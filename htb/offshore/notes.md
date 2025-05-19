### nmap:
Nmap scan report for 10.10.110.2
Host is up (0.058s latency).
All 1000 scanned ports on 10.10.110.2 are in ignored states.
Not shown: 1000 filtered tcp ports (no-response)

Nmap scan report for 10.10.110.3
Host is up (0.067s latency).
Not shown: 999 filtered tcp ports (no-response)
PORT    STATE SERVICE  VERSION
443/tcp open  ssl/http nginx
|_http-title: 400 The plain HTTP request was sent to HTTPS port

Nmap scan report for 10.10.110.123
Host is up (0.061s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ed:da:93:ee:2e:2b:7a:02:4d:97:3d:1b:f2:40:ba:f6 (RSA)
|   256 7e:de:fa:0c:9d:4c:6c:01:7c:0a:0c:f1:74:4d:f3:5f (ECDSA)
|_  256 15:ab:fc:b8:a2:fa:f1:57:d7:3f:bc:ab:ad:d0:cc:99 (ED25519)
80/tcp   open  http        Apache httpd 2.4.29 ((Ubuntu))
8000/tcp open  http        Splunkd httpd
|_http-server-header: Splunkd
| http-robots.txt: 1 disallowed entry 
|_/
8089/tcp open  ssl/unknown
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Nmap scan report for 10.10.110.124
Host is up (0.058s latency).
Not shown: 999 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
|_http-title: Offshore Dev
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

### Target 1
10.10.110.2


### Target 2
10.10.110.3

```
PORT    STATE SERVICE  VERSION
443/tcp open  ssl/http nginx

ffuf -w /usr/share/wordlists/dirb/common.txt -u https://10.10.110.3/FUZZ
```


### Target 3
10.10.110.123
```
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http        Apache httpd 2.4.29 ((Ubuntu))
8000/tcp open  http        Splunkd httpd
8089/tcp open  ssl/unknown
```

gobuster: Revealed /index.html, /robots.txt, and directories like /css, /js, /images.

nikto: Found outdated Apache (2.4.29), missing security headers, directory indexing on /css/ and /images/, internal IP leak (127.0.1.1), and potential inode leaks via ETags.

```
ffuf -w /usr/share/wordlists/dirb/common.txt -u http:10.10.110.123/FUZZ
ffuf -w /usr/share/wordlists/dirb/common.txt -u http:10.10.110.123:8000/FUZZ
```

$ searchsploit apache 2.4.29
-------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                |  Path
-------------------------------------------------------------------------------------------------------------- ---------------------------------
Apache + PHP < 5.3.12 / < 5.4.2 - cgi-bin Remote Code Execution                                               | php/remote/29290.c
Apache + PHP < 5.3.12 / < 5.4.2 - Remote Code Execution + Scanner                                             | php/remote/29316.py
Apache 2.4.17 < 2.4.38 - 'apache2ctl graceful' 'logrotate' Local Privilege Escalation                         | linux/local/46676.php
Apache CXF < 2.5.10/2.6.7/2.7.4 - Denial of Service                                                           | multiple/dos/26710.txt
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuck.c' Remote Buffer Overflow                                          | unix/remote/21671.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (1)                                    | unix/remote/764.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (2)                                    | unix/remote/47080.c
Apache OpenMeetings 1.9.x < 3.1.0 - '.ZIP' File Directory Traversal                                           | linux/webapps/39642.txt
Apache Tomcat < 5.5.17 - Remote Directory Listing                                                             | multiple/remote/2061.txt
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal                                                           | unix/remote/14489.c
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal (PoC)                                                     | multiple/remote/6229.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Execution (1)  | windows/webapps/42953.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Execution (2)  | jsp/webapps/42966.py
Apache Xerces-C XML Parser < 3.1.2 - Denial of Service (PoC)                                                  | linux/dos/36906.txt
Webfroot Shoutbox < 2.32 (Apache) - Local File Inclusion / Remote Code Execution                              | linux/remote/34.pl
-------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results


### Target 4
10.10.110.124

```
PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
```

gobuster: Found /Login (401), /dashboard (302 to /Login), and directories like /Content, /Scripts, with 500 errors on /About, /Contact.   

nikto: Identified ASP.NET app, missing security headers, internal IP leak (172.16.1.24), TRACE method enabled, and license files.   

Likely an AD-integrated dev portal with authentication vulnerabilities.

```
$ hydra -L /usr/share/wordlists/seclists/Usernames/top-usernames-shortlist.txt -P /usr/share/wordlists/seclists/Passwords/Common-Credentials/10k-most-common.txt -t 4 -w 30 10.10.110.124 http-get "/Login" -V
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).
```

