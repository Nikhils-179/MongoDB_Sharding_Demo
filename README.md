



MongoDB sharding is a form of horizontal scaling.
Partioning of data is done on range based basic by defining a field shard keys.
Each set of the shard data is chunks. 
Loads balancer moves data based on the size of chunk


Sharding Process :

1. Config servers - small monoods where metadata of clusters are stored.
2. Mongos - client connects to view whole cluster as single entity.
3. Mongod - Process for data store.(shared fields)

mongoDB sharding setup.



1. Create Directory for Sharding Setup :

Mkdir MogoDB_Sharding_Demo
Cd MongoDB_Sharding_Demo 
Mkdir cfg0 cfg1 cfg2 //represents config servers
Mkdir a0 a1 a2 b0 b1 b2 c0 c1 c2 d0 d1 d2 // shared environments


2. Start Config Servers:
Config servers stores metadata and configuration settings for the cluster.

Mongod --configsvr --dbpath cfg0 --port 26050 --fork --logpath log.cfg0 --replSet cfg
Mongod --configsvr --dbpath cfg1 --port 26051 --fork --logpath log.cfg1 --replSet cfg
Mongod --configsvr --dbpath cfg2 --port 26052--fork  --logpath log.cfg2 --replSet cfg

- Login to first config server

Mongosh --port 26050

- Initate replication for the config server :
rs.initiate()
- Add the secondary nodes 26051 , 26052 on primary cfg0 server

Add the secondary nodes running on port 26051 , 26052 using rs.add()

- check status rs.status() // whether all the members are added 


3. Initiate shards

mongod --shardsvr --replSet a --dbpath a0 --port 26000 --fork --logpath loh.a0 
mongod --shardsvr --replSet a --dbpath a1 --port 26001 --fork --logpath loh.a1    
mongod --shardsvr --replSet a --dbpath a2 --port 26002 --fork --logpath loh.a2   
Mongod --shardsvr --replSet b --dbpath b0 --port 26100 --fork --logpath log.b0
mongod --shardsvr --replSet b --dbpath b1 --port 26101 --fork --logpath log.b1  
mongod --shardsvr --replSet b --dbpath b2 --port 26102 --fork --logpath log.b2  
Mongod --shardsvr --replSet c --dboath c0 --port 26200 --fork --logpath log.c0 
Mongod --shardsvr --replSet c --dboath c1 --port 26201 --fork --logpath log.c1 
Mongod --shardsvr --replSet c --dboath c2 --port 26202 --fork --logpath log.c2 
Mongod --shardsvr --replSet d --dboath d0 --port 26300 --fork --logpath log.d0 
Mongod --shardsvr --replSet d --dboath d1 --port 26301 --fork --logpath log.d1 
Mongod --shardsvr --replSet d --dboath d2 --port 26302 --fork --logpath log.d2 


-Multiple instances on same server
ps -ef | grep mongod|grep a0     (check the status of shard a0



4. Initiate replication in each of shard servers
a
-Login to a0 server (mongosh --port 26000)
-Initiate replication(rs.initiate())
-Add other two servers(rs.add("localhost:26001") (rs.add("localhost:26002")
b
-Login to b0 server (mongosh --port 26100)
-Initiate replication(rs.initiate())
-Add other two servers(rs.add("localhost:26101") (rs.add("localhost:26102")
c
-Login to c0 server (mongosh --port 26300)
-Initiate replication(rs.initiate())
-Add other two servers(rs.add("localhost:26201") (rs.add("localhost:26202")
d
-Login to d0 server (mongosh --port 26300)
-Initiate replication(rs.initiate())
-Add other two servers(rs.add("localhost:26301") (rs.add("localhost:26302")


5.Interaction point for clients to our shared env

mongos --configdb "cfg/localhost:26050,localhost:26052,localhost:26052" --fork --logpath log.mongos1 --port 26061 

mongos --configdb "cfg/localhost:26050,localhost:26052,localhost:26052" --fork --logpath log.mongos2 --port 26062 


6.login to mongos instance(on port 26061)
Mongosh --port 26061

7.Add multiple instances as shards 
Sh.addShard("a/localhost:26000")
sh.addShard("b/localhost:26100")
Sh.addShard("c/localhost:26200")
sh.addShard("d/localhost:26300")


8.sh.status() - different shards will be shown 
9.show dbs 
10.Add myDB as sharding
Sh.enableSharding("mydb")
11.sh.status -mydb will also be part of shared db
12.sh.shardCollection("mydb.sales"(collection name) ,shardKey{_id:1})
13.sh.status()
14.as data will grow in data will be distributed across multiple shards                                                           
