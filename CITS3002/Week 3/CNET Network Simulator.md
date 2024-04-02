**Introduction:**

- Enables experimentation with DLL, network layer and transport layer networking protocols consisting of WAN, LAN and WLAN links.
- Cnet provides the application and physical layers and user-written protocols are required to "fill in" any necessary internal layers and to overcome corrupted and lost frames that Cnets physical layer randomly introduces.
- Nodes can be selected with the mouse to display its terminal output, generated through `printf()`. 
- By default, cnet executes the same code within every network node and the only way for nodes to communicate is via the Physical Layer.

*Setup:*

- **Topology file:** Defines the nodes, lengths, node and link attributes and identifies location of the nodes.
	- More hosts can be added to create more nodes in the network, creating more links between machines.
- **Source file:** C program set especially for the use case in event-driven programming.
	- C program is compiled into a shared object which replicates variables for different nodes, scheduling the execution of each of the nodes.
	- We need to include `<cnet.h>`.

*Syntax of C Code:*

- `Cnet` is an event-driven programming language, meaning that the code is executed when a certain event occurs. The event handler is a function that handles specific events, such as timer expiration, packet arrivals etc.
	- In the case of `EVENT_HANDLER(reboot_node)`, execution commences at the event handler for rebooting the node. When node reboots, function is executed to completion, returns and then the event ends.
	- Another frequent event is when a timer expires, or `timeouts`. This is triggered whenever a timer started by the program expires. Typically, when a timeout occurs, the program will re-transmit whatever it was previously attempting to transmit. This code will typically be in the pseudo code form:
```
	If something doesn't finish in 2 seconds time:
		execute event_handler(timeouts)
```

- When event corresponding to the event handler, for example `EV_APPLICATION_READY`, is ready to begin, the layer below will work to pull messages from the ready-event layer. 
	- So in this case, the presentation layer would begin to pull messages from the application layer.
- Rather then `main()` calling sequence of functions, the event reboot_node works to register our interests indicated in the events and when events occur, the simulator calls these event handlers to handle them.
- There are ways to pause and single-step the simulation while it is currently running.

*Compiling code:*

- When we run the cnet command, the code is not compiled by you but rather, the topology file compiles the code, which then compiles the code for you. This will show a window running the cnet network simulator. 
- The simulator beginning signifies the event `reboot_node`. However, nothing is running as there is no information yet that has been transferred across the simulated network.

**CNET simulation model:**

- cnet supports a model of networking in which nodes are either hosts, routers, mobiles or access points. The different nodes have different properties:
	- Host and mobile nodes have application layers which generates messages to deliver to other hosts and mobiles.
- Nodes are connected by a variety of networking links, including WAN, LAN or WLAN.

![[layers.png]]

- All nodes have a Physical layer and hosts/mobiles have an application layer.
- Nodes initially do not have much knowledge about the network. All inter-node communication necessary to learn this information must traverse the Physical layer.
- Mobile nodes can move around the map whilst the other forms are stationary.
- Links are numbered within each node from 0 to the number of physical links the node has.
	- The first real link is number 1 and every node will have a link number 1. At runtime, the link elements can be accessed through `linkinfo[0]`, `linkinfo[1]`, `linkinfo[2]`.

*Protocol Layers:*

- cnet protocols are written from the POV of each node, writing code as if we are inside the kernel of each node.
- We have the ability to write all interior protocols between the Application and Physical layers.
	- A network of only two nodes should only have a single layer between Application and Physical: the DLL layer. 
	- Network between more then two nodes should have network layer as well as DLL to manage packet routing and message fragmentation.
	- Network in which the nodes may crash will require session layer to ensure that old packets that have been floating around the network between crashes are not considered part of the actual communication.
- Protocols are written using an event-driven programming style, as if we were writing the protocols as part of an OS's kernel. Our OS must act as efficiently as possible before handing control to cnet for it to schedule other activities.

*Lifetime of a message:*

- A message is first generated in the Application Layer of a node (eg `node0`) for the delivery to another node (eg `node1`). Application Layer informs our interior protocol layers, via an event, that a message requires delivery. 
	- The interior protocols do not care about the content of the message and treats it as a byte stream. 
	- We must use the physical layer, accessible via each nodes link number 1, to deliver the message.

*Procedure for transmission of message:*

