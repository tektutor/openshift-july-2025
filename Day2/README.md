# Day 2

## Info - Container Orchestration Platform Overview
<pre>
- Container Orchestration Platforms supports below features
  - HA availability to our applications
  - Scale up/down applications either manually/automatically based on traffic or rules
  - Rolling update
    - Upgrading application from one version to other without any down time
    - Revert to older stable version if new version is found to be unstable or faulty
  - Defining Network Policy
    - control who can access certain applications
    - control which applications within container orchestration can access other applications running in container orchestration platform
  - In built monitoring facility
    - to check the health of our applications periodically and repair them when required
    - to check if our application is not just running but it is also responding
    - performance
    - checking application logs for troubleshooting
 - User Management
   - Role Based Access Control to ensure only authorized users can access your applications securely
 - Service
   - decide what is the scope of your application
   - should it be restricted to cluster-level access or should it be accessible 
     outside the orchestration platform as well  
- Examples
  - Docker SWARM
  - Google Kubernetes
  - Red Hat Openshift
</pre>

## Info - Docker SWARM
<pre>
- is Docker's native Container Orchestration Platform
- is opensource
- it is light-weight, hence can be setup quickly in a laptop/desktop with normal system configuration
- very user-friendly, good for learning, R&D, dev/qa setup
- not a production-grade Container Orchestraiton Platform
- supports only Docker based containerized application workloads
</pre>

## Info - Kubernetes
<pre>
- is an opensource Container Orchestration Platform from Google
- Developed in Go lang
- easy to setup, robust, time tested
- it is production-grade
- mostly command-line, doesn't support production-grade Dashboard
- community support is there, in case you are struck with some production issues
- Kubernetes can be installed locally ( on-prem ), public/private/hybrid clouds
- AWS, Azure, GCP also provide managed Kubernetes Clusters in their public cloud
- The popular Managed Kubernetes Services are
  - AWS - eks (Elastic Kubernetes Service )
    - One can get support from Amazon 
    - they are very expensive than Azure but highly reliable
    - more reliable than Azure
  - Azure - AKS ( Azure Kubernetes Service )
    - One can get support from Microsoft
    - they are very expensive though
- One big difference between Docker SWARM and Kubernetes
  - Docker SWARM only supports Docker containerized application workloads
  - Kubernetes supports any Container Runtime/Engine that implements the CRI
- Supports 3 types of Services
  - ClusterIP Service (Internal service)
  - NodePort Service ( External Service )
  - LoadBalancer Service ( External Service - generally used in public cloud environments like eks/aks )
    - in order to support this in on-prem k8s setup, we could use Metallb operator
- Kubernetes 
  - allows deploying application only using existing container images
  - not possible to deploy application from source code
- Kubernetes allows one to extend the Kubernetes features by 
  - Custom Resource Definitions (CRD)
  - Custom Controllers
  - Custom Operators
</pre>

## Info - Red Hat Openshift
<pre>
- it is Red Hat's Distribution of Kubernetes
- Red Hat Openshift comes with many additional features on top of Kubernetes
- Red Hat Openshift is an Enterprise product that comes with commercial license when used for profit
- supports both CLI and Webconsole Graphic User Interface
- supports User Management
- supports all the features offered by Kubernetes
- Supports many additional features
  - Route to access your application externally with a user-friendly url
- S2I ( Source to Image ) - new feature which is supported by Openshift and not available in Kubernetes
  - this allows one to deploy application from source code ( GitHub, GitLab, etc )
  - application build can be done within Openshift
- Virtualization 
  - can be done within Red Hat Openshift
- Openshift also enforces many best practices unlike Kubernetes
- Openshift reserves ports 0 thru 1024 for internal use, they are not available for user applications
- Openshift supports only Red Hat Enterprise Core OS (RHCOS) in Openshift master nodes
- Openshift allows using either Red Hat Enterprise Linux(RHEL) or RHCOS in Openshift worker nodes
- Openshift recommends using RHCOS in master and worker nodes
- In case your Openshift cluster uses RHCOS for master nodes and RHEL in worker nodes, then upgrading Openshift from webconsole or using oc command will not work
- Openshift supports new features like Build, BuildConfig which aren't supported by Kubernetes 
- comes with Prometheus monitoring tool with Graphana visual dashboards pre-integrated
- comes with pre-integrated RedHat marketplace from where we could selectively install additional operators
- comes with Internal Image Registry
- Public cloud vendors like AWS and Azure provided Managed Red Hat Openshift clusters
  AWS
  - ROSA
  Azure
  - ARO
</pre>

## Info - Cluster
<pre>
- Group of physical servers or Group of virtual machines running locally, or a group of ec2 instances running in public AWS cloud
- cluster is a group of servers
  - these servers are normally called nodes
  - these servers comes with some Operating Systems
  - there are types of servers
    1. Master Node
    2. Worker Nodes
- In a Cluster, there will be many master and worker nodes, put together they are called cluster
</pre>

## Info - OpenShift Master Node
<pre>
- this can be Physical Server or a Virtual Machine locally or on cloud with RHCOS Operating System installed in it
- this nodes comes with pre-installed Podman Container Engine and CRI-O container runtime
- common components 
   - kube-proxy
   - CoreDNS
   - kubelet 
- In Kubernetes/Openshift Master Nodes a special set of components will be running 
- the special set of components are officially referred as Control Plan Components
- control plane components
  - API Server
  - etcd  
  - Scheduler
  - Controller Managers
- Control Plane components collectively supports the Container Orchestration features
</pre>

## Info - Openshift Worker Node
<pre>
- this can be a Physical Server or a Virtual Machine locally or on cloud with either RHEL or RHCOS Operating System installed in it 
- this is where user applications will be running
- in addition to the user applications, some minimal Openshift common components also runs there
  - kubelet - a service that communicates with the CRI-O Container Runtime via the CRI interface
  - kubelet runs on both master and worker nodes
  - kube-proxy, CoreDNS
</pre>
