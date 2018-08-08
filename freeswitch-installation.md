# Freeswitch setup using docker

Follow the steps to initially setup a freeswitch docker image on a docker enabled system

### Clone the repository

```sh
$ git clone -b developer https://gitlab.com/akashranjan555/freeswitch.git
```

### Build the docker image

```sh
$ cd freeswitch
$ sudo docker build -t <image_name> .
```

You will now have the freeswitch image built, and can be seen in the docker images using the following command

```sh
$ sudo docker images
```

### Run a docker container with the following command

```sh
$ sudo docker run -d --name <container_name> -it --privileged --net host <image_id>
```

The container will be launched in detached mode, and can be seen in the running containers list using the following command

```sh
$ sudo docker ps
```

### Enter a running container to make changes in the Freeswitch configuration files

The following command will enter the currently launched container

```sh
$ sudo docker exec -it <container_name> /bin/bash
```