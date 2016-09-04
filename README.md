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
