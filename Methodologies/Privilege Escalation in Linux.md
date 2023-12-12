**Privilege Escalation** is the act of exploiting a bug, a design flaw, or a configuration oversight in an operating system or software application to gain elevated access to resources that are normally protected from an application or user.

## Attack Vectors Methodology

Below are the steps to finding vulnerable services allowing for elevated permissions on Linux or Unix-based Operating Systems. The following methodologies should be performed [[Remote Code Execution (RCE)|once an attacker has gained a user shell]].

1. **Underprivileged users with [[sudo]] permissions.**

	When looking for potentially exploitable services, attackers may use the following command to list services executable as sudo:

		sudo -l

	*command output:*

		┌──(daniel㉿ubuntu)-[~/Documents]
		└─$ sudo -l                  
		[sudo] password for daniel: 
		
		User daniel may run the following commands on kali:
	    (ALL : ALL) ALL

	This command lists all the privileges permissible to an underprivileged user (i.e., Daniel). Do note—any program or service that can be executed as a user with sudo permissions can be exploited fairly easily; hence, it's not unusual to execute this command first.

2. **Finding services given escalated permissions; [[SUID Binaries]]**

	When looking for potentially exploitable services, an attacker may use the following command to list services given elevated privileges:

		find / -perm /2000 2> /dev/null

	*command output:*

		┌──(daniel㉿ubuntu)-[~/Documents]
		└─$ find / -perm /2000 2> /dev/null
		/usr/local/share/fonts
		/usr/local/lib/python2.7
		/usr/local/lib/python2.7/dist-packages
		/usr/local/lib/python2.7/site-packages
		/usr/sbin/unix_chkpwd
		/usr/bin/expiry
		/usr/bin/dotlockfile
		/usr/bin/ssh-agent
		/usr/bin/chage
		/usr/bin/plocate
		/usr/bin/write
		/usr/bin/crontab

3. **Finding all open ports/running services on a local machine.**

		netstat -auntp

	*command output:*

		sau@pc:~$ netstat -auntp
		Proto Recv-Q Send-Q Local Address           Foreign Address         State
		tcp        0      0 127.0.0.1:8000          0.0.0.0:*               LISTEN-
		tcp        0      0 0.0.0.0:9666            0.0.0.0:*               LISTEN-
		tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN-
		tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN-
		tcp        0    360 10.10.11.214:22         10.10.14.13:60952     ESTABLISHED-
		tcp6       0      0 :::50051                :::*                    LISTEN-
		tcp6       0      0 :::22                   :::*                    LISTEN-
		udp        0      0 127.0.0.53:53           0.0.0.0:*
		udp        0      0 0.0.0.0:68              0.0.0.0:*