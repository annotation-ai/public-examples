This example is for https://blog.annotation-ai.com/dockerizing-gocv/

## Prerequisites
- Install OpenCV ([Linux](https://docs.opencv.org/4.x/d7/d9f/tutorial_linux_install.html) / [Windows](https://docs.opencv.org/4.x/d3/d52/tutorial_windows_install.html) / [MacOS](https://docs.opencv.org/4.x/d0/db2/tutorial_macos_install.html))
- Install Golang ([Linux / Windows / MacOS](https://go.dev/doc/install))
- Install Docker Engine ([Linux](https://docs.docker.com/desktop/install/linux-install/) / [Windows](https://docs.docker.com/desktop/install/windows-install/) / [MacOS](https://docs.docker.com/desktop/install/mac-install/))

## Build the GoCV Container
```bash
$ docker build -t dockerizing-gocv .
```

After the build is completed, check the image size.
```bash
$ docker images

REPOSITORY         TAG      IMAGE ID       CREATED         SIZE
dockerizing-gocv   latest   43d032497c94   8 seconds ago   66.4MB
```

## Run the container
```bash
$ docker run --rm -it dockerizing-gocv

gocv version: 0.31.0
opencv lib version: 4.6.0
```
