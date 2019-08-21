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
* when the throughput decreases compression becomes more effective. When the throughput is low, the actual data transfer is the main bottleneck and the cost of (de)compressing the data becomes less signicant
## PROTOCOL DESIGN SPACE
* The protocol design space is generally a trade-off between computation and transfer cost.
* If computation is not an issue, heavy-weight compression methods are able to considerably reduce the transfer cost. 
* If transfer cost is not an issue (for example when running a client on the same machine as the database server) performing less computation at the expense of transferring more data can considerably speed up the protocol
### Protocol Design Choices
#### Row/Column-wise
* column-major formats will have advantages when transferring large result sets, as data stored in a column-wise format compresses signicantly better than data stored in a row-wise format
* The problem with a pure column-major format is that an entire column is transferred before the next column is sent. If a client then wants to provide access to the data in a row-wise manner, it rst has to read and cache the entire result set. For large result sets, this can be infeasible
#### Vector-based protocol
* chunks of rows are encoded in column-major format. To provide row-wise access, the client then only needs to cache the rows of a single chunk, rather than the entire result set
* As the chunks are encoded in column-major order, we can still take advantage of the compression and performance gains of a columnar data representation