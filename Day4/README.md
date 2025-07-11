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

## Info - Ingress
<pre>
- it is not a service
- it is a Kubernetes/Openshift resource to define routing(forwarding) rules
- the ingress rules are picked up by the Ingress Controller and then it configure the HAProxy or Nginx(F5)
- in case of Openshift, out of the box it comes with Ingress Controller
- in our lab, I have used HAProxy Load Balancer, hence HAProxy Ingress Controller will setup the routing rules for us when we create ingress rule
- ingress represents a group of services ( clusterip, nodeport, loadbalancer services )
</pre>

Let's deploy nginx
```
cd ~/openshift-july-2025
git pull
cd Day4/ingress
oc project jegan

oc apply -f nginx-deploy.yml
oc apply -f hello-deploy.yml
oc apply -f nginx-svc.yml
oc apply -f hello-svc.yml
oc apply -f ingress.yml

oc get svc
oc describe svc/nginx
oc describe svc/hello
oc get ingress
oc describe ingress/tektutor

curl http://tektutor.apps.ocp4.palmeto.org/nginx
curl http://tektutor.apps.ocp4.palmeto.org/hello
```

## Lab - Deploying your application using S2I docker strategy
Note
<pre>
- S2I - means Source to Image
- In Kubernetes, application can be deployed in cluster only using existing container image
- In Openshift, apart from deploying application using existing container image, it supports S2I
- S2I source strategies like docker, source
- In case of docker strategy, Openshift generates a Build Config with that captures the github repo url and strategy that must be used to build
- Openshift start a build, this creates an instance of Build Config called Build
- The Build Controller, monitors the Build resource and spins a Pod to perform the build activity
- The Build Pod, clones the GitHub repository and looks for Dockerfile as we mentioned docker strategy
- In Docker strategy, it performs a custom image build using the Dockerfile
- As part of image build, as per the instructions mentioned in the Dockerfile the application source is compiled to generate an application executable
- With the application binary, it creates a custom image as mentioned in the Dockerfile, pushes the image into internal Openshift Image Registry
- Using the newly pushed image in OpenShift Image Registry, the application is deployed in your project namespace
- The BuildConfig also automatically creates a service for the application
- We need to create a route to access the application to test it further
</pre>

```
oc delete project jegan
oc new-project jegan

oc new-app --name=hello \
https://github.com/tektutor/openshift-july-2025.git \
--context-dir=Day4/hello-microservice \
--strategy=docker

oc expose svc/hello
oc logs -f bc/hello

curl http://hello-jegan.apps.ocp4.palmeto.org
```

Expected output
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/9a0e159e-8fa1-472b-9879-eb2daaf24edc" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/ed1bdb6b-8c51-4c61-978b-7f666791253d" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/927ac1bd-3362-4c4a-b62a-9e2b5ad1ed18" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/b61d7df9-8823-411f-936a-004b492cf42c" />

## Lab - Deploying your application using S2I source strategy

Note
<pre>
- S2I - means Source to Image
- In Kubernetes, application can be deployed in cluster only using existing container image
- In Openshift, apart from deploying application using existing container image, it supports S2I
- S2I source strategies like docker, source
- In case of source strategy, Openshift generates a Build Config with that captures the github repo url and strategy that must be used to build
- Openshift start a build, this creates an instance of Build Config called Build
- The Build Controller, monitors the Build resource and spins a Pod to perform the build activity
- The Build Pod, clones the GitHub repository and uses the Docker Image mentioned in the new-app command for the Build purpose
- The application source code is compiled to generate an application executable using the Docker Image we suggested in the new-app command 
- Build Config generates a custom image injecting the applicaiton binary as the startup application, pushes the image into internal Openshift Image Registry
- Using the newly pushed image in OpenShift Image Registry, the application is deployed in your project namespace
- The BuildConfig also automatically creates a service for the application
- We need to create a route to access the application to test it further
</pre>
```
oc delete project jegan
oc new-project jegan

oc new-app --name=hello \
registry.access.redhat.com/ubi8/openjdk-17:1.15-1.1682053058~https://github.com/tektutor/openshift-july-2025.git \
--context-dir=Day4/hello-microservice \
--strategy=source

oc expose svc/hello
oc logs -f bc/hello
oc get route

curl http://hello-jegan.apps.ocp4.palmeto.org
```

