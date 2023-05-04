- **2. Comparison with existing Messaging Systems**                 
    - Many systems do not focus as strongly on throughput as their primary design constraint. For example, JMS has no API to allow the producer to explicitly batch multiple messages into a single request. This means each message requires a full TCP/IP roundtrip, which is not feasible for the throughput requirements of our domain. Third, those systems are weak in distributed support. There is no easy way to partition and store messages on multiple machines.            
    - Finally, many messaging systems assume near immediate consumption of messages, so the queue of unconsumed messages is always fairly small. Their performance degrades significantly if messages are allowed to accumulate, as is the case for offline consumers such as data warehousing applications that do periodic large loads rather than continuous consumption.                
            
- **3. Kafka Architecture and Design Principles**               
    - **Terminology:**              
        - A stream of messages of a particular type is defined by a topic.          
        - A producer can publish messages to a topic.           
        - The published messages are then stored at a set of servers called brokers.            
        - A consumer can subscribe to one or more topics from the brokers, and consume the subscribed messages by pulling data from the brokers.            
    - We support both the point-to point delivery model in which multiple consumers jointly consume a single copy of all messages in a topic, as well as the publish/subscribe model in which multiple consumers each retrieve its own copy of a topic.             
    - Kafka cluster typically consists of multiple brokers. To balance load, a topic is divided into multiple partitions and each broker stores one or more of those partitions. Multiple producers and consumers can publish and retrieve messages at the same time.           
                
- **3.1 Efficiency on a Single Partition**              
    - Every time a producer publishes a message to a partition, the broker simply appends the message to the last segment file.             
      For better performance, we flush the segment files to disk only after a configurable number of messages have been published or a certain amount of time has elapsed. A message is only exposed to the consumers after it is flushed.              
    - Under the covers, the consumer is issuing asynchronous pull requests to the broker to have a buffer of data ready for the application to consume.         
    - Another unconventional choice that we made is to avoid explicitly caching messages in memory at the Kafka layer.          
      Instead, we rely on the underlying file system page cache. This has the main benefit of avoiding double buffering---messages are only cached in the page cache. This has the additional benefit of retaining warm cache even when a broker process is restarted.          
      Since Kafka doesn’t cache messages in process at all, it has very little overhead in garbage collecting its memory, making efficient implementation in a VM-based language feasible.          
    - The information about how much each consumer has consumed is not maintained by the broker, but by the consumer itself.            
      However, this makes it tricky to delete a message, since a broker doesn’t know whether all subscribers have consumed the message. Kafka solves this problem by using a simple time-based SLA for the retention policy.                
                
- **3.2 Distributed Coordination**              
    - Each producer can publish a message to either a randomly selected partition or a partition semantically determined by a **partitioning key and a partitioning function**.         
    - Kafka has the concept of consumer groups. Each consumer group consists of one or more consumers that jointly consume a set of subscribed topics, i.e., each message is delivered to only one of the consumers within the group.           
      Different consumer groups each independently consume the full set of subscribed messages and no coordination is needed across consumer groups.            
      The consumers within the same group can be in different processes or on different machines. Our goal is to divide the messages stored in the brokers evenly among the consumers, without introducing too much coordination overhead.              
    - In order for the load to be truly balanced, we require many more partitions in a topic than the consumers in each group.              
    - To facilitate the coordination, we employ a highly available consensus service Zookeeper          
        - Zookeeper has a very simple, file system like API.            
        - One can create a path, set the value of a path, read the value of a path, delete a path, and list the children of a path.             
        - It does a few more interesting things:            
            - one can register a watcher on a path and get notified when the children of a path or the value of a path has changed;             
            - a path can be created as ephemeral (as oppose to persistent), which means that if the creating client is gone, the path is automatically removed by the Zookeeper server;             
            - zookeeper replicates its data to multiple servers, which makes the data highly reliable and available.            
        - Kafka uses Zookeeper for the following tasks:             
            - detecting the addition and the removal of brokers and consumers,          
            - triggering a rebalance process in each consumer when the above events happen, and             
            - maintaining the consumption relationship and keeping track of the consumed offset of each partition. Specifically, when each broker or consumer starts up, it stores its information in a broker or consumer registry in Zookeeper.           
        - The broker registry contains the broker’s host name and port, and the set of topics and partitions stored on it.          
        - The consumer registry includes the consumer group to which a consumer belongs and the set of topics that it subscribes to.            
        - Each consumer group is associated with an ownership registry and an offset registry in Zookeeper.             
        - The ownership registry has one path for every subscribed partition and the path value is the id of the consumer currently consuming from this partition (we use the terminology that the consumer owns this partition).           
        - The offset registry stores for each subscribed partition, the offset of the last consumed message in the partition.           
            
- **3.3 Delivery Guarantees**           
    - Kafka only guarantees at-least-once delivery. Exactly once delivery typically requires two-phase commits and is not necessary for our applications.           
    - If an application cares about duplicates, it must add its own de duplication logic, either using the offsets that we return to the consumer or some unique key within the message.                
    - Kafka guarantees that messages from a single partition are delivered to a consumer in order. However, there is no guarantee on the ordering of messages coming from different partitions.         