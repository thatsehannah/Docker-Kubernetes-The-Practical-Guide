* To create an image, create a file called `Dockerfile` (without an extension)
    * Dockerfile describes to Docker how our container should be configured by adding instructions (more on this later)

* To build an image from the Dockerfile, in your terminal, navigate to the directory where the Dockerfile lives, and run `docker build .`

* We can run a container based on the image build with the Dockerfile by executing the `docker run [image]` command, where [image] is the ID of the image (could also use an alias maybe?)
    * If our container has a port we want to communicate to, we need to publish that port on the container. Add the `-p` flag on the docker run command and provide the following: `[host-port]:[container-port]`
      * For example: `docker run -p 3000:80 [image]`
        * Assuming we exposed port 80 in our Dockerfile
        * If we visit localhost:3000 on our machine, we will be able to access the application from our docker container
      * This is useful if we want to sent HTTP requests to an application running in a container
      * By default, there's no connection between container and host OS

* To stop a container from the terminal, first run `docker ps` command which will bring up a list of docker containers, grab either the CONTAINER ID or the NAMES value, then run `docker stop [container-value]`

* A container is another process on your machine that has been isolated from all other processes on the host machine. When running a container, it uses an isolated filesystem that is provided by a container image.
    * The image must contain everthing needed to run an application * all depenedencies, configuration, scripts, binaries, environment variables, etc.

* Container * The running unit of software; the thing that you run in the end.

* Images * The templates / blueprints for containers. The images contain the code and required tools to execute the code.
    * You use images to create multiple containers

* Two ways of getting an image to run a contaienr
    * Use an existing, pre-built images
      * Colleague built it already OR official images from Docker Hub (hub.docker.com)
      * Example
        * `docker run node` -> will use the node image from Docker Hub to create a container based on the image
          * In this case, this will run the Node interactive shell
        * More than likely, the image won't be available locally on your machine when you first execute this command. So, the image will then be pulled from Docker Hub automatically, in which the image will then be downloaded locally onto your machine.
      * Running `docker run [image]` creates a new container. You can check by running this command: `docker ps -a`
        * "ps" -> processes
        * "-a" -> flag that shows you all the processes (containers) Docker created for us

* Executing docker run doesn't really do much because, for example, when docker run node, the interactive shell that is exposed by node isn't automatically exposed by the container to us.

* `docker run -it [image]`
    * We're telling docker we want to expose an interactive session from inside the container to our hosting machine

* We can have multiple containers that are based on the same image running at the same time.

* Creating your own, custom image requires writing your own Dockerfile, which will be based off another image.

* Dockerfile contains the instructions that we want to execute when we build our own image.

* Start Dockerfile with the `FROM` instruction
  * Allows you to build your image based on anther base image

* `COPY` instruction
    * Tells Docker which files that live on our local machine should go into the image
    * You need to specify 2 paths:
      * First path is the path outside of the image where the files live that should be copied into the image
      * Second path is the path inside of the image where those files should be stored. Every image (thus container as well) has its own internal file system which is detached from your file system.
        * It's not recommended to use the root folder in your container to copy the files to. Use a subfolder (e.g. /app)
        * `COPY [FROM HOST FILE SYSTEM] [TO IMAGE FILE SYSTEM]`
      * Example
        * COPY . /app
          * By specifying just a dot for the first path, this tells Docker to copy the files from the same folder that contains the Dockerfile (current directory)
            * Excludes the Dockerfile
          * With this, all folders & subfolders that are in the same folder as the Dockerfile will be copied to an "app" folder in the container
      * If you specify a folder to copy the files to that doesn't exist yet in the container, it will be automatically created.

* By default, all commands will be executed in the working directory of your image/container, which, by default, is the root folder folder in the image/container file system.

* To specify what directory to execute certain commands in, set the `WORKDIR` instruction to that directory
    * Any subsequent commands that follow this instruciton will be executed from inside the directory you specified.
    * Example
      * `WORKDIR /app`  
        `COPY . .`  
        `RUN npm install`  
        * Here, we're setting the working directory to `/app`, which is where the npm install command will be ran at.
    * If the directory specified doesn't exist, it will automatically be created.

* `RUN` instruction
    * Tells Docker to execute commands inside the image
    * Normal to install dependencies here, such as npm install, for when the image is created.

* `CMD` instruction
    * Executes a command not when an image is created, but when a container is started based on the image
    * Takes an array of strings that is composed of each piece of the command split into separate strings
    * Example: if we want to start a node server when a container is created
      * `CMD ["node", "server.js"]`
    * The first string should be the executable, and the following string[s] should be the parameters.
    * If no CMD instruction is specified, the CMD of the base image will be executed by default.

