### Systems Design
---
##### Fundamentals
```
Client-Server Model 

Step 1)
   Client makes a DNS query, retrieves the IP address of some domain, and contacts the server  
   When running a server through cloud providers, the providers can grant you IP addresses
  
Step 2)
   Sends HTTP request to server along with source address (address of sender)
  
Step 3)
   Server listens to requests on ports, and sends HTML/CSS/JS files to source address 
  
Storage
   1) Disk Storage   : permanent / persistent storage with high latency (hard disk)
   2) Memory Storage : temporary / transient storage with low latency (RAM)
  
Capacity
   1) Latency    : time between stimulation and response 
   2) Throughput : actual output of a system or machine in a given time / how many requests are handled  
   3) Bandwidth  : theoretical output of a system or machine in a given time 
   4) Bottleneck : constraint of a system 

Time in Latency and Throughput are independent
   1) latency is concerned with the time taken to deliver a message
   2) throughput is concerned with how much data can be transmitted per time unit (frequency)  

Availability : uptime in a given amount of time (usually per year)
   1) SLA (Service Level Agreement) : an assurance for the uptime of a service 
   2) Redundancy : having alternatives when a failure happens 
      * Passive Redundancy : Uses excess capacity to reduce failures 
      * Active Redundancy  : Monitors and reconfigures capacity in downtimes 

Proxy : a server that acts as a middleman between a client and another server
   1) Forward Proxy : acts on the behalf of the client, could mask the identity of client (VPNs) 
   2) Reverse Proxy : acts on the behalf of the server (load balancer, cache, filter, logging) 
```

##### Caching
```
Caching : save certain data/files/results in a caching layer to retrieve them faster 

Read Examples
   1) Servers cache results retrieved from databases 
   2) Clients cache computationally heavy operation 
   3) Browsers cache HTML / JS / image files 
   4) DNS servers cache DNS records
   5) CDNs cache website contents and static files such as images or HTML files

Content Delivery Network (CDN)
   1) Pull : when a file is accessed for the first time, load it to the CDN, and will be cached thereafter (initially slow)
   2) Push : put files into the CDN to be cached (initially fast but some files may never be used)
    
Write Examples
   1) Write Through Cache : posts are saved to both cache and database at the same time 
   2) Write Back Cache    : posts are saved to cache, and asynchronously updates database 
    
Problems of Caching :
   Caching becomes difficult for mutable data as this might result in displaying stale (outdated) data to certain users
  
Caching Eviction :
   Rules to evict cached data (LRU Cache, LFU Cache, FIFO Cache)

Negative Caching : 
   1) caching for things such as misspellings (might take a long time to fail the first time)
   2) great for failures taking less time 
```

##### Load Balancing
```
Load Balancer : balances and allocates requests to DNS/servers/databases to maintain availability and throughput 

Horizontal Scaling : increase number of hardware
Vertical Scaling   : increase performance of existing hardware 

Rules for load balancing 
   1) Round Robin : start at the first item of a list of servers, sequentially look for available servers 
   2) Weighted Round Robin : ability to weigh different servers based on how powerful they are, and distribute work based on weight 
   3) Load Based Server Selection : monitor the performance and load for each server and dynamically allocate based on calculations 
   4) IP Hash Based Selection : hash IP address to determine where to send request (useful for geographical servers and when servers cache requests)
   5) Service Based Selection : different servers handle different services 
```

##### Hashing
```
Hashing   : convert an input into a fixed size value 
Collision : when two values are consistently hashed to the same value 
SHA (Secure Hash Algorithms) : cryptographic hash functions

problems : 
   1) if a server fails, hashing might still allocate requests to the failed server 
   2) when new servers are added and hashing formula changes, previous keys will be remapped, making previous caches become useless

Consistent Hashing : 
uses a hash ring where servers are distributed via a hash function (can be distributed more than once!)
after hashing a request, the request will move clockwise/counter-clockwise to the nearest server 
this does not solve but greatly reduces the problem of previous keys being remapped 

Rendezvous Hashing (Highest Ranking Hashing) : 
clients rank servers by ranking and get distributed to the highest ranking server 
when a server gets deleted, clients who lost their highest ranking server will redistribute to the next highest server
also solves the problem of previous keys being remapped 
```

##### Leader Election
```
What if we introduce redundancy to servers but we would like to ensure that only one server does a particular request (payments)? 

Leader Election      : specify one server to be responsible for a request (replicas will take over if the leader fails)
Consensus Algorithms : complicated algorithms that help select a leader among servers
```

