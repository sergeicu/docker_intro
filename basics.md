### What is Docker 

#### VM vs containers 

![Filezilla-screenshot](https://github.com/sergeicu/docker_intro/blob/main/assets/docker7.png)
![Filezilla-screenshot](https://github.com/sergeicu/docker_intro/blob/main/assets/docker2.png)

#### Dockerfiles, images, containers, dockerhub
![Filezilla-screenshot](https://github.com/sergeicu/docker_intro/blob/main/assets/docker5.png)
![Filezilla-screenshot](https://github.com/sergeicu/docker_intro/blob/main/assets/docker4.png)
#### Workflow 
![Filezilla-screenshot](https://github.com/sergeicu/docker_intro/blob/main/assets/docker6.png)


### Install Docker 

### Dockerhub vs Github 

### Download someone's container 
#### docker hub 
#### docker pull command 


### Deploy someone's Docker 
### eg 1 anima 
### eg 2 biomedia 
### eg 3 crkit 
### eg 4 dce reconstruction 

### Docker cheatsheet 
[Source](https://www.docker.com/sites/default/files/d8/2019-09/docker-cheat-sheet.pdf)

### Docker and python 


### Docker and conda 


### Docker and GPU 
This requires a special setup as you will need to provide Docker with route to your GPU config. Best idea is to start with NVIDIA docker image [here](https://github.com/NVIDIA/nvidia-docker).  

TBC: add more links here 


### Docker and matlab 
- To deploy your matlab 'apps' with docker you must first compile your matlab _source code_ into executable _binaries_.  
- Do this via Matlab Compiler. 
- You can deploy compiled matlab binaries with Matlab Compile Runtime (MCR), which is freely distributed.  
- Package your matlab binaries with MCR into a Dockerfile. Flywheel has provided us with recipes for building docker images with MCR [here](https://github.com/flywheel-apps/matlab-mcr)


IMPORTANT: Compiler is NOT installed on CRL machines, but it is available on matlab supplied via Research Computing.   
Follow my guide on [E2](https://github.com/sergeicu/e2/blob/main/research-computing.md) and setup an interactive matlab session like [here](http://websvc4.tch.harvard.edu:8090/display/RCK/Visualization+job) (require VPN access). Then follow Matlab Compiler tutorial [here](https://www.mathworks.com/help/compiler/getting-started-with-matlab-compiler.html). Ask me or Yao for help if necessary.   

### Build your own Docker 
#### centos 
#### ubuntu 
#### python libraries 


### Expose Docker to external files 

### Expose Docker to external ports 

### Expose docker to external code 

### Build your code within docker to do one thing 



### Singularity vs Docker 
![Filezilla-screenshot](https://github.com/sergeicu/docker_intro/blob/main/assets/docker8.png)

### BCH Proxies 

