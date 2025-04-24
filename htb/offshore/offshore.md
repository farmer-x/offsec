
# Enumerate the target:  

## Network
`nmap -sn 10.10.110.0/24 -oG ping_sweep.txt`
```
Nmap scan report for 10.10.110.2
Host is up (0.055s latency).
Nmap scan report for 10.10.110.3
Host is up (0.057s latency).
Nmap scan report for 10.10.110.123
Host is up (0.057s latency).
Nmap scan report for 10.10.110.124
Host is up (0.057s latency).
Nmap done: 256 IP addresses (4 hosts up) scanned in 5.06 seconds
```

### Port Scan

`nmap -sC -sV -p 80,443,445,389,636,88,3389 --open -iL live_hosts.txt -oA offshore_scan`

```
Nmap scan report for 10.10.110.3
PORT    STATE SERVICE  VERSION
443/tcp open  ssl/http nginx
| tls-alpn: 
|   h2
|_  http/1.1
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=pfSense-5ad49c9e3b8b9/organizationName=pfSense webConfigurator Self-Signed Certificate/stateOrProvinceName=State/countryName=US
| Subject Alternative Name: DNS:pfSense-5ad49c9e3b8b9
| Not valid before: 2018-04-16T12:52:46
|_Not valid after:  2023-10-07T12:52:46
|_http-title: pfSense - Login
| tls-nextprotoneg: 
|   h2
|_  http/1.1

Nmap scan report for 10.10.110.123
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: ACME Bank
|_http-server-header: Apache/2.4.29 (Ubuntu)

Nmap scan report for 10.10.110.124
PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
|_http-title: Offshore Dev
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

```

10.10.110.3: Running **pfSense** (web interface on HTTPS/443, nginx, self-signed cert).

10.10.110.123: Running **Apache 2.4.29** on Ubuntu (HTTP/80, site titled "ACME Bank").

10.10.110.124: Running **Microsoft IIS 10.0** on Windows (HTTP/80, site titled "Offshore Dev", TRACE method enabled).



### SMB Check

`crackmapexec smb 10.10.110.0/24 --gen-relay-list relay_targets.txt`

## Service Enumeration

### WebServers (80/443)

`10.10.110.3` - no results   
`10.10.110.123` - Acme Bank   
```
<h4>our links</h4>
 <p><a href="#">Funds transfer</a></p>
 <p><a href="#">Mobile banking</a></p>
 <p><a href="#">Deposits</a></p>
 <p><a href="#">New joint accounts</a></p>
 <p><a href="#">Internet online banking</a></p>
 <p><a href="#">Balance enquiry</a></p>

```

`10.10.110.124` - Offshore Dev
```
     <div class="skills">
      <h1> Skills</h1>
      <p class="skillsdesc"> Technology stack we work on:</p>
      <div class="skillz">
      <ul class="skills-list">
        <li>JavaScript</li>
        <li>Vue.js</li>
        <li>ASP.NET</li>
        <li>PHP</li>
      </ul>
      <ul class="skills-list">
        <li>HTML</li>
        <li>CSS</li>
        <li>Python</li>
        <li>C</li>
      </ul>
      </div>
```
Intalled GoBuster: `sudo apt install gobuster`
Installed Seclists: `sudo apt -y intall seclists`

    NOTE: this installed in `/usr/bin/seclists` 

### GoBuster Directories   

gobuster dir -u http://10.10.110.123 -w /usr/share/seclists/Discovery/Web-Content/common.txt -o web_enum.txt
```
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.htaccess            (Status: 403) [Size: 278]
/.hta                 (Status: 403) [Size: 278]
/.htpasswd            (Status: 403) [Size: 278]
/css                  (Status: 301) [Size: 312] [--> http://10.10.110.123/css/]
/fonts                (Status: 301) [Size: 314] [--> http://10.10.110.123/fonts/]
/images               (Status: 301) [Size: 315] [--> http://10.10.110.123/images/]
/index.html           (Status: 200) [Size: 22567]
/js                   (Status: 301) [Size: 311] [--> http://10.10.110.123/js/]
/robots.txt           (Status: 200) [Size: 575]
/server-status        (Status: 403) [Size: 278]
```

gobuster dir -u http://10.10.110.124 -w /usr/share/seclists/Discovery/Web-Content/common.txt -o web_enum.txt

```
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/Content              (Status: 301) [Size: 152] [--> http://10.10.110.124/Content/]
/Contact              (Status: 500) [Size: 3420]
/About                (Status: 500) [Size: 3420]
/Default              (Status: 500) [Size: 3420]
/Login                (Status: 401) [Size: 1293]
/Scripts              (Status: 301) [Size: 152] [--> http://10.10.110.124/Scripts/]
/about                (Status: 500) [Size: 3420]
/aspnet_client        (Status: 301) [Size: 158] [--> http://10.10.110.124/aspnet_client/]
/contact              (Status: 500) [Size: 3420]
/content              (Status: 301) [Size: 152] [--> http://10.10.110.124/content/]
/dashboard            (Status: 302) [Size: 123] [--> /Login]
/default              (Status: 500) [Size: 3420]
/favicon.ico          (Status: 200) [Size: 552]
/fonts                (Status: 301) [Size: 150] [--> http://10.10.110.124/fonts/]
/index.html           (Status: 200) [Size: 3079]
/login                (Status: 401) [Size: 1293]
/scripts              (Status: 301) [Size: 152] [--> http://10.10.110.124/scripts/]
```

