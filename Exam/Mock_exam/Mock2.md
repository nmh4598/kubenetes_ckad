## 1. Take a backup of the etcd cluster and save it to /opt/etcd-backup.db.

```bash
export ETCDCTL_API=3
etcdctl snapshot save --endpoints https://[127.0.0.1]:2379 --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key  /opt/etcd-backup.db
```

## 2. Create a Pod called ``redis-storage`` with image: ``redis:alpine ``with a Volume of type ``emptyDir`` that lasts for the life of the Pod.

Specs on the below.

Solution: 
Use the command ``kubectl run`` and create a pod definition file for ``redis-storage`` pod and add volume.

Alternatively, run the command:

``kubectl run redis-storage --image=redis:alpine --dry-run=client -oyaml > redis-storage.yaml``
and add volume ``emptyDir`` in it.

Solution manifest file to create a pod ``redis-storage`` as follows
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: redis-storage
  name: redis-storage
spec:
  containers:
  - image: redis:alpine
    name: redis-storage
    volumeMounts:
    - mountPath: /data/redis
      name: temp-volume
  volumes:
  - name: temp-volume
    emptyDir: {}

```

## 3. Create a new pod called ``super-user-pod`` with image ``busybox:1.28``. Allow the pod to be able to set ``system_time``.

The container should sleep for 4800 seconds.

Solution:

Solution manifest file to create a pod ``super-user-pod`` as follows
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: super-user-pod
  name: super-user-pod
spec:
  containers:
  - command:
    - sleep
    - "4800"
    image: busybox:1.28
    name: super-user-pod
    securityContext:
      capabilities:
        add: ["SYS_TIME"]
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```
## 4. A pod definition file is created at`` /root/CKA/use-pv.yaml``. Make use of this manifest file and mount the persistent volume called ``pv-1``. Ensure the pod is running and the PV is bound.

mountPath: ``/data``

persistentVolumeClaim Name: ``my-pvc``

Solution: 

Add a p``ersistentVolumeClaim`` definition to pod definition file.

Solution manifest file to create a pvc`` my-pvc`` as follows:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
       storage: 10Mi

```
And then, update the pod definition file as follows:
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: use-pv
  name: use-pv
spec:
  containers:
  - image: nginx
    name: use-pv
    volumeMounts:
    - mountPath: "/data"
      name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: my-pvc
```
Finally, create the pod by running:
``kubectl create -f /root/CKA/use-pv.yaml``

## 5. Create a new deployment called ``nginx-deploy``, with image ``nginx:1.16`` and ``1 ``replica. Next upgrade the deployment to version ``1.17`` using rolling update.


Solution: 

Explore the ``--record`` option while creating the deployment while working with the deployment definition file. Then make use of the k``ubectl apply`` command to create or update the deployment.

To create a deployment definition file ``nginx-deploy``:
```bash
$ kubectl create deployment nginx-deploy --image=nginx:1.16 --dry-run=client -o yaml > deploy.yaml
```
To create a resource from definition file and to record:
```bash
$ kubectl apply -f deploy.yaml --record
```
To view the history of deployment ``nginx-deploy:``
```bash
$ kubectl rollout history deployment nginx-deploy
```
To upgrade the image to next given version:
```bash
$ kubectl set image deployment/nginx-deploy nginx=nginx:1.17 --record
```
To view the history of deployment ``nginx-deploy:``
```bash
$ kubectl rollout history deployment nginx-deploy
```

## 6. Create a new user called john. Grant him access to the cluster. John should have permission to ``create, list, get, update and delete pods`` in the development namespace . The private key exists in the location: ``/root/CKA/john.key`` and csr at ``/root/CKA/john.csr.``

``Important Note``: As of kubernetes 1.19, the CertificateSigningRequest object expects a ``signerName``.

Please refer the documentation to see an example. The documentation tab is available at the top right of terminal.

Solution: 