Expected output
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/aa3705f3-8bd7-411f-b5b9-582a51c9eed9" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/bc90fbb4-c620-4270-b9fb-453de6e2494c" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/420627b8-d792-4f42-bdfd-a2423653e531" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/fccedbc9-7afc-4907-8178-0f631b31b630" />

If you wish to list out the imagestreams, buildconfig, build deployment, etc
```
oc get all
oc describe deploy/hello
oc describe imagestream/hello
oc describe buildconfig/hello
oc describe build/hello
```

## Lab - Scheduler affinity Preffered and Required

Deploy requred-deploy.yml when no nodes has disk=ssd label
```
cd ~/openshift-july-2025
git pull
cd Day5/scheduler
oc apply -f preferred-deploy.yml
oc get po -o wide
```
We can notice that even though no nodes meets the criteria, scheduler went ahead and deployed the pods.

Now, let's delete the deployment, assign disk=ssd label to worker2 node and redploy
```
oc delete -f preferred-deploy.yml
oc label node/worker02.ocp4.palmeto.org disk=ssd
oc get nodes -l disk=ssd

oc apply -f preferred-deploy.yml
oc get pod -o wide
```

Now you may notice, all the pods gets deployed into worker2 node as only worker2 node has SSD disk. 

Let's understand the required affinity condition
```
oc delete -f preferred-deploy.yml
oc apply -f required-deploy.yml
oc get po -o wide
```

Now you may notice, all the pods are deployed into worker2 node as only worker2 node has SSD disk.
```
oc delete -f preferred-deploy.yml

oc label node worker02.ocp4.palmeto.org disk-

oc apply -f required-deploy.yml
oc get po -o wide
```
Now all the pods will be in pending state as no nodes meets the criteria.  This is the difference between Preferred and Required Scheduler Affinity.


## Info - Helm 
<pre>
- it is a package manager for Kubernetes & Openshift applications
- anyone who deployed multi-pod application with declarative manifest scrips will know how difficult it would be to follow certain order while deploy and reverse order while undeploying the application
- in case we skip the order, things take longer time to figure out or cleanup the deployed resources
- Instead we could use helm to package our application as Helm charts
- Helm can be used to bundle/package many manifest(yaml) files into Helm Charts, can be used to install/uninstall/upgrade application neatly
</pre>

## Demo - Installing Helm in the lab machine
```
cd ~
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod +x ./get_helm.sh
sudo ./get_helm.sh
```

<img width="1909" height="638" alt="image" src="https://github.com/user-attachments/assets/38ead400-f584-4cc7-a77c-2fb60871cd6f" />

## Lab - Packaging our wordpress deployment into a Helm Chart and deploy/undeploy wordpress using helm package manager 

```
cd ~/openshift-july-2025
git pull
cd Day5/helm
helm create wordpress
tree wordpress
cd wordpress/templates
rm -rf *
cd ../..
cp manifest-scripts/*.yml wordpress/templates
echo "" > wordpress/values.yaml

helm package wordpress
helm install wordpress wordpress
```
<img width="1909" height="977" alt="image" src="https://github.com/user-attachments/assets/b7a204b9-589e-4ad9-a845-61904c76f858" />
<img width="1909" height="977" alt="image" src="https://github.com/user-attachments/assets/32b25312-3c31-4662-be63-aed88b816c55" />
<img width="1909" height="1084" alt="image" src="https://github.com/user-attachments/assets/8c7342d1-4b60-4dea-95cd-a0400a01be7a" />
<img width="1909" height="1084" alt="image" src="https://github.com/user-attachments/assets/1e77db06-d374-4eaf-bc09-c35bde6bccb1" />
