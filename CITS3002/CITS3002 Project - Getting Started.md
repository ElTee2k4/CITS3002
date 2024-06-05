There are two significant networking aspects to be covered in the project:
1. Each station server needs to support queries from a web-browser (receive and respond to queries) using the TCP/IP transport protocol. Long-term connection is established between two processes until either end closes the connection.
2. Each station server communicates with other station servers using the UDP/IP datagram protocol. While no long-lasting connection is established between the two, they can communicate by writing and reading self-contained messages. 
	- Each message (the payload of the datagram) will arrive at only the intended destination zero of more times.

**Communication between web-browser and station server:**

- A port number is a small integer identifying a 'communication endpoint' on a computer or server, enabling the OS to direct network traffic to the appropriate process or service.
- We can work to create a server-client communication where the server listens for incoming connections on a chosen port, receives HTTP requests from the client, processes them and sends back a HTTP response containing the requested content.
- Using `nc -l 4444` allows you to receive the following output:

```bash
foo:bar$ nc -l 4444 

GET / HTTP/1.1
Host: localhost:4444
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:125.0) Gecko/20100101 Firefox/125.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: none
Sec-Fetch-User: ?1

```

- The above means that a server has been set up and ready to listen for an incoming request. If you then type in HTML response in the shell and close the connection, the response will show up on the intended URL. This is a simple TCP/IP Server to Client connection on a server.

**Communication between station servers:**

- Station servers communicate with each other using UDP/IP datagrams.  Unlike TCP/IP, they do not establish or maintain a connection between servers. Instead, servers send datagrams on-demand, meaning they are sent as discrete packets without the need for a connection setup or teardown process.
- Unlike TCP, UDP allows for communication without prior connection setup. This means servers can simply send datagrams to each other as needed without establishing ongoing connections.
- Each station server knows the port numbers on which its neighbouring stations are expecting datagrams. However, they do not have the knowledge of the ports used by other stations. 

**Defining and executing your own transport network:**

![[Pasted image 20240501101928.png]]

- Each station has a unique name, directly connected to others by physical route, has a URL enabling TCP/IP connection from a web-browser and has a port for UDP incoming requests. They only know its neighboring open port numbers and all URLs, TCP and UDP packets will use distinct ports.
- Four distinct OS processes are required to execute the network simulation, each representing a station. These processes are invoked from the command line or a shell script, with each process receiving command-line arguments specifying station name, unique port for TCP/IP based communication, unique port for UDP/IP, and UDP/IP Ports of directly connected stations.
	- The `&` at the end of each shell command indicates that it is running in the background, allowing you to continue using the shell.

```sh
shell>  ./station North_Terminus 2210 2608 localhost:2606 &  

shell>  ./station East_Station 2230 2606 localhost:2608 localhost:2602 localhost:2605 &  

shell>  ./station.py West_Station 2220 2602 localhost:2605 localhost:2606 &  
  
shell>  ./station.py South_Busport 2240 2605 localhost:2606 localhost:2602 &
```

- When invoked, each station process first initialises its TCP and UDP ports, and then reads a CSV file providing its own timetable information. While your servers will need to read in and parse the contents of the timetable files, you can assume all of their content is correct.

```CSV
North_Terminus,-31.8448,115.7963  
07:15,12,Stop1,07:54,East_Station  
08:15,12,Stop1,08:46,East_Station  
  
14:20,12,Stop1,14:50,East_Station
```

- The first line contains the stations name, latitude and longitude. Second and subsequent lines each define a single bus connection leaving the station. 
- The second bus is read as: "At 7:15, bus number 12 leaves stop1, arriving at 7:54 at East station."

*Simplifying the building and execution of your transport networks:*

- 