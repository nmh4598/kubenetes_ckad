# 1 
In this lab, you will get hands-on practice with creating Kubernetes objects imperatively.


All the questions in this lab can be done imperatively. However, for some questions, you may need to first create the YAML file using imperative methods. You can then modify the YAML according to the need and create the object using kubectl apply -f command.

# 2
Deploy a pod named nginx-pod using the nginx:alpine image.

Use imperative commands only.

````
kubectl run nginx-pod --image=nginx:alpine
````

# 3 
Deploy a redis pod using the redis:alpine image with the labels set to tier=db.

Either use imperative commands to create the pod with the labels. Or else use imperative commands to generate the pod definition file, then add the labels before creating the pod using the file.

Ans:

Run the command to generate the definition file:
````
kubectl run redis --image=redis:alpine --dry-run=client -oyaml > redis-pod.yaml
````
Add given labels tier=db under the metadata section

````
apiVersion: v1
kind: Pod
metadata:
  labels:
    tier: db
  name: redis
spec:
  containers:
  - image: redis:alpine
    name: redis
  dnsPolicy: ClusterFirst
  restartPolicy: Always
````
Then run the command: kubectl create -f redis-pod.yaml to create the pod from the definition file.

OR

Use the imperative command: -
````
kubectl run redis -l tier=db --image=redis:alpine
````

# 4 

Create a service redis-service to expose the redis application within the cluster on port 6379.

Use imperative commands.
````
kubectl create service clusterip --help
````
````
kubectl expose pod redis --port=6379 --name redis-service
````
Check
````
kubectl get svc redis-service
kubectl describe svc redis-service
````

# 5

Create a deployment named webapp using the image kodekloud/webapp-color with 3 replicas.

Try to use imperative commands only. Do not create definition files.

````
kubectl create deployment webapp --image=kodekloud/webapp-color --replicas=3
````
Check
````
kubectl get deploy
````

# 6
Create a new pod called **custom-nginx** using the **nginx** image and expose it on container port **8080**.
````
kubectl run custom-nginx --image=nginx --port=8080
````

# 7
Create a new namespace called dev-ns.

Use imperative commands.
````
kubectl create namespace dev-ns 
````
or 
````
kubectl create ns dev-ns
````

# 8 
Create a new deployment called redis-deploy in the dev-ns namespace with the redis image. It should have 2 replicas.
````
kubectl create deployment redis-deploy --image=redis --replicas=2 -n dev-ns
````

# 9 
Create a pod called httpd using the image httpd:alpine in the default namespace. Next, create a service of type ClusterIP by the same name (httpd). The target port for the service should be 80.
````
kubectl run httpd --image=httpd:alpine --port=80 --expose
````