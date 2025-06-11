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
- Nmap scan:
```bash
nmap -sS -sV -p- 10.10.110.0/24