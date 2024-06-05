- Client/server computing is the logical extension of modular programming.
	- Modules create the responsibility for easier development. Not just a single program does all of the work but different programs with different resources work together to complete computation.
	- Programming became more robust through functions. Local variables isolated data so that other pieces of code cannot accidentally execute another programs code.
	- Libraries supported repeatability and parallel function calls were supported.
	- However, it became even easier to access function calls over networks, where we send requests to a remote CPU, which does the computation for us.
- Pieces of a program are placed in different places and each piece is now specialised. 
- Client/server computing takes this a step further by recognising that modules do not need to be executed within the same memory space.
	- The calling module becomes the client whilst the called module is the server. Servers do not have a particular dedicated role. A piece of software can be either a client or server and can only be one at one time. 
	- Server to receive query and a client if it cannot answer that query and needs to send it again with modification.
- Probably through a web browser, might want to contact server and if its not a neighbour to a station, the server becomes a client and sends the packet to another server.
	- We can be a client and server at multiple times. If the client sends off multiple queries, those responses may come back in any order. The one that comes back first is not necessarily the query you initially sent off.
- Communication between functions is much different then communication between client/server through the network difficulties. This is addressed through protocols.
	- Error corrections and manipulations occur.
	
*Asynchronous servers:*

- If server is receiving requests of TCP Port, its possible that two clients can send a query at the same time through these ports.
	- You do not have to wait for the query to be delivered to the first person before you can accept new queries.
	- It can receive queries in order from two or more clients, send queries (via UDP) to other servers and whilst waiting, get more queries from other browsers.
		- This means that the query that you sent off first may not be the answer to the query you sent of first.
- These can happen one at a time or simultaneously (multiple server, client requests or responses).

*Partitioning Client/Server Responsibilities:*

- In moving a single, monolithic application to a separated client/server application, we must address a number of issues:
	- *Functional partitioning:* Identifying if there distinct functions or tasks that can be separated and assigned to different components (client/server).
	- *Data-driven partitioning:* Determining if the data can be divided into separate sections that can be managed independently. This could involve centralising certain types of data or distributing them across multiple tasks or servers.
	- *Management of global variables:* If there are numerous global variables being accessed, it may signal a requirement to streamline the code.
	- *Timing:* Actions such as preforming timeout functions when a timer has expired has no equivalent in distributed coding. The only way to facilitate this is to communicate over the network.
	- *Implicit communication:* Hidden communication mechanisms within the application, such as shared variables, exceptions or signals, can complicate the partitioning process. 
- The way we use a large address space within a program is not possible if we make references within that address space to itself. 
	- If your using dynamic data structures, it is difficult to get that data structure to a remote site. You have to pass information through the network, meaning that the programming model has to change.

*Two-tier architecture:*

- Where a client talks directly to a server, with no intervening server. It is typically used in small environments.
	- We can add to the single server, but this results in an ineffective system as the server becomes too overwhelmed with requests.
	- It can dismiss requests once a limit is hit or we could have multiple servers where the client can pick a server. But the onus is on the client to choose a good server. The backend, however, should be handling this decision.
- **Three-tier architecture**: Introduced another server called an agent between the traditional server and the client.
	- The agent provides translation services, metering services (limiting requests to traditional server) or intelligent agent services (mapping a request to a number of different services).
	- The agent forwards a query to another server sitting further behind. Its not role of the agent to answer the query, but simply pass the query on to another server in a way that it understands.
- The query is parsed to intermediate, choosing the least busy server to send and then the traditional server may be able to send the message directly back to the client.

**Concurrency in Servers:**

- The primary motivation for providing concurrency in servers is speed. Speed is essential as we do not want the client to believe that they are not getting the response fast enough.
- Concurrency is derived from using a 'non-queuing' model of execution, either by using a new server to support each client or to provide faster, 'time-sliced' responses to each client.
	- Overlapping computation and communication. I/O to slow devices (databases too) will be the slowest part of the process, rather then computation.
- If no concurrency is available to the server, pending requests from new and existing models are either blocked or refused.
- In general, clients leave their concurrency to the OS unless the app is so large that it is the only process on the CPU.

**Iterative server:**

- A server handles a single query at once, gets the response and delivers back to the client.
- Each connection with a distinct client will be handled until its completion.

```C
//Open a socket, listen, bind an address
//....

keep_going = true;

while(keep_going) {
    new_client   = accept(mysocket, ....); //blocks incoming requests

    if(new_client == -1) {
        perror("accept");
        keep_going = false;
    }
    else {
        service(sd); //The computation, presuming the client has sent a query, 
        //parsed it and will send something back to the client

        keep_going = service(new_client);

        shutdown(new_client, SHUT_RDWR); //Now we are finished with the client and we will shutdown the socket. then the loop will repeat, will block incoming queries again.
        close(new_client);
    }
}

//When we know that we have finished, we shutdown the socket we were advertising on.
shutdown(mysocket, SHUT_RDWR);
close(mysocket);

```

