# Service accounts

## 1. How many Service Accounts exist in the default namespace?

```bash
controlplane ~ ➜  kubectl get serviceaccounts 
NAME      SECRETS   AGE
default   0         10m
dev       0         76s
```
2

## 2. What is the secret token used by the default service account?

```bash
controlplane ~ ➜  kubectl describe serviceaccounts default
Name:                default
Namespace:           default
Labels:              <none>
Annotations:         <none>
Image pull secrets:  <none>
Mountable secrets:   <none>
Tokens:              <none>
Events:              <none>
```
look at the Tokens field : none

## 3. We just deployed the Dashboard application. Inspect the deployment. What is the image used by the deployment?

```bash
controlplane ~ ➜  kubectl describe deployment
Name:                   web-dashboard
Namespace:              default
CreationTimestamp:      Thu, 27 Apr 2023 06:54:47 +0000
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               name=web-dashboard
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  name=web-dashboard
  Containers:
   web-dashboard:
    Image:      gcr.io/kodekloud/customimage/my-kubernetes-dashboard
    Port:       8080/TCP
    Host Port:  0/TCP
    Environment:
      PYTHONUNBUFFERED:  1
    Mounts:              <none>
  Volumes:               <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   web-dashboard-65b9cf6cbb (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  87s   deployment-controller  Scaled up replica set web-dashboard-65b9cf6cbb to 1
```
Image:      gcr.io/kodekloud/customimage/my-kubernetes-dashboard

## 4. What is the state of the dashboard? Have the pod details loaded successfully?

Failed

## 5. What type of account does the Dashboard application use to query the Kubernetes API?

**Hint**: As evident from the error in the web-dashboard UI, the pod makes use of a service account to query the Kubernetes API.

pods is forbidden: User "system:serviceaccount:default:default" cannot list resource "pods" in API group "" in the namespace "default"

Service Account

## 6. Which account does the Dashboard application use to query the Kubernetes API?

**Hint**: Again, the name of the Service Account is displayed in the error message on the dashboard. The deployment makes use of the default service account which we inspected earlier.

Ans: namespace "default"

## 7. Inspect the Dashboard Application POD and identify the Service Account mounted on it.

Run the command
```bash
controlplane ~ ➜  kubectl get po -o yaml 
apiVersion: v1
items:
- apiVersion: v1
  kind: Pod
  metadata:
    creationTimestamp: "2023-04-27T06:54:47Z"
    generateName: web-dashboard-65b9cf6cbb-
    labels:
      name: web-dashboard
      pod-template-hash: 65b9cf6cbb
    name: web-dashboard-65b9cf6cbb-k4q56
    namespace: default
    ownerReferences:
    - apiVersion: apps/v1
      blockOwnerDeletion: true
      controller: true
      kind: ReplicaSet
      name: web-dashboard-65b9cf6cbb
      uid: bdc4e0b9-c7b8-4d5b-bb1a-d6097d0729fc
    resourceVersion: "879"
    uid: d1729575-3d2f-4ff8-bd73-da1f83fb5968
  spec:
    containers:
    - env:
      - name: PYTHONUNBUFFERED
        value: "1"
      image: gcr.io/kodekloud/customimage/my-kubernetes-dashboard
      imagePullPolicy: Always
      name: web-dashboard
      ports:
      - containerPort: 8080
        protocol: TCP
      resources: {}
      terminationMessagePath: /dev/termination-log
      terminationMessagePolicy: File
      volumeMounts:
      - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
        name: kube-api-access-kcjcm
        readOnly: true
    dnsPolicy: ClusterFirst
    enableServiceLinks: true
    nodeName: controlplane
    preemptionPolicy: PreemptLowerPriority
    priority: 0
    restartPolicy: Always
    schedulerName: default-scheduler
    securityContext: {}
    serviceAccount: default
    serviceAccountName: default
    terminationGracePeriodSeconds: 30
    tolerations:
    - effect: NoExecute
      key: node.kubernetes.io/not-ready
      operator: Exists
      tolerationSeconds: 300
    - effect: NoExecute
      key: node.kubernetes.io/unreachable
      operator: Exists
      tolerationSeconds: 300
    volumes:
    - name: kube-api-access-kcjcm
      projected:
        defaultMode: 420
        sources:
        - serviceAccountToken:
            expirationSeconds: 3607
            path: token
        - configMap:
            items:
            - key: ca.crt
              path: ca.crt
            name: kube-root-ca.crt
        - downwardAPI:
            items:
            - fieldRef:
                apiVersion: v1
                fieldPath: metadata.namespace
              path: namespace
  status:
    conditions:
    - lastProbeTime: null
      lastTransitionTime: "2023-04-27T06:54:47Z"
      status: "True"
      type: Initialized
    - lastProbeTime: null
      lastTransitionTime: "2023-04-27T06:54:52Z"
      status: "True"
      type: Ready
    - lastProbeTime: null
      lastTransitionTime: "2023-04-27T06:54:52Z"
      status: "True"
      type: ContainersReady
    - lastProbeTime: null
      lastTransitionTime: "2023-04-27T06:54:47Z"
      status: "True"
      type: PodScheduled
    containerStatuses:
    - containerID: containerd://835a83fb3fea42f73c8ee6f1a7ecfdf0d61d9608f64fbb2eb3f425ea4ce4f6f4
      image: gcr.io/kodekloud/customimage/my-kubernetes-dashboard:latest
      imageID: gcr.io/kodekloud/customimage/my-kubernetes-dashboard@sha256:7d70abe342b13ff1c4242dc83271ad73e4eedb04e2be0dd30ae7ac8852193069
      lastState: {}
      name: web-dashboard
      ready: true
      restartCount: 0
      started: true
      state:
        running:
          startedAt: "2023-04-27T06:54:51Z"
    hostIP: 172.25.0.22
    phase: Running
    podIP: 10.42.0.9
    podIPs:
    - ip: 10.42.0.9
    qosClass: BestEffort
    startTime: "2023-04-27T06:54:47Z"
kind: List
metadata:
  resourceVersion: ""
```
and inspect serviceAccount.

