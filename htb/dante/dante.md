# Dante

### Goals
- Breach perimeter
- Explore Network
- Move laterally/vertically - gain admin access
- Reach domain administrator

### Skills
- Enumeration
- Privilege escalation
- Lateral movement
- Web application exploitation

### Resources 
- Entry point: 10.10.110.0/24
- The firewall at 10.10.110.2 is out of scope


#### Enumeration
- Nmap tcp scan:
```
nmap -sS -sV -p- 10.10.110.0/24
```
- nmap udp scan: 
```
nmap -sU 10.10.110.0/24
```
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-11 10:03 CDT
Nmap done: 256 IP addresses (0 hosts up) scanned in 206.27 seconds


