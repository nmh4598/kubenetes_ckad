# Security Context

## 1. What is the user used to execute the sleep process within the ubuntu-sleeper pod?

```
controlplane ~ ➜  k get pods
NAME             READY   STATUS    RESTARTS   AGE
ubuntu-sleeper   1/1     Running   0          52s

controlplane ~ ➜  kubectl exec ubuntu-sleeper -- whoami
root
```

## 2. Edit the pod ubuntu-sleeper to run the sleep process with user ID 1010.

Note: Only make the necessary changes. Do not modify the name or image of the pod.

- Pod Name: ubuntu-sleeper
- Image Name: ubuntu
- SecurityContext: User 1010
The second command applies a manifest file that creates a new pod with the same name but with a securityContext that specifies a runAsUser of 1010.
```
spec:
  containers:
  - command:
    - sleep
    - "4800"
    image: ubuntu
    securityContext:
      runAsUser: 1010
    imagePullPolicy: Always
    name: ubuntu
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-kw5wm
      readOnly: true
```

````
controlplane ~ ➜  k edit pod ubuntu-sleeper
error: pods "ubuntu-sleeper" is invalid
A copy of your changes has been stored to "/tmp/kubectl-edit-2816374107.yaml"
error: Edit cancelled, no valid changes were saved.

controlplane ~ ✖ k replace --force -f /tmp/kubectl-edit-2816374107.yaml
pod "ubuntu-sleeper" deleted
pod/ubuntu-sleeper replaced
````

# 3. A Pod definition file named multi-pod.yaml is given. With what user are the processes in the web container started?

The pod is created with multiple containers and security contexts defined at the Pod and Container level.

````
controlplane ~ ➜  cat multi-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  securityContext:
    runAsUser: 1001
  containers:
  -  image: ubuntu
     name: web
     command: ["sleep", "5000"]
     securityContext:
      runAsUser: 1002

  -  image: ubuntu
     name: sidecar
     command: ["sleep", "5000"]
````
The User ID defined in the securityContext of the container overrides the User ID in the POD.: **1002**

# 4. With what user are the processes in the sidecar container started?

The pod is created with multiple containers and security contexts defined at the Pod and Container level.

The User ID defined in the securityContext of the POD is carried over to all the containers in the Pod.: **1001**

# 5. Update pod ubuntu-sleeper to run as Root user and with the SYS_TIME capability.

Note: Only make the necessary changes. Do not modify the name of the pod.

- Pod Name: ubuntu-sleeper
- Image Name: ubuntu
- SecurityContext: Capability SYS_TIME

````
controlplane ~ ✖ k edit pod ubuntu-sleeper
error: pods "ubuntu-sleeper" is invalid
A copy of your changes has been stored to "/tmp/kubectl-edit-3171825858.yaml"
error: Edit cancelled, no valid changes were saved.

controlplane ~ ✖ k replace --force -f /tmp/kubectl-edit-3171825858.yaml
pod "ubuntu-sleeper" deleted
pod/ubuntu-sleeper replaced
````
Add this: 
````
    capabilities:
        add: ["SYS_TIME"]
````
or 
````
    capabilities:
        add:
        - SYS_TIME
````
````
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper
  namespace: default
spec:
  containers:
  - command:
    - sleep
    - "4800"
    image: ubuntu
    name: ubuntu-sleeper
    securityContext:
      capabilities:
        add: ["SYS_TIME"]
````
