#Active-Directory 

**Active Directory** is Microsoft's directory service developed for Windows that is responsible for storing and managing networks of users, computers, and other network resources, allowing admins to set and manage permissions within the environment.

## About Active Directory

- Over 95% of fortune 500 companies use Active Directory in some capacity.
- Admins may manage/adjust permissions for multiple devices belonging to the network using Group Policy (GP).
- Active Directory is not inherently secure by default, with numerous potential footholds for hackers out of the box, it must be properly configured to be considered safe to use.
- Even basic Users belonging to an AD account that is underprivileged, can enumerate some of the following objects:

	- Domain Computers
	- Domain Users
	- Domain Group Information
	- Organizational Units (OUs)
	- Default Domain Policy
	- Functional Domain Levels
	- Password Policy
	- Group Policy Objects (GPOs)
	- Domain Trusts
	- Access Control Lists (ACLs)

## Forests

Forests are hierarchical, structural representations of an AD. 

At a very (simplistic) high level, an AD structure may look as follows:

```shell-session
INLANEFREIGHT.LOCAL/
├── ADMIN.INLANEFREIGHT.LOCAL
│   ├── GPOs
│   └── OU
│       └── EMPLOYEES
│           ├── COMPUTERS
│           │   └── FILE01
│           ├── GROUPS
│           │   └── HQ Staff
│           └── USERS
│               └── barbara.jones
├── CORP.INLANEFREIGHT.LOCAL
└── DEV.INLANEFREIGHT.LOCAL
```

At the very top or root of the structure is the Forest: the absolute security boundary. In the above example, INLANEFREIGHT.LOCAL would be considered the root domain. Contained in the root domain are its subdomains.

It also isn't uncommon to see multiple domains or Forests bound together by something called trust relationships. This trust relationship allows two or more domains to effectively access all resources whether it belongs to the domain or not. However, by default, users belonging to one domain are unauthenticated to machines in other domains, meaning *Users can only be authorized to machines in their corresponding domain*. 

## Terminology



- **Domain** ^d81502
	- A domain is a logical group of objects such as computers, users, OUs, groups, etc. We can think of each domain as a different city within a state or country. Domains can operate entirely independently of one another or be connected via trust relationships. ^aaa5ab