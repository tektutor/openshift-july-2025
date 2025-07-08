# Day 1

## Info - Boot Loaders
<pre>
- boot loader software utility loads the Operating system
- while boot loader is pretty much always the one which loads the OS, 
  you could also use thie tool to install multiple OS on the same machine, 
  this is typically referred as dual/multi boot
- it is a system utility that is installed in the Master Boot Record(MBR)
- Sector 0, Byte 0 in your hard disk is called Master Boot Record, which is just 512 bytes
- in legacy BIOS, the boot loader has to fit within 512 bytes, but the modern UEFI BIOS allows the boot loader utility 
  to be stored in hard disks, hence there is no size restrictions imposed
- When we boot a system, once the BIOS POST(Power On Self Test) completes, the BIOS will instruct the CPU to run the 
  Boot Loader
- The Boot Loader then scans the hard disk(s) looking for Operating Systems
- In case the Boot Loader detects multiple OS, it then prepares a menu and gives us options to choose the OS you 
  wish to boot into
- Only one OS can be active at any point of time
- Examples
  - LILO 
    - Linux Loader
    - Not used anymore
     - was used in Older Linux OS in 1990's
     - it is an open source boot loader
  - GRUB 
    - almost used by all Linux Distributions including Fedora, Ubuntu, Rocky Linux, Red Hat Enterprise Linux, etc., )
  - BootCamp (Commercial licensed product - Used in Macbooks)
</pre>  

## Info - How many Virtual Machines at the max can be provisioned on a system with below configurations ?
<pre>
- Intel Processor with 4 Physical Cores
- 16 GB RAM
- 512 GB HDD/SDD
- In this system, how many maximum VMs can run at the same time?
  - With Intel Hyperthreading or AMD Simultaneous Multithreading (SMT), each Physical processor supports running multiple 
    threading concurrently, most processor CPU cores supports aleast 2 threads per core, 
    hence 4 Cores x 2 = 8 Logical/Virtual Cores
  - 1 Host OS + 7 Guest OS can run at the same time
  - i.e total 8 OS can run parallely 
- Host OS
  - is the Operating system that we install directly on top of the Hardware
  - this also requires some dedicated hardware resources
    - Processor Cores
    - RAM
    - Storage (HDD/SDD)