- An accept call will block the server, waiting for a client. When the client comes along, it will be doing a connect call. Accept and connect pair up and the accept will return a different socket descriptor.
	- Now, the server_socket will be different to the new_client socket, which is representative of the client socket. 
	- Now one socket is an advertising socket which the client can respond to, and the other socket is used to respond directly to the client.

*Managing one process per client:*

- If the server needs to perform a lot of work or computation for each client request, spawning a new process for each client connection can be beneficial.
	- I.e, a single client may be willing to wait a couple of seconds for a single server to respond but multiple clients would not be willing to wait the combined total of all their requests which the server has to addressed sequentially.
- By assigning a separate process to handle each connection, it creates the illusion for the client that they have a dedicated server responding to their requests. 
	- Hardware will be an issue however.
- When we fork a new process, the parent data is replicated in the child. This means all descriptors are replicated, meaning that a file/socket will be opened twice. This means that its not defined which process gets the request.
	- Solution is to close off the descriptors we do not need. The parent has things shutdown immediately so that the child is the process which communicates with the client.

```C
//  Open a socket, listen, bind an address_
....
bool keep_going = true;

while(keep_going) {
    int new_client = accept(mysocket, ....);    

    if(new_client == -1) {
        perror("accept");
        keep_going = false;
    }
    else {
        switch (fork()) {
        case -1:
            keep_going = false;
            perror("fork");
            break;

        case  0: {
            extern bool service(int sd);

            close(mysocket);
            (void)service(new_client);

            shutdown(new_client, SHUT_RDWR);
            close(new_client);
            exit(EXIT_SUCCESS);
            break;
        }
        default:
            shutdown(new_client, SHUT_RDWR);
            close(new_client);
            break;
        }
    }
}
shutdown(mysocket, SHUT_RDWR);
close(mysocket);

```

*Concurrent servers:*

https://www.youtube.com/watch?v=Y6pFtgRdUts&ab_channel=JacobSorber

- Most standard I/O Functions use a blocking approach, meaning that we call a function and the function blocks the current thread until the function is complete.
- Asynchronous calls make a request, rely on an event or signal to let you know when the request completes whilst doing other things to then handle the completion.
- `select()` takes a group of file descriptors and tells you when there is something to read on any of them.
	- Rather then polling each port, we can easily look at each of the returned values and handle them accordingly.

```C
fd_set current_sockets, ready_sockets; //A set (or collection) of file descriptors

FD_ZERO(&current_sockets); //Initalises the current set
FD_SET(server_socket, current_sockets); //Add a socket to the set.
;
while(true){
	ready_sockets = current_sockets; //This is done as select is destructive and we need a backup of the current sockets

	//Tell the range of files within a set, the set of files that you want to read, write and errors. The last argument is an optional timeout value.
	if(select(FD_SETSIZE, &ready_sockets, NULL, NULL, NULL) < 0) {
		perror("SELECT ERROR");
		exit(EXIT_FAILURE);
	}//Select filters the sockets and only gives back those who are ready for writing

	for (int i = 0; i < FD_SETSIZE; i++){ //Checks through each socket to see if 
	//it is ready and set from our program
	
		if(FD_ISSET(i, &ready_sockets)) { //ISSET checks if that is a set socket
		
			if(i == server_socket){ //The socket could be the server_socket
				//This is a new connection that we can accept
				//accept.....
				FD_SET(client_socket, &current_sockets);
			}
			
			else{
				//This is a client socket, we handle the connection in whatever 
				//way we want to.
				service(i)
				FD_CLR(i, &current_sockets)
			}
		}
	}
}

```


- With a concurrent server, a single server handles many clients within the same process. This obviates the need for inter-process communication between multiple servers.
- `select()` works to inform our process which descriptors are ready for I/O. Descriptors may be to open files, devices, sockets or pipes.
	- `select` asks program to manage multiple descriptors, by asking if anyone has anything for anyone.

*Internet Supervisor Daemon:*

- Another way to keep the server machine as responsive as possible is not to have a lot of processes blocked waiting for new connections.
- `inetd` is a system daemon found in Unix-like OS which acts as a super server, listening for incoming network connections on various ports and spawning the appropriate service daemons or handling the connections itself.
	- Multi-protocol server which sits internally on a select call and when client calls, based on the port number, the piece of software can decide what to do. We don't need a single server to exclusively handle a particular protocol.
- Each network service typically runs its own daemon, waiting for incoming connections on its assigned port. However, having many idle daemons consumes memory and process slots