Ans: serviceAccount: default

## 8. At what location is the ServiceAccount credentials available within the pod?

Run the command 
```bash
controlplane ~ ➜  kubectl describe pod
Name:             web-dashboard-65b9cf6cbb-k4q56
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/172.25.0.22
Start Time:       Thu, 27 Apr 2023 06:54:47 +0000
Labels:           name=web-dashboard
                  pod-template-hash=65b9cf6cbb
Annotations:      <none>
Status:           Running
IP:               10.42.0.9
IPs:
  IP:           10.42.0.9
Controlled By:  ReplicaSet/web-dashboard-65b9cf6cbb
Containers:
  web-dashboard:
    Container ID:   containerd://835a83fb3fea42f73c8ee6f1a7ecfdf0d61d9608f64fbb2eb3f425ea4ce4f6f4
    Image:          gcr.io/kodekloud/customimage/my-kubernetes-dashboard
    Image ID:       gcr.io/kodekloud/customimage/my-kubernetes-dashboard@sha256:7d70abe342b13ff1c4242dc83271ad73e4eedb04e2be0dd30ae7ac8852193069
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Thu, 27 Apr 2023 06:54:51 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      PYTHONUNBUFFERED:  1
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-kcjcm (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-kcjcm:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  7m57s  default-scheduler  Successfully assigned default/web-dashboard-65b9cf6cbb-k4q56 to controlplane
  Normal  Pulling    7m56s  kubelet            Pulling image "gcr.io/kodekloud/customimage/my-kubernetes-dashboard"
  Normal  Pulled     7m53s  kubelet            Successfully pulled image "gcr.io/kodekloud/customimage/my-kubernetes-dashboard" in 3.201071067s (3.201096487s including waiting)
  Normal  Created    7m53s  kubelet            Created container web-dashboard
  Normal  Started    7m53s  kubelet            Started container web-dashboard
```
look for volume mount path

Ans: /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-kcjcm (ro)

## 9. The application needs a ServiceAccount with the Right permissions to be created to authenticate to Kubernetes. The default ServiceAccount has limited access. Create a new ServiceAccount named dashboard-sa.
- Service Account Name: dashboard-sa

```bash
controlplane ~ ✖ kubectl create serviceaccount dashboard-sa
serviceaccount/dashboard-sa created

controlplane ~ ✖ k get sa
NAME           SECRETS   AGE
default        0         25m
dev            0         15m
dashboard-sa   0         2m4s
```

