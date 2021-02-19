# FSDS Docker Container

A Docker container to run FSDS

- Authors: [Dhruval PB](http://github.com/Dhruval360), [Aditya NG](http://github.com/AdityaNG)

# Getting Started

## Running manually

First make sure the host system is setup with the appropriate runtime, refer to [Setup](#setup).
To obtain your Diplay Cookie, run `xauth list`. Refer to [Display Cookie](#display-cookie)
After that, you should be able run the container using the following, which will pull the container and launch the app :

```bash
docker run --runtime=nvidia -it -d --gpus all --net=host \
	-e DISPLAY -v /tmp/.X11-unix -e NVIDIA_DRIVER_CAPABILITIES=all \
	--env DISPLAY_COOKIE="(DISPLAY_COOKIE)" adityang5/fsds:v1 \
	/bin/sh /fsds/run.sh
```

## With Dockerfile

To create your own docker container to work with, you can use the following template

```Dockerfile
FROM adityang5/fsds:v1

COPY . /app

CMD "sh /app/run.sh"
```

# Setup

Refered to https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/user-guide.html

## Systemd drop-in file

```bash
sudo mkdir -p /etc/systemd/system/docker.service.d

sudo tee /etc/systemd/system/docker.service.d/override.conf <<EOF
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd --host=fd:// --add-runtime=nvidia=/usr/bin/nvidia-container-runtime
EOF

sudo systemctl daemon-reload \
  && sudo systemctl restart docker
```

## Daemon configuration file

The nvidia runtime can also be registered with Docker using the daemon.json configuration file:

```bash
sudo tee /etc/docker/daemon.json <<EOF
{
    "runtimes": {
        "nvidia": {
            "path": "/usr/bin/nvidia-container-runtime",
            "runtimeArgs": []
        }
    }
}
EOF

sudo pkill -SIGHUP dockerd
```


# Build From Scratch  

Build command 

```bash
docker build -t fsds_build:v1 .
```

To run the container

```bash
docker run --runtime=nvidia -it -d --gpus all --net=host -e DISPLAY -v /tmp/.X11-unix -e NVIDIA_DRIVER_CAPABILITIES=all --env DISPLAY_COOKIE="(DISPLAY_COOKIE)" fsds_build:v1 /bin/sh /fsds/run.sh
```

# Display Cookie

Replace (DISPLAY_COOKIE) with the your machine's xauth display cookie, it should look like the following: 
```bash
xauth list
username/machine:0 MIT-MAGIC-COOKIE-1 [32 character string]
```

# Specifying a settings.json

TODO : This is being implemented
