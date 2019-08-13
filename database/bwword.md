## Base Node and Delta Chains
### Logic Node has two component
base node and delta china. inner base node holdes sorted key and nodeID(not point). leaf base node holds sorted key and value
### Map Table contains the logic entry for each node. 
Update it use simple CAS(Compare and swap) operation. if failed, break and restart
### Consolidation and Garbage Collection
if the delta china to long, will decrease the look up speed. so need do garbage collection. if delta china larger than some threadhold, will trigger gc. when do gc, copy value to private memory and then do action of delta china, and then replace the old node.
### Structural Modification, node split and merge. 
first do logic operation,  and then do physical operation. logic will create special delta
### Non-unique Key Support
contain collection of spresent and sdlete. traversal the delta china, if operation is delte and value is not in present collection, add it to delete collection. if action is insert and value is not in delete collection, add into present collection. 
### Garbage Collection
1. have an global epoch, every thread will have an private space, maintain an local epoch.
2. when do action, get global epoch, set this action epoch
3. when thread complete all action, thread get global epoch and set it to local epoch
4. trigger gc. gc thread will get the mini epoch from all thread local epoch, and then compare with it action epoch, reclaim all memory for action epoch which less than min epoch.
