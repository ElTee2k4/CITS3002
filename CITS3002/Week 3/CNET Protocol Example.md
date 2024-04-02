**Hello World example:****

```
#include <cnet.h>

void reboot_node(CnetEvent ev, CnetTimerID timer, CnetData data){

	printf("Hello World\n");

}
```

- The source code must include the standard cnet header file.
- Although the main(), exit() and return are absent, the protocol executes seemlessly. 
	- Every node that implements this protocol undergoes a reboot when CNET triggers its event-handler for the `EV_REBOOT` event.
	- The handler receives parameters: `ev = EV_REBOOT`, `timer=NULLTIMER`, `data=0`.
- The output will be printed to the standard output window for each node. The handler concludes by returning control to its invoker, which is the CNET internal scheduler.

*EVENT_HANDLER macro:*

- To make protocols easier to read, we often use the EVENT_HANDLER C macro, defined in the `<cnet.h>` header file.

```
EVENT_HANDLER(reboot_node){

	printf("Hello World\n")

}
```

*Timers:*

- 
