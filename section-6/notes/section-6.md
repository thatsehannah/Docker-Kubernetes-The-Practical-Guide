* What is Docker Compose?
  * Tool that allows to replace multiple `docker build` commands and multiple `docker run` commands with one configuration file and a set of orchestration commands to build images, start and stop containers
  * With the one configuration file, you can share with anyone which can be executed with one command
  * Works better with multiple containers but can also work with single containers

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
      * `environment` -> will contain nested entries
        * For every environment volume, add a dash (`-`) and define the environment variable key-value pair
        * You can specify an environment file in the docker compose configuration
          * Use the `env_file` key and list the env file using the relative path from the docker-compose.yaml file
          * The environment variables defined in the file(s) will be read in in the container
    * You need a dash when you have a list of single values (`./env/mongo.env`) and you don't need a dash when you have a list of key-value pairs (`MONGO_INITDB_ROOT_USERNAME: elliot`)

* No need to specify whether to remove the container on stoppage because by default, when you bring your services down, it automatically removes the containers

* We can specify the detached mode when we start our services

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

