# Topic: NETWORKING

## What is TCP? How it works?

TCP is a transport layer protocol.
- Delivers messages reliably and in order.
- Detects modification in packets during delivery and corrects them.
- Can handle network congestion.
- Applications/Protocols that use TCP: HTTP, Email, File Transfers.

## What is UDP? How it works?

UDP is a transport layer protocol.
- UDP does not ensure messages deliver in order.
- Detects modification in packets but does not correct them.
- Generally faster than TCP because of the reduced overhead of ensuring packet in-order and uncorrupted.
- Applications that use UDP: DNS, live streaming, voice over IP, etc.

## What are the differences between TCP and UDP? And in which case we use which?

The key difference between TCP and UDP is TCP has a mechanism to resend loss packets, but UDP doesn't. So TCP suits in the case where packet loss can not be acceptable, like transfer file, request... UDP suits in the case packet loss can be tolerated, like in real-time voice/video calls.

## How TCP open a connection? What does it need to open a connection?

TCP needs to open a connection to determine if the server is available to respond and know there is a way from it to the server before transferring data. To open a connection, firstly server must be bound and listen to a port, it is passive open. Then a process called 3 way handshake happens:

- Client sent SYN packet to the server with sequence num A
- Server sends SYN-ACK packet to the client with rand sequence num B and ack num A' = A+1
- Client sends ACK packet to the server, with ack num B' = B+1 and the sequence number is A' ( the ack num it received in step 2)

## Why there are 3 way handshakes but not 2 way?

- 2-way will establish a half-duplex connection only. It means this is a one-direction connection the client is the only sender and the server is the only receiver. 
- 3-way will establish a full-duplex connection, both client and server are sender and receiver.

## What is syn, ack mean?

- Syn means synchronizaton. SYN packet is sent when the sender tries to connect to the receiver.
- Ack means acknowledgment. ACK packet is sent by the receiver to the sender to help the sender know that the SYN packet that it sent before reached the destination successfully.

## Why they have to send 2 "random" sequence numbers? The purpose of this sequence number?

- Sequence numbers are used to keep track of the order of data. 
- The initial sequence number needs to be chosen randomly so the data in one flow will not be conflicted with other data bytes in other flows which are transmitted in the same TCP connection. Because sequence number is 32-bit integer so the maximum value of a sequence number is 2^32 - 1. So if the amount of data is greater than 4GB, at least one sequence number value will be reused.

## What if the 3rd handshake fail? How the server can detect it and what does it do in this case?

Retry and timeout.

## How TCP handles the connection?

TCP handles connections by providing reliable, full-duplex communication through mechanisms like sequence numbers, acknowledgments, and flow control. It ensures data is transmitted in order, without loss, and manages congestion to maintain a stable connection.

## What happens if some bits are wrong due to connection errors? How to detect them and fix them?

If some bits are wrong, the checksum of this packet will not match. In this case, the receiver will discard this packet.

## How the timeout is handled? what if the timeout is expired?

TCP uses timeouts to handle situations where data acknowledgment is not received within a certain period. If a timeout expires without receiving an acknowledgment, TCP assumes the data was lost and initiates a retransmission of that data. This process continues until either the data is successfully acknowledged or a maximum number of retries is reached, leading to a connection termination.

## What will happen if some "packet" is missing on the way?

When a packet is missing, network congestion is assumed to happen. TCP has its own way to deal with network congestion. It is called TCP Congestion control. 
- It starts with slow-start phase, in which the transmit rate (congestion window) grows exponentially, doubling each round trip time. Then when the congestion window hits a threshold, it increase linearly. 
- When a packet is lost, congestion avoidance begins. The transmit rate is cut into half, then increase linearly, usually by 1 MSS each RTT.

## How to detect the appropriate number of packets to send (speed of sending packet)?

Like above.

## How TCP close the connection?