## 10. We just added additional permissions for the newly created dashboard-sa account using RBAC.

If you are interested checkout the files used to configure RBAC at /var/rbac. We will discuss RBAC in a separate section.


## 11. Enter the access token in the UI of the dashboard application. Click Load Dashboard button to load Dashboard

Create an authorization token for the newly created service account, copy the generated token and paste it into the token field of the UI.

To do this, run kubectl create token dashboard-sa for the dashboard-sa service account, copy the token and paste it in the UI.

```bash
controlplane ~ ➜  kubectl create token dashboard-sa
eyJhbGciOiJSUzI1NiIsImtpZCI6ImpXOG1lQXk0a0liZ2VINHd6TGVOU3MtMXBvVUlfNHFHMjFtNU5ZNWFtNlEifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiLCJrM3MiXSwiZXhwIjoxNjgyNTgzMTM0LCJpYXQiOjE2ODI1Nzk1MzQsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJkZWZhdWx0Iiwic2VydmljZWFjY291bnQiOnsibmFtZSI6ImRhc2hib2FyZC1zYSIsInVpZCI6ImZhMDY4NTg3LTUzMzgtNDk0MS05OTBmLTllM2E3ZmI5NGIxZCJ9fSwibmJmIjoxNjgyNTc5NTM0LCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6ZGVmYXVsdDpkYXNoYm9hcmQtc2EifQ.e1VrbIiCw5tX5TJfd02OIyZut808H0zT4eyvzfnJphsSJg096GwC5EbVX6JYUtdXpkc6d7TarY_mCGusv16x9BIwlnvoXDnXvA6y01JpIk9NTQoJoMgvoGzUOVrCOntAmETvA-7XrVow1LcD2AvZdidK8kNXPYWF91c9JGPAHt5j00RsnpR1RhIShAc5-34Lpbv23ueiEvo00M9eahgTRc4k7ATHbT1sFb2MCEXdraSaj-W5Ncf-VFNdOFuA4Frm7ZR7MGIRVhxOi-VTEQGwF_71GpydbUQzLi7EveV0IwVI0FfWo8u8ZhoRJ0AXlEJhvl743nhH433Xv6RdGfAX2Q
```

## 12. You shouldn't have to copy and paste the token each time. The Dashboard application is programmed to read token from the secret mount location. However currently, the default service account is mounted. Update the deployment to use the newly created ServiceAccount

Edit the deployment to change ServiceAccount from default to dashboard-sa.
- Deployment name: web-dashboard
- Service Account: dashboard-sa
- Deployment Ready

```bash
controlplane ~ ✖ k get deployment -o yaml > dashboard.yaml

controlplane ~ ➜  k get deployment
NAME            READY   UP-TO-DATE   AVAILABLE   AGE
web-dashboard   1/1     1            1           21m

controlplane ~ ➜  k delete deployment web-dashboard
deployment.apps "web-dashboard" deleted

vi dashboard.yaml
```

```bash
apiVersion: v1
items:
- apiVersion: apps/v1
  kind: Deployment
  metadata:
    annotations:
      deployment.kubernetes.io/revision: "1"
    creationTimestamp: "2023-04-27T06:54:47Z"
    generation: 1
    name: web-dashboard
    namespace: default
    resourceVersion: "883"
    uid: fa6d7004-8912-4689-a7a5-2d6ee983b6d9
  spec:
    progressDeadlineSeconds: 600
    replicas: 1
    revisionHistoryLimit: 10
    selector:
      matchLabels:
        name: web-dashboard
    strategy:
      rollingUpdate:
        maxSurge: 25%
        maxUnavailable: 25%
      type: RollingUpdate
    template:
      metadata:
        creationTimestamp: null
        labels:
          name: web-dashboard
      spec:
      #here
        serviceAccountName: dashboard-sa
        containers:
        - env:
          - name: PYTHONUNBUFFERED
            value: "1"
          image: gcr.io/kodekloud/customimage/my-kubernetes-dashboard
          imagePullPolicy: Always
          name: web-dashboard
          ports:
          - containerPort: 8080
            protocol: TCP
          resources: {}
```

```bash
controlplane ~ ➜  k apply -f dashboard.yaml
deployment.apps/web-dashboard created
```
## 13. Refresh the Dashboard application UI and you should now see the PODs listed automatically.


```bash
```