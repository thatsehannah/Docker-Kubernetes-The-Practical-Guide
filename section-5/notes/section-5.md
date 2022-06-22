* To start a MongoDB container, simply run the `docker run mongodb` command and that will spin up a container
  * Add the necessary flags and options (`--rm`, `-t [name]`, `-d`) if needed

* For working with React containers, you would need to run the container in interactive mode (`it`). This will show you the development server.
  * Also don't need to run in detached mode (`-d`) 

* Using container names in React code when referencing a container in the same network is not ideal because the Javascript (or TypeScript) code is being ran in the browser. So, for example, if we have this block of code here -> `await fetch('http://goals-backend/goals/'` where `goals-backend` is the name of the container where our backend is being ran in, it will not be able to resolve the container name. The only thing that is being ran in the container is the development server; the browser doesn't know about the container that the development server is running in.
  * Therefore, the correct code would still be `await fetch('http://localhost/goals/'`
  * In order for this to work however, we would have to publish the port from the backend container when we run it.
  * Also, since the React project only runs in the browser, we don't need to add it to the network that the backend and mongodb containers are on.

* 