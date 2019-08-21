## STATE OF THE ART
### Overview
* the dominant cost of this query is the cost of result set (de)serialization and transferring the data.
### Network Impact
#### Latency
* High latency is particularly problematic when either the client or the server has to receive a message before it can proceed. 
* latency will increase when they send explicit conrmationmessages from the client to the server to indicate that the client is ready to receive the next batch of data
* the performance of all systems is heavily inuenced by a high latency, because of the underlying TCP/IP layer does send acknowledgement messages when data is received
#### Throughput
* Reducing the throughput of a connection adds a variable cost to sending messages depending on the size of the message.
* when the throughput decreases compression becomes more eective. When the throughput is low, the actual data transfer is the main bottleneck and the cost of (de)compressing the data becomes less signicant
### Result Set Serialization