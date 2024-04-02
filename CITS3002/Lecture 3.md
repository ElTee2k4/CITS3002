**Data Link Layer:**

- Provides the following services between the Physical and Networking layers:
	- Bundling and unbundling groups of bits into frames for the Physical Layer.
	- Throttling the flow of frames between sender and receiver
	- Detecting and correcting 'higher-level' transmission errors, such as the sequencing of packets
- The Data Link Layer in the sender believes it is talking directly to the DLL in the receiver. Because of a computer's structure, a DLL is needed between every link between devices. This means that if a computer has two links to two other devices, it will have two DLLs which manage the two respective layers.
	- These will generally be the same technology running the two, meaning we might be able to run the same code. But because they run at different speeds, they need their own different variables.
- The DLL doesn't care about the format of the physical layer, whether its network or wired, meaning that it can work independent of the physical medium it is on.

*Ack basics:*

- An ack is an acknowledgement that the user received a packet that was not corrupted or was deemed to be correct.
- If the frame received by the receiver is incorrect, it will either not ack the frame or will will send back a nack, or a negative acknowledgement indicating that the frame was received but not recognised.
	- If I don't know whats there, the DLL just sends a nack and doesn't try to guess what it was.
- The ack can be really small, such as one bit or two bits for the nack as well. However, we tend to deal in bytes.

**DLL Complexity:**

- A query flows from source to destination and the destination replies and then sends larger file back which the source then acks. 
- This introduces the need for a state, which is maintained at both ends which indicates what it has sent recently, what it is expecting back and other indicators of activity.
	- At the receiver, this could be what it is expecting to receive and what it has just previously received.

*Simplex connectionless:* 

- Only sends data in one way and doesn't establish a connection for data transfer.
- Sender sends its frames without waiting for the receiver to ack them. No attempt is made to detect or re-transmit lost frames.
	- Most modern technologies (ethernet) use this method and leave error resolution to higher layers.
- Also known as <u>unacknowledged connectionless service</u>. 

*High-duplex connectionless:*

- Each frame sent is acked. Frames which are lost or garbled are re-transmitted if the receiver requests them (again) or after a suitable timeout.
- Known as <u>acknowledged connectionless service</u>.

*Full-duplex connection-oriented:*

- Each frame is individually numbered and is guaranteed by the DLL to be received once and only once and in the right order. The result is that the DLL presents a reliable frame *stream* to the network layer.
- Also termed as <u>acknowledged connectionless service</u>.

**Declarations of Introductory Protocols:**

- The DLL encapsulates a raw data packets into frames by adding a DLL header and trailer onto it.
	- Frame becomes `DLL Header- Encapsulated Packet - DLL Footer`.
- Suitable library features work to send a frame (`to_physical_layer`) and receive a frame (`from_network_layer`) . They compute, append or check the checksum value.
- Receiver is waiting in a loop `wait_for_event(&event)` and will only return when an event has occurred. When returned, the event variable tells the receiver exactly what event occurred.
	- If frame arrives undamaged, `event = frame_arrival`.

*Frame structure:*

```
#define MAX_DATA_SIZE 1000    //The upper bound of the frame(how large it can be)

typedef struct {

	int len; //Length of the payload
	char data[MAX_DATA_SIZE]; Actual payload
	
} FRAME;

#define FRAME_HEADER_SIZE (sizeof(frame) - sizeof(FRAME.data));

#define FRAME_SIZE(f) (FRAME_HEADER_SIZE + f.len)

```

1. We give an upper bounds on how much data should be sent. We cannot send all the information in one go as we want other connections to have a chance.
	- We do not always use up the full size of the `MAX_DATA_SIZE`.
2. A frame struct is created to support an array of bytes (payload) and aggregate the data together:
	- The length of the payload is assigned to keep track of how many bytes is necessary for each transmission.
	- The actual array of bytes is inputted into the struct.
- The next two steps are defined at the front as it is defining the head of the frame, and thus is at the head of the code.
3. This macro calculates the size of the frame excluding the payload data, giving the size of the frame header.
4. Calculate the total size of the frame given frame `f`. Adds the `FRAME_HEADER_SIZE` to `f.len()`.
- This abstraction of code makes it so that the anything added to the frame will be added to the header. 

**Unacknowledged simplex communication-less transmission system:**

- There is an assumption that nothing goes wrong, there is an infinite amount of data to send and the receiver can receive infinite amounts of data in return.
	- However, we cannot assume it is infinite speed and instead, assume that they ill block if they cannot immediately satisfy the protocol.
- As DLL executes, it will pull data from above, possibly add header and fields, and then push it down to the layer below.

![[Screenshot from 2024-04-01 13-04-55.png]]

