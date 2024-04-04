**Simplified Satellite Broadcasting:****

- Local Area Network (LAN) is the network that a user will be most engaged with.
	- Can be both wireless and wired.
- Satellites are being involved in even terrestrial transmission (Starlink Satellites). Satellites are almost essential to remote areas as NBN barely reaches.
- A simplified satellite recording scheme can be seen as the following:
	- Many users share a **single** channel
	- Propagation at the speed of light
	- However, the distance travelled is large (35,880km), meaning that there is still a large latency, roughly a quarter of a second.
	- **Bandwidth** (amount of information received per second) is currently 5x higher then typical LAN based networks because it is less limited by the speed of local infrastructure.
		- Bandwidth is very high not because of frequency, but because of the amount of channels at roughly the same frequency.
	- **Cost** is the same whatever the distance the sender and receiver. 
	- Satellite acts as repeater of incoming signals.
	- If two stations broadcast simultaneously, the satellite will receive and re-broadcast the sum of these two signals, resulting in garbage (collision)
	- A sender can listen to the re-broadcast of their own packets and determine whether a collision has occurred. Notice that there are no acks.
	- Users are uncoordinated and can only communicate via the channel.
- If two devices are communicating via a satellite, their communication goes from person-satellite-person.
	- Mobility is an advantage but satellites themselves are far away.
- There is an issue where the sender and receiver are in the same footprint, dictating how we are going to communicate.

*Over the same network:*

- Communication in satellites occur over the same channel, meaning that all data transmissions share a single communication pathway. There is only one physical medium or link through which all communication traffic passes.
	- All devices within the network can send and receive data using the same frequency or communication medium.
	- Multiple users share the same satellite transponder for communication.
- Conversely, communication that isn't over the same channel is where different communication pathways are used for transmitting data between different entities within the network.
	- Separate physical or logical pathways are established for different communication streams. This can include networks with dedicated frequencies for different communication purposes.

*Advantages for network protocols:*

- No need for DLL acks as each sender can verify the correctness of their own messages through hearing them getting echoed back to them.
- All users share the same communication channel, meaning that there is no need for distinct subnet or network layer routing.
- Since the medium is shared, no congestion problems typically associated with networks where multiple devices content for bandwidth.
- Mobile users are supported as they can access network from many different locations.
- Roughly the same latency for people close together vs far apart (20 m vs 2000 m)

*Implications for network protocols:*

- Long propagation delay (time from sender to receiver) of at least 270 msec, due to travelling vast distances through space.
- All users receive messages, introducing security implications (however, encryption).
- No central control of unethical users as it becomes challenging to enforce policies or restrict access.
- A single communication channel is used for uncoordinated users, who can transmit at any time without permission. If two or more try to use medium at the same time, there will be a collision.

*Conventional channel allocation:*

- For most casual users, there is no need for the amount of bandwidth that a satellite offers. Thus, we divide the bandwidth for multiple users.
- There are two common schemes to share the medium:
- **FDMA (Frequency division multiplexing allocation)**: The channel is divided into N frequency slots for a maximum of N users. Guard bands are placed between these to limit interference
	- Bandwidth is taken and instead of transmitting over a very wide part of the spectrum, we only transmit bands. Because we don't use all the bands, guard bands are there where no one transmits to ensure that adjacent bands do not interfere.
- **TDMA (Time division multiplexing allocation):** Divides channel into time slots or intervals, typically very small (125 microseconds). Each user is allocated one or more time slots during which they can transmit data.
	- Rather then letting everyone transmit in 1 second, you can transmit for 1 tenth of a second and then pass responsibility to another person. This is done in this way so only one person is using their part of the medium at a time.
	- You do not jump in when it is someone else's turn, you must wait until they are finished their transmission time.
- Both FDMA and TDMA are very inefficient since the actual number of users is significantly smaller or larger than the number of available frequency bands or time slots.
- Furthermore, communication patterns and data transmission rates vary significantly over time, which FDMA and TDMA are not suited for.
- **Frequency hopping:** Instead of transmitting on one frequency, the frequency used continuously changes for security reasons as someone cannot continuously listen to one frequency channel.
	- The frequencies, however, are not random as the sender and receiver agree and exchange their channel information. They know ahead of time the channels that they will use.

*Terminology:*

- **Offered load** refers to the amount of traffic that the network devices are trying to send onto the network medium at a given time.
	- If we are in an environment where multiple users are transmitting at the same time, there will be collisions. When only one frame is offered into the physical medium, it will be successful. If there are two or more frames transmitted in the same physical medium, then there will be collisions.
- **Throughput:** Amount of data moved successfully from one place to another in a given time period.

*Pure ALOHA:*

