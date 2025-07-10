# Day 4

## Lab - Cloning this training repository
```
git clone https://github.com/tektutor/openshift-july-2025.git
cd openshift-july-2025
```


## Lab - Rolling update
<pre>
- Rolling update is a K8s feature which is also supported by Red Hat Openshift
- this feature helps us upgrade our live application from one version to another without any time
</pre>

Let's deploy nginx with docker image bitnami/nginx:1.18 declaratively
```
oc create deployment nginx --image=bitnami/nginx:1.18 --replicas=3 --dry-run=client -o yaml > nginx-deploy.yml
```

Make sure the imagePullPolicy is updated/added to convey the image must be pulled only when not present locally as shown below
<pre>
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: bitnami/nginx:1.18
        name: nginx
        imagePullPolicy: IfNotPresent
</pre>

Let's create the deploy with nginx 1.18 version
```
oc apply -f nginx-deploy.yml
oc get po
```
![image](https://github.com/user-attachments/assets/2702f177-2276-4d64-9026-bef807fd6bac)

Let's verify what container image is used by the nginx pods
```
oc get po
oc describe pod/nginx-f9f796dfd-4hkrr
```
![image](https://github.com/user-attachments/assets/50eed831-7132-4fae-8f4d-c05cdcdb2677)

Let's try to upgrade the nginx from version 1.18 to 1.19 using the Red Hat Openshift rolling update feature

Let's update the image in the nginx-deploy.yml as shown below
<pre>
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: bitnami/nginx:1.19
        name: nginx
        imagePullPolicy: IfNotPresent  
</pre>

Let's apply the changes
```
oc apply -f nginx-deploy.yml
```
![image](https://github.com/user-attachments/assets/a68d3f7b-3963-4dd9-9add-786e6dcc860a)
![image](https://github.com/user-attachments/assets/4822a5ef-ea45-4c9f-b617-026ab7739c66)
![image](https://github.com/user-attachments/assets/2370652e-41ed-41b2-a673-8822b35bdfe3)

## Lab - Canary Deployment Strategy to split the traffic to 2 different version of your application
Create your first deployment
```
oc create deployment nginx-v1 --image=bitnami/nginx:latest --replicas=3 --dry-run=client -o yaml > nginx-deploy-v1.yml
```

Make sure the imagePullPolicy is updated to IfNotPresent before you apply into the cluster
```
oc apply -f nginx-deploy-v1.yml
```

You may now create a clusterip service for the first deployment
```
oc expose deploy/nginx-v1 --port=8080
```
Create the second deployment
```
oc create deployment nginx-v2 --image=tektutor/spring-ms:1.0 --replicas=3 --dry-run=client -o yaml > nginx-deploy-v2.yml
```
Make sure the imagePullPolicy is updated to IfNotPresent before you apply into the cluster
```
oc apply -f nginx-deploy-v2.yml
```

You may now create a clusterip service for the first deployment
```
oc expose deploy/nginx-v2 --port=8080
```

Now let's create a route as shown belo
<pre>
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: nginx
  name: nginx
spec:
  host: nginx-jegan.apps.ocp4.palmeto.org
  port:
    targetPort: 8080
  to:
    kind: Service
    name: nginx-v1
    weight: 50
  alternateBackends:
  - kind: Service
    name: nginx  
</pre>

In the above yaml file, you need to replace 'jegan' with your project name before proceeding.

You may create the route now
```
oc apply -f route.yml
oc get route
oc describe route/nginx
```

You can access the route from web browser
```
nginx-jegan.apps.ocp4.palmeto.org 
```

## Lab - Deploying Wordpress and mysql multi-pod application that uses Persistent Volume and Claim
You need to modify the mysql-pv.yml, mysql-pvc.yml, mysql-deploy.yml, wordpress-pv.yml, wordpress-pvc.yml, wordpress-deploy.yml and replace 'jegan' with your name.  Also you need to update the IP address of NFS server to 192.168.10.200 if you are working on Server1 otherwise 192.168.10.202. 

```
cd ~/openshift-july-2025
git pull
cd Day4/wordpress-mysql-multi-pod-deployment-with-persistent-storage
./deploy.sh
```

If all goes well, in the Openshift webconsole you will see like this
![image](https://github.com/user-attachments/assets/3e7a9ae5-b062-4c2b-a34c-17de4a2fafda)

If you click on the wordpress route(the upward arrow on the wordpress), you are supposed to see the page below
![image](https://github.com/user-attachments/assets/454c3a3d-275d-49ab-90a5-7c5b98ceee6e)

The access mode mentioned in PV/PVC
<pre>
- In our Openshift cluster, we have 3 master and 3 workers nodes, total 6 nodes(Virtual Machine)
- ReadWriteOnce - All pods from the same openshift node can read and write to the volume(external storage)
- ReadWriteMany - All pods from every openshift nodes can read and write to the voluem(external storage)
</pre>
