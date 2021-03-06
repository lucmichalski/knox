# knox

A package containing implementations of port knocking using the libpcap wrappers in Google's gopacket library. You can use it to create services that monitor your network interfaces for a specific sequence of ports, even if the ports in question are closed. This package includes support for executing an arbitrary "payload" after a successful port knock occurs, and tracks which IP address it received the sequence from.

## Dependencies

- [gopacket](https://github.com/google/gopacket)

## Example

This is an example of a simple port knocking listener that triggers on ports 1337, 1338 and 1339:

```go
package main

import "fmt"
import "knox"

func main() {
	ch := make(chan knox.Hostinfo, 0)
	sig := make(chan error, 0)
	go knox.PortStream("eth0", "45.33.32.156", ch, sig)
	res := <-sig
	if res != nil {
		fmt.Println("Error in setting up the sniffer. Check permissions, interface name, IP?")
		fmt.Println(res.Error())
		return
	}
	var basic_payload knox.SimplePayload
	knox.PortKnocker(ch, basic_payload, 1337, 1338, 1339)
}
```
