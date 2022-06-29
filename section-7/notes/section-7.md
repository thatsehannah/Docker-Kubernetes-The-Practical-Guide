- Utility Containers
  - Containers that only have an environment in them
  - They don't start an application, but you run them in conjuction with some command to execute a certain task
  - You can set certain projects up without installing its dependencies first
  - Useful to mirror your container's file system to your own file system for when you're working with dependencies you might not want to install on your machine.
    - Of course, you will have to use a bind mount to do so

- `exec` command
  - Allows you to execute certain commands inside a running container
  - Besides the command specified in the Dockerfile
  - Needs the `-it` flag to provide input
  - Example
    - `docker exec -it [container] [command]`
  - Can be used, for example, to read log files in a running docker container
  
- We can override the initial command that's specified in the CMD instruction in the Dockerfile in the `docker run` command
  - Example
    - `docker run [image] [command]`

- alpine images are smaller, more lightweight version of images
  - Example
    - node:14-alpine is a slimmer version of the node image

- `ENTRYPOINT` instruction in the Dockerfile
  - Whatever you enter after the docker run is appended after what you put in the entrypoint value
  - Example
    - `ENTRYPOINT ["npm"]`
    - This means that we can run `docker run -it node install` and this will be equivalent to running `npm install` inside of the container

- `docker-compose exec`
  - Run commands in an already running container (which were created by docker compose)

- `docker-compose run`
  - Allows us to run a single service from our docker-compose yaml file by the service name followed by a command that should be appended after our `ENTRYPOINT` instruction
  - Example: if we have `ENTRYPOINT ["npm"]` defined in the Dockerfile
    - `docker-compose run npm-service init`
    - This will run `npm init` in the container
  - You can add the `--rm` flag to this command to remove the container once it's done executing the command you give it
  - You can simply run this command for a single service, i.e. a utility container, so that you 