- One of the earliest multiple access protocols where devices can transmit data whenever they have it to send, without waiting for any synchronization or permission from a central authority.
- Collisions will occur if two or more devices attempt to transmit at the same time. If the checksum of both blocks were calculated, then they would be different.
- After a collision, devices involved in the collision wait for a random amount of time before attempting to retransmit their data, in an attempt to reduce the likelihood of another collision.
- Pure ALOHA got a bandwidth utilization of 18%, which is incredibly high for a system with no constraints.
- To increase this number, the opportunity for collision needs to be reduced. The obvious solution is to not allow two nodes to transmit at the same time.

*Slotted ALOHA:*

- In Slotted ALOHA, time is divided into discrete slots and devices are only allowed to transmit data at the beginning of these slots. The duration of the slot is equal to the maximum time it takes for a signal to propagate across the network. 
- When a device has data to send, it waits for the beginning of the next time slot before transmitting. The synchronized timing reduces the chance of collisions compared to Pure ALOHA, where transmissions could occur at any time.
	- Collisions still occur but it does make a difference to transmit at the start of a slot rather then in the middle of one as there is a lower chance of two or more devices attempting to transmit simultaneously.
- This system requires nodes to have synchronized source or another resource that sends a beep at the beginning of every 100 slots.

![[slotted.gif]]
- Slotted ALOHA maxes out at around 37% bandwidth utilization. However, there is still a method for getting 100% bandwidth utilization using ALOHA. 
	- Point to Point communication: By having one slot for one user to pass information to and from another user, we provide 100% utilization of bandwidth. This gets utilized by LANs.

**Local Area Networking:**

- LANs is a router-less network, using the same protocol stack for each device and only using a uniform, local networking media.
- LANs:
	- Span a relatively small area (typically within a single building)
	- Typically are privately owned and operated, fostering a unified administrative control
- All nodes within a LAN adhere to a standardized set of communication protocols. 
- LANs are also subject to defined limitations, including restraints on transmission distance, packet size and amount of transmissions.
- The medium is shared across the nodes in a LAN

![[Pasted image 20240403093416.png]]

- **Star network:** Install IBM devices into a cardboard and instead of connecting to a long cable, you all connect to a hub. The hub works to regenerate, amplify and retransmit the signal across the network.

*Carrier Sense Networks:*

- Carrier Sense Multiple Access requires that each station first check the state of the medium before sending their information.
	- Each computer first senses if the wire is idle. If it is, it will send its data, avoiding a collision.
	- If you have two computers do try to send their data at the same time, a collision will occur. The computers will then wait a random amount of time before the data is attempted to be sent again.
- CSMA was only relevant in half-duplex networks where data communicates in both directions but not at the same time.

*How this affects shared media:*

- All frames are of constant (or small, bounded) length, to ensure that no node monopolizes the medium.
- We assume there are no transmission errors, other than those of collisions. We don't want to spend a lot of time calculating checksums.
- No node monopolizes or captures the entire medium.
- The random delay after a collision is uniformly distributed and large compared to the frames' transmission time.
- Frame generation attempts (OLD and NEW) form a Poisson process with mean G frames per time.
- A station may not transmit and receive at the same time but we do want to hear collision after we have transmitted.
- Each station can sense the transmission of other stations.
- Sensing of the channel state can be performed at the same time as transmitting.
- The propagation delay is small compared to the frames' transmission time, and identical for all stations.
 - If we introduce randomness, we might have an issue where the randomness colludes against us and we have to wait a long time to its out turn.
	- Thus, there needs to be a guaranteed upper time for when the data will be transmitted. 

*1-Persistant CSMA:*

- When a node has a frame to transmit:
	- It continuously senses the shared transmission medium to check the state: idle or busy.
	- If the medium is idle, then it transmits immediately. However, if the node is busy, then it will continuously sense the medium until it becomes idle.
	- Once shared medium is idle, the node will transmit its frame with probability 1, meaning that the node will transmit its frame with absolute certainty without hesitation.
	- If there is a collision, then the node will wait a random amount of time and then transmit again.

![[inslots.gif]]
- Nodes have numbers on a LAN from 1 to n. Someone transmits during transmission period. In contention period, you give the other nodes a chance to transmit.
	- If 1 is successful, it cannot immediately transmit until other have a chance during contention period.
	- Nodes get to transmit in a Round-Robin fashion. 
- There is a guaranteed upper bound, although not predictable as you do not know when other nodes want to transmit.
- 1-persistant CSMA has the highest chance of collision as two or more nodes may attempt to transmit at the same time, when the channel is idle, without any hesitation.

*Non-persistent and p-persistent CSMA:*

 - The better solution is to include randomness to our protocols, which will result in less collisions. Persistence is a value in which we can vary transmission times.
 - **Non-persistent:** When a node has a frame to transmit, it senses whether the medium is idle or busy. 
	 - If idle, the node transmits its information immediately. If the medium is busy, it waits a random time and repeats the logical cycle again.
	 - Reduces the chance of collision as there is a lower chance the nodes will wait the same amount of time until the network is idle. 
	 - However, it reduces the efficiency of the network as the shared medium may be idle when there are nodes waiting to transmit.
