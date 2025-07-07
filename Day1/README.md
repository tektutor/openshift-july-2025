# Day 1

## Pre-test url ( Complete it from the Palmeto Cloud Machine )
<pre>
https://forms.cloud.microsoft/r/VnKfhdE33L  
</pre>  

## Boot Loaders
<pre>
- this is typically referred as dual/multi boot
- it is a system utility that is installed in the Master Boot Record(MBR)
- Sector 0, Byte 0 in your hard disk is called Master Boot Record, which is just 512 bytes
- in legacy BIOS, the boot loader has to fit within 512 bytes, but the modern UEFI BIOS allows the boot loader utility to be stored in hard disks, hence there is no size restrictions imposed
- When we boot a system, once the BIOS POST(Power On Self Test) completes, the BIOS will instruct the CPU to run the Boot Loader
- The Boot Loader then scans the hard disk(s) looking for Operating Systems
- In case the Boot Loader detects multiple OS, it then prepares a menu and gives us options to choose the OS you wish to boot into
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

## System Configuration
<pre>
- Intel Processor with 4 Physical Cores
- 16 GB RAM
- 512 GB HDD/SDD
- In this system, how many maximum VMs can run at the same time?
  - 4x2 = 8 Logical/Virtual Cores
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
  - this helps organizations save cost in terms of infrastructure, server procurement, better hardware utilization, real-estate cost (rent/lease)
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
- When we installed Docker in Windows, it installs a thin linux-layer which provides the Linux Kernel
- Linux containerized applications can run on Windows with the help of the Linux kernel that comes with Docker/Podman
- Linux containerized applications can run on Mac with the help of the Linux kernel that comes with Docker/Podman
</pre>

## Info - How Dot Net Containerized applications are supported in Linux/Mac?
<pre>
- MONO is Dot Net Framework implemented for Linux/Mac
- it is opensource framework which implements the .Net Specifications
- hence, technically you can develop,compile and run .Net applications natively in Linux/Mac
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

## Lab - Checking the docker version
```
docker --version
```

Expected output
![image](https://github.com/user-attachments/assets/8255be05-bfe3-4764-a9c5-2e444d16de1d)

## Lab - Finding more details about your docker installation
```
docker info
```

## Lab - Listing the docker images in the local docker registry
```
docker images
```

Expected ouptut
![image](https://github.com/user-attachments/assets/a50f6eee-67f9-41de-a8ff-a1ebad66476b)

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

## Lab - Deleting a docker image from local docker registry
```
docker images | grep hello-world
docker rmi hello-world:latest
docker images | grep hello-world
```

## Lab - Finding more details about a docker image in local registyr
```
docker image inspect mysql:latest
```
