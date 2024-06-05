**Internet Transport Layer Protocols:**

- The transport layer serves as the intermediary between application programs and the OS's delivery mechanisms.
- Although standardised protocols are readily accessible, they are immutable. Consequently, applications are integrated with them through libraries and OS syscalls.
- The OS assumes the responsibility of handling and caching data it encounters. Requests can be made to these underlying transport protocols via the kernel, allowing it to efficiently manage them with access to hardware resources and NIC cards for traffic delivery.
	- Only from Windows 95 where internet protocols were introduced into OS kernel. Prior to this, they were implemented as a user-level library.
- Kernel (which handles scheduling and memory) enables packets to have a straight path from the NIC card to a memory buffer within a process.
- The Transport Layer deals with TCP and UDP datagrams whilst the Application Layer deals with processes. Thus, we need to have a mechanism to get from the NIC, to the kernel, to the transport layer protocol to the right process. 
	- A process might also have multiple network connections and ports open as well. Thus, the uploading and downloading of information must be done in a asynchronous manner. 
	- We need an addressing mechanism which will determine the communication channel the process is using.

![[Screenshot from 2024-04-30 11-07-15.png]]

- Layered stacks add their own headers to the payload, with the Transport Layer providing ports within the header (which distinguish an endpoint of connectivity within a process on a remote machine).

**Port numbers:**

- Port numbers serve as additional identifiers beyond IP addresses, enabling communication between individual operating system processes on hosts. 
- In the context of transport protocols such as TCP, each incoming frame is distinguished by a 16-bit positive port number, designating the specific "software end-point" intended to receive the data payload.
- TCP plays a crucial role in segmenting incoming datagrams, using these port numbers as indices to route each segment to its corresponding communication end-point.
- Port numbers below 1024 are considered reserved ports, typically requiring elevated privileges such as `root`.
	- These port numbers should only be used for the services that are defined to use these numbers. For example, web servers should only use port 80 or 443. The OS assumes that only web servers will speak to port 80. These are defined in tables on your PC. 
	- Software can map the name and the port number.
	- There are ports which are over 1023 that are pre-assigned but not reserved. For example, x11 protocol (6000) provides display rendering over a network. Thus, we have to have other protections to grab port 6000 before the booting process or another process can grab the port.
- Port numbers complement IP addresses by facilitating communication between individual processes on hosts, with TCP efficiently managing the routing of data packets based on these port identifiers.

**Transmission Control Protocol:**

- TCP transforms 'raw' IP into a full-duplex reliable character stream. TCP uses a 'well-understood' sliding window with selective-repeat protocol and conveys a number of important fields in its TCP frame header.

*Header:*

![[Screenshot from 2024-04-30 11-42-23.png]]

- A protocol that taking datagrams that is correctly routed to the right host and this protocol has the responsibility to send it to the right process.
- Processes might have one or more ports, meaning that a TCP packet will have a source port and a destination port.
- Checksum as TCP is a reliable protocol options which require length fields to ensure whether options are being used.

*Sliding window and TCP:*

- The size of the sliding window protocol which is being implemented within TCP.
	- Sliding windows send multiple frames out before receiving ACK back. Window is the size the protocol can send and the size of the datagrams in which it can receive.
	- Pipelining and piggybacking allows the datagrams to travel faster then what it was prior to this. It allows this to occur as pieces of information go to the same place. 
	- Sliding Window Protocols occur poorly if it goes wrong. There is an agreement between source and destination, thus, to what the window size is so that it doesn't fail.
- TCP begins with a cautious approach, ensuring reliable communication through a stop and wait protocol, and gradually adapts to more efficient methods such as sliding window.
	- If sliding window is successful, the window size will grow and this continues to happen until an error occurs. If an error is encountered, it returns to the simple stop and wait protocol and redoes the procedure again.
- In LAN, sliding window will grow to the maximum and will continue to use it. On a fast unreliable network, it will use a smaller sized window that will continuously go back to stop and wait.

*Sequence and acknowledgement numbers and TCP:*

