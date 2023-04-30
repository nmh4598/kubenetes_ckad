# Solution – CKA Mock Exam 1 (optional)

## 1. Deploy a pod named ``nginx-pod`` using the ``nginx:alpine`` image.

Once done, click on the ``Next Question`` button in the top right corner of this panel. You may navigate back and forth freely between all questions. Once done with all questions, click on ``End Exam``. Your work will be validated at the end and score shown. Good Luck!

- Name: nginx-pod
- Image: nginx:alpine

```bash
controlplane ~ ➜  k run nginx-pod --image=nginx:alpine
pod/nginx-pod created
```

```bash
controlplane ~ ➜  k get pod
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          10s

controlplane ~ ➜  k describe pod nginx-pod
Name:             nginx-pod
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.34.235.3
Start Time:       Sun, 30 Apr 2023 14:21:37 -0400
Labels:           run=nginx-pod
Annotations:      <none>
Status:           Running
IP:               10.244.0.4
IPs:
---
```

## 2. Deploy a messaging pod using the redis:alpine image with the labels set to tier=msg.

Solution: 

Use the command 

    kubectl run messaging --image=redis:alpine -l tier=msg

## 3. Create a namespace named ``apx-x9984574.``

Solution: 

Run the command:

    kubectl create namespace apx-x9984574

## 4. Get the list of nodes in JSON format and store it in a file at ``/opt/outputs/nodes-z3444kd9.json``

Run the command: 

    kubectl get nodes -o json > /opt/outputs/nodes-z3444kd9.json

## 5. Create a service`` messaging-service`` to expose the ``messaging`` application within the cluster on port ``6379.``

Use imperative commands.

Solution :

Run the command: 

    kubectl expose pod messaging --port=6379 --name messaging-service

## 6. Create a deployment named ``hr-web-app`` using the image ``kodekloud/webapp-color`` with ``2`` replicas.

```bash
kubectl create deployment hr-web-app --image=kodekloud/webapp-color --replicas=2
```

## 7. Create a static pod named`` static-busybox`` on the controlplane node that uses the ``busybox`` image and the command ``sleep 1000``.

Solution 
Create a pod definition file in the manifests directory. For that use command
```bash
kubectl run --restart=Never --image=busybox static-busybox --dry-run=client -oyaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yaml
```

## 8. Create a POD in the finance namespace named temp-bus with the image ``redis:alpine.``

```bash
kubectl run temp-bus --image=redis:alpine --namespace=finance --restart=Never
```

## 9. A new application ``orange`` is deployed. There is something wrong with it. Identify and fix the issue.

Solution : 
To know more details of ``orange`` pod:
```bash
kubectl describe po orange
```
and look under the ``initContainers`` section. There is an issue with the given command.
To update the pod with an easiest way by running command:
```bash
$ kubectl edit po orange
```
It's not possible to update the changes in the running pod so after saving the changes. It will create a temporary file in the default location ``/tmp/``.
Use that manifest file and replace with the existing pod:
```bash
kubectl replace -f /tmp/kubectl-edit-xxxx.yaml --force
```
Above command will delete the existing pod and will recreate the new pod with latest changes.

## 10. Expose the ``hr-web-app`` as service ``hr-web-app-service`` application on port ``30082`` on the nodes on the cluster.

The web application listens on port ``8080.``
```bash
kubectl expose deployment hr-web-app --type=NodePort --port=8080 --name=hr-web-app-service --dry-run=client -o yaml > hr-web-app-service.yaml
```
to generate a service definition file.

Now, in generated service definition file add the ``nodePort`` field with the given port number under the ``ports`` section and create a service.

## 11. Use JSON PATH query to retrieve the osImages of all the nodes and store it in a file`` /opt/outputs/nodes_os_x43kj56.txt``.

The ``osImages`` are under the ``nodeInfo`` section under ``status`` of each node.
```bash
kubectl get nodes -o jsonpath='{.items[*].status.nodeInfo.osImage}' > /opt/outputs/nodes_os_x43kj56.txt
```
## 12. Create a `Persistent Volume` with the given specification.

Solution manifest file to create a persistent volume ``pv-analytics`` as follows:
```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-analytics
spec:
  capacity:
    storage: 100Mi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  hostPath:
      path: /pv/data-analytics
```

```bash

```

```bash

```

```bash

```

```bash
```
```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash
```
```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash
```
```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash
```
```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash
```