Solution manifest file to create a CSR as follows:
```yaml
---
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: john-developer
spec:
  signerName: kubernetes.io/kube-apiserver-client
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VhbTlvYmpDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQUt2Um1tQ0h2ZjBrTHNldlF3aWVKSzcrVVdRck04ZGtkdzkyYUJTdG1uUVNhMGFPCjV3c3cwbVZyNkNjcEJFRmVreHk5NUVydkgyTHhqQTNiSHVsTVVub2ZkUU9rbjYra1NNY2o3TzdWYlBld2k2OEIKa3JoM2prRFNuZGFvV1NPWXBKOFg1WUZ5c2ZvNUpxby82YU92czFGcEc3bm5SMG1JYWpySTlNVVFEdTVncGw4bgpjakY0TG4vQ3NEb3o3QXNadEgwcVpwc0dXYVpURTBKOWNrQmswZWhiV2tMeDJUK3pEYzlmaDVIMjZsSE4zbHM4CktiSlRuSnY3WDFsNndCeTN5WUFUSXRNclpUR28wZ2c1QS9uREZ4SXdHcXNlMTdLZDRaa1k3RDJIZ3R4UytkMEMKMTNBeHNVdzQyWVZ6ZzhkYXJzVGRMZzcxQ2NaanRxdS9YSmlyQmxVQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRQ1VKTnNMelBKczB2czlGTTVpUzJ0akMyaVYvdXptcmwxTGNUTStsbXpSODNsS09uL0NoMTZlClNLNHplRlFtbGF0c0hCOGZBU2ZhQnRaOUJ2UnVlMUZnbHk1b2VuTk5LaW9FMnc3TUx1a0oyODBWRWFxUjN2SSsKNzRiNnduNkhYclJsYVhaM25VMTFQVTlsT3RBSGxQeDNYVWpCVk5QaGhlUlBmR3p3TTRselZuQW5mNm96bEtxSgpvT3RORStlZ2FYWDdvc3BvZmdWZWVqc25Yd0RjZ05pSFFTbDgzSkljUCtjOVBHMDJtNyt0NmpJU3VoRllTVjZtCmlqblNucHBKZWhFUGxPMkFNcmJzU0VpaFB1N294Wm9iZDFtdWF4bWtVa0NoSzZLeGV0RjVEdWhRMi80NEMvSDIKOWk1bnpMMlRST3RndGRJZjAveUF5N05COHlOY3FPR0QKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==
  usages:
  - digital signature
  - key encipherment
  - client auth

```
To approve this certificate, run: ``kubectl certificate approve john-developer``

Next, create a role ``developer ``and rolebinding ``developer-role-binding``, run the command:
```bash
$ kubectl create role developer --resource=pods --verb=create,list,get,update,delete --namespace=development

$ kubectl create rolebinding developer-role-binding --role=developer --user=john --namespace=development
```
To verify the permission from kubectl utility tool:
```bash
$ kubectl auth can-i update pods --as=john --namespace=development
```

## 7. Create a nginx pod called ``nginx-resolver`` using image ``nginx,`` expose it internally with a service called ``nginx-resolver-service.``

Test that you are able to look up the service and pod names from within the cluster. Use the image ``busybox:1.28`` to create a pod for dns lookup. Record results in ``/root/CKA/nginx.svc`` and ``/root/CKA/nginx.pod`` for service and pod name resolutions respectively

Solution: 
Use the command ``kubectl run`` and create a nginx pod and busybox pod. Resolve it, nginx service and its pod name from`` busybox`` pod.

To create a pod ``nginx-resolver`` and expose it internally:
```bash
kubectl run nginx-resolver --image=nginx
kubectl expose pod nginx-resolver --name=nginx-resolver-service --port=80 --target-port=80 --type=ClusterIP
```

To create a pod ``test-nslookup``. Test that you are able to look up the service and pod names from within the cluster:

```bash
kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup nginx-resolver-service
kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup nginx-resolver-service > /root/CKA/nginx.svc
```
Get the IP of the ``nginx-resolver`` pod and replace the dots(.) with hyphon(-) which will be used below.
```bash
kubectl get pod nginx-resolver -o wide
kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup <P-O-D-I-P.default.pod> > /root/CKA/nginx.pod
```

## 8. Create a static pod on ``node01`` called ``nginx-critical`` with image ``nginx ``and make sure that it is recreated/restarted automatically in case of a failure.

Use ``/etc/kubernetes/manifests`` as the Static Pod path for example.

Solution: 

To create a static pod called nginx-critical by using below command:

```bash
kubectl run nginx-critical --image=nginx --dry-run=client -o yaml > static.yaml
```

Copy the contents of this file or use ``scp`` command to transfer this file from ``controlplane`` to ``node01`` node.

```bash
root@controlplane:~# kubectl get nodes -o wide

# Perform SSH
root@controlplane:~# ssh node01
OR
root@controlplane:~# ssh <IP of node01>
```
On ``node01`` node:

Check if static pod directory is present which is ``/etc/kubernetes/manifests``, if it's not present then create it.
```bash
oot@node01:~# mkdir -p /etc/kubernetes/manifests
```
Add that complete path to the ``staticPodPath`` field in the kubelet ``config.yaml`` file.

```bash
root@node01:~# vi /var/lib/kubelet/config.yaml
```
now, move/copy the static.yaml to path ``/etc/kubernetes/manifests/.``
```bash
root@node01:~# cp /root/static.yaml /etc/kubernetes/manifests/
```
Go back to the ``controlplane`` node and check the status of static pod:
```bash
root@node01:~# exit
logout
root@controlplane:~# kubectl get pods 
```

