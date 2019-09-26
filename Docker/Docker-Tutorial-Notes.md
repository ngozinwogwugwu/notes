Ngozi's Docker Notes (https://docs.docker.com/get-started/)
## Part 1: Orientation
- Docker is a tool to create applications that run on containers.
- Image: a package - contains code, environment variables, configurations
- Container: an instance of that package. It runs natively on Linux. It's different from a VM because it shared the kernel with your machine.

## Part 2: Containers
Docker is nice because you can deal with all of the dependencies in your dockerfile, and you don't have to install anything on your machine. https://docs.docker.com/get-started/part2/#the-app-itself has an example dockerfile, along with a tutorial for builing your first app.

You build the app using the command `docker build`, which creates the image (you view the image using `docker image ls`, not `ls`)

You run the app using `docker run` (you can specify things like the port)
  * use the tag -d to run the container in the background. To kill the process, use the command `docker container stop <process ID>`. Grab the process ID from the `docker container ls` command

- note: if you want to use bash to look around the container, use the command `docker exec -it <container id> bash`

Docker has registries. These are like git repos, but for docker images. Log in using the command `docker login`
You should tag your images when you push them to registries. The syntax for this is `docker tag image username/repository:tag`
you can push your docker to the registry using the command `docker push username/repository:tag`, and it'll show up on https://hub.docker.com/r/nnwogwugwu/docker_practice/ . Once you do this, docker can pull down the image and run it in a container. This includes all of the dependencies.

## Part 3: Services
A service here refers to front-end/back-end/database. Each of these would be considered a service, and each one of these would run as its own container. Compose is a way to coordinate all of the services (define the ports they use, how they get spun up, etc.)

### Docker Compose
You use **Compose** for docker applications that have more than one container. You can use a YAML file to configure the applications services, and then use Compose to read the YAML file and start everything

`docker-compose.yml` -> this defines how containers should behave in production. The one in the tutorial defines the following:
- which image we're referencing, how many instances of it we run
- network settings, which ports the aplication uses
- limits on resources (CPU, memory)

### Running a load balanced app
In order to run an app (a set of containers, or services) you'll need to **initialize a swarm** and **deploy a stack**
**initialize the swarm:** `docker swarm init` (makes your machine a manager)

**deploy the stack:** 

`docker stack deploy -c docker-compose.yml getstartedlab` (this creates a network and a service)
once you've deployed, you can run `docker container ls`, and you'll get the containers that are up. `docker container ls -q` also does this, but in a more organized way. This app is cycling services, so if you run that command a few times, you'll get different results. The command `docker service ls` will show you details for the service that you made.

**A single container running in a service is called a Task** This means that we have five tasks (or containers) running as part of this service. This is defined in docker-compose.yml.

Since the service is up and running, you can go to **http://localhost:4000/** to see it in action. If you reload the page, the **Hostname** value updates. This is because the app is load balanced. It's cycling through hosts it provides.

### Scaling the app
Update the **replicas** command in `docker-compose.yml`, then run `docker stack deploy -c docker-compose.yml getstartedlab` again. Docker updates without tearing down the stack first!

### Taking down the Swarm
`docker stack rm getstartedlab` Take down the app
`docker swarm leave --force`    Take down the swarm

## Part 4: Swarms
Swarm: a dockerized cluster, a group of machines that:
 1. are running docker
 2. are joined to a cluster
- **nodes:** machines on the cluster
- **swarm manager:** a machine that executes commands on the cluster
- **workers:** machines on the cluster that aren't the manager. They don't issue commands, but they're there to provide capacity
- **routing mesh:** allows any node on the swarm to accept connections on published ports for any task running on the swarm, even if it's not running anything. The node routes the request to an active container

### Creating a Swarm Cluster
1. Make a couple of VMs
- `docker-machine create --driver virtualbox <myvm1>`
- `docker-machine ls`
- `docker-machine ssh`
2. Initialize the swarm
- `docker swarm init`
- or: `docker-machine ssh myvm1 "docker swarm init --advertise-addr <myvm1 ip>"`
- ^ this will give you the command needed to join other machines to the cluster.
3. Add some nodes
- `docker swarm join`
- or: `docker-machine ssh myvm2 "docker swarm join --token <token> <ip>:2377"`
- `docker node ls`

4. Break it down
- `docker swarm leave`

### Deploying an app
You can do this from your local machine or from the swarm manager.
- you could configure your shell to talk to the Docker Daemon on the VM
- you could make the swarm manager handle deploy

### Iterating & Scaling an app
- to scale: update `docker-compose.yml`
- to edit: update code, rebuild, push the image
for both, run `docker stack deploy`

