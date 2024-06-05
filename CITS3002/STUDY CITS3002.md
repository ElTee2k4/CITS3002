**INTERNET LAYER IN THE INTERNET:**

**Introduction:**

- Internet is viewed as a collection of networks or Autonomous Systems that are interconnected.
	- Constructed from high bandwidth lines and fast routers.
	- Backbones called Tier 1 networks are connected to ISPs, which provide the internet to LANs.
- The glue that holds the entire internet together is the Internet Protocol (IP). Unlike most older network layer protocols, IP was designed from the beginning with internetworking in mind.
	- Network layers job is to provide a best effort way to transport packets from source to destination without regard to whether these machines are on the same network or whether there are other networks between them.
- In the internet, the transport layer divides data into IP Packets, typically around 1500 bytes, for routing through routers to the destination. The network layer reassembles these packets at the destination, handing them to the transport layer for delivery to the receiving process.

**IP Version 4 Protocol:**

- In internetworking, each node needs to be individually addressed, done using IP Addresses. The Internet layer works to identify endpoints via IPv4 addresses.
	- The internet protocol provides an unreliable, best-effort connectionless packet delivery system.
- Each address consists of a network portion and a local portion.
- IPv4 datagram consists of a header and a body or payload. The header has a 20-byte fixed part and a variable length option part.

![[Screenshot from 2024-06-05 17-23-22.png]]

- The bits go from left to right and top to bottom, with the first bit of the version field being transmitted first (big-endian).
- NOTE: Endianness refers to how computers store multi-byte data, such as integers, in memory.
	- In big-endian, the most significant byte (representing the largest value within the data) is stored at the lowest memory address. 
		- When transmitting data, the bytes are sent in the order they are stored in memory in, starting with the most significant byte first (the byte representing the highest order part of the data or the first byte).
		- IP address 192.168.1.1, it would be stored in memory as 192,168,1,1
	- In little endian, the least significant byte is stored at the lowest memory address. Bytes are transmitted in reverse order, starting with the least significant byte.
		- IP address 192.168.1.1, it would be stored in memory as 1,1,168,192
	- Internet standard uses big-endian to ensure consistency when transferring data between different computer architectures.

Fields:

- Version: Version of the protocol the datagram belongs to. V4 dominates today.
- IHL (Internet Header Length): Lengths are predetermined by the protocol and reflect its implementation.
	- Some fields specify the lengths of the header, the payload and the entire datagram itself.
- Differentiated services: Distinguish between different classes of services with different combinations of reliability and speed.

IP Addresses:

