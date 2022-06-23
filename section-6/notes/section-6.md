* What is Docker Compose?
  * Tool that allows to replace multiple `docker build` commands and multiple `docker run` commands with one configuration file and a set of orchestration commands to build images, start and stop containers
  * With the one configuration file, you can share with anyone which can be executed with one command
  * Works better with multiple containers but can also work with single containers

* Docker Compose does not replace Dockerfiles for custom images

* Docker Compose does not replace images or containers

* Docker Compose is not suited for managing multiple containers on different machines

* Writing Docker Compose Files
  * Services (containers)
    * Below every container, you define which port should be published, environments, volumes, networks
    * Basically everything you can do in the terminal