*Tags:* #commands 

Short for '*super user do*', **sudo** is a Linux command allowing users to temporarily elevate their current user accounts to have root privileges.

## Usage

	sudo

Preceding any command with sudo will run said command as root.

## Notable Options

	-l

list user's privileges or check a specific command; use twice for longer format.

## Example

The following command creates a file titled 'helloWorld.txt' as root.

	sudo touch helloWorld.txt

The following command Lists a users privileges.

	sudo -l