It does a 4-way handshake. Firstly peer A send a FIN packet to peer B. Then peer B send an ACK packet for the FIN it receives. After that peer B send it own FIN packet to peer A. Then peer A send ACK packet to the peer B. Connection is over. Step 2 and 3 can be merged by one, so it will be 3-way handshake instead.

## What if the internet is dropped in the middle of the connection? Or in case one peer is crash?

- Internet connection dropped:
    - If the Internet connection dropped, both TCP peers will eventually detect the loss of connectivity (such as timeout).
    - TCP will continue to retransmit unacknowledged data for a period of time, but if the connectivity is not restored within a timeout period, the connection will be considered lost.
    - Applications using TCP should handle connection loss gracefully, such as by reconnecting or notifying user.
- Peer Crashes:
    - If one of the peer crashes, the other peer will eventually detect lack of response.
    - The alive peer may continue to send data and acknowledgement, but if it receives no responses for an extended period, it will consider the connection "dead", then it can notify user or close the connection.

## How long you can keep a TCP connection alive?

- To keep a TCP connection alive over extended periods of inactivity, you can use TCP keep-alive mechanisms. These are special packets sent periodically to check the health of the connection. The frequency and settins for TCP keep-alive packets can often be configured at the operating system or application level.

- If we do not use keep-alive mechanisms, the duration for which a TCP connection can be kept alive depends on various factos: Firewalls, routers, load balancers along the network path may have their own timeouts and rules that can impact the maximum connection duration.

## How Ping command works? What is TTL? How does TTL will be changed?

- Ping command sends a packet to the destination server, waits for the response then calculates round trip time.

- Time-to-live is a mechanism that prevents a packet to be forward around the internet forever. Like the name, it is the number of times or hops, that packet can be forward before it is discarded by a router. Each time a router receives a packet, it reduces TTL of that packet by 1. If TTL reaches zero, the router will discard this packet then send an ICMP message back to the sender. The recommended default value of TTL is 64.

## How HTTP works?

HTTP is an application protocol that transfers hypertext messages between client and server. It uses specific request methods to perform specific tasks. They include:

- GET: requests data from server
- HEAD: similar to GET but only requests the headers of a resource without actual content. It is used to check resource meta-data like last modification timestamps.
- POST: submits data to server (the data can be command, web form, an item to add to a database, etc.)
- PUT: update or replace a resource at the specified URL with the provided data. If the resource existed, PUT replaces it. If the resource does not exist, PUT creates it.
- DELETE: deletes the resource at the specified URL.
- OPTION: requests information about the communication options available for the target resource.

## Why did people say that HTTP is stateless? The reason they make it stateless?

People say HTTP is stateless because each request is executed independently. Stateless provides high scalability.

## Can we make a persistent HTTP connection? pros and cons of this way?

Yes, this approach allows multiple HTTP requests and responses to be sent over a single TCP connection without closing it after each transaction.
- Pros
    - Reduce Latency and Reduce Resource Usage: CPU does not need to set up new TCP connections.
    - Improve Network Efficiency: Reduce the network overhead associated with establishing and closing TCP connections.
- Cons
    - Resources will be occupied: Long open HTTP occupies resource of other processes.

## Why HTTP require cookie each time we send the request?

No it doesn't required cookie.

## Can someone use your cookie and log in your Facebook account? How to mitigate this?

If Facebook stores authentication straight info in cookies normally and does not do anything special, the answer is yes. To mitigate this:
- Use HttpOnly flag: Prevents cookie being accessed from client-side scripts.
- Use Secure flag: Cookie can only be tramsitted over secure connections.

## What is HTTP session? How does authentication work in HTTP? 

Since HTTP is stateless, HTTP session and cookie are two ways to connect some requests together. 
- Session is created and stored at server side, usually just temporary. Session ID will be sent to client, client may submit it to server as a cookie, or url parameter, so server will know which session this request belongs to. Session ends when user logout, close web browser, or timeout. Session is more secure, compared to cookie.
- Cookie is stored at client browser. Cookie will expire at the set time.

