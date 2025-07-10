# Day 3

## Info - What happens when we run the below command?
```
oc create deployment nginx --image=bitnami/nginx:latest --replicas=3
```
![Openshift Internals](openshift-internals.png)
The below chain of activities happen internally within the Kubernetes/Openshift cluster
<pre>
- oc client tool makes a REST call to API server requesting it to create a deployment named nginx
- The API Server receives the request, it then creates a Deployment database record(YAML) in etcd database
- API Server then triggers a broadcasting event saying New Deployment created
- Deployment Controller receives this event, it then retrieves the details from the event
- Deployment Controller makes a REST call to API server, requesting API Server to create a ReplicaSet for the deployment nginx
- API Server creates a ReplicaSet reccord in the etcd database
- API Server sends a broadcasting event saying new ReplicaSet created
- ReplicaSet Controller receives the event, it then retrieves the details from the event
- ReplicaSet Controller makes a REST call to API Server, requesting API Server to create 3 Pods
- API Server creates 3 Pod database entries in etcd
- API Server sends broadcasting event saying new Pod created( this happens for each Pod )
- Scheduler receives this event, it then identifies healthy nodes where these Pods can be scheduled
- Scheduler makes REST call to API Server sharing its scheduling recommendations
- API Server retrieves the Pod entries for which Scheduler has shared the scheduling recommendation, it then updates those Pod database entries in the etcd database
- API Server sends broadcasting events saying Pod scheduled to so and so Node
- Kubelet Container Agent running on the respective node receives this event, it then downloads the necessary container image with the help of CRI-O container runtime, it then creates the necessary container including pause containers
- Kubelet makes a REST call to API Server sharing regular updates about the health of each container running on that node, each container has a mapping details for respective Pods
- API Server receives the REST call status update from kubelet, it then updates the Pod status in the ectd database
  
</pre>

## Lab - Pod port-forward for quick testing
Execute the below command in one terminal
```
oc project jegan
oc get pods
oc port-forward pod/<your-pod-name> 9999:8080
```
While the above command is running in a terminal, you can open another terminal and type the below command to access the web page served by that pod ( Try the below in a second terminal )
```
curl http://127.0.0.0:9999
```

Once you are done with the testing, you can go to the first terminal and press Ctrl + c to stop port-forwarding.

Port forward is used for testing purpose only, not to be used in production as it is accessible only on the machine where port-forwarding is done.

## Lab - Creating an internal ClusterIP Service for our nginx deployment

Kubernetes/Openshift Service
<pre>
- represents a group of load-balanced Pods from a single deployment
- the load balancing is done by kube-proxy(Pod) running in every node
- the kube-proxy will identify the Pods from a specific deployment using the selector label mentioned in nginx or respective deployment
</pre>

```
oc project jegan
oc expose deploy/nginx --type=ClusterIP --port=8080
```

List the services
```
oc get services
oc get service
oc get svc
```

Finding more details about a service
```
oc describe svc/nginx
```

