This example I am taking reference from [Docker Compose of MongoDB](https://medium.com/faun/managing-mongodb-on-docker-with-docker-compose-26bf8a0bbae3
) example to setup the docker compose of the creation of mongodb service.

It has two files:
* init-mongo.js
* docker-compose.yml

### init-mongo.js
This file contains the create the username and password for the database while initlize the mongodb container.

```json
db.createUser ({
user: "myuser",
pwd: "mypass",
roles: [{
        role: "readWrite",
        db: "mydb"
}]
})
```
### docker-compose.yml
This file contains the configuration of service creation of mongodb.

```yml
version: '3'
services:
  mongodatabase:
    image: mongo
    container_name: my_cloud_mongodb
    environment:
      - MONGO_INITDB_DATABASE=mydb
      - MONGO_INITDB_ROOT_USERNAME=myuser
      - MONGO_INITDB_ROOT_PASSWORD=mypass
    volumes:
      - ./init-mongo.js:/docker-entrypoint-initdb.d/init-mongo.js:ro
      - ./mongo-volume:/var/db/mongodb
    ports:
      - '27017-27019:27017-27019'
```

#### Explain the details:
* ```version``` is a version of docker-compose file format, you can change to the latest version
* ```database``` on line 3 is just a service name, you can change the name whatever you want
* ```image``` must be mongo, because you want to create a container from mongo image
* ```container_name``` is a name for your container, it’s optional
* ```environment``` is a variables that will be used on the mongo container
* ```MONGO_INITDB_DATABASE``` you fill with a database name that you want to create, make it same like init-mongo.js
* ```MONGO_INITDB_ROOT_USERNAME``` you fill with username of root that you want
* ```MONGO_INITDB_ROOT_PASSWORD``` you fill with password of root that you want
* ```volumes``` to define a file/folder that you want to use for the container
* ./init-mongo.js:/docker-entrypoint-initdb.d/init-mongo-js:ro means you want to copy init-mongo.js to /docker-entrypoint-initdb.d/ as a read only file. /docker-entrypoint-initdb.d is a folder that already created inside the mongo container used for initiating database, so we copy our script to that folder
./mongo-volume:/data/db means you want to set data on container persist on your local folder named mongo-volume . /data/db/ is a folder that already created inside the mongo container.
* ```ports``` is to define which ports you want to expose and define, in this case I use default mongoDB port 27017 until 27019

### Execution Command
```$ docker-compose up```
