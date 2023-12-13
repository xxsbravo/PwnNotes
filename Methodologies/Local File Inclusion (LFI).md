A **Local File Inclusion (LFI)** is a type of security vulnerability in web applications that allows an attacker to include files on a server through the web browser. This typically occurs when input from the user, such as file paths or parameters, is not properly validated or sanitized, enabling an attacker to traverse the file system and include sensitive files, potentially leading to unauthorized access, data exposure, or code execution on the server.

# Discovering an LFI

When a penetration tester discovers and confirms the existence of a **LFI**, usually, we look to the ```/etc/passwd``` file to see which users are given bash privileges, allowing us to differentiate genuine users from services running on the machine. It is a great first step in exploiting or pwning a system, but it can be daunting to know what to do next. So, in the segment below, we'll be discussing what you should do after you discover an **LFI**.

# Sensitive File Cataloging

Like most things in hacking, what you should look for can be broken down into a list. Below is a general list of files to look for, if you've got nowhere else to look:

|**Typical File Location**|**Description**|
|---| ------|
|*/etc/shadow*|This file stores password hashes on Unix/Linux systems. Access to this file could potentially lead to password cracking or other attacks.|
|*/etc/hostname OR /etc/hosts*|These files may reveal information about the system's network configuration.|
|*/etc/mysql/my.cnf*|Configuration files for database servers may contain credentials or other sensitive information.|
|*/etc/nginx/sites-available/default*|Analyzing this file is valuable as it provides insights into the web server's setup, potential misconfigurations, proxy configurations, and other details that can aid in identifying vulnerabilities|

#LFI 

---