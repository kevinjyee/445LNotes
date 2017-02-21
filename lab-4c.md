#Lab 4
##Motivation
 
 IoT
IP addresses, DNS (what, why), UDP (what, why), TCP (what, why), sockets (types, what, why),
Steps involved in client-server paradigm
- n nodes n(n-1)/2 Links 
- O(n^2) 

##IP Addresses
1) 32-bit 
2) $$2^{32}$$ = 4 billion
3) Will eventually change to 128-bit Address
4) Serves as Private Identifiers 
5) Each device has unique Private IP

Private IP for each device but use Public host IP Address to connect to the internet 
![](http://i.markdownnotes.com/blob_tgHPa6z)

##CC3100
1) USe CC3100 to extend cycle per instruction + memory. 
2) Firmware for TCP/IP and Wi-Fi Driver 

##Client - Server Paradigm

![](http://i.markdownnotes.com/blob_AUPjQ2p)
TCP or UDP transport protocol

**UDP**: Speed vs Reliability. Missing a few frames do not matter 

What is a client - server paradigm? 

###Network Programming = Socket Programming
*Creates sockets to communicate with each other*
Sockets is a portal that you can send TCP packets 

**Server** host/network.org
*A connection socket is all the info including ip address and port *
1) Creates a Connection Socket
2) Waits for clients at the connection socket 
**Two types of sockets on a server**
Connection SOcket : Allows Client to get into the socket 
Individual SOcket - CLient uses this private socket 

**Client**
1) Client creates a client socket connect to host.network.org
2) Sends request to Client 
3) The server socket accepts the request on the Server Socket 
4) Read Responses 

![](http://i.markdownnotes.com/blob_Kz70i7t.jpg)


##Analysis and Discussion (1 page maximum)
In the client server paradigm, explain the sequence of internet communications sent `

1) **Explain client -server paradigm**

In the client-server paradigm, a server creates a connection socket and waits for a client to connect to the connection socket. The client then creates a client socket and sends a request to the waiting server. When free, the server will accept the client request using a server socket. The server socket will create a thread to serve the client which has requested it.  This thread will process any request data coming from the client, and if necessary will cause the server to create and send a response to the client any number of times. In the context of saving data, the client will send a request to the server with data and a request to save the data on the server. After the client socket connects with a server socket, the server can then process this request and store the requested data.

2)** What is the purpose of the DNS?**

DNS, the Domain Name System, is a sort of translation scheme between website domain names and the IP addresses of the machines which host those websites. It is a layer of abstraction that creates a more user-friendly means of accessing different domains (e.g. being able to type google.com into one’s address bar instead of 8.8.8.8). This also means that even if the underlying IP address for a domain changes, a user may still access that domain with the same domain name they are used to--a good use of information hiding that takes the burden of IP address knowledge off the average user of the Internet.

3) **What is the difference between UDP and TCP communication? More specifically when should we use UDP and when should we use TCP?
**

UDP and TCP are both Internet communication protocols, however they share distinctions in transmission, speed, and reliability. UDP consists of sending information asynchronously in packets. As a result of this asynchronous transmission method, data on the receiving end could end up in a different order than how it was sent, or might not arrive at all. This means UDP suffers from a certain amount of unreliability, but what it lacks in reliability it gains in speed. Due to its speed, UDP is an ideal protocol for such communication needs as streaming services and video game servers, where speed is a must and losing packets is okay. TCP, on the other hand, is a synchronous, connection-based method of data transmission. It requires a “handshake” between client and server sockets, which guarantees a stable connection and mitigates data loss. The overhead which allows for this reliability comes at the cost of speed, so TCP is a slower protocol than UDP.