## What is JWT?
JWT is standard for creating data with opt-in signature and encryption, used for authentication, for example:

`JWT = HMACSHA256(base64(header) + "." + base64(payload), secretKey)`

- This is the process of using JWT:
    - When a user logs in using their credential, the server verifies the credentials and issues a JWT signed using its own secret key.
    - The JWT is sent back to the user. The user's client will store this JWT in local storage or as a cookie.
    - When the user makes subsequent requests to the server, the JWT is included, typically in `Authorization` header.
    - The server checks the JWT to ensure it's valid, and also check for the token's expiration, the issuer, etc.

## Which type of "data" HTTP can help us to get or push? (binary file? image? text file? video file? music file?)

HTTP can help us get or push all kinds of files like text, image, binary, video, music...

## REST/RESTful?

REST stands for representational state transfer. It's a set of constraints that describes how an API should work. It includes four main principles:

- Client-Sever: There is always a client and a server. Client sends request to server and server reply with response.
- Stateless: Server handles client request independently. Client's request must contain enough information that server requires.
- Uniform Interface: Resources are accessed and manipulated using standard HTTP methods and follows standard conventions for resource naming.
- Cacheability: The response to a request should implicitly or explicitly label itself as cacheable or non-cacheable. If the response is cacheable, the client application can resuse the response data for later equivalent requests.

An API that follows REST constraints is called RESTful.

## AJAX technique?

AJAX is Asynchronous Javascript and XML, it is a technique to create dynamic and interactive web applications. AJAX allows web pages to send and receive data from server asynchronously, meaning that it can communicate with server without reloading the whole page.

## How HTTPS work?

In short, HTTPS uses SSL - Secure Socket Layer to protect the communication by encryption. Before exchanging data, an SSL connection must be established first, then data will be encrypted then transferred safely.

Here how HTTPS works in details:
- Client initiates a TCP connection to the server.
- TLS Hanshake happens:
    - ClientHello: The client sends a "ClientHello" message to the server, indicating that it wants to establish a secure connection and specifying the cryptographic algorithms and protocols it supports.
    - ServerHello: The server responds with a "ServerHello" message, selecting the strongest encryption protocol and key exchange method that both client and server support.
    - Exchange of Public Keys: The sever sends its public key certificate to the client. The client then verifies the authenticity of the certificate by checking its digital signature against a trusted certificate authority.
    - Key Exchange: After the client verifies the public key certificate of the server, they agree on a shared secret key that will be used for symmetric encryption and decryption of data.

## Learn about some useful headers

- Request Headers
    - User-Agent: Information about client's application type, operating system, software version, etc.
    - Accept: Informs server the types of media that client can process.
    - Authorization: Credentials for authenticating.
    - Cookie: Contains cookies sent by server with the Set-Cookie header.
- Response Headers
    - Content-Type: Indicates the media type of the resource(e.g. `text/html; charset=UTF-8`).
    - Content-Length: Length of the response body in bytes.
    - Expires: The time after which the response is considered stale.
    - Location: Used in redirection.


## When you type "google.com" into your browser, that will happen when you type enter till everything is displayed on your screen?

