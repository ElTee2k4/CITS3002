**Introduction:**

- Enables experimentation with DLL, network layer and transport layer networking protocols consisting of WAN, LAN and WLAN links.
- Cnet provides the application and physical layers and user-written protocols are required to "fill in" any necessary internal layers and to overcome corrupted and lost frames that Cnets physical layer randomly introduces.
- Nodes can be selected with the mouse to display its terminal output, generated through `printf()`. 
- By default, cnet executes the same code within every network node and the only way for nodes to communicate is via the Physical Layer.

*Syntax of C Code:*

- `Cnet` is an event-driven programming language, meaning that the code is executed when a certain event occurs. The event handler is a function that handles specific events, such as timer expiration, packet arrivals etc.
	- In the case of `EVENT_HANDLER(reboot_node)`, execution commences at the event handler for rebooting the node. 
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






A

- EVENT_HANDLER is the replacement for the main() function. Event handlers are functions that handle specific events, such as timer expirations, packet arrivals, etc. 
- `EVENT_HANDLER(timeouts)`: In this case, timeouts is triggered whenever event `EV_TIMER1` occurs.