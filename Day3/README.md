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
```
oc project jegan
oc get pods
oc port-forward pod/<your-pod-name> 9999:8080
```
While the above command is running in a terminal, you can open another terminal and type the below command to access the web page served by that pod
```
curl http://127.0.0.0:9999
```
