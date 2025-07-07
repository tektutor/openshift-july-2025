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

## Docker Alternatives

## Docker High Level Architecture
