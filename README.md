# MemcacheServer
Author: Karthick Santhanam
Language: Java
Assignment: Memcache Server Implementation Refer to Assignment.txt

Compile:javac com/ks/slack/.*java
Run:    java com/ks/slack/MemcacheServerMain

Notes:
1) MemcacheServerMain instantiates and starts the MemcacheServer.
2) MemcacheServer is can be instantiated multiple times if needed to be. (We can have multiple instances running)
3) Before starting the server, its executor can be configured which dictates how the server threading model 
would be handled for incoming connections. By default we use Executors.newCachedThreadPool(). 
This allows dynamic scaling of threads. In a real world, a load balancer will decide and initiate another instance 
of server if a given server is running at its optimum level.
4) Dispatcher is responsible for accepting new incoming socket and handing it to executor which may spawn a new thread.
5) SocketExchange is per socket thread that uses MemcacheExchange to parse request and send response.
6) MemcacheExchange handles the parsing based on the Memcache Binary Protocol Spec. It has lot of scope for improvement.
7) Memcache itself is just a in memory hashmap. Threads perform synchronized reads and writes.
8) I have verified the set and get working well with python-binary-memcached client.
9) I have also verified the thread pool working well when multiple clients tries to read and write at the same time.

Pros and cons of my design:
	-All read and writes are synchronized for simplicity. This can be improved and optimized based on
	number of outstanding writes. A read of keyA need not be synchronized with an outstanding write of keyB
	-Every socket is handled in a new thread via a cached thread pool. Its easy to implement and avoids concurrency 
	issues and provides scaling when needed however this means there can be overhead in context switches.
	-If we a lot of clients are going to request at the same time then due to increasing number of threads, the 
	latency of all the responses also increases.

Future tasks and enhancements: 
    -Create a generic MemcacheServer prototype and Handler (similar to HttpServer) so that the design can be extended.
    -SocketExchange threads can be combined together using socket channel. We can then select on the group of sockets. 
    this can avoid the overhead of context switches that can happen as the events from sockets can be serialized and
    processed in a single thread.
    -Optimize synchronization for read and writes.
	-Requests can be prioritized (high priority, normal priority) if needed to be.
	-Handle socket and server shutdown properly
MemcacheServer
