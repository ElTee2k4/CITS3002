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
-  Frequency hopping: Instead of transmitting on one frequency, the frequency used continuously changes for security reasons as someone cannot continuously listen to one frequency channel.

Pure ALOHA:

