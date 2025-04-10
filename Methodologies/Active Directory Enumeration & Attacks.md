#Active-Directory

## Enumeration

In the **enumeration** phase, it is our objective to identify users, hosts, and vulnerabilities which we may later use to take advantage of. 

When enumerating servers utilizing **AD**, it's crucial to find as much data as possible about the following Points of Interest or POIs:

| **Data Point**                  | **Description**                                                                                                                 |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| `AD Users`                      | We are trying to enumerate valid user accounts we can target for password spraying.                                             |
| `AD Joined Computers`           | Key Computers include Domain Controllers, file servers, SQL servers, web servers, Exchange mail servers, database servers, etc. |
| `Key Services`                  | Kerberos, NetBIOS, LDAP, DNS                                                                                                    |
| `Vulnerable Hosts and Services` | Anything that can be a quick win. ( a.k.a an easy host to exploit and gain a foothold)                                          |

## TTPs
(**Tools, Tactics & Procedures**)

Enumerating an AD environment can be overwhelming if just approached without a plan. There is an abundance of data stored in AD, and it can take a long time to sift if not looked at in progressive stages, and we will likely miss things. We need to set a game plan for ourselves and tackle it piece by piece. Everyone works in slightly different ways, so as we gain more experience, we'll start to develop our own repeatable methodology that works best for us. Regardless of how we proceed, we typically start in the same place and look for the same data points. We will experiment with many tools in this section and subsequent ones. It is important to reproduce every example and even try to recreate examples with different tools to see how they work differently, learn their syntax, and find what approach works best for us.

We will start with `passive` identification of any hosts in the network, followed by `active` validation of the results to find out more about each host (what services are running, names, potential vulnerabilities, etc.). Once we know what hosts exist, we can proceed with probing those hosts, looking for any interesting data we can glean from them. After we have accomplished these tasks, we should stop and regroup and look at what info we have. At this time, we'll hopefully have a set of credentials or a user account to target for a foothold onto a domain-joined host or have the ability to begin credentialed enumeration from our Linux attack host.

## Recon & Enumeration Principles