- The first thing that happens is DNS lookup. The browser will get the corresponding IP address with the domain name from its cache, from the computer cache, from the local host file. If there is no data in these places, then the browser will send a request to DNS server to obtain the IP address. ([More](https://serverfault.com/questions/208172/how-does-my-browser-know-where-to-get-data-from-a-server))
- The second thing is browser will detect if HTTPS is required or not in case we don't type the full url. Browser can get this information from its history data, from a preload list. Otherwise, it will send an HTTP request. (When server receives the request, server can do appropriate action to force user uses HTTPS if it wants.)
- The third thing is TCP connection establishment. The TCP connection can be established by a process called 3-way handshake. If HTTPS is used, after TCP connection is established, the SSL connection establishment process will start.
- Fourthly, after having the connection, client will send data to the server and server will send a response back to client through this connection.
- The next thing is browser will render the data from response based on the content type. If there is no content type, the broswer will read the body of response to know which kind of data it is. If the content type is html, broswer will parse it then get resources like images, js files, css files if necessary.

## DNS lookup (in case you already access google.com before and also in case you do not know the IP of google.com)

- DNS information is cached in browser and operating system. If there is no corresponding data in both of these places, DNS request will be sent to the DNS resolver. 
- The resolver will make necessary request to Root DNS Server -> Top Level Domain server -> Authoriative Domain Name server to get the IP address of the requested host, then return to user.

## Which protocol DNS use and why?

DNS primarily uses UDP as transport protocol because it's fast and DNS data usually is small and fits an UDP packet. TCP is used when the data is greater than 512 bytes, which can not be fit on a single UDP packet.

## How to know "google.com" require HTTP or HTTPS? how browser can know and redirect from HTTP to HTTPS?

There are at least two ways that make the web we are accessing change from using HTTP to HTTPS.

- The first way is the server will redirect to HTTPS when the HTTP request comes.
- The second way is using HSTS, HTTP strict transport security. When a browser sends the first HTTP request to server, server will add a header into the response indicates that the next request will require using HTTPS. Browser will automatically use HTTPS next time based on that information. In addition, browsers like Chrome have a HSTS preload list, they will automatically use HTTPS for all of the sites in that list.

## After you get the HTML content for "google.com" how to get the \*.js and image files?

Chain requests.

## When getting \*.js or image files do we use another TCP connection or use the same one as in the get HTML content? How DNS lookup work in this case?

When getting .js or image files, the browser typically reuses the same TCP connection established for fetching the HTML content. However, there are a few cases:
- If the resources reside on a different domain than the HTML content, then we need to create new connection.
- If the resources has already cached, then we do not need any connection.

## After your browser display "google.com" fully, is there any connection open?

It depends on if the connection is keep alive or not.

## Caching can apply to which steps? How caching applied?

Caching can be applied at various steps in the process of delivering web content to users.
- Browser Caching: The web browser can stored resources (images, stylesheets, JavaScript files, etc.) locally on device for the next time user visiting web page faster.
- CDN Caching: Resources cached in CDNs over the world help reduce latency.
- Server-Side Caching: Many information can be cached in the backend server to reduce latency.

## What is the connection pool? It's advantages and disadvantages? How to implement connection pool in your programing language?

Connection pool is a cache of database connections so connection can be reused when future request comes. Opening a connection is costly, so after a connection is opened, it is placed into connection pool so it can be reused later.

Advantages
- Improve performance
- Increase scalability: Connection pooling allows an application to handle spikes in traffic without overloading database server.
- Reduce load on server's CPU and memory (fewer connections are being created and destroyed).

Disadvantages
- Idle connections may outweigh the benefits of improve performance

## What is socket?

Socket is an endpoint for a program to send and receive data across the network. Socket allows communication between processes on the same machine or different machines.

## Why socket is a "file" in linux?

Not only socket, everything is treated as files in Linux operating system. It provides a common and universal interface for things to communicate in this OS.

## What is src port when you create a connection to a "server"?

Source port can be assigned by the OS when the application opens a socket to connect to the server. Programmer can manually set a source port for the application.

## How one server can handle multiple connections to the same port?

As below.

## What is the maximum number of connections a server can handle? (if it has unlimited resource) (in case of the same client and in case of multiple clients)

TCP has 4 fields to identify the uniqueness of a socket: source IP, source port, destination IP, destination port. Theoretically, if server listens on a port, a client can open a maximum of 2^16 connection. that's is the maximum number of ports a client can open. For multiple clients, the number can be multiplied by 2^32, it is the possible number of source IP addresses. So the maximum number of connections is 2^48.

But in practice, the maximum number of connections depends on the kernel configuration. Because each socket needs a file descriptor, the number of sockets is limited by the maximum number of file descriptors that the machine allows. In addition, a file descriptor needs a certain amount of memory so it can also be limited by the number of available memory.

## When you open multiple tabs on your chrome, how OS knows which packet (both sending and receiving) correspond to which tab? (how about in se you open many tabs to the same page "for eg: google.com")

Because each connection has its own socket that is used to exchange data.

## What are the maximum numbers of connection your machine can connect to "google.com" (if you have unlimited resource)

It is the maximum number of ports my machine can open, which is 2^16.

## Can two processes listen to the same port on your machine? Why? How?

Yes, and it is useful for multicast purpose. Socket needs to be specified as a reused socket before binding to do so.

## What is buffer? Why we always need buffer when working with "file"?

Buffer is a segment of memory that holds data before it is processed or transferred. Buffers are used to improve performance of I/O operations by reducing the overhead of transferring data between devices.

## What is unix socket? When to use it?

Unix socket or IPC (Inter-process Communication) socket is a data communication endpoint that allows processes in a same machine to communicate with others.

## What is the loopback network interface (localhost)?

The loopback network interface (localhost), is a virtual network interface that allows a computer to communicate to itself. Any traffic that a computer program sends to localhost is addressed to the same computer. Using the loopback interface bypasses any local network interface hardware.

## What is TCP proxy? reverse proxy? and VPN?

- A TCP proxy server is a server that acts as an intermediary between and client and server. TCP proxy servers are used for:
    - Anonymizing web traffic: Hide clients on the internet. The server only knows that request came from the proxy server and doesn't know about the actual clients.
    - Filtering and controlling web traffic: For example, it can be used to block certain websites or limit amount of bandwidth that users can use.
    - Load balancing and caching: A TCP proxy server can be used to distribute traffic between multiple servers.
    - Security: For example, it can be used to scan traffic for malware or to block unauthorized access to certain resources.
- There are 2 types of TCP proxy servers:
    - Forward proxy: Used by clients to connect to destination server, hides clients on the Internet.
    - Reverse proxy: Used by server to protect server from client. It hides server from client. From the client point of view, client sends data to the reverse proxy and receives data from it too.
- VPN stands for Virtual Private Network, it allows devices on a public network to be connected as they are in a private network.

## How load balancer works?

Load-balancer distributes load to servers. It can do it randomly, uses an algorithm like round-robin, or distribute requests based on the current load of each server.

## When we send a packet to a load balancer how does it forward to the desired server? (Does it keep any data on its memory?)

As above (No).

## When the server wants to send data back to the client, does the connection need to go through the load balancer?

It depends on whether the server and the load balancer are in the same private network and share the same IP address.

## What is different between reverse proxy and load balancer?

Reserve proxy acts as an intermediary without any load distributing operation.

## Can load balancer be a bottleneck? (Because it is the end point of too many requests) (bottleneck about RAM or CPU or Network?)

Yes. And it also can be a single point of failure.

## How your router at your home works?

- Router connects devices within a network by forwarding packets between them. Packets might be sent between devices, or from a device to the internet and vice versa. It maintains an ARP (address resolution protocol) table to know where to forward a packet to.
- In addition to these basic functions, routers also perform other tasks, such as:
    - DHCP (Dynamic Host Configuration Protocol): Assigning IP address to devices.
    - NAT (Network Address Translation)

## Inside LAN network, it uses IP or MAC address? Why?

Inside a LAN network, it uses MAC address cause we don't need a layer 3 protocol to communicate between devices that are in the same local network.

## How does it know which packet comes from (or arrive at) which machine?

Router will use the ARP table to know the MAC address of the device which this packet is sent to.

## What is the difference between Hub and Switch inside LAN?

- Hub is operated on Physical layer and supports only broadcast transmission.
- Switch is operated on Data link layer and supports unicast, multicast, and broadcast transmissions.

## How src IP/PORT and dst IP/PORT change on the way to the server?

To be defined.


