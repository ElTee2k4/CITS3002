**Requirements of Internetworking:**

- Networks come in differing topologies and speeds and no single network configuration suits everyone.
- The technology known as *internetworking* draws the multitudes of networking technologies into a common framework that combines networks into internets.
	- Connection of general networks into a common framework. The most common example is the internet.
	- Strives for portability and independence of the OS that they run on.
- An internetwork does a multitude of actions including:
	- Supporting a link between networks. The networks themselves don't have to be apart of the internet and rather, we just need to be able to connect two networks of different implementation.

![[Screenshot from 2024-04-21 19-48-03.png]]

- There are two ways to connect dissimilar networks: One is to support all dissimilar networking configurations (which is impossible) and two is to set a standard that all those networks have to match.
	- If internetworking is effectively a virtual wire between two networks, the role of the virtual wire is to set a standard that two network configurations that are converted into this common format.
	- However, most vendors have given up in trying to support their own network suite.
	- Intranetwork simply means that the visibility of the system is constrained to the physical location they are sourced from.
- Responsibilities are best met by a common framework and all protocols are free.
- Managing data that needs to be exchanged by different networks. Internetworking defines what the data looks like through a canonical representation of data that data types are converted to.
- Nodes only need to require the minimum amount of resources it needs in order to successfully become part of the network. It doesn't require all of the advancements of other nodes.

*Initial internetting concepts:*

- Concepts that made the internet successful include:
	- The ability to hide dissimilarities between hardware and softwares.
	- Everything about the internet is documented and not owned by any company and country. 

*Internet Request for Comments:*

- Request for comments define what a protocol has to do, what a node has to do to conform to protocol and are still updated at a constant rate.

**TCP/IP Protocols:**

- Reduced complexity: TCP/IP combines some functionalities of the OSI model, leading to a less complex structure of only 4 layers instead of 7. This reduces the amount of data exchanged between layers and simplifies communication.
- Efficiency: With fewer layers, data doesn't need to be parsed through as many steps, potentially improving efficiency.
- Similarly to the OSI model, the TCP/IP layers believe that they are talking to each other, rather then the adjacent layers.
	- For example, a web browser believes it is talking directly to the web server (both applications), even when it is travelling through different nodes and layers.

![[Screenshot from 2024-04-22 11-17-39.png]]

*Modes:*

- Depending on the quality of service and the length of connections required at layers, four host-host layer protocols are in frequent use:
	- A reliable connection-oriented data protocol providing reliable, sequenced delivery (TCP).
		- A file stream between one stream and another without duplicates and error. Reliability prioritised.
		- If applications don't require this, then we can fall back to lesser protocols which offer reduced types of service.
	- A low overhead minimum functionality datagram protocol (UDP).
		- Does not guarantee delivery or order of datagrams, making it suitable for apps where some packet loss is acceptable. It is much faster and more lightweight due to lack of built-in error checking and re-transmission mechanisms.
		- The only guarantees is error corruption will not occur and the datagram will arrive at the correct protocol.
		- For media and other forms of data, it is acceptable to use these forms of data. If some datagrams are missing, the application is left to resolve what is missing.
	- Reliable datagram protocols (RDP), providing low overhead communication for 'burst' delivery between random endpoints (speech and low-def video)
	- A real-time streaming protocol, characterised by the need of handling a steady stream with minimum delay variance.
- The most common distributed file system is called NFS (Networked File System) or NTFS (New Technology File System), which use UDP for file transfer.
	- This is wise in some situations as communication within a building or a LAN is good enough as error rate is very low.
- The process/application layer protocols define resource sharing and remote access.

*Larger application layer:*

- The expansive Application layer within TCP/IP allows for diverse protocols and functionalities to be implemented without strict layer restrictions.
	- Encryption, for example, can be handled within the Application layer without needing specific lower-layer support, providing flexibility and leverages existing encryption libraries within applications.
	- Different applications don't have to assume that protocols exist within the stack and thus, can bring their own encryption standards and other protocols into the sending of the app.

*Mapping OSI Layers to TCP/IP:*

- Network Access: This layer incorporates functionalities from OSI's Data Link and Physical layers, handling tasks such as media access control and physical transmission of data.
- Internet Layer: Remains similar to the OSI's network layer and deals with routing of packets across internetwork.
- Transport Layer: This layer functions identically in both models, managing reliable (TCP) or unreliable (UDP) data transfer between applications.
- Application Layer: TCP/IP has a single comprehensive application layer. This contrasts with the OSI model, where multiple layers (Session, Presentation and Application) handle application-specific tasks.

**Traditional Class-based IP Version 4 Addressing:****

- In internetworking, each node needs to be individually addressed, done using IP addresses. Internet Layer identifies endpoints via IPv4 Addresses.
- If every device was accessible over the global internet, each device would require its own unique address (worldwide). However, this isn't the case. Thus, each address consists of a network portion and a local portion.
	- Uses unsigned 32-bit integers to represent a particular endpoint. It is an address which corresponds to NIC card (meaning if there are two NICs within a computer, you will have two NICs).
	- Original design of the internet had 256 networks. Very early internet connectivity was between universities and large corporations (NASA, Boeing).
