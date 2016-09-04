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
