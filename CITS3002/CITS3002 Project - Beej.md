**Two types of Internet Sockets:**

1. Stream Sockets (`SOCK_STREAM`): Reliable two-way connected communication streams.
	- HTTP uses stream sockets to get pages. Stream sockets achieve high level of data transmission quality by using TCP/IP protocol.
2. Datagram sockets (`SOCK_DGRAM`): Unreliable connectionless: Sending a datagram may arrive at the other end, but it could be out of order but error-free.
	- Don't have to maintain a connection.

*Struct s:*

- Socket descriptor: An int which is a reference to the socket which is open.
- `struct addrinfo`: A structure which is used to prepare socket address structures for subsequent use, such as in host name and service name lookups.
	- It holds information such as flags, address family, socket type, address length, address pointers, canonical hostname and a pointer to the next node in a linked list,
	- Typically filled out by `getaddrinfo()` and can be used to store information about IP addresses and ports.
	- 