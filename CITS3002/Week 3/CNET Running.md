![[tracetimeline.png]]

- Each of the items in bold indicate when the application layer/physical layer is ready to push/pull messages to internal protocols.
	- The indented lines are the event handler's actions in handling the event. 
	- The simulator is unaware of the processing that you perform on a frame of data. All that it knows is when to push and pull a data stream based on the event handlers that you provided to it.
	- This means that you would have to act upon the protocol in your own way.
- The timeline is a representation of the events that have occurred between the sender and receiver.
	- Dashed lines between nodes indicate that a frame was corrupted. Data loss is seen as line going halfway to the node and then disappearing it.
- Clicking on a node will show all `printf()` functions. All options are event-driven rather then sequential.
- Running the simulator will produce a file called `example.cnet`.