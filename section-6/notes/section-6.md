* What is Docker Compose?
  * Tool that allows to replace multiple `docker build` commands and multiple `docker run` commands with one configuration file and a set of orchestration commands to build images, start and stop containers
  * With the one configuration file, you can share with anyone which can be executed with one command
  * Works better with multiple containers but can also work with single containers
  * Mainly solves the repitition of writing of cumbersome docker commands

* Docker Compose does not replace Dockerfiles for custom images

* Docker Compose does not replace images or containers

* Docker Compose is not suited for managing multiple containers on different machines

* Writing Docker Compose Files
  * Indent twice because yaml uses indentation to express dependencies
  * Services (containers)
    * Below every container, you define which port should be published, environments, volumes, networks
    * Basically everything you can do in the terminal
  * You will create a `docker-compose.yaml` file to begin with docker compose
    * Make sure key names are spelled correctly
    * Specify a version key initially, which is the docker compose specification version
      * Determines the syntax, features that are available
      * Visit here to stay up to date -> https://docs.docker.com/compose/compose-file/compose-versioning/
    * Next, specify the services key
      * Takes nested values
      * Needs at least one child element
      * The children elements will be the containers
      * Choose a name of your choice for your containers
      * `image` -> name of the image you want to use
        * First configuration defined under the container
      * `volumes` -> will contain nested entries
        * For every volume, add a dash (`-`) and define the volume like you would for the `docker run` command
        * For named volumes used in your services, you will first need to add a `volumes` top level key (same level `services`) and add the named volumes there
          * Syntax is the name of the named volume followed by a colon and nothing after the colon
          * Example
            * version: '3.8'  
              services:  
                  [...]  
              volumes:  
                  data:  
          * Different containers can use the same volume
          * Anonymous volumes and bind mounts don't need to be specified
        * For bind mounts, instead of using the absolute path like you would in the `docker run` command, you can use the relative path relative to the docker-compose file
      * `environment` -> will contain nested entries
        * For every environment volume, add a dash (`-`) and define the environment variable key-value pair
        * You can specify an environment file in the docker compose configuration
          * Use the `env_file` key and list the env file using the relative path from the docker-compose.yaml file
          * The environment variables defined in the file(s) will be read in in the container
          
* You need a dash when you have a list of single values (`./env/mongo.env`) and you don't need a dash when you have a list of key-value pairs (`MONGO_INITDB_ROOT_USERNAME: elliot`)

* No need to specify whether to remove the container on stoppage because by default, when you bring your services down, it automatically removes the containers

* No need to specify a network when using docker-compose, Docker will automatically create a new network for all of the services defined in the yaml file out of the box

* You can add your own network by specifying the `networks` key in the docker-compose file under your specified service

* `docker compose up`
  * Starts services with docker-compose
  * Run this command in the folder where docker-compose.yaml lives in your project
  * Will start the containers as well pull and build the images that will be required
  * Creates default network, the volumes, and containers
  * By default, starts in attached mode
  * To start in detached mode, add the `-d` flag
    * Example
      * `docker compose up -d`

* `docker compose down`
  * Stops services with docker-compose
  * Removes all containers, default network
  * Does not delete volumes by default
    * Add the -v flag to delete volumes
      * Example
        * `docker compose down -v`

* Since docker compose replaces the **build** step, we can give it all the information it needs to build an image with the `build` key as a dependency to a service. 
  * Build option takes the relative path to the Dockerfile
    * Example
      * backend-service:  
            build: ./backend  

* You can also provide the build key with nested dependencies if, for example, your Dockerfile is named "Dockerfile-dev". You will have to provide 2 additional child dependencies to the build key.
  * `context` -> the path to the folder that holds your dockerfile
  * `dockerfile` -> specifies the file name
  * By default, if your Dockerfile is named is "Dockerfile", you won't have to use this alternative way
  * Example
    * backend-service:  
          build:
              context: ./backend
              dockerfile: Dockerfile-dev
  * There's another depenceny you can use: `args` for if you need to specify arguments defined in your Dockerfile

* For ports, just provide the `ports` key under the service and provide a single value list of the ports you need
  * Remember, single value ports use dashes
  * The value is a string, e.g. '3000:3000'

* `depends_on` key
  * If you want container A to be started before starting container B, you provide this dependency to let docker compose know that.
  * Takes a single value list for that specific service that lists the services that that service "depends on"
  * Provide the name of the service you chose
  * Example
    * mongodb:  
        image: [...]  
      backend:  
        build: ./backend  
        depends_on:  
            - mongodb  

* The service names that are defined in your docker compose file can be used throughout your code when you need to talk to those containers.
  * Example: if one of the services you defined in your docker compose file was named `mongodb-service`
    * `mongodb://mongodb-service:27017/course-goals?authSource=admin`

* `stdin_open` and `tty` keys
  * Same as the `-it` flag on the docker run command; when you need an interactive mode
  * Set both of these to `true`
  * `stdin_open` opens standard input
  * `tty` attaches the terminal

* `--build` flag on `docker compose up`
  * If you want to force docker to rebuild the image(s)
  * By default, the image defined by the `build` key in the docker compose file will only be built once and cache it unless it is removed
  * Useful if you make changes to the Dockerfiles or any other code
  * Example
    * `docker compose up --build`
  * If you just want to build images and not start a container thereafter, just run `docker compose build`
  * If you provide this flag and nothing has changed, the image will not be rebuilt and Docker will use the cached layer

* `container_name` key
  * Used to assign custom names for the actual container name
  * The service name is not the container name