- 32 bit numbers are used for both sequence and acknowledgement numbers, which seems quite large. However, these refer to the bytes being delivered in the current segment.
	- For example, we send 100 bytes to the destination with sequence number 0. ACk comes back. We then send 300 bytes using sequence number 300. 
	- Sequence number looks similar to an index in an array which reflects how many bytes are carried in the payload.
- This is implemented for when errors occur, where we can send the number indicated in the sequence number again with a different length, which will indicate that the piece is being sent back again.

**TCP/IP and Applications:**

- Provides 6 major features for applications:
	1. Connection orientation: For two programs to employ TCP/IP, one program must first request a connection to destination before the connection can proceed.
		- Cannot expect a random TCP frame and expect receiver to make sense of it.
	2. Reliable connection setup: Both applications must agree to connection without interference from other packets from other connections.
		- Negotiation on window sizes, how much can be sent at a given point etc.
	3. Point-to-point communication: Each communication session has exactly 2 endpoints.
		- Only one source can speak to one destination.
	4. Full-duplex: Once established, a single connection may be used for messages in both directions. (Requires buffering at both ends, allowing computation to continue whilst connection is active)
		- Both ends can read and write.
	5. Stream interface: From app POV, data is sent and received as a continuous stream of bytes (apps need not communicate using fixed-size records and data may be written and read in blocks of arbitrary size).
		- Looks and acts like a file.
	6. Graceful connection shutdown: TCP/IP guarantees to reliably deliver all 'pending' data once a connection is closed.
		- When one end quits, it should inform the other end that it will be quitting.

*TCP/IP 3-way connection establishment and sequence numbers:*

- A three-way handshake is employed in which sender and receiver agree upon the sequence numbers that they use to exchange data.
	- Sequence numbers are random numbers agreed upon by the sender, providing security as random numbers outside of this range will be discarded.
- TCP's internal open sequence. If machine A wishes to establish a connection with machine B, A transmits the following message:
	- `A->B: SYN,ISNa`: A wishes to synchronise with B. Initial sequence number, in this case, is `a`.
	- `B->A: SYN, ISNb, ACK(ISNa)`: B replies to say that its willing to synchronise with A, it sends its initial sequence number and acknowledges A's initial seq_num.
	- `A->B: ACK(ISNb)`: Finally, A will acknowledge B's initial seq_num and the connection commences.

**TCP/IP Retransmissions:**

- To ensure reliable communication, retransmissions are triggered by timeouts.
- Determining the timeout value for retransmissions is crucial due to the vast range of network distances and delays. With numerous destination hosts, finding an optimal timeout value becomes challenging.
- The Retransmission Timeout (RTO) value is a crucial parameter in TCP that determines how long the sender waits before retransmitting a segment. It is essential to set the RTO value appropriately to balance:
	- Delivery speed: Smaller RTO leads to faster retransmits, but can waste bandwidth and hurt performance if too small due to normal network delays or congestion.
	- Reliability: Larger RTO improves reliability by waiting for ACKs, but can significantly slow data delivery if too large due to lost segments.
- TCP dynamically adjusts the RTO based on dynamic estimate of the round trip time. TCP uses an exponential smoothing technique to calculate a smoothed RTT (SRTT) and a smoothed mean deviation (SMDEV).
	- The timeout value is predicted and it adapts to the environment that it senses.
	- If SRTT calculation is correct, we will get a RTO that is only slightly longer then the actual RTT. Hosts close together will not notice the change in timeout as latency is so small but hosts far apart are willing to tolerate the timeout being every once in a while.

*Network Application Program Interfaces (APIs):*

- Interface between software and network services. Network services (transport layer protocols) are in the kernel, meaning that syscalls must be made in order to access them.
- Use of networking should look like the use of files (which is a well understood paradigm that has been used for 60+ years).
- Calls to Unix `open()`returns a file descriptor which is then used in calls to `read()` and `write()`.
- It is preferable to use this type of semantics, but this is difficult for a number of reasons:
- We would like to use the same system calls to access the network. For example, `read()` and `write()` regardless of how they were created. However, networks and files are slightly different. 
	- For instance, you can continue to write to a file after it is deleted, or write to a file after permissions have been elevated. For a network, while you might be able to open a network connection, you do so at the time that the connection is created. 
	- Any of the intermediate nodes might go down, which will mean that the connection cannot be read anymore.