10.10.110.123 (Apache 2.4.29, "ACME Bank") and 10.10.110.124 (IIS 10.0, "Offshore Dev") are the most promising for web-based attacks.   

10.10.110.124 (Offshore Dev, IIS 10.0)   
Based on the lab’s emphasis on modern web attacks and your gobuster results:
- the /Login and /dashboard endpoints (with a 401 and redirect to /Login) suggest an authentication portal that could be the entry point. 
- -The 500 errors on pages like /About and /Contact hint at potential misconfigurations or exploitable flaws.



### Vulnerabilty Scan

nikto -h http://10.10.110.3 > nikto_scan_3.txt   

`no results`

nikto -h http://10.10.110.123 > nikto_scan_123.txt   

```
---------------------------------------------------------------------------
+ Server: Apache/2.4.29 (Ubuntu)
+ /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ /images: IP address found in the 'location' header. The IP is "127.0.1.1". See: https://portswigger.net/kb/issues/00600300_private-ip-addresses-disclosed
+ /images: The web server may reveal its internal or real IP in the Location header via a request to with HTTP/1.0. The value is "127.0.1.1". See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2000-0649
+ /: Server may leak inodes via ETags, header found with file /, inode: 5827, size: 56a53b69b1580, mtime: gzip. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2003-1418
+ Apache/2.4.29 appears to be outdated (current is at least Apache/2.4.54). Apache 2.2.34 is the EOL for the 2.x branch.
+ OPTIONS: Allowed HTTP Methods: OPTIONS, HEAD, GET, POST .
+ /css/: Directory indexing found.
+ /css/: This might be interesting.
+ /images/: Directory indexing found.
+ /icons/README: Apache default file found. See: https://www.vntweb.co.uk/apache-restricting-access-to-iconsreadme/
```


nikto -h http://10.10.110.124 > nikto_scan_124.txt   

```
---------------------------------------------------------------------------
+ Server: Microsoft-IIS/10.0
+ /: Retrieved x-powered-by header: ASP.NET.
+ /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ /aRUMuPNU.: Retrieved x-aspnet-version header: 4.0.30319.
+ RFC-1918 /aspnet_client: IP address found in the 'location' header. The IP is "172.16.1.24". See: https://portswigger.net/kb/issues/00600300_private-ip-addresses-disclosed
+ /aspnet_client: The web server may reveal its internal or real IP in the Location header via a request to with HTTP/1.0. The value is "172.16.1.24". See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2000-0649
+ OPTIONS: Allowed HTTP Methods: GET, HEAD, OPTIONS, TRACE .
+ /LICENSE.txt: License file found may identify site software.
+ /license.txt: License file found may identify site software.
+ /LICENSE.TXT: License file found may identify site software.

```





## Current Status
**10.10.110.123 (ACME Bank, Apache 2.4.29):**  
gobuster: Revealed /index.html, /robots.txt, and directories like /css, /js, /images.

nikto: Found outdated Apache (2.4.29), missing security headers, directory indexing on /css/ and /images/, internal IP leak (127.0.1.1), and potential inode leaks via ETags.   

Likely a banking web app, ripe for modern web attacks (e.g., SQLi, XSS, API flaws).

**10.10.110.124 (Offshore Dev, IIS 10.0):**   
gobuster: Found /Login (401), /dashboard (302 to /Login), and directories like /Content, /Scripts, with 500 errors on /About, /Contact.   

nikto: Identified ASP.NET app, missing security headers, internal IP leak (172.16.1.24), TRACE method enabled, and license files.   

Likely an AD-integrated dev portal with authentication vulnerabilities.

# FootHold

### 10.110.110.3

#### pfense exploits

```
Shellcodes: No Results

```
#### Webpage - pfsense login

Try default creds
- `admin:pfsense`  -NO

### 10.110.110.123

#### Webpage - Acme Bank
```
CSS -  Bootstrap v3.3.4  
robots.txt - stock  
/images - Apache/2.4.29  
/admin - NO  
/js   
- jQuery EasIng v1.1.2 
- jQuery v2.1.4 
```


searchsploit `apache 2.4.29`   
`No actionalbe results`

The app is a responsive banking portal, likely with login forms, APIs, or client-side logic vulnerable to modern web attacks (SQLi, XSS, API flaws, auth bypass) per the lab’s focus.

FireFox Network Results:
`No results`

curl http://10.10.110.123/js/ | grep "\.js" >> 123-js.txt
grep -i "api\|token\|password"  123-js.txt

`No results`

gobuster dir -u http://10.10.110.123 -w /usr/share/seclists/Discovery/Web-Content/api/api-endpoints.txt   
`No results`

#### Forms Results
`<script>alert(xxs)</script>`  
Contact Form: no XXS
Get Started: no XXS



### 10.110.110.124

#### Webpage - SPA Offshore Developers
- About, Projects, Contact
  
  /login : brings up a prompt for credentials

  admin:admin - NO
