## Common Path
* read path: read cache -> cache miss read sql -> write to cache
* write path:write db -> delete key
## reduce load read data from database when cache miss
lease resolve problems
1. stale set: set a value which doesn't reflect the latest value
2. thundering herd: heavy write and read activity
memcache pool used to split hot key and cold key, hot key may in pool, cold key may in another pool
replication within pool: high throughput whole system
## Handling Failures
have an extra pool to host some failed key. for example, when one server contain hot key, then the response may failed, so the client will try to get result from extra pool, so the failure will decrease.
## In a Region: Replication
increase web server and memcache is not enough when demand increase.
hot key will become more popular when web server increase.
## Regional Invalidations
add layer call mcsqueal to monitor sql database, when change send delete request to mcrouter, mcroute unpack message and send to real memcache server.
## Region Pool
cluster independently cache data depending on request. if request is radnomly route, so every front cluster will cache the same data. so the memory is inefficient for large, rarely accessed items. so the challenges of scaling memcache is decide how to replicated across all frontend cluster.
## cold cluster warmup
web server in the cold cluster, will try to get data from memcache, if miss , refetch data from warn cluster instead of from persist storage.
so exist inconsistencies. for example, web server in cold cluster update database, and update operation haven't been received in warm cluster. and then when web server try to fetch data from cold cluster, cache miss, it will get data from warm cluster, so it is stale value. solution is make delete toke available 2 second(for example), so even cache missing, when it try to add data which get from warn cluster will failed, and re-fetch from database. 
## Across Regions: Consistency
One region consist of a storage cluster and several frontend clusters. one region hold an master database and another region hold an read-only replicas. so key issue is how to maintaing consistency between data in memcache and the persistend storage. and stem from replica database may lag behind the master database.
### Writes from a master region
i if web server send invalidate to backup region's memcache,  the request may arrive before the data have been replicate from the master region. 
use remote marker to resolve, step 1. set remote marker rk 2. update master database 3. delete k in local region. so when fetch k, cache miss, and the look up remote marker rk, exist get data from remote database, otherwise local database.