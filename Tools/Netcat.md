**netcat** is a computer networking utility for reading from and writing to network connections using [[TCP]] or [[UDP]].

## Usage (Listening)

	nc [options] [hostname] [port]

## Notable Options

	-l

The below command executes **netcat** in listening mode. Doing so creates a server that awaits an inbound connection to be established with said server on a specified [[Port]].

	-n

Numeric-only [[Internet Protocol Address|IP Addresses]], no [[Domain Name Server (DNS)|DNS]].

	-v

 Netcat command for output to be verbose. Pair with two v's for additional verbosity  

	-p

The local port number to be designated.

## Example

Below is a typical example of how **netcat** is used to listen for inbound connections on a local machine.

	sudo nc -lvnp 1337