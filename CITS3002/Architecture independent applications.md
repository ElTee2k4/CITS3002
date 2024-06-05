**Automation of developing networking through client-based software**

- The way we construct network-based client software, after a while it becomes all the same. A lot of socket programming is continuously reused. If the code is written well, then for particular programs, the re-usability is high.
- Networking has grown out of structured computing and we use modularity instead. People began to identify semantic coding that we need to execute multiple times, making functions and parameter calls.
- Each of those functions, or block of code, can thus be executed on different computers, contacting it and using it across different networks.

**Remote Procedure Calls:**

- Original designers called these remote procedure calls. The remote Procedure Call is a protocol that allows a program to execute a procedure (or function or subroutines) on a remote system as if it was local.
	- Remote procedure calls may allow us to automate the development and execution of a program. However, you are not sharing an address space, meaning variables that are shared cannot be supported.
	- We are going to focus on restricting the access of the remote process.
- If we call a function and give it parameters, we must do this before the remote procedure commences. Locally, variables would be pushed on a stack or heap (depending on size and dynamically allocated memory).
- However, this is difficult when dealing with different computers as pointers on one computer doesn't map to an address space on another. This means that we ave to pass everything on the program to another computer.

*Handling Dynamic Data Structures through Serialisation:*

- Instead of sending a memory address, we pass a representation of the list. This might involve sending each node's data along with information on how the nodes are connected.
- Code that understands the shape (such as it being a list and not just random code) is required to reconstruct the list correctly on the remote side.
- You can also send back self-referential structure back to the original sender with the structure changed in a particular way. This is called serialisation.
- Usually supported with libraries.

**RPC Execution Order:**

- There is a client and server, each with a different process running. They both have support from the OS kernel, facilitating the network.
- We need to define how they communicate. In modern programming, we prototype a function, we warn the compiler to say whats coming up. Something similar will be done here.
- The server starts off first.
- With this execution order, its possible to only worry about the implementations of both client and server and how they send information. We don't need to worry about translation of results or arguments or routing algorithms.

*CLIENT-SIDE:*

- *Client Calls Client Stub:* The client application calls a local procedure known as the client stub. To the client, it appears as if the client stub is the actual procedure on the remote server that it wants to call.
	- Client stub connects the program to the standard libraries and is automatically generated.
	- The client just believes it is calling a local function. 
- *Marshalling by Client Snub:* The client stubs role is to package the procedures arguments and convert them into a standard format, called marshalling.
	- The stub then creates one or more network messages containing these packages arguments.
- *Sending network messages to local kernel:* These messages are sent to the clients local kernel using a system call. The kernel is the core part of the OS managing system resources and communication between hardware and software.
- *Transmitting messages to remote kernel:* The local kernel sends the network messages to the remote kernel. This can be done using TCP or UDP.

*SERVER-SIDE:*

- *Server stub waiting for requests:* The server stub is waiting for incoming client requests. It acts as an intermediary on the server side.
- *Unmarshalling by server stub:* Upon receiving the network messages, the kernel responds to the server stub waiting for requests and passes the information along. The server stub extracts the arguments and may convert them into the server's native format.
- *Executing Server Procedure:* The server stub makes a local procedure call to invoke the actual server function using the unmarshalled arguments.
	- We have to identify what we want to call on the server on the client side. This is because there can be multiple clients and servers. 
	- We need to declare what server it wishes to call and identify the procedures that we want to call. 
	- Address and parameters are usually thrown away by the compiler and it just generates raw code. Thus, if we wish to call a function, we don't know the memory address it corresponds to. The server stub will provide the mapping between the local functions and their references. It pushes the parameters onto the server stack.
- *Returning results to Server Stub:* After the server procedure completes, it returns the results to the server stub, along with any necessary output arguments.

*Example of Transparent Access:*

- This would be great if it was only used for our own application programs. However, Google and Microsoft attempt to create entire operating systems in this manner.
- Distributed files systems, for example, are built using RPC paradigms. That is, to read a file on a remote file system in a different address space, they will all provide mapping between traditional system call and packets to the server.
	- All these distributed file systems work to provide a mapping between the standard `read()` function call and a series of network packets to send a request over a network, read the block of code on the server-side and then send this block back.
- For example, suppose we read a file:

```C
fd = open( _"/home/uniwa/staff9/staff/00012349/linux/src/example.c"_, O_RDONLY, 0);
```

- There will be a mount point `/home/uniwa/staff9` that determines whether the file is available locally or remotely.
	- It refers to a file system on a remote machine as well as a series of RPC requests which are made to access the remote file.
- If remotely, there will be RPC.

![[Screenshot from 2024-05-20 20-40-37.png]]

- In the diagram above, there will be a layer within the system kernel that determines whether the file (based on the naming convention above) is stored locally or remotely. 
- If remote, the syscall will be sent to the NFS client, which will convert the call into a series of packets and network requests.
	- Doesn't occur in user space due to efficiency. 
	- Server layer is implementing the file system for the client. 

*Issues with networked RPCs:*

- When calling a function within our own address space, few issues arise a long as there is sufficient stack space to push parameters and receive correct results.
- With networked RPCs, new problems occur: the network might be unreachable:
	- The host computer might not exist
	- No path to the host
	- We be in the middle of opening files and then the connection fails. We might have to more rigorously check the return result
- Networking can deliver missing or duplicate information. If our code relies on previous data bits, this becomes a problem.
	- This can be mitigated by restructuring the server-client relationship. For example, in distributed file systems, its unclear who tracks write progress.
	- The client could track its position or the server could manage it for each connection.
	- A crash mid-operation might go undetected, causing retries, and if the client reconnects, the server loses track of the client position.
- We must support idempotent operations to resolve this. Instead of constant appending, the client specifies where to write, so if the server crashes, the client knows its last position.

**Sun Microsystems First RPC Compiler:****

- Sun Microsystems created two significant aspects of RPC. One was a RPC generator, or compiler.
- `rpcgen` is a compiler for creating remote procedure call server and client stubs. 
	- The user writes a specification file that outlines the prototype of the functions without providing their implementation.This file is similar to a header file in C, containing declarations of the functions that will be available for remote calling.
	- These do not include the actual implementation of these functions.
- All the coding, running `rpcgen`, compiling and linking are typically done in one development environment. The resulting executable are then deployed to their respective machines.
- You provide `rpcgen` with three items:
	- The code for the server: This contains the actual implementation of the functions.
	- The specification file: This outlines the functions and their prototypes.
	- The code for the client: This contains the code that will call the remote procedures.
- `rpcgen` processes the input and generates three files:
    - **Client Stubs**: Code that handles the communication from the client side.
    - **Server Stubs**: Code that handles the communication on the server side.
    - **Header File**: Contains mappings of variable names to constants and other necessary declarations.
- The generated client and server stubs are then compiled into object code. These are linked with a networking library. This provides the necessary functions and protocols for network communication.
- Finally, you get the client program (which initiates RPC calls to the server) and the server program (listens for incoming RPC calls and executes the corresponding functions).
- We generate the client and server at the same time or a company may charge for you to access the service and give to the user the specification file, which will build the client and server.

*Naming and interface binding:*

- How does the client-stub know who to call and where it is? There is a standard service called the port mapper on port 111 and when a new new RPC server comes into existence, it registers itself with this mapping process.
- The server passes it the
	- Program number, program version number and the port number on that machine.
- Thereafter, the first time a client-stub needs to locate a remote procedure, it first asks the database server. The server maps the procedure name to the network address in a process called binding.

Semantics of RPCS:

- 











