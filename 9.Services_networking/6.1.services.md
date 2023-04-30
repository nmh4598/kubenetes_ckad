# Services 

## 1. How many Services exist on the system?

In the current(default) namespace

```bash
controlplane ~ ➜  k get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   4m51s

controlplane ~ ➜  k get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   4m57s
```
## 2. What is the type of the default kubernetes service?
ClusterIP

## 3. What is the targetPort configured on the kubernetes service?
```bash
controlplane ~ ➜  k describe svc kubernetes
Name:              kubernetes
Namespace:         default
Labels:            component=apiserver
                   provider=kubernetes
Annotations:       <none>
Selector:          <none>
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.43.0.1
IPs:               10.43.0.1
Port:              https  443/TCP
TargetPort:        6443/TCP
Endpoints:         192.32.161.6:6443
Session Affinity:  None
Events:            <none>
```
TargetPort:        6443/TCP

## 4. How many labels are configured on the kubernetes service?

2 Labels:            component=apiserver
                   provider=kubernetes

## 5. How many Endpoints are attached on the kubernetes service?
Endpoints:         192.32.161.6:6443 so 1 endpoints

## 6. What is the image used to create the pods in the deployment?
Update the /root/service-definition-1.yaml file as follows:

```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
  namespace: default
spec:
  ports:
  - nodePort: 30080
    port: 8080
    targetPort: 8080
  selector:
    name: simple-webapp
  type: NodePort
```
Run the following command to create a webapp-service service as follows: -

```bash
kubectl apply -f /root/service-definition-1.yaml
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