- The sender process:
	- `While(true)` assumes that nothing will go wrong and that we are running as fast as possible.
	- It isn't infinite speed and READ_NETWORK_LAYER and WRITE_PHYSICAL_LAYER will block if they cannot immediately satisfy what is requested of them.
	- DLL will wait if nothing is available to pull down from the network layer or pushed down to the physical layer. 
- The receiver process:
	- Simply reads data from the physical layer and writes data to the network layer.
- Parameters for each of the functions allow the other end to deposit values for the other layer.
	- The len, frame and link variables are pushed across the layers.
- Link variable assumes that in the future, there will be multiple links. However, for now, there will always be 1 link and link will always be set to 1.

**Half-duplex Stop and Wait Protocol:**

- We do not want the sender to overrun the receiver. In other words, we don't want to much data to arrive quickly.
	- This may cause inadvertent overwrites of data.
- Thus, the receiver now controls the amount of data which will be sent. Now, sender blocks its processes and waits for the receiver to ack its frame that it sent.
- Once the ack arrives, the sender can send the next frame (still assuming it has infinite frames to send).

![[Screenshot from 2024-04-01 13-34-23.png]]

- A faster sender, thus, will not upset a slow receiver.

**Detecting Frame Corruption:**

- Frames will be corrupted at times during transmission, introducing the need for checksum. 

![[Screenshot from 2024-04-01 13-38-37.png]]

- The structure of the actual frame is now changed. The payload is still an array at the end of the frame but now the FRAMETYPE is established (ack, nack and datatype).
	- A checksum will now be added as well.

![[Screenshot from 2024-04-01 13-40-06.png]]
- The sender now works to calculate the checksum of what it is sending and this is sent as well. This is sent as well as the checksum of the message (in the form of redundant bits).
- Arriving header receives the header and its checksum and calculates the checksum again.
	- NOTE: The checksum is of the entire frame, including the header, and OVERWRITES a checksum field in the header. The checksum initially is zero, but as long as the sender and receiver agree on the initial value, the overwrite shouldn't matter as the check sums would be the same anyway. 
- We transmit the frame as many times as needed to get it through accurately. We now have a nested infinite loop within another infinite loop. Eventually, when the traffic is all freed, we will break out of the first infinite loop and go into the other one.
- The data frame will be long whilst the ack frame will be much shorter. Ack frame doesn't have any data associated with it, no payload or anything. The ack frame just consists of the frame header.
	- The length, type and checksum are all inclusive in this header, which is longer then one byte long.

![[Screenshot from 2024-04-01 17-34-33.png]]

- The receiver reads as much as it can, remembers the checksum that arrived. Calculate the checksum of what arrived. If the arrived checksum is the same as the one that we calculated, then we can be sure that the piece of data is correct.
	- Or else, it will say that it got something but it was wrong, resulting in the transmission of a nack.

**Detecting Frame Loss:**

- Loss happens when we try to push a signal too far and thus, the signal degrades over time (attenuation).
	- If the ratio of the signal is too weak in comparison to the ratio of background noise, then this is where loss occurs.
- There is still the possibility that errors on the channel cause the frames to be lost entirely. If the `NACK` and `ACK` got lost, the sender would be waiting forever.
- Thus, we introduce a time in which the ack should be received in. If it is not received in this time slot, then the frame is sent again.
- The sender assumes the data is loss and if it doesn't receive the ack in time, it will send the frame again.
- However, this includes another problem, if the ack was being sent back to the sender and was lost itself, then the sender will send the frame again and the receiver will have two duplicate frames.
- Representation of the problem now needs to manage time as well as the transformation.
	- In the future, we have to inform our self that the time has elapsed. However, if the frame comes in, we do not want the future to occur.

![[Screenshot from 2024-04-01 17-52-21.png]]
![[Screenshot from 2024-04-01 18-10-12.png]]`

- `network_layer_ready`: Function is called when the network layer is ready to send data. It reads data from the network layer, constructs a frame, calculates the checksum and then writes the frame to the physical layer. It starts a timer to handle acks and re transmissions.
- `physical_layer_ready`: Function called when a frame arrives at the physical layer. It reads the frame and checks if it's an ack. If it is, it starts the network layer. If not, it re-sends the frame and starts the timer again. 
-  `timer_has_expired`: Function called when a timer expires, indicating that an ack wasn't received. It re-sends the frame by re-transmitting the frame over the same communication link.
- In this case, the same code will be run on both sender and receiver to utilise full-duplex communication where both devices act as senders and receivers.

**Using simulation to develop network protocols:**

- We need to look at simulation in order to look at different aspects of computer networks within the same framework.
	- We can change time, capacities of lengths, the topology that nodes are connected in and error rates.

*cnet networking simulator:*

- Topology file defines the nodes and lengths and connectivity of the file and says where the code is.