- Guest OS
  - Guest OS runs within the Virtual Machine created using Hypervisor softwares
  - Each Guest OS has to be allocated with dedicated hardware resources
    - Processor Cores
    - RAM
    - Storage (HDD/SDD
</pre>  

## Info - Processor 
<pre>
   - comes in 2 packages
     1. SCM ( Single Chip Module ) - one IC will host 1 Processor
     2. MCM ( Multiple Chip Module ) - one IC will host many Processors
        - one IC comes with 4/8 Processors
   - each Processor comes with multiple CPU Cores
     - 32 cores
     - 64 cores
     - 128 cores
     - 256 cores
     - 512 cores
    - motherboard with 4 Processor Sockets
    - let's assume we install MCM based IC with 4 Processor on each IC
    - Total Processors - 4 x 4 = 16 Processors
    - Assume, each Processor supports 128 cores
    - Total cores = 128 x 16 = 2048 physical cores
    - Total logical/virtual cores = 2048 x 2 = 4096
</pre>

## Hypervisor Overview
<pre>
- is a virtualization technology
- It is a Hardware + Software Technology
  - You need a Processor that supports Virtualization
  - In Intel Processors, the virtualization feature set is called VT-X
  - In AMD Processors, the virtualization feature set is called AMD-V
- Processors supports Multiple CPU Cores
  - Each Physical core supports two logical/virtual cores minimum
  - Server grade Processors may support 2/4/8 virtual cores per Physical core
- it came around year 2000
- it disrupted the way the IT industry works
- with limited servers we can run multiple Guest Operating Systems
  - this helps organizations save cost in terms of infrastructure, server procurement, better hardware utilization, 
    real-estate cost (rent/lease)
  - hence, every company irrespective of size adapted this technology
- this helps us run multiple OS in a single machine side by side
- more than one OS can be active at the same time
- there are two of Hypervisors
  - Type 1 
    - a.k.a Bare-Metal Hypervisors
    - used in Workstations and/or Servers
    - examples
      - VMWare vSphere
      - KVM
  - Type 2
    - is used in Desktops/Workstations/Laptops
    - requires a Host OS
    - it is installed on top of Host OS ( Windows, Mac or Linux )
    - examples
      - Oracle VirtualBox ( Windows, Mac & Linux )
      - Parallels ( Mac OS-X )
      - Hyper-V ( Windows )
      - VMWare Workstation ( Linux & Windows )
      - VMWare Fusion ( Mac OS-X )
- each VM(Virtual Machine) runs one fully functional Operating System
- each Guest OS must be allocated with dedicated Hardware resources, hence this type of virtualization is called Heavy-weight virtualization
</pre>

## Info - Hypervisor High Level Architecture
![Hypervisor Architecture](HypervisorHighLevelArchitecture.png)

## Info - Installing Docker Community Edition in Ubuntu
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo usermod -aG docker $USER
sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker
sudo $USER
docker --version
docker images
```
## Containers Overview
<pre>
- is an application virtualization technology
- this is light-weight virtualization
- technically each container is an applicaiton process, they are not Operating System
- due to some similarities, generally people tend to complare containers with vms, which technically wrong
- because each VM represents one fully functional Operating System, while each container represents one single application
- Similarities between containers and VMs
  - Just like an Operating System has its own dedicated ports 0-65535, containers also get's their own ports
  - Just like an Operating System has its own file system, containers also has their own file system
  - Just like an Operating System may one or more network cards, containers can also have one or more network cards(virtual)
  - Just like an Operating System gets IP Address(es), containers can also acquire one or more IP addresses( mostly Private IPs )
- Containers run on top of the Host OS, i.e wherever Docker/Podman is installed, the hardware resources allocated to the OS where containers are running are shared by all containers
- Containers don't have their own OS Kernel, they depend on the OS where the containers are running
- each container runs in a separate namespace
- namespace helps isolating the resources used by one container from the other containers
- Linux kernel features that enables the containerization
  - Namespace
  - Control Groups(CGroups)
</pre>

## Info - If Container is a Linux Technology how we are able to use Docker in Mac/Windows?
<pre>
- When we install Docker for Windows or Docker for Mac, it installs a thin linux-layer which provides the Linux Kernel
- Linux containerized applications can run on Windows with the help of the Linux kernel that comes with Docker
- Linux containerized applications can run on Mac with the help of the Linux kernel that comes with Docker
</pre>

## Info - How Dot Net Containerized applications are supported in Linux/Mac?
<pre>
- MONO is a Dot Net Framework implementation for Linux/Mac
- it is an opensource framework which implements the .Net Specification
- hence, technically one can develop,compile and run .Net applications natively in Linux/Mac just like Windows
- however, mostly .Net Core applications work smoothly which is good enough for Dot Net Microservices
</pre>

## Docker Overview
<pre>
- Docker is developed in Go Lang by a company called Docker Inc
- Docker comes in 2 flavours
  1. Docker Community Edition - Docker CE ( open source )
  2. Docker Enterprise Edition - Docker EE ( requires license )
</pre>

## Container Engine Overview
<pre>
- is a high-level software that helps us manage containers and container images  
- it is very user-friendly
- Container Engine depends on Container Runtimes to manage containers and container images internally
- For example
  - Docker depends on containerd which in turn depends on runC Container Runtime 
  - Podman depends on CRI-O container runtime
</pre>

## Container Runtime Overview
<pre>
- is a low-level software that helps us manage containers and container images
- not so user-friendly, hence no end-user use this directly
- Examples
  - runC 
  - CRI-O
</pre>

## What all will be there in a Container?
<pre>
- one application executable
- dependent libraries and/or softwares to run the application executable
- it is a way one can bundle and ship the application in a container format that can be readily executed as it is pre-configured
</pre>

## What is a Container Image?
<pre>
- Container Image is a way to bundle/package your application + application depencies ( library/framework, softwares )
- Container Image is a blueprint/template of an application
</pre>

## What is a Container?
<pre>
- Container is a running instance of a Container Image
- We can create any number of containers from a single Container Image
- For example
  - Using a mysql container image, we can create 100s of mysql containers
</pre>  
</pre>

## Docker Alternatives
<pre>
- Containerd
- Podman
  - follows rootless architecture, 
  - hence it is used in Red Hat Openshift 4.x onwards, Docker or any other container engine is not supported in Red Hat Openshift
</pre>

## Docker High Level Architecture
![Docker Architecture](DockerHighLevelArchitecture.png)

## Lab - Checking the docker version
```
docker --version
docker info
```

Expected output
![image](https://github.com/user-attachments/assets/44682d15-f085-4c72-813e-f763627574c9)

## Lab - Listing the docker images in the local docker registry
```
docker images
```
Expected ouptut
![image](https://github.com/user-attachments/assets/b168ce79-77dc-4155-97d7-aac6719e2abf)

## Lab - Troubleshooting Permission denied errors while running docker commands
Check the user groups your user belongs to
```
id
```
If the docker usergroup is missing, that explains why you are getting the permission denied error.

Any user which is part of docker user group, they gain read/write permission to the unix socket.  

You can run this command to force your user to refresh the groups it belongs
```
newgroup docker
## or you may run
su $USER
id
```
Now you should be able to see your user is part of docker group, hence you won't get permission denied errors moving forward. But you may have to redo this for every terminal you are opening in separate window/tab.

## Lab - Downloading image from Docker Hub Remote Registry to Docker Local Registry
```
docker images
docker pull mysql:latest
docker images | grep mysql
```
Expected output
![image](https://github.com/user-attachments/assets/a89df2d1-8cd2-46bc-81c7-2ecb287a1895)

## Lab - Deleting a docker image from local docker registry
```
docker images | grep hello-world
docker rmi hello-world:latest
docker images | grep hello-world
```
Expected output
![image](https://github.com/user-attachments/assets/9584b1ab-3b83-49b7-83b2-d41e7b83f732)

## Lab - Finding more details about a docker image in local registry
```
docker image inspect mysql:latest
```
Expected output
![image](https://github.com/user-attachments/assets/f7ed00cd-fd97-4cc8-861e-1cc3bfe2ce4b)
![image](https://github.com/user-attachments/assets/892db4d8-70f2-46ea-a959-c0c23cb8c1f9)


## Lab - Listing all currently running containers
```
docker ps
```
![image](https://github.com/user-attachments/assets/bdd432ef-bf22-4945-b5be-7d99f7a3f478)


## Lab - List all containers even if they are not running
```
docker ps -a
```

## Lab - Create a hello-world container
```
docker run --name hello-jegan hello-world:latest
docker ps
docker ps -a
```
Expected output
![image](https://github.com/user-attachments/assets/ade99371-5154-4e8c-b91e-8f9fec143352)

## Lab - Creating a container in foreground/interactive mode
```
docker run -it --name ubuntu-jegan --hostname ubuntu-jegan ubuntu:latest /bin/bash
pwd
whoami
ls -l
hostname
hostname -i
```
Expected output
![image](https://github.com/user-attachments/assets/99c0329b-a44f-4dde-8903-2cafaea7755e)

In the above command
<pre>
it - stands for interactive terminal
name - name of the container, using this docker server can identify a container
hostname - is name used on the os level
ubuntu:latest - is the docker image used to create the container
/bin/bash - is the application you wish to run within the container once the container is put to run
</pre>

To come out of the container shell, the below will also terminate(stops) the container
```
exit
```
Expected output
![image](https://github.com/user-attachments/assets/ac5fe7e7-7ab0-4fda-a7eb-fe5c91694837)

## Lab - Stopping a running container
```
docker ps | grep jegan
docker stop ubuntu-jegan
docker ps -a | grep jegan
```
Expected output
![image](https://github.com/user-attachments/assets/89f16c98-0da4-4821-9a96-73d08993ada7)


## Lab - Start the exited container, this will start the container in the background(daemon) mode
```
docker ps -a | grep jegan
docker start ubuntu-jegan
docker ps | grep jegan
```
Expected output
![image](https://github.com/user-attachments/assets/e445d03a-c6cd-46e8-b84f-39bbd866fda7)

## Lab - Restart a running container
```
docker restart ubuntu-jegan
```
Expected output
![image](https://github.com/user-attachments/assets/4a45eaf1-25a8-4567-94b9-5922622b8296)


## Lab - Rename a container
```
docker ps
docker rename <old-container-name> <new-container-name>
docker ps
```
Expected output
![image](https://github.com/user-attachments/assets/795cf60c-3651-480e-9115-cacdbc12c0e0)

## Lab - Getting inside a running container shell, the assumption is the container is running
```
docker exec -it ubuntu-jegan /bin/bash
ls
exit
```
Expected output
![image](https://github.com/user-attachments/assets/fb51db8b-aa38-4ce1-ad3e-7a909c38b3fe)

## Lab - Let's setup a load balancer with 3 web server behind the lb container
Let's create 3 web server containers, replace 'jegan' with your name
```
docker run -d --name nginx1-jegan --hostname nginx1-jegan bitnami/nginx:latest
docker run -d --name nginx2-jegan --hostname nginx2-jegan bitnami/nginx:latest
docker run -d --name nginx3-jegan --hostname nginx3-jegan bitnami/nginx:latest
```

Let's list the web server containers we created 
```
docker ps | grep jegan
```
Expected output
![image](https://github.com/user-attachments/assets/14c1196f-266a-4e90-b74d-27e72d143e32)

Let's create the load balancer container, in the below command the port 80 on the left has to be different for different participant, you can change it to maybe 81,82 or any port available on the server ( 100 to 65535 ).  The port on the right side is fixed, it has to be 8080 for bitnami/nginx:latest image.
```
docker run -d --name lb-jegan --hostname lb-jegan -p 80:8080 bitnami/nginx:latest
docker ps | grep jegan
```

Expected output
![image](https://github.com/user-attachments/assets/8ae6069f-7128-441e-9a5e-7e2d2734c6b7)

We need to find the IP address of our nginx web server containers
```
docker inspect -f {{.NetworkSettings.IPAddress}} nginx1-jegan
docker inspect -f {{.NetworkSettings.IPAddress}} nginx2-jegan
docker inspect -f {{.NetworkSettings.IPAddress}} nginx3-jegan
```
Expected output
![image](https://github.com/user-attachments/assets/a4fe436d-b837-4031-8e22-4b36b449ab95)

In order to configure the nginx-lb to work as a Load Balancer, we need the nginx.conf file from lb container, let's copy it to locl machine and configure it
```
docker cp lb-jegan:/opt/bitnami/nginx/conf/nginx.conf .
cat nginx.conf
```
Expected output
![image](https://github.com/user-attachments/assets/b5246d9f-d31a-4b29-9b45-74055f7eebfc)

You need to replace the IP address of your nginx web server containers in the copied nginx.conf file
Let's configure the lb container nginx.conf file as shown below
![image](https://github.com/user-attachments/assets/a38becd5-a9d0-426a-9eb9-316c0065d8a7)

You may use this as a reference to update your nginx.conf as shown below
```
# Based on https://www.nginx.com/resources/wiki/start/topics/examples/full/#nginx-conf
# user              www www;  ## Default: nobody

worker_processes  auto;
error_log         "/opt/bitnami/nginx/logs/error.log";
pid               "/opt/bitnami/nginx/tmp/nginx.pid";

events {
    worker_connections  1024;
}

http {
	upstream backend {
		server 172.17.0.2:8080;
		server 172.17.0.3:8080;
		server 172.17.0.4:8080;
	}

	server {
	        listen 8080;

		location / {
			proxy_pass http://backend;
		}
	}
}
```

We need to copy the updated nginx.conf from local machine to the lb container

docker cp nginx.conf lb-jegan:/opt/bitnami/nginx/conf/nginx.conf
```
In the above command, if you are using bitnami/nginx:latest image, you should the above path to /opt/bitnami/nginx/conf/nginx.conf

We need to restart the lb container to apply config changes
```
docker restart lb-jegan
docker ps
docker logs lb-jegan
```

In order to verify the pages are coming from which web server, let's customize the index.html files in all the web servers
```
echo "Web Server 1" > index.html
docker cp index.html nginx1-jegan:/app/index.html

echo "Web Server 2" > index.html
docker cp index.html nginx2-jegan:/app/index.html

echo "Web Server 3" > index.html
docker cp index.html nginx3-jegan:/app/index.html
```
In case you are using bitnami/nginx:latest image, the above path should be /app/index.html in the place of /usr/share/nginx/html/index.html

Now try to access the lb balancer using your local machine IP or just localhost from the web browser
```
http://localhost
```
Each time you refresh the page, you are supposed to get response from different web server containers in a round robin fashion.
