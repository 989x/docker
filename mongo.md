### resource

MongoDB inside Docker Container
- https://www.youtube.com/watch?v=uklyCSKQ1Po

docker hub - mongo
- https://hub.docker.com/_/mongo

### solve

mongo: command not found on mongodb 6.0 docker container [duplicate]
- https://stackoverflow.com/questions/73582703/mongo-command-not-found-on-mongodb-6-0-docker-container

</br>

### set up docker mongo

```bash
docker pull mongo:latest

docker images

mkdir mongodb-local

cd mongodb-local

docker run -d -p 2717:27017 -v ~/mongodb-local:/data/db --name mymongo mongo:latest

docker ps
```

### usage

```bash
docker exec -it mymongo bash

root@a...1:/# mongosh
	
show dbs

use test

db.user.insert({ "name":"truly mittal" })

db.user.find()
```




