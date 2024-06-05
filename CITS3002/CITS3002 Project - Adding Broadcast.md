- A new function called broadcast function was added, which was responsible for broadcasting the station name to all neighbouring stations using UDP.

```C
struct sockaddr_in neighbour_address;
neighbour_address.sin_family = AF_INET;
neighbour_address.sin_addr = INADDR_ANY;
```

- The data structures for the neighbouring addresses was initialised in a similar way to the main UDP client socket.

```C
string message = "{\"name\":\"" + station_name + "\"}";
const char* msg = message.c_str();
```

- The message is made and converted from C++ string into a C string.

```C
for (const string& neighbour : neighbour_udp) {
    size_t colon_pos = neighbour.find(":");
    string ip = neighbour.substr(0, colon_pos);
    int port = stoi(neighbour.substr(colon_pos + 1));

    neighbour_address.sin_port = htons(port);
    sendto(udp_server, msg, strlen(msg), 0, (struct sockaddr*)&neighbour_address, sizeof(neighbour_address));
}

```

- Then for each of the neighbours (using C++ notation), the colon character is found and the string is split into the IP address and the Port number.




```C
void broadcast_name(int udp_server){
	struct sockaddr_in neighbour_address;
	neighbour_address.sin_family = AF_INET;
	neighbour_address.sin_addr.s_addr = INADDR_ANY;
	  
	string message = "{'name':'" + station_name + "'}";
	const char* msg = message.c_str();
	
	for (const string& neighbour : neighbour_udp) {
		size_t colon_pos = neighbour.find(":");
		string ip = neighbour.substr(0, colon_pos);
		int port = stoi(neighbour.substr(colon_pos + 1));
		
		neighbour_address.sin_port = htons(port);
		sendto(udp_server, msg, strlen(msg), 0, (struct sockaddr*)&neighbour_address, sizeof(neighbour_address));
		
	}
}

```