Assume that we are Penetration Testers looking to attack an **Active Directory** [[Active Directory#^aaa5ab|Domain]] by the name of `INLANEFREIGHT.LOCAL`.
## Tools

|Tool|Description|
|---|---|
|[PowerView](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1)/[SharpView](https://github.com/dmchell/SharpView)|A PowerShell tool and a .NET port of the same used to gain situational awareness in AD. These tools can be used as replacements for various Windows `net*` commands and more. PowerView and SharpView can help us gather much of the data that BloodHound does, but it requires more work to make meaningful relationships among all of the data points. These tools are great for checking what additional access we may have with a new set of credentials, targeting specific users or computers, or finding some "quick wins" such as users that can be attacked via Kerberoasting or ASREPRoasting.|
|[BloodHound](https://github.com/BloodHoundAD/BloodHound)|Used to visually map out AD relationships and help plan attack paths that may otherwise go unnoticed. Uses the [SharpHound](https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors) PowerShell or C# ingestor to gather data to later be imported into the BloodHound JavaScript (Electron) application with a [Neo4j](https://neo4j.com/) database for graphical analysis of the AD environment.|
|[SharpHound](https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors)|The C# data collector to gather information from Active Directory about varying AD objects such as users, groups, computers, ACLs, GPOs, user and computer attributes, user sessions, and more. The tool produces JSON files which can then be ingested into the BloodHound GUI tool for analysis.|
|[BloodHound.py](https://github.com/fox-it/BloodHound.py)|A Python-based BloodHound ingestor based on the [Impacket toolkit](https://github.com/CoreSecurity/impacket/). It supports most BloodHound collection methods and can be run from a non-domain joined attack host. The output can be ingested into the BloodHound GUI for analysis.|
|[Kerbrute](https://github.com/ropnop/kerbrute)|A tool written in Go that uses Kerberos Pre-Authentication to enumerate Active Directory accounts, perform password spraying, and brute-forcing.|
|[Impacket toolkit](https://github.com/SecureAuthCorp/impacket)|A collection of tools written in Python for interacting with network protocols. The suite of tools contains various scripts for enumerating and attacking Active Directory.|
|[Responder](https://github.com/lgandx/Responder)|Responder is a purpose-built tool to poison LLMNR, NBT-NS, and MDNS, with many different functions.|
|[Inveigh.ps1](https://github.com/Kevin-Robertson/Inveigh/blob/master/Inveigh.ps1)|Similar to Responder, a PowerShell tool for performing various network spoofing and poisoning attacks.|
|[C# Inveigh (InveighZero)](https://github.com/Kevin-Robertson/Inveigh/tree/master/Inveigh)|The C# version of Inveigh with a semi-interactive console for interacting with captured data such as username and password hashes.|
|[rpcinfo](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/rpcinfo)|The rpcinfo utility is used to query the status of an RPC program or enumerate the list of available RPC services on a remote host. The "-p" option is used to specify the target host. For example the command "rpcinfo -p 10.0.0.1" will return a list of all the RPC services available on the remote host, along with their program number, version number, and protocol. Note that this command must be run with sufficient privileges.|
|[rpcclient](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html)|A part of the Samba suite on Linux distributions that can be used to perform a variety of Active Directory enumeration tasks via the remote RPC service.|
|[CrackMapExec (CME)](https://github.com/byt3bl33d3r/CrackMapExec)|CME is an enumeration, attack, and post-exploitation toolkit which can help us greatly in enumeration and performing attacks with the data we gather. CME attempts to "live off the land" and abuse built-in AD features and protocols like SMB, WMI, WinRM, and MSSQL.|
|[Rubeus](https://github.com/GhostPack/Rubeus)|Rubeus is a C# tool built for Kerberos Abuse.|
|[GetUserSPNs.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/GetUserSPNs.py)|Another Impacket module geared towards finding Service Principal names tied to normal users.|
|[Hashcat](https://hashcat.net/hashcat/)|A great hash cracking and password recovery tool.|
|[enum4linux](https://github.com/CiscoCXSecurity/enum4linux)|A tool for enumerating information from Windows and Samba systems.|
|[enum4linux-ng](https://github.com/cddmp/enum4linux-ng)|A rework of the original Enum4linux tool that works a bit differently.|
|[ldapsearch](https://linux.die.net/man/1/ldapsearch)|Built-in interface for interacting with the LDAP protocol.|
|[windapsearch](https://github.com/ropnop/windapsearch)|A Python script used to enumerate AD users, groups, and computers using LDAP queries. Useful for automating custom LDAP queries.|
|[DomainPasswordSpray.ps1](https://github.com/dafthack/DomainPasswordSpray)|DomainPasswordSpray is a tool written in PowerShell to perform a password spray attack against users of a domain.|
|[LAPSToolkit](https://github.com/leoloobeek/LAPSToolkit)|The toolkit includes functions written in PowerShell that leverage PowerView to audit and attack Active Directory environments that have deployed Microsoft's Local Administrator Password Solution (LAPS).|
|[smbmap](https://github.com/ShawnDEvans/smbmap)|SMB share enumeration across a domain.|
|[psexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py)|Part of the Impacket toolkit, it provides us with Psexec-like functionality in the form of a semi-interactive shell.|
|[wmiexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/wmiexec.py)|Part of the Impacket toolkit, it provides the capability of command execution over WMI.|
|[Snaffler](https://github.com/SnaffCon/Snaffler)|Useful for finding information (such as credentials) in Active Directory on computers with accessible file shares.|
|[smbserver.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbserver.py)|Simple SMB server execution for interaction with Windows hosts. Easy way to transfer files within a network.|
|[setspn.exe](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc731241\(v=ws.11\))|Adds, reads, modifies and deletes the Service Principal Names (SPN) directory property for an Active Directory service account.|
|[Mimikatz](https://github.com/ParrotSec/mimikatz)|Performs many functions. Notably, pass-the-hash attacks, extracting plaintext passwords, and Kerberos ticket extraction from memory on a host.|
|[secretsdump.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/secretsdump.py)|Remotely dump SAM and LSA secrets from a host.|
|[evil-winrm](https://github.com/Hackplayers/evil-winrm)|Provides us with an interactive shell on a host over the WinRM protocol.|
|[mssqlclient.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/mssqlclient.py)|Part of the Impacket toolkit, it provides the ability to interact with MSSQL databases.|
|[noPac.py](https://github.com/Ridter/noPac)|Exploit combo using CVE-2021-42278 and CVE-2021-42287 to impersonate DA from standard domain user.|
|[rpcdump.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/rpcdump.py)|Part of the Impacket toolset, RPC endpoint mapper.|
|[CVE-2021-1675.py](https://github.com/cube0x0/CVE-2021-1675/blob/main/CVE-2021-1675.py)|Printnightmare PoC in python.|
|[ntlmrelayx.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py)|Part of the Impacket toolset, it performs SMB relay attacks.|
|[PetitPotam.py](https://github.com/topotam/PetitPotam)|PoC tool for CVE-2021-36942 to coerce Windows hosts to authenticate to other machines via MS-EFSRPC EfsRpcOpenFileRaw or other functions.|
|[gettgtpkinit.py](https://github.com/dirkjanm/PKINITtools/blob/master/gettgtpkinit.py)|Tool for manipulating certificates and TGTs.|
|[getnthash.py](https://github.com/dirkjanm/PKINITtools/blob/master/getnthash.py)|This tool will use an existing TGT to request a PAC for the current user using U2U.|
|[adidnsdump](https://github.com/dirkjanm/adidnsdump)|A tool for enumerating and dumping DNS records from a domain. Similar to performing a DNS Zone transfer.|
|[gpp-decrypt](https://github.com/t0thkr1s/gpp-decrypt)|Extracts usernames and passwords from Group Policy preferences files.|
|[GetNPUsers.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/GetNPUsers.py)|Part of the Impacket toolkit. Used to perform the ASREPRoasting attack to list and obtain AS-REP hashes for users with the 'Do not require Kerberos preauthentication' set. These hashes are then fed into a tool such as Hashcat for attempts at offline password cracking.|
|[lookupsid.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/lookupsid.py)|SID bruteforcing tool.|
|[ticketer.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ticketer.py)|A tool for creation and customization of TGT/TGS tickets. It can be used for Golden Ticket creation, child to parent trust attacks, etc.|
|[raiseChild.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/raiseChild.py)|Part of the Impacket toolkit, It is a tool for automated child to parent domain privilege escalation.|
|[Active Directory Explorer](https://docs.microsoft.com/en-us/sysinternals/downloads/adexplorer)|Active Directory Explorer (AD Explorer) is an AD viewer and editor. It can be used to navigate an AD database and view object properties and attributes. It can also be used to save a snapshot of an AD database for offline analysis. When an AD snapshot is loaded, it can be explored as a live version of the database. It can also be used to compare two AD database snapshots to see changes in objects, attributes, and security permissions.|
|[PingCastle](https://www.pingcastle.com/documentation/)|Used for auditing the security level of an AD environment based on a risk assessment and maturity framework (based on [CMMI](https://en.wikipedia.org/wiki/Capability_Maturity_Model_Integration) adapted to AD security).|
|[Group3r](https://github.com/Group3r/Group3r)|Group3r is useful for auditing and finding security misconfigurations in AD Group Policy Objects (GPO).|
|[ADRecon](https://github.com/adrecon/ADRecon)|A tool used to extract various data from a target AD environment. The data can be output in Microsoft Excel format with summary views and analysis to assist with analysis and paint a picture of the environment's overall security state.|