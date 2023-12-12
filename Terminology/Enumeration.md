'***Enumeration** is the most critical part of all. The art, the difficulty, and the goal are not to gain access to our target computer. Instead, it is identifying all of the ways we could attack a target we must find.*'

## Getting a Foothold

### nmap
- To begin, initiate a **Port Scan** using [[nmap]].
	- [ ] Be sure to scan [[nmap#^3b321d|all 65,535 ports]] for reassurance. It would sure suck to miss something.
	- [ ] Maybe there's a vulnerable service version. Make sure to use version detection.

### Webservers
- [ ] Any default credentials?
- [ ] Review services hosted on the webserver itself.
- [ ] Fuzz for domains.
- [ ] Bruteforce directories.
- [ ] Check input fields. Are they vulnerable to XSS/SSRF?
- [ ] Any open alternate HTTP Ports?

### Kerberos