* Since Docker containers are isolated from our local environment, if we're exposed a port inside of it, we won't be able to access it from our local machine without a specific instruction.

* `docker build [path-of-Dockerfile]`
    * Allows you to create an image based on the instructions in the Dockerfile
    * Specify the path where Docker is able to find the Dockerfile
      * If you specify a dot, you're telling Docker that the Dockerfile is in the same directory that we're running the command in.

* `EXPOSE` instruction
    * Lets Docker know that when a container is started, we want to expose a certain port to our local system
    * Doesn't really open up the port to our local machine. It's mainly for documentation purposes; good practice to add to Dockerfile to document which ports will be exposed by your container, but more needs to be done.

* To actually expose a port, you will do so when executing the docker run command. Specifically, add the `-p` flag to the docker run command.
    * `docker run -p [port-on-host-machine]:[port-in-container] [image]`
    * "-p" stands for publish
    * Tells Docker which port on your host machine that the port exposed inside the container should be accessible on.
    * Example: say we've exposed port 80 inside of our container
      * `docker run -p 3000:80 [image]`
      * We can now access the container's port 80 from our local machine's 3000 port

* For all docker commands where an ID can be used, you don't have to use the full ID; you can also use the first few characters, enough for it to be unique from other containers
    * Example: if you have an image ID of abcdefg
      * Instead of `docker run abcdefg`, you could run `docker run abc` and target the same image

* Images (and all of the files in them) are read-only once they're built. Once you've copied your files from your local to the image, whatever you do to those files locally does not affect the image's version of the files. By default, to have changes in yoour local files be reflected in the image, you will have to build the image again by running `docker build .` .

* Images are layer-based, which means that when you build or rebuild an image, only the instructions that we changed and any subsequent instructions are re-evaluated.
    * So if you changed some code in a file, the COPY instruction and every instruction after that will be re-evaluated.
    * When you don't make any changes and you rebuild the image, Docker will use the cache instructions for each instruction because Docker recognizes that the instructions from the previous build and the instructions from the current on haven't changed.
    * Whenever you build an image, Docker caches every instruction result and when you rebuild an image, Docker will use the cached results if there is no need to run an instruction again.

* Every instruction represents a layer in your Dockerfile except the CMD instruction.

* If you run a container based on an image, that contaienr adds a new extra layer on top of the image.

* For optimization purposes, if you have a file like package.json that will be copied over if you incldue the "COPY . /app" instruction, it would be best to extract that file to its own COPY instruction followed by RUN npm install instruction. You will then have the "COPY . /app" instruction follow after that.
    * Example
      * `COPY package.json /app`  
        `RUN npm install`  
        `COPY . /app`  
        `CMD ["node", "server.js"]`  
    * This will prevent Docker from running npm install when it doesn't have to. Like when we make a change to the soruce code. So effectively, npm install will only run when the image is first built or if there are any changes to the package.json file itself.

* `docker ps` command
    * shows all running containers
    * Adding the `-a` flag shows all containers you've ran in the past, including stopped containers

* If nothing changed about our image and we've stopped a container based on that image, there's no need to create a brand new container. We can restart the existing container.

* `docker start [container]` command
    * starts a stopped container
    * Provide either the CONTAINER ID or NAME
    * Container runs in the background, unlike when you run docker run
      * In other words, it's not blocking us in the terminal (node server specific example)

* Detached mode
    * Container runs in the background
    * Default for docker start command

* Attached mode
    * Container runs in the foreground
    * Default for docker run command
    * Means we're listening to the output of the container

* To run a container in detached mode, add the `-d` flag to the docker run command
    * Example
      * `docker run -d -p 8000:80 [image]`

* You can attach yourself to a detached container again by running `docker container attach [container]`
    * Or you can simply run `docker attach [container]`

* If you're in detached mode but want to view the logs of a container, you can run `docker logs [container]`
    * You can add the `-f` flag to enter follow mode, which is basically an alternate way to attach to the container and listen to the output of the container.
    * Example
      * `docker logs -f [container]`

* You can attach to a container that you started in detached mode without restarting the container itself; just run the attach command on the running container.

* To find images that would be necessary to build your image on, visit Docker Hub.

* By default, docker run attaches us to the container so we can listen to output printed by the container, but we're not attached to it to where we can enter anything into the application running in the container.

* "-i" and "-t" flags on docker run
    * `-i` -> allows you to launch the container in interactive mode (even when not in attached mode). Means we'll able to input something in the container.
    * `-t` -> creates a terminal to input things in
    * Combining these two flags allows the container to listen for our input and we'll get a terminal exposed by the container.
    * Example
      * `docker run -it [image]`

* Run a stopped container in attached mode if you need to interact with it combined with the "-i" flag
    * Example
      * `docker start -ai [image]`

