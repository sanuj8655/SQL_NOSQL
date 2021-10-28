
# Redis
* terminal> redis-cli

```
PING

INFO

CONFIG GET *

CONFIG GET bind

CONFIG GET loglevel
```

* Strings
```
KEYS *

SET dbda_01_name "James Bond" 
SET dbda_01_batch "Sep 2020"
SET dbda_01_email "james@bond.com" 
SET dbda_01_image "imagedata"

GET dbda_01_batch
GET dbda_01_email

KEYS *

DEL dbda_01_batch

KEYS *

GET dbda_01_batch
```

* Lists
```
LPUSH hobbies Action
LPUSH hobbies Adventure Travel
RPUSH hobbies Sports

LRANGE hobbies 1 3
LRANGE hobbies 0 -1

LPOP hobbies
RPOP hobbies

LRANGE hobbies 0 -1

RPUSH hobbies Adventure

LRANGE hobbies 0 -1
```

* Sets
```
SADD fruits Orange

SADD fruits Mango Watermelon Grapes

SMEMBERS fruits

SADD fruits Mango

SMEMBERS fruits

SISMEMBER fruits Mango

SISMEMBER fruits Banana
```

* Sorted Sets
```
ZADD friends 2 Sameer

ZADD friends 4 Rahul

ZADD friends 6 Soham 1 Vishal 3 Nitin

ZRANGE friends 0 -1

ZRANGE friends 0 -1 WITHSCORES

ZPOPMIN friends

ZRANGE friends 0 -1

ZREM friends Rahul

ZRANGE friends 0 -1
```

* Hashes

```
HMSET person_1 name "Bill Gates"

HMSET person_1 age 60

HMSET person_1 address "USA"

HMSET person_1 company "Microsoft"

HGETALL person_1

HMGET person_1 name

HMGET person_1 company

KEYS *
```

* Publisher/Subscriber using Redis

```

```

* Transactions

```
MULTI

SET dbda_2_name "Sherlock"

SET dbda_2_address "England"

GET dbda_2_address

EXEC

KEYS *

MULTI

SET dbda_3_name "Harry"

GET dbda_3_name

DISCARD

KEYS *
```
