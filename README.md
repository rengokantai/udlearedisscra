#### udlearedisscra
#####2
######Thinking
Different
- Traidition db: scan entire table or scan entire index
- Redis - Direct data retrieval commands
- No internal query engine
- You decide how to store and retrive data  
######General security
- specified commands can be disabled or renamed
- normal users should not be able to run CONFIG or FLUSHALL  
- To disable, rename to empty string.
```
rename-command CONFIG 12345678
rename-command CONFIG ""
```
######quiz
redis is data structure server.

#####4
######kv pairs
MSETNX:
- will not perform if even a single key already exists  
MGET:  
- mget a b
- if a key is missing it still return other keys  
GERRANGE:
- getrange stringname 0 -1 //entire  
RENAME
- rename key newkey  
RENAMENX
- renames key to newkey if newkey does not exist  
GETSET
- set key and return old value  //can be used with INCR for an automatic reset


SETEX
- give a timeout.
- setex k 10 "v" //same as set k "v", expire k 10  

PSETEX /
- in milliseconds
- setex k 1000 "v"
- PTTL, same as TTL(millisecond)

PERSIST
- remove expire on a key
- persist k

######SCAN and MATCH
SCAN Guarantees
- Full iterations will retrive all elements that were present in the collection from the start to the end
- Never returns any element that was not present in the collection from start to finish  
COUNT
- default count=10 ,we can overrite: SCAN COUNT 20  

MATCH
- match a pattern specified
- SCAN 0 MATCH k*  

KEYS pattern(should be avoided in prod env)  

demo:
```
mset k1 "1" k2 "2" k3 "3" k4 "4" k5 "5" k6 "6" k7 "7" k8 "8" k9 "9" k10 "10" k11 "11"
```
then
```
scan 0
scan 4 count 3
scan 0 match key1*
```
list all keys
```
KEYS *
```
random
```
RANDOMKEY
```
######Client and comfig commands
examples
```
config get port
config get *
config get *max-*-entries*
```
set 
```
config set k "v"
````
INFO
```
INFO server
INFO clients
INFO memory
INFO persistence
INFO stats /replication/cpu/commandstats/cluster/keyspace/all/default
```
config resetstat
```
CONFIG RESETSTAT
```
COMMAND  /  COMMAND INFO  
- returns details about all redis commands
```
COMMAND INFO GET
```
CLIENT LIST
- returns info and stats on the clients connected to server 
```
CLIENT LIST 
```
```
CLIENT SETNAME name  //set this client name
CLIENT GETNAME //returns the name of the current client connection
CLIENT KILL ip:port
CLIENT KILL id
```
#####Data types
######List
```
LRANGE k 0 -1
```


######Set
test is member
```
SISMEMBER k "v"
```
list all members
```
SISMEMBERS k
```
return length
```
SCAD k
```
SMOVE (move v from k1 to k2)
```
SMOVE k1 k2 "v"
```
combine
```
SUNION k1 k2
```
return random member of set
```
SRANDMEMBER k 3
```
pop random number of nembers
```
SPOP k  //pop 1 item
SPOP k 3 //pop 3 items
```
######sortedset
- score is required
- must be float/integer  
- 1=1.0  
mind the parameter order.
```
ZADD k 100 "v"
```

list all elements[stackoverflow](http://stackoverflow.com/questions/11504154/get-all-members-in-sorted-set)  
from low to high
```
zrange k 0 -1
```
byscore
```
ZRANGEBYSCORE k 100 200
```
ZCARD,ZCOUNT
```
ZCARD k
ZCOUNT k(100,200)
```
ZRANK
```
ZRANK k "v"
ZREVRANK k "v"
```
add zcore for element in sortedset
```
ZINCRBY k 10 "v"
```
zscore
```
ZSCORE k "v"
```
######hash
HKEYS,HVALS,HGETALL(k and v), HINCRBY  (param order is diff from ZINCRBY!)
```
HINCRBY k v 1
```
HSTRLEN (get value string length)
```
HSTRLEN k v
```
######quiz
What is the default sort order of a list? - By insertion
When deleting a value from a hash using HDEL, what is returned? - The number of fields deleted

#####6
######persistence
persistence process
- client sends write command to database(client memory)
- database receives the write(server meomry)
- database calls system call that writes data on disk(kernel buffer)
- The OS transfers the write buffer to the disk controller(disk cache)
- disk controller writes to physical media (physical disk)  

pools - multiple redis servers running on the same machine using diff ports
- more cpus
- better tuning
- efficient memory usage

replication
- async
- multiple slaves
- connection from other slaves
- non blocking on slave side
- data redundancy
- slave read-only  


replication process
- Master starts saving in the bg and starts buffering all new commands that will modify the dataset
- After bg saving the master transfers the database file to the slave
- the slave saves the files to the disk and loads it into memory
- the master sends all buffered commands to the slave  


persistence options
- RDB - point in time snapshots
- AOF - write operation logging
- disabled
- Both RDB and AOF

######21 RDB snapshot
RDB: Redis Databse file
- simplest persistence mode
- enabled by default!
- single-file point-in-time representation
- use snapshots  

Advantages
- easy to use
- compact
- perfect for backup and recovery
- maximize redis performance
- allows faster restarts with big datasets acmpared to AOF  

snapshotting
- controlled by user
- can be modified at runtime
- snapshots are produced as .rdb files
- SAVE and BGSAVE commands  


SAVE, BGSAVE:
```
SAVE
```
Performs sync save, producing point-in-time snapshot of all data inside of the redis instance in the form of an .rdb.  
Should not be called in prod as it will block all other clients.
```
SAVE 60 1000
```
save interval 60 sec,if at least 1000 keys canged  

RDB disadvantages
- Limited to save points
- Not good if you want to minimize the chance of data loss if redis stops working
- Needs to fork() often which can be CPU perf, time consuming  

######22 AOF
Append only file
- Main option
- Every operation gets logged
- Log is the same format used by clients
- can be piped to another instance
- dataset can be reconstructed  

AOF rewrite
- used when AOF file gets big
- rewrite database from scratch
- directly access data in memory
- no need for disk access
- once written, the temp file is synced on to disk  

fsync options
- no fsync(done by os, every 30s)
- every second(default)
- every query(slow)  

AOF advan
- mush more durable
- single file with no corruption
- automatically reerite in the bg if it get too bug
- easy to undestand log/instructions  

AOF disadvan
- takes longer to load in memory on server restart
- usnslly bigger than the equi RDB files
- can be slower then RDB depending on the fsync policy
- bugs  


######23 RDB AOF in action
```
apt install -y rdiff-backup
```
backup (the dest dict must not exist)
```
rdiff-backup --preserve-numerical-ids --no-file-statistics /var/lib/redis  /home/ke/redis
```
change mode
```
redis-cli
BGREWRITEAOF
```
change config
```
appendonly yes
```
