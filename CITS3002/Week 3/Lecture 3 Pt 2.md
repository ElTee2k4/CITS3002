*Piggybacking:*

- Piggybacking is a network protocol efficiency technique in which an ack is not sent directly to the sender but rather, sent with an outgoing data frame back to the origin.
	- This no longer assumes that there is a designated sender and receiver but rather, a two way communication system is established.
	- Better use of channel bandwidth as ack field only needs a few bits in comparison to an entire frame being sent back for acks beforehand.
- Communication systems must decide how long to wait for an opportunity  to piggyback an ack onto an outgoing frame.
	- DLL uses a fixed waiting time (different to sender timeout period). If the ack is ready to be piggybacked during this time, it will be. If not, then an individual ack frame will be sent.

**Sliding Window Basics:**

- Each frame in this protocol is numbered. These numbers are known as the sequence number (seq_num).
- Both sender and reciever maintain "windows" represented by ranges of seq_nums. This window is a sequence of acceptable seq_nums that the sender is allowed to submit and the receiver is allowed to accept.
- **Sender Window:** Frames that have been sent but are not yet acked.
	- When a frame has been sent, the first ack is set to the value 0 and waits until it receives the ack. When it does, the 0 seq_num is removed from the window and the lowerbound is set to 1, which the sender expects next.
- **Receiver Window:** Defines which frames is can accept:
	- Frames within the window are stored in the receivers buffer. The receiver first expects frame 0, meaning the lower bound is 0 and the upper bound is 1.
	- Frame 0 arrives to the receiver and is accepted. The window rotates and the lower and upper bounds increment to the right. The receiver is now expecting frame 1.
	- If a frame arrives out of order, it is discarded and an ack is sent for the highest in-order seq_num received so far. The sender then falls back to the numbered frame and sends it again.

*One bit sliding window protocol:*

- Uses stop and wait since the sender transmits a frame and waits for the ack before sending the next frame.
- Sender and receiver window size are both 1. Thus, the sender can only send one frame and the receiver can only receive one frame. Thus, the upper bounds will always be in 1 bit increments.
- The sender will track the next frame to send whilst the receiver tracks the frame it is expecting.
	- Sender is idle. Its window at this point points to nothing. When the sender is ready, it sends one frame to the receiver and waits for the ack. Its upper bound increases by 1, window now points to 0.
	- Receiver is idle, and its window is pointed to 0, waiting for frame 0 to arrive. Once frame 0 arrives, the receiver sends back the ack and rotates both the upper and lower bounds up by an increment of 1. Now, the window points to 1.
		- Within this step, the receiver will check for duplicates. If the receiver detects a duplicate, it will typically ignore the duplicate frame and take no action beyond that.
	- Sender receives the ack, meaning that its lower bound will increment by one and point to nothing again. As soon as it sends frame 1, it will point to 1, suggesting that it is waiting for frame 1 to be acked.


![[Pasted image 20240405180459.png]]

<u>Frame pipelining:</u>

- The wire connecting two computers acts similarly to a pipe. If the size of the pipe (in terms of length) corresponds to the size of bytes of the frame, then the frame will fill the entire pipe. 
- This means we have to wait for the frame to *flow* through the pipe to continue moving forward.
- If the pipe is very long, then we can transmit much more data with *gaps* between them. We will be most efficient when the pipe is completely full.

*Go back n protocol:*

- The N of this protocol name refers to the sender window size. It means that N frames can be sent before expecting an ack from the receiver.
	- If it is go-back-5, five frames can be sent by the sender before expective an ack.
- Uses the concept of protocol pipelining. The sender can send multiple frames before receiving the ack for the first frame.
	- If it is go-back-5, then 5 frames can be sent before expecting the ack for the first frame.
- Number of frames that can be sent depends on the window size of the sender.
- If the ack of a frame is not received within an agreed time period, **all frames in the current window are transmitted.**
- Size of the sending window determines the sequence number of the outbound frames.
	- For example, if the sending window size if 4, then the sequence numbers will be 0,1,2,3,0,1,2,3,0,1 and so on.

<u>Example:</u>

- The window size is four and as such, frames 0,1,2 and 3 are sent to the receiver. The receiver now sends an ack for frame 0.
- Thus, the lower bounds and upper bounds of the window will slide up. The window now consists of 1,2,3 and 4. The ack for 2 is received by the sender, and now the window slides again and the window now consists of 2,3,4 and 5. Sender sends 5 to the receiver.
- However, receiver doesn't ack 2, because the frame is lost or corrupted. The timeout ran out. The sender (having held the current window size in the buffers) can no go back to 2 and retransmit the entire window (2,3,4 and 5) again.
	- This illustrates go-back-n, where N refers to the window size. We have to go back 4 spaces, back to the first frame where the issue was first encountered.
- Throughout this process, the receiver discards any frames received after the missing frame to maintain the integrity of the data stream. It also removes any duplicate frames.

*Disadvantages:*

- Go-back-n is inefficient due to the potential for retransmitting multiple correct frames when an error has occurred. The protocol's efficiency is highly dependent on the error rate and the RTT between sender and receiver.
	- It is a waste of bandwidth as the sender must retransmit all frames from the lost or corrupted ones onwards, not just the problematic frame.
- Receiver window is always set to 1, meaning that the receiver can only accept one frame at a time and will always look for the next expected frame in the sequence.

*Selective Repeat ARQ:*

- In this protocol, only the erroneous frames are retransmitted. Correct frames are received and buffered.
- Receiver has to keep track of the sequence numbers and it stores received frames in its memory buffer. It selectively issues NACKs solely for frames that are either missing or damaged.
	- If any seq_num is missing, the receiver will either send a NACK or no ack as an indication that the frame was lost.

<u>Example:</u>

- The window size is four and as such, frames 0,1,2 and 3 are sent to the receiver. The receiver now sends an ack for frame 0.
- Thus, the lower bounds and upper bounds of the window will slide up. The window now consists of 1,2,3 and 4. The ack for 2 is received by the sender, and now the window slides again and the window now consists of 2,3,4 and 5. Sender sends 5 to the receiver (same as go-back-n).
- Receiver doesn't receive the ack for frame 2 as it was lost. The sender will go back to frame number 2, send this (as it is still within the sliding window), and then go back to 5 and wait for the next ack to send 6.
- Thus, it doesn't send the entire window again and only sends the erroneous packets.

*Disadvantages:*

- Lost acknowledgements make the sender believe that the frames are lost when they are not.
- A large window size (multiple frames) with a limited sequence number space (0-7) creates ambiguity. If a frame's ack (such as 0) is lost, that frame may be transmitted as part of the error recovery and then transmitted again when the sender returns to its normal sending procedure.
	- Thus, a window size half the size of the maximum sequence number helps prevent this issue. This reduces the chance of significant overlap between outstanding frames and newly sent frames, even if some acks are lost.
