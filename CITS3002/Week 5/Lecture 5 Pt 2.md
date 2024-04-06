**Network Layer Routing Algorithms:**

- Routing algorithms are a part of the network layer software that decides on which outgoing line an incoming packet should go.
	- For *virtual circuits* the route is chosen once for each session. Virtual circuit is a set-up between source and destination before any data transmission occurs.
	- For *datagrams* the route is chosen once for each packet. In a datagram approach, each packet is treated independently and can take its own unique oath through the network to reach its destination.

*Robustness:*

- Robustness refers to the ability of a routing algorithm to maintain stable and efficient communication in the face of various challenges, including changes in network topology, failures of network components and fluctuations in network traffic.
- Robustness often distinguishes between two main classes of routing algorithms - adaptive and non-adaptive.
- Robustness is significant as:
	- Once a network is running it is difficult to change the routing algorithm software
	- Network topologies often change and the better a routing algorithm can cope with changes in topology, the more robust it is.

*Two classes of routing algorithms:*

- **Non-adaptive algorithms:** Pre-compute the routes between every pair of hosts in the network before the network becomes operational.
	- Pre-computed routes are loaded into each host's routing table and are static as the routes remain fixed unless manually updated.
	- Doesn't adjust to changes in network conditions or topology.
- **Adaptive Routing Algorithms:** Dynamically adjust their route choices based on the current state of the network. There are three forms of adaptive routing:
	- *Global Algorithms:* Use information periodically collected from the whole network
	- *Local algorithms:* Use only information that's available to each individual router, such as queue lengths, congestion levels and waiting times. Routing decisions are made autonomously by each router based on its local information.
	- *Combined algorithms:* Use a combination of global and local information to make routing decisions. Strikes a balance between the centralized control of global algorithms and the distributed decision-making of local algorithms.
	- Initially, adaptive algorithms start off with very little knowledge and often defer to default link sharing. Over time, it learns more about the network its using and maintains this in a table to use over time.
- Adaptive routing choices are often referred to as dynamic routing because the routes can change in response to network connections and events.

*Flooding (non-adaptive algorithm):*

![[Pasted image 20240404113015.png]]

- `down_to_networkLayer()`: Function is called when the application layer wants to send a packet. It reads the destination, message and length, fills packet header information and sends the packet to each link in the data link layer.
- `up_to_networklayer():` Checks if the packet is an ack or packet. If data packet, it writes the message to the application layer and prepares to send ack. If ack, it prepares to send more data.
	- If the packet is not meant for the current node, it means it was received as part of flooding process. It sends the packet our again on all of its outgoing links, flooding the network with the packet.
	- Takes a lot of unnecessary paths and a lot of duplicate messages could arrive at the receiver.

**Distance Vector Routing (DVR):**

- DVR maintains a table in each router of the best known distance to each destination and the preferred link to that destination.
- Each node sends its neighbors vectors of information that the node can use to devise a table of distances travelled to each of the nodes in the network.
- The routing tables, thus, measure the time a message will take to get from sender through intermediaries to the destination.
	- The sender doesn't ask the nodes about this but rather, it is periodically informed about what remote nodes can do.

![[distancevector.jpg]]

- From the perspective of J, input is directly given to A, I, H and K about distances to each of these nodes.
	- From there, these fours nodes are able to give J information about their neighbors and how long they take to travel to each of its neighbors. 
	- J eventually collects all information and from here, is able to create a table of each nodes distance from each other, thus it being to calculate it would take for it to get to one of these nodes.
	- Eventually, it gets the best link it should take to get to a node and create a new routing table for each node.
- The algorithm is adaptive as nodes become congested. Congestion occurs when multiple packets need to be sent to a link and it needs to be ordered correctly.
	- DLLs with stop-and-wait may cause NLs to have multiple pieces of data it needs to give below and above. Thus large queues for each link may develop, thus creating congestion. These queues will grow and shrink over time. 

Count-to-infinity problem:

- A fundamental issue that arises in distance vector routing algorithms that require all nodes to be modified when uncertainty is low.

https://www.youtube.com/watch?v=f2ic7kVnhrs&ab_channel=TheNerds

![[Pasted image 20240404125036.png]]

- Consider the following network. Each node has a routing table with appropriate timing. However, if C gets disconnected from B, B will notice this and delete its current table value for C. 
	- It looks at its neighbor and sees that A will connect to B with a cost of three. Thus it changes its distance value to 4 via A (1 from B to A and apparently 3 to get to C) and sends the packet to A.
	- A notices that the value for C has changed and sees that B will have a distance now of 4 to get to C. A changes its value to 5 and sends the packet to B. This continuously occurs.

![[Pasted image 20240404125508.png]]

