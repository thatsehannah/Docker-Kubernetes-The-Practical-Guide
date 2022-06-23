* To start a MongoDB container, simply run the `docker run mongodb` command and that will spin up a container
  * Add the necessary flags and options (`--rm`, `--name [name]`, `-d`) if needed

* For working with React containers, you would need to run the container in interactive mode (`it`). This will show you the development server.
  * Also don't need to run in detached mode (`-d`)

* If you have 2 containers, container A and container B, and container A needs to talk to container B, you can reference container B's name in your code in container A
  * Example
    * `mongodb://mongodb-container:27017/course-goals?authSource=admin`
  * Just make sure the referenced container is running already

* Using container names in React code when referencing a container in the same network is not ideal because the Javascript (or TypeScript) code is being ran in the browser. So, for example, if we have this block of code here -> `await fetch('http://goals-backend/goals/'` where `goals-backend` is the name of the container where our backend is being ran in, it will not be able to resolve the container name. The only thing that is being ran in the container is the development server; the browser doesn't know about the container that the development server is running in.
  * Therefore, the correct code would still be `await fetch('http://localhost/goals/'`
  * In order for this to work however, we would have to publish the port from the backend container when we run it.
  * Also, since the React project only runs in the browser, we don't need to add it to the network that the backend and mongodb containers are on.

* For data to persist in the mongodb container in the event of stopping or removing the container, we should set up a named volume when running the mongodb container
  * Example
    * `docker run --name mongodb -v data:/data/db --rm -d --network [network] [image]`
      * Remember: `data:` is the name of the volume that will be created, `/data/db` is where mongodb writes its data files by default

* For security reasons, we should include these 2 environment variables in the `docker run` of the mongodb image command to limit access to our mongodb database: `MONGO_INITDB_ROOT_USERNAME` & `MONGO_INITDB_ROOT_PASSWORD`
  * You will set them to whatever username and password you would like.
    * Example
      * `docker run --name mongodb -v data:/data/db --rm -d --network [network] -e MONGO_INITDB_ROOT_USERNAME=elliot -e MONGO_INITDB_ROOT_PASSWORD=secret [image]`
  * In the mongodb connection string in your node application, you will have to include that username and password to gain access to the database.
  * Make sure that this is appended to end of the connection string as well -> `?authSource=admin`
    * Example
      * `* mongodb://elliot:secret@mongodb:27017/course-goals?authSource=admin`
  * It's best practice to set environment variables to these username and password values in the Dockerfile