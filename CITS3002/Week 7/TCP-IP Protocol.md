**Dependencies:**

- Many internet protocols depend on each other and they demand the services provided by other protocols.
- For example, the file transfer protocol demands that it operates over a reliable stream protocol, delivered to a host on a network which provides flow control.
- The TCP/IP suite provides a structured way for devices to communicate across networks. Compatibility at the protocol level within each layer is essential for services to function properly.

![[Screenshot from 2024-04-30 08-45-29.png]]

- Transport layer is the space where the interface between software and OS support for delivery within multi-hop network will be met. Two common protocols (TCP and UDP) are used within this layer.
- The application layer sits at the top and its the primary layer where new functionalities are introduced to the internet.
	- New applications and protocols are continuously developed and can be readily incorporated into this layer. This stands in contrast to lower layers, which are more static and provide foundational network services.
	- For example, new encryption methods and remote login functionalities can be downloaded and added to the application layer. 

`BootP`:

- Bootstrap Protocol is a network protocol which allows a client machine to obtain an IP address and other configuration information from a server on a network. It is a simple protocol that is typically used during the initial boot process of a computer.
	- When a machine boots up, the `BootP` client on the machine broadcasts a UDP packet, which contains the clients MAC address.
	- The server can respond with a UDP response signifying the IP address, subnet mask, default gateway, DNS server and more for the client. 
	- Once the client receives the response, it can now start using the network.
- `BootP` can be extended to provide even more information and there are implementations that allow you to boot the entire OS over the network.

**IP Packets:**

- The Internet Protocol provides an unreliable, best-effort, connectionless, packet delivery system. 
- Internet datagrams resemble 'standard' physical-layer frames, but are designed to be encapsulated within the normal network framing schema. Hence, Internet datagrams are said to run on top of traditional networks.

![[Screenshot from 2024-04-30 09-08-31.png]]

- IP datagrams contain various fields, each with a specific purpose. The field lengths are predetermined by the protocol and reflect its implementation.
	- Some fields specify the lengths of the header, the payload and the entire datagram itself.
	- The second-to-last field allows for various optional instructions, such as preventing packet fragmentation. It's length can fluctuate, meaning that you can find its size by subtracting both header and payload lengths from the total datagram length.
- The diagram above represents the frame format of a datagram. There are fields within a datagram that have well-defined roles. Fields and their lengths represent what is required by the protocol and how it is implemented.
- Some of these fields talk about length of header, payload and combined length. Second last field is for options, which you can provide options such as (i don't want the packet to be fragmented). Options field, thus, is of variable length and is found by subtracting header length by payload.

*Structure of IP:*

- Checksum: 16 bit checksum of the header only. The checksum is the one's complement of the one's complement sum of the header. 
	- The header must be unmodified as we need to ensure that the source and destination are not modified.
	- If other layers care about the payload, then other layers will add their own checksums for these sections (transport layer).
- Internet Protocol: The number will correspond to what protocol the packet is using for its delivery. However, violates OSI model again as the delivery is dealt with in the transport layer.

![[Screenshot from 2024-04-30 10-05-17.png]]

- However, it is used a lot for congestion control as we can discard erroneous packets based on the importance a protocol places on error correction (such as UDP being low and TCP being high).
- TTL: Time to Live is a measure of the hops. Each time the datagram travels through a host, the TTL is decremented before being sent out again. The number its decremented by is the number of hosts the packet is willing to travel to. 
	- When it gets to zero, it can be discarded as its travelled to far.
	- Hop count used in flooding to ensure that packet doesn't get lost.

*Internet Control Message Protocol (ICMP):*

- A method to report routing errors. ICMP allows gateways and hosts to exchange bootstrap and error information. Gateways send ICMP datagrams when they cannot deliver a datagram or to direct hosts to use another gateway.
- Hosts send ICMP datagrams to test the 'liveness' of their network.
- `ping` program in UNIX systems, for example, sends ICMP echo messages to a specified machine. Upon request of the echo request, the destination returns an ICMP echo reply suggesting there is no route to the host.
	- First time we invoke this, it takes a while longer to get to the host and the second time, the ping time drops as the route to the host is now figured out.
- On web browser, Error 404 means that there was no route to a host. It doesn't mean a host doesn't exist but it means that we couldn't get there.
- Other messages include the below:

![[Screenshot from 2024-04-30 10-22-02.png]]

- Time exceeded: Not the time, but the response to the TTL field (so hops exceeded).

*Traceroute*:

- `Traceroute` utilises the IP protocol 'ttl' field and attempts to elicit an `ICMP TIME_EXCEEDED` response from each gateway along the path to some host.
	- It increases the TTL count until the destination is reached.
	- The good thing about this is that all the nodes across the route will send back its IP address indicating that the TTL count has exceeded. However, using the IP addresses, we are able to track where the packet ends up.