- **P-persistent:** This method is employed if the continuous time of the shared channel is divided into time intervals called slots, with each slot equal to or greater then the maximum propagation time.
	- When a node has a frame to transmit, it continuously senses the medium or idle or busy state. Once the node detects the medium as idle, the following occurs:
		- The node transmits with probability p.
		- The node waits for the beginning for the next time slot with probability 1 - p and checks medium again. If idle, it goes to the first step. If busy, it pretends a collision has occurred and applies the back-off procedure.

![[Pasted image 20240403110305.png]]

**IEEE-802.x LAN standards - The Ethernet System:**

- Generally, we do not use persistence as most LANs want to use the cheapest available option.
- The DLL and the Physical Layer are typically built into the same Network Interface Card where checksums can be sent directly alongside the data signals across the network.
- Ethernet is apart of the IEEE-802 family of protocols for LANs (802.3), which uses a 1-persistence CSMA with Collision Detection method.
- The same protocol has stayed from 1970 till today.

**Physical Properties:**

![[Pasted image 20240403112542.png]]

**Ethernet's contention algorithm:**

- Regardless of whether we have a single cable or a hub arrangement, there will still be collisions.
- Mitigating collisions can be achieved through strategic topology adjustments, ensuring that pairs of nodes communicate over dedicated wires or cables that no one else uses, thus minimizing shared communication pathways.
	- However, even then, there is still a very small chance that these two nodes will have a collision.
- Thus, we must have a mechanism to deal with collisions.

*Binary exponential back-off:*

- Before transmitting, every station listens to the network medium. If it detects idle, it begins transmitting its frame.
- If two or more stations transmit simultaneously and their signals collide, a collision is detected. When a station detects collision, it immediately stops transmitting.
- Each colliding station initiates a back-off process where they randomly select a waiting period before reattempting to transmit.
	- After the first collision, each station selects a back-off time between the range of the 0 and 1 slot. The slot time for ethernet is typically 51.2 microseconds, so retransmission will occur anytime between these two slots. 
	- If a second collision occurs, the back-off time is chosen from the range of 0-3 slot times.
	- For subsequent collisions, the back-off time range increases exponentially. Specifically, after the $i$th collision, the backoff time is randomly selected from the
	 $0$ to $2^i-1$ slot times. 
- Maximum collisions is 10 collisions (1023 back-offs) after which the station stays at 1023 slots for 6 more times. After 16 collisions, the station reports back to the network layer that the connection is severely congested.
- This procedure can occur for any number of nodes, but as the number of nodes increase, there will be a greater chance of two of these nodes colliding due to the smaller space between ranges.

**Ethernet Addressing Schemes:**

- There are multiple devices connected on a shared medium, and every node hears what everyone transmits.
- Every message will be transmitted with a header that states who the message was for via addresses.
- A message can be addressed to individual devices, all devices or refer to a group of the devices via a single address.

*Wired ethernet header structure:*

- **Preamble (62 bit long):** The preamble is simply an alternating sequence of 0 and 1s, up to 62 bits, followed by 11 (Standard frame delimiter).
	- The reason for this is that any other device previously unconnected to the network knows that this sequence is a preamble through the alternating sequence.
- **Destination and Source Address (6 bytes each)**: Destination address will come first so the node can immediately see if the message is meant for them or not.
	- If all 48 destination bits are set to 1, then the message is a *broadcast* message meant for everyone on the network.
	- Bit 47 in the destination indicates the addressing domain (0 for individual address or a specific node on the network and 1 for multicast address, indicating the address is meant for a group of nodes)
	- Bit 46 indicates whether the address is for the current LAN or for a more global station on another LAN.
- **Length (2 bytes):** Length of the payload (value up to 64000).
- **Data (46-1500 bytes)**: The minimum size of 46 bytes is mandated to ensure that the transmitting node keeps sending data long enough for its transmission to be detectable by other nodes on the network, even in the event of a collision. 
	- This is desirable so that if a collision does occur, all nodes are notified and can act accordingly.
	- If the data is sent in small, shorter bursts, we might not be able to hear the collision.
- **CRC (4 bytes):** CRC32 checksum is encapsulated on the end of the packet.
- It is the network layers job to route all traffic from one ethernet segment to another ethernet segment.
- There are 2<sup>46</sup> possible different ethernet cards. However, these numbers only have to be unique on the single ethernet segment they are connected to.

**Packet transport mechanisms:****

- **Truncated packet filtering:** If a node detects a collision partway through its own transmission, it immediately stops transmitting.
	- This means that the resulting packets will be truncated packets. These will be ignored by hardware as these must've occurred during collision.
- **Collision consensus enforcement:** Whenever a station detects a collision of its own transmission, it deliberately *jams* the ether to ensure that other colliding stations hear the collision as quickly as possible and then to stop transmitting.

