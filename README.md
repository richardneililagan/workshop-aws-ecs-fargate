Module 01: Create and Dockerize a Node.js web server
===

In this module, we'll walk through creating a very simple web server,
making sure it works, then wrapping that into a Docker image and running it
as a Docker container.

While it would be helpful if you're fluent with Node.js / Javascript,
no previous experience is required as all code will be provided for you.

---

## Implementation Overview

Each of the following sections provides an implementation overview,
as well as detailed step-by-step instructions you can follow to complete
the module. 

If you're already familiar with how to do the steps yourself,
the overview should give you enough information to do them.

If you need additional guidance, feel free to use the detailed instructions.

> **Note**: We'll just call the **AWS Management Console** 
> just the **console** from now on.


### 1. Create a Node.js web server

Feel free to use a different platform (e.g. `python`) if you're more comfortable
with it, and are confident you can continue with the lab using it.

#### High-level instructions

Create a web server that responds to requests to `/` with:

> Hello from `<< the server's HOSTNAME envvar >>`!

The `HOSTNAME` envvar should default to `local` if not set.

The server should listen on the port dictated by the `PORT` envvar.
If `PORT` is not set on the machine, it should default to `8080`.

<details>
  <summary><strong>Step-by-step instructions (expand for details)</strong></summary>
  <p>

  1. In your workspace, create a working directory for your application.
     ```
     mkdir my-app
     cd my-app
     ```
  2. Initialize a Node.js app by running `npm init -y`. This will create a `package.json` file in your directory.
  3. Install `express` and `dotenv-defaults` as app dependencies.
     ```bash
     npm install --save --save-exact express dotenv-defaults
     ```
  4. Create a file in your directory called `app.js` and open it in the IDE. Put in the following code:
     > **IMPORTANT:** Pay attention where single quotes (`'`) and backticks (`` ` ``) are used!
     ```javascript
     require('dotenv-defaults').config()

     const express = require('express')
     const app = express()

     // :: ---

     const { HOSTNAME, PORT } = process.env

     app.get('/', (request, response) => {
       const message = `Hello from ${HOSTNAME}!`
       response.send(message)
     })

     app.listen(PORT, () => console.log(`App server listening on port ${PORT}.`))
     ```
  5. Create a file in your directory called `.env.defaults` and open it in the IDE. Put in the following code:
     ```
     HOSTNAME=local
     PORT=8080
     ```
  6. Start the server with `node app`. You should get the message:
     ```
     App server listening on port 8080.
     ```
  </p>
</details>

To verify that your server is running, open a **second** terminal window, 
and `curl` the node server:

```bash
$ curl localhost:8080

Hello from ip-172-31-29-46!
```


### 2. Create a docker image containing your application

Docker images are blueprints of an separated environment that can run an application.
They're created using a set of step-by-step instructions in a manifest called a `Dockerfile`.

#### High-level instructions

Create a docker image of the node server you just created.

<details>
  <summary><strong>Step-by-step instructions (click to expand)</strong></summary>
  <p>

  1. Create a file in your directory called `Dockerfile` and put in the following code:
     > **Important:** use a node image base with the same version as your environment. Use `node --version` to check.

     > **Note:** [More information on how to write a Dockerfile here](https://docs.docker.com/engine/reference/builder/).

     ```Dockerfile
     FROM node:8.16

     WORKDIR /usr/src/app
     EXPOSE 8080

     # :: install app dependencies
     COPY package*.json ./
     RUN npm install

     # :: copy the rest of the source
     COPY . .

     # :: container default command
     CMD ["node", "app"]
     ```
  2. Create another file called `.dockerignore` and put in the following code:
     ```
     node_modules
     npm-debug.log
     ```
  3. On your terminal, build the docker image using `docker build -t <<namespace>>/server .`
     > **Note:** Docker images are normally categorized using namespaces --- think of this as a repository ---
     > and the actual image name. An example name could be `my-workshop/server` or `nadinelustre/app`.

     > **Important:** Don't forget the dot!
     ```bash
     docker build -t my-app/server .
     ```
  4. Verify your image exists by running `docker images`.
  </p>
</details>

### 3. Run a docker container using your new image

Docker containers are the running instances of a docker image. 
If a docker image is the blueprint, a docker container is the actual application.

You can run multiple containers from a single docker image,
and are only constrined by the amount of resources on the host machine.

#### High-level instructions

Run two copies of your docker image, mapped to two different host ports.
Verify that both are running and can be accessed.

<details>
  <summary><strong>Step-by-step instructions (click to expand):</strong></summary>
  <p>
  
  1. Run your new image, binding it to host port `8080`.
     > * The `--rm` flag ensures this container is deleted when it is stopped.
     > * The `-it` flag connects the shell inside the container into the current terminal you're in.
     > * The `-p A:B` flag pipes the host port `A` to the container port `B`.
     ```bash
     docker run -it --rm -p 8080:8080 <<namespace>>/server
     ```
  2. On another terminal verify the container is running using `docker ps`.
  3. Verify the container can be talked to using `curl localhost:8080`.
  4. Run **another** container, binding it this time to host port `3000`.
     > Try crafting the right command yourself! 🍕
  5. Again, in yet another terminal, verify that now 2 containers are running.
  6. Verify you can talk to the second container using `curl localhost:3000`.
  7. Stop the two containers by using `docker stop <container 1 ID> <container 2 ID>`.
  </p>
</details>

---

## Some additional stuff you might want to know:

* If you use `-d` instead of `-it` in `docker run`, the container runs as a background process.
* If you don't use `--rm`, the container stays when you stop it.
  You can then start it again with `docker start`.
* You can give a container a name using `--name`.
* You can specify / override envvars in the container using `-e`. 
  You can use this multiple times.
* You can also use the `-p` flag multiple times to specify multiple port forwards.

More information on `docker run` [can be found here](https://docs.docker.com/engine/reference/run/).

---

## Summary

In this module, we created a simple server, and after verifying that it's working,
wrapped it in a docker image. We then used this docker image to run multiple
docker containers on our machine, effectively running multiple copies of a 
single application while keeping them separated from each other.