* Docker is not just for web apps; it can be used to Dockerize simple utility applications which need input and provides some output.

* Deleting Images & Containers
    * `docker rm [container]` -> removes container
      * Make sure container is stopped before attempting to remove it
      * You can remove multiple containers at once; just separate them witha space between
        * Example
          * `docker rm [container] [container] [container]`
      * You can remove all stopped containers at once by running the `docker container prune` command
    * `docker rmi [image]` -> removes image
      * To list all images, run the docker images -a command
      * You can only remove images if they're not being used by any contaienr anymore, including stopped containers. Therefore, you must remove contaienrs that use the image BEFORE removing the image itself.
      * You can remove all images which aren't being used in contaienrs by running the `docker image prune` command
      * You can remove multiple images at once by separating them with a space
        * Example
          * `docker rmi [image] [image] [image]`

* To automatically remove a container after it stops, run the docker run command with the `--rm` flag
    * Example
      * `docker run -p 8000:80 --rm [image]`
      * Once you stop this container and run the `docker ps -a` command, the container won't appear
    * Useful for web apps where frequent changes are made and one container may not ever be used again

* `docker image inspect [image]` command
    * Gives detailed information about an image
    * Can find full image ID
    * Can find the date the image was created
    * Can find container configuration, such as ports that will be exposed, environment variables, and entrypoint.
    * Can find which docker version is used
    * Can find the operating system that's being used in the container
    * Can also find the layers of the image
      * The layers from the base image are included as well

* `docker cp [source-path] [dest-path]`
    * Allows you to copy files or folders into a running container or out a running container
    * The source-path is the folder or file we want to copy
      * When copying a folder, you can specify a file in that folder or, to copy everything, provide a dot.
        * Example
          * `docker cp dummy/test.txt` OR `docker cp dummy ./`
    * The dest-path is the path you want to copy to
      * You will need the container name, then a colon, then the path inside the container you want to copy to.
    * Example
      * `docker cp dummy/test.txt [container]:/test`
    * Useful for adding soemthing to the container without restarting the container and rebuilding the image, such as configuration files for a web server
    * Useful for, if your container generates log files, copying these log files from the container to the local machine

* To give your container its own name, run the docker run command with the `--name` option followed by the name of your choice.
    * Example
      * `docker run --name goalsapp [image]`
    * Makes it easier to stop the container without having to run docker ps to find the CONTAINER ID or NAMES

* You can also name your images by giving it a tag. Tags consists of two parts
    * The name, also called repository, of your image
    * The tag, which is a specific version of that image
    * With name, you can define a group of images. With tags, you can define a specialized image with that group
    * Tag is optional
    * Both name and tag are separated by a colon
    * Example: in the Dockerfile
      * `FROM node:12`
        * Here, you're specifying that you want to use the specific 12 tagged version of the node image
        * node is the specialized group of images, and 12 is the tag (more than likely the version) of the specialized image
    * To give your own image a name (and an optional tag), provide the `-t` option followed by the name of your choice when running docker build command
      * Make sure to specify the `-t` flag before giving the path of the Dockerfile
      * Example
        * `docker build -t goals:latest .`

* To remove all images, including tagged images, run the `docker image prune -a` command

* Two ways to share an image
    * Share the Dockerfile and source code that belongs to an application to build the image
      * Will have to run docker build
    * Share complete built image
      * Just download the image and run a container based on it.
      * No build step required; everything is included in the image already 
      * Can push these images to Docker Hub or any Private Registry of your choice
        * Docker Hub is free for personal use

* `docker push` command
    * Pushses an image to Docker Hub (or to private registry when specified)
    * If using Docker Hub, when you create a new repository (a.k.a image), you have to create it locally in order to push it
    * Example
      * `docker push thatsehannah/node-hello-world`
        * This will push the image tagged with thatsehannah/node-hello-world to Docker Hub

* If you haven't created the image you want to push already, you can just run `docker build -t [tag-name-from-docker-hub]`

* The tag name consists of the name you gave the repository (a.k.a image) prefixed with your Docker ID, separated by a slash.
    * Example: `thatsehannah/node-hello-world`
      * thatsheannah is my Docker ID, node-hello-world is the repository

* If we've already built the image we want to push to Docker Hub, we can rename it and retag it.
    * Use the `docker tag` command, which takes two arguments. First arg is the old name. Second arg is the new name.
    * Example
      * `docker tag node-app thatsheannah/node-hello-world`
    * When you rename an image, you don't replace of the old image, you're basically cloing it with a different name.

* Before you push an image to Docker Hub, you must first login from the command line using the `docker login` command, in which you just put in your username & password

* To download an image from Docker Hub onto your machine, simply run the `docker pull` command
  * Example
    * `docker pull thatsehannah/node-hello-world`

