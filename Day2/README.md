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
