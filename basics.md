### What is Docker 

#### VM vs containers 
goal: explain the difference between Virtual Machines and Containers (such as Docker or Singularity containers) 
![image](https://github.com/sergeicu/docker_intro/blob/main/assets/docker7.png)
![image](https://github.com/sergeicu/docker_intro/blob/main/assets/docker2.png)

#### Dockerfiles, images, containers, dockerhub
goal: explain the main building blocks of docker ecosystem and the differences between them. 
![image](https://github.com/sergeicu/docker_intro/blob/main/assets/docker5.png)
![image](https://github.com/sergeicu/docker_intro/blob/main/assets/docker4.png)
#### Workflow 
goal: show a basic workflow schedule and explain why it is needed
![image](https://github.com/sergeicu/docker_intro/blob/main/assets/docker6.png)


### Install Docker 
[My instructions are here](https://github.com/sergeicu/anima-docker/blob/main/install-docker.md)

### Dockerhub vs Github
- Dockerhub is just like Github, but for Docker images. 
- It is a repository of pre-built Docker images
- Instead of spending hours/days on building your own images, you can re-use someone's recipes that have already been compiled into Docker images. 
- Simply do `docker pull $username/$imagename:$tagname` (equivalent to `git clone $repository_name`)
- e.g. `docker pull ubuntu:latest` - pulls a Docker Ubuntu image
- run it via `docker run ubuntu:latest /bin/bash` - you can immediately access a terminal inside an ubuntu operating system and test your app with it 



### Deploy ready made Docker images as containers 
Use prebuild images immediately. 

#### Case study 1: anima software
- Background: I needed to estimate Myelin Water Fraction maps for my data with a conventional (non deep learning) algorithm. 
- Problem: The algorithm was available as 1. C++ source code that needed to be compiled 2. Ubuntu OS binary files. I had tried to compile this software from source code to no avail. 
- Solution: I decided to use precompiled binaries. Note that binaries were NOT available for CentOS. So I built a Docker Ubuntu image instead and ran this software from within docker container. 


Pull:  
- `docker pull sergeicu/anima_t2_only`
Run:  
- `docker run -it --rm sergeicu/anima_t2_only /bin/bash`
- `-it` - run interactively  
- `--rm` - delete container once you exit it 
- `/bin/bash` - initial command to run 
Expose your own data:   
- `docker run -it --rm -v $localfolder:/data sergeicu/anima_t2_only /bin/bash`
- `$localfolder` - full path to your folder. 
- `/data` - location (&name!) of this folder inside docker  

WARNING: you absolutely must run `chmod ugo+rw $localfolder` before starting this Docker container. Else Docker will not be able to access your data. This gives read/write access to ALL users. 

[source](https://hub.docker.com/r/sergeicu/anima_t2_only)

#### Case study 2: biomedia/mirtk toolbox 
- Background: I needed to use a toolbox from my PhD supervisor's group to do some registration / interpolation tests. 
- Problem: The toolbox was available as 1. C++ source code that needed to be compiled 2. Docker image. It would take me hours to compile this C++ source code into binaries. 
- Solution: I decided NOT to spend hours compiling this software and just run docker image in <1hour. 

Pull:   
- `docker pull biomedia/mirtk`
Run:   
- `docker run -it --rm -v $outdir:/data biomedia/mirtk transform-image $input $output -sinc -target $target` 
- `transform-image` - is a command that is invoked straight away as docker image is deployed into a container. This removes the need to access Docker interactively (which we do via `/bin/bash`) 
- `$input $output -sinc -target $target` - this are the standard input arguments that this command would require. 
- If I would have compiler this software on CentOS, executing it would be without Docker would be like this: `biomedia/mirtk transform-image $input $output -sinc -target $target` 

[source](https://hub.docker.com/r/biomedia/mirtk)

#### Case study 3: crkit docker
(WARNING: work in progress) 
- Background: I wanted to make crkit available on my local home computer, which uses Windows 
- Problem: I would not be able to run crkit using Windows. I could only install it via linux system. 
- Solution: I decided to build a crkit docker, so it could be deployed by anyone anywhere (with docker access) 

[Link (warning - work in progress)](https://github.com/sergeicu/crkit-docker)

#### Case study 4: Dynamic Contrast Enhanced Reconstruction (DCE)
(WARNING: work in progress) 
- Background: We need to share our code with collaborators, which consists of python + matlab code. 
- Problem: Our collaborators want to use a single line command to reconstruct their MRI data. They don't have capacity/time/ability to reproduce our software environment, including the exact operating system, python dependencies, and necessary matlab license,etc 
- Solution: We are building a docker image that we can send them and they can simply do `docker run $image_name $command_name $input $output $other_options` 

TBC (warning - work in progress)


### Docker cheatsheet 
![image](https://github.com/sergeicu/docker_intro/blob/main/assets/build_share.png)
![image](https://github.com/sergeicu/docker_intro/blob/main/assets/run.png)  

[Source](https://www.docker.com/sites/default/files/d8/2019-09/docker-cheat-sheet.pdf)



### Build your own Docker image

A Docker image consists of read-only layers each of which represents a Dockerfile instruction. The layers are stacked and each one is a delta of the changes from the previous layer. Consider this Dockerfile:
```
FROM ubuntu:18.04
COPY . /app
RUN make /app
CMD python /app/app.py
Each instruction creates one layer:
```
`FROM` creates a layer from the ubuntu:18.04 Docker image.
`COPY` adds files from your Docker client’s current directory.
`RUN` builds your application with make.
`CMD` specifies what command to run within the container.

[source](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

Here is example of my own Dockerfile for 'anima' software (see above for more info): 
```
FROM ubuntu:18.04

MAINTAINER Serge V "serge.vasylechko@tch.harvard.edu"

# basic tools   
RUN apt-get update && apt-get install -y \
    unzip \ 
    wget  

# Anima binaries   
RUN wget https://github.com/Inria-Visages/Anima-Public/releases/download/v4.0/Anima-Ubuntu-4.0.zip && unzip Anima-Ubuntu-4.0.zip && rm -rf Anima-Ubuntu-4.0.zip && mv Anima-Binaries-4.0/ anima/

# python3 
RUN apt-get install -y python3-pip python3-dev \
  && cd /usr/local/bin \
  && ln -s /usr/bin/python3 python \
  && pip3 install --upgrade pip

# anima helper script 
COPY src/run_anima.py /
RUN chmod 666 /run_anima.py

CMD ["python", "/run_anima.py"]
```

Build:   
- `cd $directory_with_Dockerfile`
- `docker build --no-cache -t $name_for_your_image .` 
- `$name_for_your_image` - by convention we set this to - `<dockerhub_username>/<docker_name>:<build_release>` - e.g. `sergeicu/anima:latest`
- `--no-cache` - builds from scratch (not always necessary but can be a life saviour in certain situations)



### Docker and python 
There are many flavours of python ready made images that you can pull from docker hub. e.g. 
- https://hub.docker.com/_/python - full python image (100+Mb)
- https://hub.docker.com/_/alpine - tiny python image (<10Mb) 

### Docker and conda 
TBC - in short - no necessity to use conda. Can just use pip directly. But conda can also be installed. 

### Docker and GPU 
- This requires a special setup as you will need to provide Docker with route to your GPU config. 
- Best place to start is NVIDIA docker image [here](https://github.com/NVIDIA/nvidia-docker).  
- Example Docker image is [here](https://github.com/sergeicu/docker_intro/blob/main/gpu.md)

TBC: add more links here 


### Docker and matlab 
- To deploy your matlab 'apps' with docker you must first compile your matlab _source code_ into executable _binaries_.  
- Do this via Matlab Compiler. 
- You can deploy compiled matlab binaries with Matlab Compile Runtime (MCR), which is freely distributed.  
- Package your matlab binaries with MCR into a Dockerfile. Flywheel has provided us with recipes for building docker images with MCR [here](https://github.com/flywheel-apps/matlab-mcr)


IMPORTANT: Compiler is NOT installed on CRL machines, but it is available on matlab supplied via Research Computing.   
Follow my guide on [E2](https://github.com/sergeicu/e2/blob/main/research-computing.md) and setup an interactive matlab session like [here](http://websvc4.tch.harvard.edu:8090/display/RCK/Visualization+job) (require VPN access). Then follow Matlab Compiler tutorial [here](https://www.mathworks.com/help/compiler/getting-started-with-matlab-compiler.html). Ask me or Yao for help if necessary.   



### Singularity vs Docker 
![image](https://github.com/sergeicu/docker_intro/blob/main/assets/docker8.png)

### Flywheel 
- Flywheel builds tools that wrap around containers.-
- Flywheel uses singularity instead of docker for reasons highlighted above. 
- Once you build 2-3 of your own docker images, you should be able to proceed to Flywheel tutorials relatively easily (else it would be a real struggle) 
- Flywheel tutorial can be found [here](https://docs.flywheel.io/hc/en-us/sections/360003262473-Getting-Started-Guide-for-Developers). 

### BCH Proxies 
TBC 

### Expose Docker to external ports 
- tbc 
 
### Expose docker to external code 
- tbc 
### Build your code within docker to do one thing 
- tbc 