- From the POV of the message, this is what happens (error free):
1. `node0`'s application layer generates message and announces it with the EV_APPLICATIONREADY event.
2. `node0`'s interior protocol calls CNET_read_application to read (and own) the messae and to learn its length and intended destination
3. `node0`'s interior protocol does whatever it wants with the message, typically including it in the payload of a frame or packet structure for transmission.
4. `node0` calls CNET_write_physical to transmit a block of bytes via its link 1.
5. After transmission delay, the bytes arruve at `node1`'s physical layer via link 1.
6. `node1`'s physical layer announces the arrival via EV_PHYSICALREADY event.
7. `node1`'s interior protocol calls CNET_read_physcial to read (and now own) the bytes.
8. `node1`'s interior protocol does whatever it wants with the message, typically copying it from the payload.
9. `node1`'s interior protocol finally calls CNET_write_application to deliver the message to the application layer.
- Interior protocols must detect and manage errors in the network itself, because errors corrupt and lose frames.

*Simulation attributes:*

![[Pasted image 20240402104917.png]]

**Event driven programming style:**

- cnet employs an event driven style of programming, meaning that execution proceeds when cnet informs protocols that an event of interest has occurred. Protocols are expected to respond to these events.
- Events occur when:
	- A node reboots
	- Application Layer has a message for delivery
	- Physical Layer receives a frame on a link
	- A timer expires
	- A debugging button is selected
	- A node is (politely) shutdown.
- No events are delivered if a node pauses, crashes or suffers a hardware failure.
- NOTE: It is not interrupt driven, meaning that the start of an event won't block another event.
- Event handling functions must first be registered to receive incoming events with a call to `CNET_set_handler(type_of_event, unique_timer, user_specified_data)`.

*Reboot Node:*

- Each node is initially rebooted by its reboot_node() function. This is the only function that you must provide and is assumed to have the name reboot_node().
- Purpose of reboot_node() is to give protocols a chance to allocate any necessary dynamic memory, initialize variables, inform cnet of which events protocols are interested in and which handlers that cnet should call when these events occur.
- Enabling the application layer will then begin the process of generating messages.

*Example of cnet event handler:*

- The standard cnet header file (`#include <cnet.h>`) must be included. 
- Execution commences at the reboot_node() function, akin to main(). 
- Within reboot_node(), two event handlers are registered using CNET_set_handler():
	- `new_message()` for the `EV_APPLICATIONREADY` which works to execute when the Application Layer has a message ready to deliver to another host.
	- `frame_arrived()` for the `EV_PHYSICALREADY` when the physical layer has a message ready to be received by the host.

```
#include <cnet.h>

EVENT_HANDLER(new_message){

	success = CNET_read_application();

}

EVENT_HANDLER(frame_arrived){

	success = CNET_read_physical();

}

EVENT_HANDLER(reboot_node){

	success = CNET_set_handler(EV_APPLICATIONREADY, new_message, 0);
	success = CNET_set_handler(EV_PHYSICALREADY, frame_aarived, 0);

}
```

- `new_message()` and `frame_arrived()` are registered by reboot mode and are executed when `EV_APPLICATIONREADY` and `EV_PHYSICALREADY` occur respectively.
	- `CNET_read_application()` and `CNET_read_physical()` serve as interfaces between the protocol implementation and the internal user-defined protocols within cnet, giving the internal protocols the ownership of these data streams.

**Providing command-line parameters when a node reboots:**

- As a special case, the third (`data`) parameter in each nodes handler for the `reboot_node()` provides command line arguments when that node reboots. The value passed in `data` is a pointer to a NULL-terminated vector of character strings.

![[Pasted image 20240402122944.png]]

- The optional command-line arguments for the `EV_REBOOT` handler may either be passed on cnet's own command-line or provided 

*Timers:*

- cnet supports 10 timer event queues providing a call-back mechanism for the protocol code, meaning that a function can be specified to be called when the timer expires.

```
CnerTimerID timer1;
timer1 = CNET_start_timer(`EV_TIMER1`, (CnetTime)5000000, timeouts);
//First parameter shows the event that will be called
//Second parameter is the time it will take
//Third parameter is the function that will be called.
```

- Timer has significance for functions handling timer events; all other handlers will simply receive the special `NULLTIMER`. 
- When a timer expires, the event handler for the corresponding event is invoked with the event and the unique timer as parameters.

*Determining physical link sever/reconnection:*

- As a special case, the third parameter can indicate which physical link has been severed or reconnected.

![[Pasted image 20240402133323.png]]