- Each computer or device accessible using IP protocols have at least one unique address within its subnet.
- 256 is a power of two, suggesting that we will enumerate the networks on the internetwork. A network of 1000 computers (hosts), for example, will have two naming conventions:
	- The name/number of the network
	- The name/number each host of 1000


*Classes of IP Addressing:*

- Internet addressing schemes accommodate both large and small network topologies.
	- We can have a small number of large networks or a large number of small networks. As time as passed, the internet is made up of a large number of small networks.
- Classes were a way to categorise IP Addresses, helping to determine:
	- The number of networks available: Each class allowed for a different number of total networks. Class A had the lowest whilst class C had the most.
	- The number of devices per network: Class A supported the most devices per network whilst Class C supported the fewest.
- The notion of classes gives the internet addressing schemes the flexibility to handle different sized network topologies:
	- Class A was designed for large networks with many hosts within, but a small number of Class A networks. Class B was suitable for medium sized networks with tens of thousands of devices whilst Class C was designed for small networks of 256 devices but there were many of these available.

![[Screenshot from 2024-04-29 13-33-42.png]]

- The preceding bits of an IP Address are scanned. We scan the number of 1s until there is a 0 and this combined bit sequence will determine the class which the node is apart of.
	- Leading bits can then be seen to ensure that the datagram is meant for the node it arrives at. If not, then it can reroute the datagram to another entity.
- Class D and E, historically, multicast addresses are defined were a group of computers anywhere and enumerating these groups allows a sequence of data to be sent to all computers in that group.
	- This worked so that registering multicast subscription will allow you to receive the same data as everyone else that receives the data.
- Dotted decimal notation is used to describe the 32-bit addresses.

![[Screenshot from 2024-04-29 18-40-47.png]]

- Expanding the first 3 bytes as binary will allow you to look at the leading bits and tell you whether it was Class A, B or C.
- Because we reserve certain bit patterns to represent certain things (such as Classes), we do not get the full capacity of IP Addresses available.
	- `localhost`, which is the computer the user is using, can direct traffic to another computer by using `localhost` address.
- Functions in methods in programming languages deal with IP Addresses. They are not necessarily treated as 32-bit integers in these languages, however.
	- There are many standard functions that convert IP Addresses, names, Ethernet numbers etc.
- Endianness refers to the byte order used to represent multi-byte data like integers in computer memory. There are two types of endianness:
	- Big-endian: This stores the most significant byte (the one representing the largest value within the 32-bit IP Address) at the lowest memory address and will be sent first through the network. 
		- The octet that holds the highest power of 2 in its binary expansion is the most significant.
	- Little endian: This stores the least significant byte at the lowest memory address.
	- Internet standard uses big-endian byte order to avoid confusion when transferring data between different computer architectures. However, all computers use little-endian, meaning that all computers need to convert to be consistent with others.

*Classless Inter-Domain Routing (CIDR):*

- Groups IP Addresses into CIDR blocks based on a shared prefix length in the binary representation of the address.
	- This allows for a wider range of network sizes to be accommodated efficiently.
	- Whilst classes define three pre-defined class sizes, CIDR lets you create variable-sized blocks based on need, allowing more users to have an IP Address without needing a bigger network. For example, an owner of Class A owns 16 million addresses, which is very unlikely to be fully utilised.
- CIDR is where the boundary between network and host addresses moves dynamically. This allows many different size networks which holds different number of hosts.

![[Screenshot from 2024-04-29 20-12-57.png]]

- Prefix represents the boundary between network and host.

*Mapping Internet Addresses to Physical addresses:*

- There needs to be a way to find the association between logical IP Address and the physical address used in the MAC layer. 
- Most devices used Ethernet which are 48 bits wide. Unfortunately, we cannot used Ethernet addresses to have 1:1 mapping between IP and Physical.
- A simple protocol will provide the mapping between the two, called Address Resolution Protocol (ARP).

![[Screenshot from 2024-04-29 20-30-06.png]]

- Host A wants to communicate with Host C on the same network. 
- However, Host A doesn't know Host C's MAC address. Host A will, thus, boradcast an ARP request containing:
	- Host A's MAC address
	- Host A's IP Address
	- Target IP Address
- All devices on the network will receive the broadcast ARP request. Only Host C will recognise its IP Address in the ARP request and sends an ARP response directly to Host A containing its MAC address and Host A's IP Address for confirmation.

*Address Resolution Protocol:*

- Supports queries and responses within the payload of an Ethernet frame. Primarily, the two that are important is the mapping between IP and MAC addresses and vise versa (RARP).
- ARP messages are encapsulated within Ethernet frames. The payload of the Ethernet frame will contain the ARP request or response.
- Because IPv6 addresses are longer than IPv4 addresses, ARP can handle this by specifying the address length within the protocol itself.
- Ethernet frames have a field that specifies the length of the payload, traditionally between 46 and 1500 bytes.
- However, beyond 1500 bytes, the field now carries a protocol number. For example,
	- Byte 1501 corresponds to an ARP request whilst another value represents an ARP reply. 
	- This violates OSI model as a lower layer (Ethernet and Physical Layer) is aware of the specific protocol carried in the payload.

