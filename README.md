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
- set key and return old value  


SETEX
- give a timeout.
- setex k 10 "v" //same as set k "v", expire k 10  

PSETEX /
- in milliseconds
- setex k 1000 "v"
- PTTL, same as TTL(millisecond)
