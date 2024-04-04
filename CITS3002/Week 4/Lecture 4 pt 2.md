*Hubs, switches and collision domains:***

- ***Hubs:** A multi-port device where ethernet packet will be re-transmitted amongst the all other ethernet devices.
	- However, you might not want every single node to hear it and one frame monopolizes the entire medium for that period of time.
- **Switches:** Switches are devices which work to amplify a signal from one node to another.
	- They have logic within them such that an arriving frame with an address will only be retransmitted on a port that has that address associated with it.
	- This allows for simultaneous messages between nodes connected to the switch.
- **Collision Domain:** Set of potential devices receiving a frame collision.. For a switch, this will only be the port that the node is sending information to whilst for a hub, the collision domain will be all devices connected to the hub.

*IEE-802.11 Wireless LAN protocol:*

- There are different topologies in the wireless domain in comparison to the wired domain as there are different power levels (due to signals travelling through the air) to the point where receiving device may not be able to discern traffic noise from the background noise.
	- It will leave the router at around 25DB and arrive at the node at around -85DB. However, we don't want WiFi to travel forever as we don't want multiple WiFi domains to interfere with one another.
- Many obstacles attenuate the signal: Glass tables, wood, walling etc. In a home situation, there could be a repeater every wall and reamplify signals afro -40DB to 20DB.

*Hidden and Exposed Node Issues:*

- Not every node is going to hear every frame is going to hear every frame, even if it is a broadcast medium. Thus, standard collision detection for wired networks won't work as the nodes aren't within range of each other.
	- Node A wishes to send something to B, but it cannot hear whether B is busy. Thus, it will detect idle and send a message, creating a collision as B is sending information to C. C is known as a hidden node.
	- A is transmitting to someone else and is within range of B. B is wanting to transmit to C but detects traffic across the network and thus, waits until idle. However, C is idle but not within the range of A or B. This is known as the exposed node.
- We need a scheme that will determine who will use the media at a particular time.

**802.11 Collision Avoidance:**

- It will be impossible to avoid all collisions, especially with mobile nodes. Thus, we are going to try avoid collisions as best as possible but when a collision does occur, we will do our best to avoid it.
	- Thus, we do not employ CSMA/CD, but rather we employ CSMA/CA. 

*Process:*

- A observes an idle medium and initially sends a *Request to Send (RTS)* frame to B. RTS frame includes how long the actual data frame will be.
- When B receives the RTS, it will reply with a *Clear to Send (CTS)* frame.
	- Any other nodes hearing the RTS frame will know that the node is about to send a data packet and should not itself transmit until the indicated length/time has expired.
	- Any node hearing the CTS frame must be close enough to the receiver and thus, also not transmit for the time being.
	- Any node that hears the RTS but not the CTS knows that it is not close enough to the receiver to interfere and thus, is free to transmit.
- Those other nodes who don't receive the packet but see the CTS or RTS can look in the header of the RTS/CTS to see how long the packet will take to send. It will then wait for this amount of time until the packet is finished sending.
- B waits for an ack back and when it gets it, the channel becomes idle once again.
- In simple terms, a node requests exclusive use of the channel, the receiver says yes and all the nodes that could interfere wait until the channel is idle once again.
- If two RTS's collide, then any receivers will not be able to guess what they were and no CTS frame will be issued.
	- In this case (if not CTS is received by the sender) then the sender assumes that the packets have collided and the standard back-off routine begins.

*Implications of CSMA/CA:*

- Bandwidth utilization will go down slightly as the nodes are using part of their bandwidth to send RTS and CTS commands. We are also slowing down communication by making nodes wait for other nodes to finish sending data.
- Thus, this standard is rarely used in modern devices.

**Access-point association:****

- In wireless networks, mobile nodes typically communicate through fixed access points rather than directly with each other. 
	- APs act like bridges, relaying information between your phone and the rest of the network.
- There are two main ways a phone associates with an AP.
- **Active scanning by phone:** The phone actively searches for APs by sending out Probe frames.
	- Any AP within range with available capacity responds with a Probe Response frame, advertising its details (like network name and supported speeds).
	- Your phone picks an AP based on factors like signal strength and security settings, then sending an association request frame.
	- If the AP accepts with an association response frame, the connection is established.
- Passive Scanning by AP: AP periodically broadcasts Beacon frames announcing their presence and capabilities. Your phone receives these beacons while searching for a network.
	- If your phone finds a better AP, it can initiate a switch. It sends a disassociation frame to the current AP and an Association request frame to the new frame, following the same steps as active scanning.
- - When a node selects a new access-point, the new access point is expected to inform the old access-point using the distribution system.










- When a device only communicates through one access point, it's associated with that access point. All devices connected to the same access point form a group called a cell.
	- Association requires access point to accept request to communicate through it.
- If devices from different cells wants to talk to each other, they need multiple access points connected together.
- Access points regularly send out signals letting nearby devices know they are available. Devices can decide to connect to a new access point based on these signals.
