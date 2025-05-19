## Pandora

### Linux - Easy - 10.129.231.134

#### Enumeration

#### nmap
  
TCP port scan:  

nmap -sC -sV -T4 10.129.231.134 >> nmap-pandora.txt

```
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
```

UDP port scan:   

nmap -sU 10.129.231.134 >> nmap-pandora.txt 

```
PORT    STATE         SERVICE
68/udp  open|filtered dhcpc
161/udp open          snmp
```

#### Website

Browser: http://10.129.231.134
- nothing loads

Curl: 
```
 curl -v http://10.129.231.134   
*   Trying 10.129.231.134:80...
* Connected to 10.129.231.134 (10.129.231.134) port 80
* using HTTP/1.x
> GET / HTTP/1.1
> Host: 10.129.231.134
> User-Agent: curl/8.13.0
> Accept: */*
```
#### SNMP

snmpwalk -v 1 -c public 10.129.231.134 >> snmp-walk.txt

```
iso.3.6.1.2.1.1.4.0 = STRING: "Daniel"
iso.3.6.1.2.1.1.5.0 = STRING: "pandora"
iso.3.6.1.2.1.1.6.0 = STRING: "Mississippi"

iso.3.6.1.2.1.25.4.2.1.5.1114 = STRING: "-u daniel -p HotelBabylon23"
```


#### SSH

Attempt connection/login   
```
ssh daniel@10.129.231.134 
```


```
daniel@pandora:~$ ls -al
total 28
drwxr-xr-x 4 daniel daniel 4096 May 19 14:13 .
drwxr-xr-x 4 root   root   4096 Dec  7  2021 ..
lrwxrwxrwx 1 daniel daniel    9 Jun 11  2021 .bash_history -> /dev/null
-rw-r--r-- 1 daniel daniel  220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 daniel daniel 3771 Feb 25  2020 .bashrc
drwx------ 2 daniel daniel 4096 May 19 14:13 .cache
-rw-r--r-- 1 daniel daniel  807 Feb 25  2020 .profile
drwx------ 2 daniel daniel 4096 Dec  7  2021 .ssh
```

NOTE: nothing useful in Daniels home directory

##### Lateral Movement
```
daniel@pandora:~$ cd ..
daniel@pandora:/home$ ls
daniel  matt
daniel@pandora:/home$ cd matt
daniel@pandora:/home/matt$ ls
user.txt
```
Flag disovered in "matts" home directory - inaccessible by daniel

```
daniel@pandora:/home/matt$ more user.txt
more: cannot open user.txt: Permission denied
```

Directory access:   
```
daniel@pandora:/$ cd root
-bash: cd: root: Permission denied

daniel@pandora:/var/log$ cat auth.log
cat: auth.log: Permission denied

```




TODO:
[ x] access whatever is hosted at port 80
[ ] probe port 22 (SSH)
[ x] port 161 UDP (snmp) 
