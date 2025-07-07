# Day 1

## Pre-test url
<pre>
https://forms.cloud.microsoft/r/VnKfhdE33L  
</pre>  


## Boot Loaders
<pre>
- this is typicalled referred as dual/multi boot
- it is a system utilities that is installed in the Master Boot Record(MBR)
- Sector 0, Byte 0 in your hard disk which is just 512 bytes
- When we boot a system, once the BIOS POST(Power On Self Test) completes, the BIOS will instruct the CPU to run the Boot Loader
- The Boot Loader then scans the hard disk(s) looking for Operating Systems
- In case the Boot Loader detects multiple OS, it then prepares a menu and gives us options to choose the OS you wish to boot into
- Only one OS can be active at any point of time
</pre>  

## System Configuration
<pre>
- Intel Processor with 4 Physical Cores
- 16 GB RAM
- 512 GB HDD/SDD
- In this system, how many maximum VMs can run at the same time?
  - 4x2 = 8 Logical/Virtual Cores
  - 1 Host OS + 7 Guest OS can run at the same time
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
    - Storage (HDD/SDD)
</pre>  

## Hypervisor Overview
<pre>
- is a virtualization technology
- It is a Hardware + Software Technology
  - You need a Processor that supports Virtualization
  - Intel, the virtualization feature is called VT-X
  - AMD, the virtualization feature is called AMD-V
- Processors supports Multiple CPU Cores
  - Each Physical Core supports two logical/virtual cores 
- it came around year 2000
- it disrupted the way the IT industry 
- this helps us run multiple OS in a single machine side by side
- more than one OS can be active at the same time
</pre>

## Containers Overview

## Docker Overview

## Docker Alternatives

## Docker High Level Architecture
