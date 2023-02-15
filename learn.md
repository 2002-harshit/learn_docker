# 3 docker innovations

- THe docker image
- The docker registry
- the docker container

The docker build-ship-run is a process of taking non-containerized software , building it, shipping it and finally running it as a container somewhere.

## The build process

> ### Docker/container image <br>
>
> - Its like a universal package manager
> - It is cross os, cross platform, and application agnostic that is it runs almost anywhere as long as you have linux or windows.
> - Lets see a docker file that is used to create a docker/container image.
>
>   ![](/resurces/image1.png)
>
> - The first line is FROM, where you can FROM from someone else's images, or the images available in the registry(docker hub)
>
>   ![](/resurces/image2.png)
>
> - Each command in the docker file FROM,RUN...will create image layers. When the first line is executed, docker finds "python" image from the docker hub(or even any other specified path), this "python" image only contains python libraries and nothing else, and hence the bottom most layer is created.
> - When the RUN command executes, then "flask" is installed and hence the second last layer is created,
>   **what docker does under the hood is basically,making small little containers for each of the layer that run the commands for that particular layer**
> - Similarly,the COPY command copies our(the host's) source code into the **image app layer**.
>
> ### These layers then finally form the image using the **docker build** command.

## The ship process

> ### Docker registry
>
> - This is used for application distribution(moving images around).

> ![](/resurces/image3.png)

> We have our container image now,we calculate a sha hash for this image, then we can push this to a registry using **docker push**, then you can got to another machine and perform docker pull.

## The run process

> ### The docker container
>
> ![](/resurces/image4.png)
>
> - The docker run command is used to run the container, this **container has its own namespace**, that is the container doesn't know about the rest of th operating system, it gets its own blank file system, own ip, a virtual nic, its own process list etc.
> - similarly if we run docker run again, we get one more identical container which is completely isolated from the original one, if you change file in one, it is not changed in others.
> - now this can be extended to many servers to ensure high availability of our app.
>
> ![](/resurces/image5.png)

## A scenario

> - docker run -d -p 8800:80 httpd
>
> run command is used to run a container, -d signifies detached.
>
> -p is for port forwarding, host:constrainer, what happens is, port 80 is the container's port that is listening to the private ip, that is only accessible inside the docker's virtual networking, but with -p we basically tell to forward the traffic from host's 8800 to container's 80, we basically expose container's 80 to our 8800.
>
> httpd is the image from which the container is built.
>
> The image is first searched in the local machine, if not found, it is pulled from the registry(docker hub).
>
> The container gets its own namespace, therefore the httpd service running is in complete isolation from the host's OS.

## Why docker

> - better isolation in a singe OS.
> - Reduced environment variances
> - Increasing our speed of change

> Now we have both docker client and the server installed in our system, by default when you run any docker command, it defaults to your local docker engine, if you want to run containers on a remote server, you can use **export DOCKER_HOST="ssh://user@host"**, now you use your own docker client cli but the commands will be executed in the server's docker engine.

> ### New docker command format
>
> - **_docker_** to see all the commands, here you will find management commands.
> - so new format **_docker <command/management> <sub-command>_**
> - old format **_docker <command>_**

> ### Image vs a container
>
> - An image is basically the application that you want to run, a container is an instance of that image that is running as a process.

> **_docker container run -p/--publish 80:80 nginx_**
>
> - it pulls down nginx image from the docker hub
> - it sends my port 80 traffic to container's port 80 where nginx is listening.
> - we can add -d/--detach also to detach this from our terminal.
> - we can also give it a name, **_docker container run -d --name <any name> -p 80:80 nginx_**

> **_docker container ls_** to view all the containers, that are running, **_docker container ls -a_** to show all the containers running or stopped.

> **_docker container stop <container id>_**
> it is used to stop a running container.

> ### **docker container run vs docker container start**
>
> **run** starts a new container, while **start** is used to start an existing container.

> **_docker container logs <name/id>_** to view the logs

> **_docker container rm <first 3 digits of id> ..._** is used to remove a container, but you cannot remove a running container, so first stop it or use -f for forceful removal.

> **_docker container top <container name/id>_** , it is used to list the processes happening inside the container.

> ![](/resurces/image6.png)
>
> ![](/resurces/image7.png)

> **_docker container stats <name/id>_** to get the live performance statistics of a container.

> **_docker container inspect <name/id>_** , it show all the container configurations.

> - **_docker container run -it <image> <command>_** ,**USED TO RUN A COMMAND IN A NEW CONTAINER** -i refers to interactive and -t allocates us a pseudo terminal, and the < command > is which we want to execute in the container. example **docker container -it --name ubuntu_os ubuntu bash**, it basically executes "bash" command in the container, and since we have -it, we get a pseudo terminal to work on, where bash can execute.
> - Another example, **_docker container run -it ubuntu ps_**, this basically creates an ubuntu container , and executes the ps command in its interactive shell which we got by using -it, and when the command completes, the container shuts down!!!, this is important
> - Another example,**_docker container run --name alpine_os alpine sh_**, we basically start a new alpine container and execute "sh" in it, but since we don't have an interactive terminal(no -it), sh executes and the container shuts down without us showing anything.

> **_docker container start -ai <existing container>_** , again -ai is used to set up an interactive terminal, and start the container.

> **_docker exec -it <container> <command>_**, this is **USED TO RUN A COMMAND IN AN EXISTING RUNNING!!! CONTAINER **, similar to run, but doesn't shut down the container when the command execution finishes., again -it is required to see a terminal to work on.