### Cleaning up, Rebooting
- tear down the app (`docker stack rm`)
- undo docker-machine shell variables
- restart docker machines


## Part 5: Stacks
Stacks are groups of interrelated services that share dependencies
- can be orchestrated and scaled together
- one stack can be sufficient for one application, but big applications take multiple stacks

### Visualizer
This is a tool that works with docker in swarm mode, and you specify its use in docker-compose.yml
- you can use it to see how the swarm is scheduling containers

### Persisting Data
You can add a redis database (redis has an official docker image) if you want to persist data
- redis always runs on the manager
- you'll need to make a **/data** directory that exists outside of the swarm. This will mean that data will persist, even if you turn off the service

## Part 6: Deploy Your App

## Cheat Sheet:
**List Docker CLI commands**
- `docker`
- `docker container --help`

**Display Docker version and info**
- `docker --version`
- `docker version`
- `docker info`

**Execute Docker image**
- `docker run hello-world`

**List Docker images**
- `docker image ls`

**List Docker containers (running, all, all in quiet mode)**
- `docker container ls`
- `docker container ls --all`
- `docker container ls -aq`

**part 2 commands**
- `docker build -t friendlyhello . ` # Create image using this directory's Dockerfile
- `docker run -p 4000:80 friendlyhello  ` # Run "friendlyname" mapping port 4000 to 80
- `docker run -d -p 4000:80 friendlyhello  `       # Same thing, but in detached mode
- `docker container ls `                               # List all running containers
- `docker container ls -a `            # List all containers, even those not running
- `docker container stop <hash>`           # Gracefully stop the specified container
- `docker container kill <hash>`         # Force shutdown of the specified container
- `docker container rm <hash>`        # Remove specified container from this machine
- `docker container rm $(docker container ls -a -q)`         # Remove all containers
- `docker image ls -a `                            # List all images on this machine
- `docker image rm <image id>`            # Remove specified image from this machine
- `docker image rm $(docker image ls -a -q)`   # Remove all images from this machine
- `docker login`             # Log in this CLI session using your Docker credentials
- `docker tag <image> username/repository:tag`  # Tag <image> for upload to registry
- `docker push username/repository:tag `           # Upload tagged image to registry
- `docker run username/repository:tag`                   # Run image from a registry

**part 3 commands**
- `docker stack ls`                                            # List stacks or apps
- `docker stack deploy -c <composefile> <appname>`  # Run the specified Compose file
- `docker service ls`                 # List running services associated with an app
- `docker service ps <service>`                  # List tasks associated with an app
- `docker inspect <task or container>`                   # Inspect task or container
- `docker container ls -q`                                      # List container IDs
- `docker stack rm <appname>`                             # Tear down an application
- `docker swarm leave --force`      # Take down a single node swarm from the manager

**part 4 commands**
- `docker-machine create --driver virtualbox myvm1`                     # Create a VM (Mac, Win7, Linux)
- `docker-machine env myvm1`                                    # View basic information about your node
- `docker-machine ssh myvm1 "docker node ls"`                             # List the nodes in your swarm
- `docker-machine ssh myvm1 "docker node inspect <node ID>"`                            # Inspect a node
- `docker-machine ssh myvm1 "docker swarm join-token -q worker"`                       # View join token
- `docker-machine ssh myvm1`                       # Open an SSH session with the VM; type "exit" to end
- `docker node ls`                                    # View nodes in swarm (while logged on to manager)
- `docker-machine ssh myvm2 "docker swarm leave"`                      # Make the worker leave the swarm
- `docker-machine ssh myvm1 "docker swarm leave -f"`                     # Make master leave, kill swarm
- `docker-machine ls`                       # list VMs, asterisk shows which VM this shell is talking to
- `docker-machine start myvm1`                                # Start a VM that is currently not running
- `docker-machine env myvm1`                          # show environment variables and command for myvm1
- `eval $(docker-machine env myvm1)`                             # Mac command to connect shell to myvm1
- `docker stack deploy -c <file> <app>`                                                  # Deploy an app 
- `docker-machine scp docker-compose.yml myvm1:~`                           # Copy file to node's home dir
- `docker-machine ssh myvm1 "docker stack deploy -c <file> <app>`                # Deploy an app using ssh
- `eval $(docker-machine env -u)`                         # Disconnect shell from VMs, use native docker
- `docker-machine stop $(docker-machine ls -q)`                                   # Stop all running VMs
- `docker-machine rm $(docker-machine ls -q)`                     # Delete all VMs and their disk images
