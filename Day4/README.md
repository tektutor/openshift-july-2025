# Day 4

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

