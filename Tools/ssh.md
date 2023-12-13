SSH (Secure Shell) is a remote cryptographic network protocol that provides a secure and encrypted way to access and manage network devices, servers, and computers over an unsecured network. It enables secure command-line access, file transfers, and tunneling of other TCP connections.

# Logging in to ssh 

To login to an *ssh* server, enter the following:

`ssh <user>@<your ip>`

*Note:* Do not  attempt to supply the password to the ssh server in the above command, as you will be prompted to login to the server with the passphrase AFTER specifying the user and IP Address. 

# Cheat Sheet

|**ssh Option**|**Description**|
|---|--------|
|`192.168.0.1`|Specifies the source IP Address for the server.|
|`-p`|Specifies the port to connect to the *ssh* server.|
|`-i`|Specifies an identity file such as an RSA-key, for a given user. If you supply the user's RSA-key, a password needn't be supplied.|
|`-l`|Specifies the login name of the user on the *ssh* server.|