##### Hot Swapping
```
What if there is a failed component that we need to replace without taking the system down?

Ways to Hot Swap
   1) If component is stateless, just replace with new component
   2) If component is stateful, replace with new component and give up on in-flight transactions
   3) If component is stateful, create hot replica, replace with new component, and let hot replica replicate state
```

##### Peer-to-Peer Networks
```
What if a server is sending data to thousands of machines? By the time the server is done sending, it might have to send again..or even be late!
If we have multiple servers, then we need to replicate the same data to all the servers..
If we shard the data, then we end up with a server sending data to thousands of machines again...

Peer-to-Peer Networks : 
   1) data to send is broken into many pieces, and instead of sending 1000GB to "1" machine at a time, we send 1GB to "1000" machines
   2) peers communicate and send missing data to other peers AS the server is sending over data

Gossip Protocol        : protocol for peers to communicate to each other and spread information
Distributed Hash Table : hash table that holds information on what peers hold what data 
```

##### Polling & Streaming/Configurations/Logging & Montoring
```
Polling   : sending a request for updated data (packets) in regular intervals (cycle of requests/responses)
Streaming : client opens a channel using "sockets" for servers to send data (client listens on server)

Configurations : "settings" for codebases written normally in JSON or YAML
   1) Static Configuration : Configurations are packaged with codebase, must deploy entire code to test, can create test cases during deployment
   2) Dynamic Configuration : Configurations outside the codebase, takes immediate change, but harder to test 


Usages for data
   1) Logging : the collection of data to use for recording/finding errors/analytics
   2) Monitoring : having transparent ways to analyze data for insights
   3) Alerting : alert of significant changes in data 
```

##### Rate Limiting
```
How do we prevent malicious requests (DoS attacks?)

Rate Limiting : limit number of operations, possibly in different tiers 
   1) Criteria : Request frequency, User/IP Address, Region, Server (10,000 requests at a time)
   2) Typically use another database/cache to check for rate limiting (Redis) because in-memory cache at the server level becomes 
      less useful when clients get redirected to another server
```     
     
##### Pub/Sub Model
```
What if a client was streaming for data and the server goes down. What if really important data doesn't get sent due to interference?

Pub/Sub Model : publisher sends messages (data) to a topic, and subscribers send back an ACK to tell topic that information has been consumed
   1) servers becomes independent of communicating with clients and do not need to waste memory on saving state 
   2) topic effectively acts as a database solution 
   3) clients are guaranteed "at least once delivery" of messages, if a topic does not receive an ACK (e.g. loses connection), it will resend messages 
   4) messages are sent using a queue (guarantees ordering)
   5) subscribers are able to replay messages or filter their subscriptions 
  
Idempotency : a characteristic where the outcome is always the same no matter how many times an operation is performed
   1) Did the subscriber receive the message? (Idempotent)   
   2) Increment Youtube View Counter (Non-idempotent)
```

##### MapReduce 
```
MapReduce : Framework for processing large datasets that are split up in distributed systems 

Map     : function that takes the data that is spread out and transforms it to key-value pairs
Shuffle : takes key-value pairs and routes them to relevant machines 
Reduce  : functions that takes shuffled pairs and transforms them into meaningful data 

Assumptions : 
   1) Distributed file system where large data is spread out across different machines 
   2) Central system that knows where the data resides, how to communicate to make map/reduce work, and knows where the output will reside
   3) MapReduce will run on different databases locally, and will not move the data to somewhere else 
   4) MapReduce is idempotent and fault-tolerant, if a server breaks, MapReduce will re-run on the failed chunk 
  
What do we have to know?
   1) We have to specify what our map function will be 
   2) Understand what sort of data is being mapped
   3) Understand what our key-value pairs will look like
   4) We have to specify what our reduce function will be 
   5) Understand what our output will look like
  
Example cases:
   1) Get the count of views on YouTube per channel 
   2) Count number of payments made per month using logs  
  
Distributed File System : cluster of machines that allow them to work as one large file system (Google File System, Hadoop)
```

##### Microservices 
```
architectural design that breaks a monolithic application into smaller pieces, which communicate through HTTP/API

pros 
   1) independent services with ability to use different programming languages
   2) easier to understand codebase and modify 
   3) when failure arises, only particular service goes down
   4) easier to scale specific process / service
   5) less commitment to a tech stack and able to change to new tech faster 

cons 
   1) testing can be complicated and managing the whole product becomes more difficult 
   2) information barriers between different services
   3) must implement means of communicating between services
   4) large upfront investment in automation as manual deployment becomes more difficult 
```