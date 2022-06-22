* It's best practice that every container should do one main thing
  * So if you have a node application that needs a database, it's recommended that you dockerize the node app and dockerize the database separately

* Container to website (or API) communication
  * Might want to send HTTPS requests from your container to the world wide web
  * Works by default

* Container to local host machine communication
  * Want to talk to some web service or local database that may live on our local machine
  * Service that is not in a container
  * `host.docker.internal` -> special domain recognized by Docker and is translated to the IP address on your machine as seen inside the Docker
    * Can be used anywhere where you need a address to your local machine 

* Container to container communication
  * A container that's running some service where your other container wants to talk to it
  * You can contact container A from container B using container A's IP Address
    * To get the IP Address, use `docker container inspect [container]` under **Networks**
    * Not ideal because whenever the IP address changes, we would have to rebuild the image(s)

* Creating Container Networks
  * You can put multiple containers inside the same network using the `--network` option on the docker run command
  * Within a Docker network, all containers can communicate with each other and IPs are automatically resolved
  * Docker will not automatically create a network when using the `--network` option, you have to create it yourself
  * `docker network create [name]` -> Creates a docker internal network
  * If multiple containers are apart of the same network, you can just use the other container's name as the address
    * Example: say we have 2 containers: `favorites` and `mongodb`.
      * In your code, if you wanted the `favorites` container to talk to the `mongodb` container, you would need to include `mongodb://mongodb:27017/swfavorites` for the URL. In `mongodb:27017`, the `mongodb` part is the name of the container. In other words, if we named the container `my-database`, the url would look like `mongodb://my-database:27017/swfavorites`