*Link State Routing:***

- *Now, nodes only inform neighbors of changes to the local topology. The nodes only transmit current information that the node is sure of between two.
- Link-state routing algorithms distribute information about the entire network to all routers in the network.
	- Each router discovers it neighbors and the cost of links to those neighbors. It builds a local map of the network based on this information.
	- Once a router knows its local topology, it creates a packet called a Link-State Announcement containing its identity, neighbors and link costs. The router sends this packet to all other routers in the network.
	- Routers receive LSAs from neighbors and update their local topology databases, if a router detects a change, it creates a new LSA and floods it to all routers.
	- Each router then is able to calculate the shortest path to every other router and based on this information, construct a routing table.

**Congestion and Flow-Control in the Network Layer:**

- If nodes act on old outdated advice, there will be errors in terms of where the data ends up.
	- Various subnets of the network will end up congested, meaning that messages get placed on queues before they can be sent.
	- Once on queue, we have to wait for everything else to go before itself can be sent. The sender doesn't receive an ack back and may decide to retransmit the message again (stop-and-wait).
	- However, this would just make the network more congested.
- *Congestion control* is concerned with ensuring that the subnet can carry the offered traffic whilst *flow control* is concerned with end-to-end control. Both have the aim to reduce the offered traffic entering the network when the load is already high.
- **Open loop** control attempts to prevent congestion in the first place rather then correcting it. Virtual circuit systems will perform open loop control by reallocating buffer space for each virtual circuit.
- **Closed loop** control maintains a feedback loop of three stages:
	- monitoring of local subnet to detect where congestion occurs.
	- passing information to where corrective action may be taken.
	- adjustment of local operation to correct problem.

*End-to-end flow control:*

- Sender typically requests the allocation of buffer space and 'time' in the intermediate nodes and the receiver.

![[end2end.gif]]

- Every destination will know what is coming in the next short interval. Its similar to stop and wait but instead of sending one singular message, you send the messages in bursts along with a request for the next set of messages.
- If a nack comes back, then the source will not send the burst.

*Load shedding:*

- Technique used in computer networking to manage network congestion and resource limitations.
- Involves deliberately discarding packets when there is insufficient router memory or network resources to handle all incoming traffic. However, this introduces an unreliable service as some packets may be discarded to priorities others.
	- Should ensure fair access to the network for all users and respect their rights.
	- This seems strange at first as the lower layers (DLL and Physical Layer) work hard to resolve errors and corruption. However, this doesn't mean that upper layers are perfect in their data transmission.

*Strategies for Discarding Packets:*

- Consider the priority of each packet, encouraging senders to send low-priority packets.
- Don't discards ACKs but throw out data instead. This is because the sender will not receive ack back and thus, send the data again.
	- ACKS indicate if the network is making progress.
- Make packets carry a hop counter, don't discard a data packer if it has travelled a long way.
	- If data hasn't travelled far from the source, we discard this as it would be easy to retransmit again one day.
- Examine the type of traffic being carried. 
	- If we know what the data was, we can discard some forms of data. Audio streaming can deal with one of these pieces being missed without effecting the quality too much. 
	- However, file transfer should not be thrown away as this is a critical piece of information that needs to be saved.
	- Network layer knows the headers it is carrying but shouldn't know the payload. Internet model, however, doesn't completely follow the OSI model. In the internet model, the network layer knows of the packet format of what its carrying. Thus, this discarding method would work.

**Methods to avoid congestion:**

- <u>Leaky bucket algorithm:</u> A bucket is symbolic for a buffer for incoming data packets. The hole at the bottom dictates the rate at which data packets can leak out. The rate is fixed and represents the maximum rate at which data can be transmitted from the source.
	- Data packets leak out of the bucket at a certain fixed rate to leave a host per unit time, providing a single sever queueing model with a constant service time. 
	- Any data that cannot fit in the bucket will be discarded.
- <u>Token Bucket algorithm:</u> Token bucket algorithm provided a bucket of permit tokens (before any packet is transmitted, a token must be consumed). Tokens 'drip' into the bucket at a fixed rate; if the bucket overflows with tokens they are simply discarded.
	- Packets are placed into an infinite queue and packets may enter the subnet whenever a token is available. Else, they must wait.
- Tries to benefit users who are not generating congestion. If there is a large file transfer, there might be a big burst of traffic and then rate at which you can send slows down.
	- This is similar to OS scheduling where process sends as fast as possible and then is forced to stop whilst other processes get a chance. We regulate the rate at which traffic leaves the buffer.

https://www.youtube.com/shorts/rQWR_D3aoG8

- A refill time is seen where the tokens refill at a fixed rate and once some tokens refill and a new request comes and immediately takes this spot.