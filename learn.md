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

> **_docker container start -ai <existing container>_** , again -ai is used to set up an interactive terminal, and start the container, **YOU CANNOT GIVE ANY COMMAND IN IT TO EXECUTE as in ruin and exec**.

> **_docker exec -it <container> <command>_**, this is **USED TO RUN A COMMAND IN AN EXISTING RUNNING!!! CONTAINER **, similar to run, but doesn't shut down the container when the command execution finishes., again -it is required to see a terminal to work on.

## Docker networks

> - Docker basically provides a virtual network that is called docker0/bridge.
>
> - When you make a new container, it is attached to this virtual network which is then connected to my laptops(host's) ethernet/wlan interface to talk to the outer world.
>
> - when you give -p/--publish to a container, you basically tell docker to forward that traffic from the host port(ethernet/wlan) to the container's port via the virtual network.
>
> if -p is not given, then no traffic from host will travel to container and vice versa
>
> containers belonging to same virtual network can talk to each other.
>
> You can make various virtual networks.

> - **_docker network ls_**, to view all the networks,
>
> ![](/resurces/image9.png)
>
> - The bridge network/also called the docker0 is the virtual network that we discussed about.
>
> - the host network is a special kind of network that skips the virtual network, and directly connects the container to the host's interface.
>
> - **_docker network inspect <network name>_**, to inspect a network and see the containers attached to it and ip etc.
>
> - **_docker network create <name(my_app_net)>_**, it is used to create your own network, **if the driver is not specified then bridge is the default, so u basically create a new virtual network, since its bridge driver.**
> - **_docker container run nginx --network my_app_net_**, runs an nginx container connected to my_app_net network.
>
> - **_docker network connect <name of the network> <name of the running container>_**, this is used to connect a container to a particular network, it wont disconnect another networks, so a container could be connected to many networks, we have disconnect also, **_docker network disconnect <network> <container>_**.

### Docker DNS and the need of it

> - For communication between containers, we cannot rely in IP because since container are really very dynamic and hence we cannot predict when they stop and spawn again with a new IP, therefore for every **CUSTOM VIRTUAL(BRIDGE) NETWORK**, that you create, you get a dns with the **host names as container names**, which can be used for inter-container comms.
>
> ![](/resurces/image10.png)
>
> - we notice **my_app_net** contains 2 containers, lets ping among them without using IP, and rather their container names.
>
> ![](/resurces/image11.png)
>
> since i didn't need a an interactive shell to work with, i did not used -it.
>
> **The default docker network docker0/bridge does not get the dns you can use --link flag, BUT CREATING A NEW VIRTUAL NETWORK IS RECOMMENDED**.
>
> - **ALSO BRIDGE DRIVER IS/CREATES A VIRTUAL NETWORK**.

### A scenario where we create 2 OS containers, and run curl on them ,ubuntu:14.04 and centos:7

> **_dc run -it --name ubuntu ubuntu:14.04 bash_**
> -it because we want an interactive terminal and the startup CMD is bash which we want to execute in that container.
>
> **_dc run -it --name centos centos:7 bash_**

### A very good assignment teaching multiple concepts.

![](/resurces/image12.png)

> - What is DNS round robin, let see, we know that when we make our own virtual network(bridge driver), we get a DNS server, DNS as we know is a simple mapping between a url and an IP, DNS round robin is a load balancing technique, where **under a single dns name, you can have multiple IPs or servers**, in order to ensure high availability, the requests to the website then cycle on different servers.
>
> creating a network **_docker network create assignment_net_**
>
> creating containers **_dc run -d --name es1 --network assignment_net --network-alias search elasticsearch:2_**, --network is given to belong to a particular network, --network-alias is used for multiple IPs under the same name, that same name ,we have given is "search"
>
> **_dc run -d --name es2 --network assignment_net --network-alias search elasticsearch:2_**
>
> **_dc run -it --name alpine --network assignment_net alpine sh_**, we add alpine container to the same virtual network(assignment_net)
>
> now we can use alpine to use nslookup.
>
> ![](/resurces/image13.png)
>
> as we can see, nslookup search shows two corresponding servers against that single name.
>
> lets contact the elasticsearch containers using curl.
>
> ![](/resurces/image14.png)
>
> **WE NOTICE THAT RESPONSES ARE SENT FROM 2 DIFFERENT SERVERS, THIS IS NOT NECESSARILY ONE BY ONE**.

## DEEP DIVING INTO IMAGES

> - It is the application binaries and the meta data for the app, and how to run the image.
>
> - **It doesn't contain any kernel, kernel modules or drivers , WE(the host) PROVIDE IT**.
>
> - An image can be seen as a series of layers, that stack on one another to form the final image, with each change to the file system(of the image), a new layer is created.
>
> - **and these layers are cached, that is if multiple images use the same layer(which can be checked using sha value), then those same layers are not pulled/pushed from the registry(docker hub)**
>
> - the above follows for pushing to docker hub also.
>
> - **_docker image history < name >_**, can be used to see different layers.

### Relation between a container and an image.

> Lets assume we have an "Apache" image, when we run a container(con1) from this image, a new read-write layer corresponding to that container is created on top of the apache image.
>
> ![](/resurces/image15.png)
>
> Now, if you make a change to any of the file of a container, that file is copied from the image to the container and is stored as a copy in the container(COPY ON WRITE), therefore **CONTAINER IS JUST A RUNNING PROCESS AND THE MODIFIED/DIFFERENT files(as compared to the image).**

### Image tags

> ![](/resurces/image16.png)
>
> an image is identified with three things, user, repo and tag.
>
> ![](/resurces/image17.png)
>
> we can see that the httpd name/tag(yes the whole thing that is user/repo:tag can also be called tag) is given to the image, lets change it.
>
> **_docker image tag httpd harshit/httpd_**, since tag is not given, it defaults to latest.
>
> ![](/resurces/image18.png)
>
> we can see a new entry(as httpd/harshit), but we know the concept of layer caching and hence we know these layers are stored just once
>
> lets push this, acc to new docker rules image should be tagged as < docker hub username >/< something > to push it to docker hub.
>
> ![](/resurces/image19.png)

## DOCKER FILE!!!!!!

## PERSISTENT DATA

> persistent data -> dta that stays past life of a container(even when they are removed), 2 ways **VOLUMES** and **BIND MOUNTS**.

### VOLUMES

> - lets see about volumes, in order to make a volume, it is mentioned in the Dockerfile as "VOLUME /var/lib/mysql", this basically tells docker whenever we create a mysql container, **make a volume(persistent storage) in the host and assign it to the /var/lib/mysql**, so when your container writes in its /var/lib/mysql, it is actually writing in that volume which u can see.
>
> - Lets see a scenario
>
> I created a mysql container
>
> ![](/resurces/image20.png)
>
> I inspected it to see the volume
>
> ![](/resurces/image21.png)
>
> we can see that /var/lib/docker/volumes/cf412978127828212992.../\_data is the actual volume, which is mapped to /var/lib/mysql of the container, and the name of the volume is sh\*t which we will fix.
>
> Lets create a db in the container
>
> ![](/resurces/image22.png)
>
> we can see that tab1 is created in /var/lib/mysql/db1
>
> lets see our system.
>
> ![](/resurces/image23.png)
>
> The same tab1.ibd table is also found here, now volumes are basically created by every mysql container which is a problem due to their size, so now **LETS CREATE NAMED VOLUMES AND SHARE THEM WITH 2 MYSQL CONTAINERS**.
>
> ![](/resurces/image24.png)
>
> we can see while creating mysql1 container, we use -v to create a named volume **_-v vol1:/var/lib/mysql_**, so vol1 is the name of the volume which is mapped to /var/lib/mysql.
>
> Since while creating mysql2 we again use vol1, which came into existence while creating mysql1, so it uses th same vol1 volume.
>
> and hence only one volume exists on the host (/var/lib/docker/volumes/vol1/\_data)
>
> The db and tables created by one container can be accessed by the other container also, but i noticed **this is not simultaneous, one of the container takes a lock**

### BIND MOUNTS

> we map a host file/directory to a container's file/directory.
> changes u make in one are reflected in another.
> we use the same -v, but on lhs starts with a "/".
> This is not mentioned in Dockerfile
>
> scenario, lets create an nginx container with bind mount to our current working directory.
>
> ![](/resurces/image25.png)
>
> our work dir is mapped to container's work dir /usr/share/nginx/html, so changes in one are reflected in another even deletion of files.
>
> even when the container is deleted, files created using the container in the mount, still remain.

### Scenario using 2 postgres containers connected to same volume, when the first container starts, logs are large because it has to do db initialisation, then it is stopped, but when the second container starts connected to the same volume, logs are small, because db initialisation is already done in that volume.

> **_dc run --name psql1 -e POSTGRES_PASSWORD=mypassword -v vol1:/var/lib/postgresql/data postgres:15.1_** > **_dc run --name psql2 -e POSTGRES_PASSWORD=mypassword -v vol1:/var/lib/postgresql/data postgres:15.2_**
