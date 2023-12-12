**smbclient** is a [[Client]] that can 'talk' to an [[Server Message Block (SMB)]] server. It offers an interface similar to that of the [[File Transmission Protocol (FTP)]]. 

## Usage

	smbclient [OPTIONS] service <password>

## Notable Options

	-L

Gets a list of shares available on a host. 

	-U

Sets the network username.

## Example

- **smbclient** attempts to list all Shares on a given network.

		smbclient -L \\\\10.10.10.100\\

- **smbclient** attempts to connect to the network as a user named 'Guest'. 

		smbclient \\\\10.10.10.100\\Users -U 'Guest'