Expected output
![image](https://github.com/user-attachments/assets/721d4379-3124-4f6e-b343-a9a412df9ae9)

Testing the ClusterIP Internal service
```
oc create deployment test --image=tektutor/spring-ms:1.0
oc get po
oc rsh deploy/test
```

Another recommended way you can test this service is by creating route
```
oc get svc
oc expose svc/nginx
```

List the routes
```
oc get routes
```

Now you can test it 
```
curl http://nginx-jegan.apps.ocp4.palmeto.org
```

## Lab - Finding the webconsole url
```
oc whoami --show-console
```

## Lab - Finding the API Server REST Endpoint url
```
oc whoami --show-server
```

## Lab - Deploying nginx in declarative style
```
oc delete project/jegan
oc new-project jegan
oc create deployment nginx --image=bitnami/nginx:latest --replicas=3 --dry-run=client -o yaml

oc create deployment nginx --image=bitnami/nginx:latest --replicas=3 --dry-run=client -o yaml > nginx-deploy.yml
```

Edit the nginx-deploy.yml file and add as shown below in the screenshot
<pre>
imagePullPolicy: IfNotPresent  
</pre>
![image](https://github.com/user-attachments/assets/34385530-624b-4a11-9b4c-7da8651ff21e)

Once you updated the imagePullPolicy, you may proceed with the commands shown below
```
oc create -f nginx-deploy.yml --save-config
oc get deploy,rs,po
```

The oc create command must be used only the first time, if you modify the yaml, we need to use apply to apply changes on the existing resources
```
oc apply -f nginx-deploy.yml
oc get pods
```

## Lab - Creating ClusterIP Internal Service for nginx deployment in declarative style
```
oc expose deploy/nginx --type=ClusterIP --port=8080 --dry-run=client -o yaml

oc expose deploy/nginx --type=ClusterIP --port=8080 --dry-run=client -o yaml > nginx-clusterip-svc.yml
oc expose deploy/nginx --type=NodePort --port=8080 --dry-run=client -o yaml > nginx-nodeport-svc.yml
oc expose deploy/nginx --type=LoadBalancer --port=8080 --dry-run=client -o yaml > nginx-lb-svc.yml

ls -l nginx*svc.yml
oc apply -f nginx-clusterip-svc.yml
oc get svc
oc describe svc/nginx
```

## Lab - Creating NodePort External Service for nginx deployment in declarative style
We need to first delete the clusterIP service as we wish to use the same name for the nginx nodeport service
```
oc delete -f nginx-clusterip-svc.yml
```
Now you may proceed as shown below to create nodeport service
```
oc expose deploy/nginx --type=NodePort --port=8080 --dry-run=client -o yaml > nginx-nodeport-svc.yml

ls -l nginx*svc.yml
oc apply -f nginx-nodeport-svc.yml
oc get svc
oc describe svc/nginx
```

## Lab - Creating LoadBalancer External Service for nginx deployment in declarative style
We need to first delete the nodeport service as we wish to use the same name for the nginx nodeport service
```
oc delete -f nginx-nodeport-svc.yml
```
Now you may proceed as shown below to create nodeport service
```
oc expose deploy/nginx --type=LoadBalancer --port=8080 --dry-run=client -o yaml > nginx-lb-svc.yml

ls -l nginx*svc.yml
oc apply -f nginx-lb-svc.yml
oc get svc
oc describe svc/nginx
```

## Lab - Adding a Custom Resource called Training

Create a file named training-crd.yml with the below content
<pre>
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: trainings.tektutor.org 
spec:
  group: tektutor.org 
  scope: Namespaced
  names:
    kind: Training 
    listKind: TrainingList
    plural: trainings 
    singular: training 
    shortNames:
    - train 

  versions:
    - name: v1
      served: true
      storage: true 
      schema:
        openAPIV3Schema:
          type: object
          properties:
              training:
                type: string
              duration:
                type: string
              city:
                type: string
              from:
                type: string
              to:
                type: string  
</pre>

Let's creating a devops-training.yml object
<pre>
apiVersion: tektutor.org/v1
kind: Training 
metadata:
  name: devops-training 
spec:
  training: "Advanced DevOps"
  duration: "5 Days" 
  city: "Bengaluru"
  from: "4th Nov 2025"
  to: "8th Nov 2025"  
</pre>

## Lab - Installing Metallb Operator in Red Hat Openshift
<pre>
- In case of on-prem bare-metal Openshift setup like our training lab, LoadBalancer service will not work out of the box
- LoadBalancer service is meant for managed Kubernetes/Openshift setup offered by public cloud like AWS/Azure
- Hence, LoadBalancer service in AWS ROSA spins an Applicaiton Loader Balancer(ALB) or Network Loader Balancer(NLB)
- In case of AWS ROSA it comes with AWS Load Balancer Operator which installs an AWS Load Balancer Controller, 
  hence when we create LoadBalancer service for our application, the AWS Load Balancer Controller automatically detects 
  that we had created LoadBalancer service and it then spins an ALB or NLB to load balance our applicaiton pods 
- In the local Openshift cluster setup, we need to install Metallb Operator to provide similar functionality
</pre>

Let's install the Metallb Operator from Openshift web console
![image](https://github.com/user-attachments/assets/4b5db602-1b46-4a96-9617-ac9ce7cdbc56)
![image](https://github.com/user-attachments/assets/c87ba32e-dd31-4a27-ac5f-1af6e9718ee3)
![image](https://github.com/user-attachments/assets/ab65a235-e645-4e02-9b4a-7b24c88a8186)
![image](https://github.com/user-attachments/assets/a376c4eb-1577-4de5-9338-baf4c8fb395d)
![image](https://github.com/user-attachmentsassets/5b5e97ed-499f-4f72-b5b1-1b614608fa59)
![image](https://github.com/user-attachments/assets/38b73f04-3222-4745-adc8-3b3725c939d5)
![image](https://github.com/user-attachments/assets/373e6253-945f-4edd-867d-7a8fe86a548e)
![image](https://github.com/user-attachments/assets/463d88fd-fa67-4a10-bd71-350b62a5b943)

Whenever we create a LoadBalancer service, the Metallb Controller assigns an external IP for each LoadBalancer service we create. 
Hence, we need to allocate a range of IP addresses that can be used by Metallb Controller to assign IP for our LoadBalancer services.

Let's create an address-pool.yml
<pre>
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: ip-addresspool-sample1
  namespace: metallb-system
spec:
  addresses:
    - 192.168.126.15-192.168.126.25
  autoAssign: true
  avoidBuggyIPs: false  
</pre>
![image](https://github.com/user-attachments/assets/ec76669a-43f3-4de7-aff4-46ec4cc7884a)

Let's allocate the IP range as shown below
```
oc apply -f address-pool.yml
```
![image](https://github.com/user-attachments/assets/8da72b00-ed4b-4c1e-9e2c-5a10865e10bf)

We need to activate the IP address pool we allocated otherwise our openshift cluster wouldn't recognize them
![image](https://github.com/user-attachments/assets/8ea2444a-1ae1-43cf-b5a0-8bc6180aa7f1)

Let's active the address pool we allocated as shown below
```
oc apply -f l2-advt.yml
```
![image](https://github.com/user-attachments/assets/4a4082ca-606d-473c-9847-f10bd58065c2)

We now need to create an instance of the metallb controller to activate/assign external ip for our loadbalancer services
![image](https://github.com/user-attachments/assets/71da29b0-6f34-4332-8042-180ea855928f)
![image](https://github.com/user-attachments/assets/67b84175-e0ea-454c-b468-489678f455dc)

Now, let's create a LoadBalancer service and see if the metallb controller is assigning an external ip address
```
oc project jegan
oc create deploy nginx --image=bitnami/nginx:latest --replicas=3 --dry-run=client -o yaml > nginx-deploy.yml
oc apply -f nginx-deploy.yml

# Create a LoadBalancer service
oc expose deploy/nginx --type=LoadBalancer --port=8080

# List the services
oc get svc
oc describe svc/nginx

# Access the external loadbalancer service
curl http://192.168.126.15:8080
```
![image](https://github.com/user-attachments/assets/5b5b8ad0-a0e1-4326-9b2a-f8ae3eff1c47)