- Another major different between a file and a network is that a file is very passive. The endpoints of networks are very active, meaning that you might get two very different outputs given two different times.
	- No longer can a syscall simply call a filename, but there are many more associations to a network that must be made for network I/O then file I/O, such as:
		- {protocol, local-address, local-process, remote-address, remote-process}

*An example Network API:*

- How they are implemented is fairly obvious: A layered approach implemented in the kernel. A server process will start whilst a client connects to the server. Thereafter, both endpoints are identical (full-duplex).
	- Client makes a request to the server and the server replies. Much like the project, station servers will receive queries from client but if station doesn't have the answer, then it will ask another server for the answer. 
	- Coding for server-client is not fixed. They can be a client and a server at the same time. Station can receive a query from a browser and receive a response at the same time.

![[Screenshot from 2024-04-30 19-57-52.png]]

- The socket layer provides the interface between user programs and the networking (via operating system syscalls).
	- It specifies whether we should use UDP or TCP. Protocol doesn't need to be defined until the lower layers.
- The protocol layer supports different protocols in use (such as TCP/IP).
- The device driver supports physical drivers.

*Domain addressing:*

-  Large combinations of protocols and drivers are specified when the kernel is configured.
	- For example, sockets that share common communication properties are grouped into address families.

```C#
#define AF_UNIX 1
#define AF_INET 2
```

- `AF_UNIX`: Defines the host that you are currently working on. Two machines communicating locally is called UNIX.
- `AF_INET` If we are communicating off our machine to another machine, then it is INET.

*Establishing socket with OS system calls:*

```C
#include <sys/socket.h>

	int family, type, protocol;
	int sd, socket(int, int, int);
	sd = socket(addr_family, type, protocol);
```


- The `socket()` call established an end point of a communications link. We establish a socket (which an endpoint of connectivity) by reserving data structure so that when data arrives, these structures will be in place.
	- We will tell the syscall what address family we are using, what is the type of protocol and the protocol that we want to use.
	- `addr_family` will be internet domain (`AF_INET`), `type` is 0 (for default) and protocol is TCP(6). 
	- NOTE: Address family determines the type of addresses that the socket can communicate with, defined via domain addresses.
- We get an integer, socket descriptor (akin to file descriptor). 
- The value space of socket and file descriptors are the same. If a process has three files open and opens a socket descriptor. The file descriptors are 1,2,3 and the socket descriptor starts at 4. The next file open will have `fd` of 5 and so on.
	- The reason this is the case is because sockets are treated as files at the system level. In fact, all I/O is treated as files, meaning that they will increment onto the `fd` numbers.
	- There is another system call called `select()` which will tell us when a descriptor is ready for reading/writing.

*Connection establishment:*

- When we establish connection between endpoints, the server must exist first and ideally, it should support multiple clients simultaneously. 
- A `listen(sd, queue_length)` queue will identify the number of clients which can connect to the server. 
- Once this is done, the server will start to accept new connections:

```C
new_socket = accept(sd, from, fromlength);
```

*Procedure for socket connection between server-client:*

![[Screenshot from 2024-05-01 08-57-53.png]]

- The steps of what will occur:
	1. Server requests, from the OS, a socket. `sd` is returned.
	2. Server then wishes to bind an address to a socket using `bind()`. The address and port will be how the application will communicate externally. If its web server, early on it will bind itself to port 80 so that a client can request a connection to port 80.
	3. Listen queue is then put onto the socket using `listen()`. Now the server is willing to accept new connections and it blocks until this occurs.
		1. Client needs a socket from the OS as well.
		2. It uses `connect()` to connect to a remote host using a port (eg port 80).
	4. Server makes the `accept()` call which will unblock the server and the connection occurs.
		1. `read()` and `write()` instructions occur between the two.
	5. Either end will do a `close` or a shutdown indicating they don't want to continue.

