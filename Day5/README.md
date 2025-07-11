# Day 5

## Info - Docker Network Model
<pre>
- For each container that we create, Docker creates a pair of veth devices (virtual ethernet - software defined network card with network stack )
- One veth devices stays in the local machine, while the other veth device is used with the container as a network card
- When we install docker, docker creates a virtual switch ( software defined ) called docker0
- all the containers by default are connected to the docker0 default network
- the subnet (IP range) assigned for docker 0 is 172.17.0.0/16 ( 256 x 256 = 65536 IP addresses in this network )
- some IP addresses are reserved for internal use
- 172.17.0.1 is assigned to docker0 bridge, as it acts as a Gateway for all containers 
- it is through this Gateway containers can reach out to Internet, and Internet can reach out to containers
</pre>  

## Info - Kubernetes/Openshift Network Model
<pre>
- Kubernetes provides an interface called Container Network Interface (CNI)
- Any Container Engine or Container Runtime that must be supported by Kubernetes must implement the CNI
- Kubernetes kubelet container agent communicates with Container Runtime via the generic CNI interface
- Kubernetes provides only Network specification and it leaves the implementation details to the Network addons vendors
- There are many Network addons available for Kubernetes
- There are about 3 networks
  - Node Network ( Node to Node communication )
  - Pod Network ( Pod to Pod communication )
  - Service Network ( Pod to Service Communication )
- Pods are assigned with Private IPs
- As per Kubernetes Network specifications
  - Pods running in any node must be able to communicate with any Pods running in any node within K8s cluster
  - kubelet must be able to communicate with all the Pods running on the local node
- Network Addons vendors implemented the Kubernetes Network Specifications, some of the popular network addons are
  - Calico
  - Weave
  - Flannel
</pre>  

## Info - Flannel Overview
<pre>
- Flannel is implemented by CoreOS company
- this is very first Kubernetes network add-on that was developed
- it uses overlay network
- it support many backends including UDP
- the drawback of UDP backend in Flannel is performance degradation as each incoming packet has to de-encapsulated and each outgoing packet has to encpasulated
- Also Flannel doesn't support Network policy
</pre>  

## Info - Weave Overview
<pre>
- is developed by a company called WeavWorks
- it supports Pod Networking,Service Network and Node Networking
- it supports Kubernetes Network policy
</pre>  

## Info - Calico Overview
<pre>
- is developed by a company called Tigera
- it used BGP protcol, it is highly efficient, high-performance guaranteed
- it also supports Kubernetes network
- BGP protocol is powering internet (world wide web), when such a complex network is efficiently working with BGP, the same benefits can be expected within Kubernetes/Openshift if we use Calico
</pre>  

## Info - Openshift Network Policy
<pre>
- Openshift Network Policy support controlling the communication between microservices/containerized application workloads in Openshift/Kubernetes
- Assume we have 4 microsevices, A, B, C & D
- If you wish to allow communication from A -> B, but not B -> A
- If you wish to allow communication from B -> C, but not A -> C
- these kind of policies can be implemented using Openshift Network Policies
- this offers extra layer of security to your applications running within Kubernetes/Openshift
</pre>

## Lab - CICD Pipline

<img width="1909" height="1084" alt="image" src="https://github.com/user-attachments/assets/2a01f8fc-4752-41b5-b65f-08c475c2acd2" />

