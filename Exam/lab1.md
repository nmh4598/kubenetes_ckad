For this question, please set the context to cluster1 by running:

    kubectl config use-context cluster1

Create a service account called `pink-sa-cka24-arch`. Further create a cluster role called `pink-role-cka24-arch` with `full permissions` on all resources in the core api group under `default` namespace in `cluster1`.


Finally create a cluster role binding called `pink-role-binding-cka24-arch` to bind `pink-role-cka24-arch` cluster role with `pink-sa-cka24-arch` service account.

```bash
student-node ~ ➜ kubectl --context cluster1 create serviceaccount pink-sa-cka24-arch
student-node ~ ➜ kubectl --context cluster1 create clusterrole pink-role-cka24-arch --resource=* --verb=*
student-node ~ ➜ kubectl --context cluster1 create clusterrolebinding pink-role-binding-cka24-arch --clusterrole=pink-role-cka24-arch --serviceaccount=default:pink-sa-cka24-arch
```

## 2 SECTION: ARCHITECTURE, INSTALL AND MAINTENANCE

Find the node across all clusters that consumes the most CPU and store the result to the file `/opt/high_cpu_node` in the following format `cluster_name,node_name`.

The node could be in any clusters that are currently configured on the `student-node`.

Solution
Check out the metrics for all node across all clusters:

```bash
student-node ~ ➜  kubectl top node --context cluster1 --no-headers | sort -nr -k2 | head -1
cluster1-controlplane   127m   1%    703Mi   1%    

student-node ~ ➜  kubectl top node --context cluster2 --no-headers | sort -nr -k2 | head -1
cluster2-controlplane   126m   1%    675Mi   1%    

student-node ~ ➜  kubectl top node --context cluster3 --no-headers | sort -nr -k2 | head -1
cluster3-controlplane   577m   7%    1081Mi   1%    

student-node ~ ➜  kubectl top node --context cluster4 --no-headers | sort -nr -k2 | head -1
cluster4-controlplane   130m   1%    679Mi   1%    

```
Using this, find the node that uses most `cpu`. In this case, it is `cluster3-controlplane` on `cluster3`.


Save the result in the correct format to the file:
```bash
student-node ~ ➜  echo cluster3,cluster3-controlplane > /opt/high_cpu_node
```

## 3 SECTION: ARCHITECTURE, INSTALL AND MAINTENANCE

For this question, please set the context to cluster1 by running:

    kubectl config use-context cluster1

We have created a service account called `blue-sa-cka21-arch`, a cluster role called `blue-role-cka21-arch` and a cluster role binding called `blue-role-binding-cka21-arch`.


Update the permissions of this service account so that it can get the `pods` only in `default` namespace of `cluster1`.

Solution
Edit the blue-role-cka21-arch to update permissions:

```bash
student-node ~ ➜  kubectl edit clusterrole blue-role-cka21-arch --context cluster1
```
Under `rules:` -> `- apiGroups:` replace `apps` with `""`

`resources:` replace `- deployments` with `- pods` and save the changes.

You can verify it as below:
```bash
student-node ~ ➜  kubectl auth can-i get pods 
--as=system:serviceaccount:default:blue-sa-cka21-arch
yes

```

## 4 SECTION: ARCHITECTURE, INSTALL AND MAINTENANCE
For this question, please set the context to `cluster3` by running:

```bash
kubectl config use-context cluster3
```
A pod called `logger-cka03-arch` has been created in the default namespace. Inspect this pod and save ALL `INFO` and ERROR's to the file `/root/logger-cka03-arch-all` on the `student-node`.

Solution

Run the command `kubectl logs logger-cka03-arch --context cluster3 > /root/logger-cka03-arch-all` on the `student-node`.

Run the command :
```bash
student-node ~ ➜ kubectl logs logger-cka03-arch --context cluster3 > /root/logger-cka03-arch-all

student-node ~ ➜  head /root/logger-cka03-arch-all
INFO: Wed Oct 19 10:38:57 UTC 2022 Logger is running
ERROR: Wed Oct 19 10:38:57 UTC 2022 Logger encountered errors!
INFO: Wed Oct 19 10:38:57 UTC 2022 Logger is running
ERROR: Wed Oct 19 10:38:57 UTC 2022 Logger encountered errors!
INFO: Wed Oct 19 10:38:57 UTC 2022 Logger is running
ERROR: Wed Oct 19 10:38:57 UTC 2022 Logger encountered errors!
INFO: Wed Oct 19 10:38:57 UTC 2022 Logger is running
ERROR: Wed Oct 19 10:38:57 UTC 2022 Logger encountered errors!
INFO: Wed Oct 19 10:38:57 UTC 2022 Logger is running
ERROR: Wed Oct 19 10:38:57 UTC 2022 Logger encountered errors!

student-node ~ ➜  
```

## 5 SECTION: ARCHITECTURE, INSTALL AND MAINTENANCE

Find the node across all clusters that consumes the most `memory` and store the result to the file `/opt/high_memory_node` in the following format `cluster_name,node_name`.

The node could be in any clusters that are currently configured on the `student-node`.

Solution

```bash
student-node ~ ➜  kubectl top node --context cluster1 --no-headers | sort -nr -k4 | head -1
cluster1-controlplane   124m   1%    768Mi   1%    

student-node ~ ➜  kubectl top node --context cluster2 --no-headers | sort -nr -k4 | head -1
cluster2-controlplane   79m   0%    873Mi   1%    

student-node ~ ➜  kubectl top node --context cluster3 --no-headers | sort -nr -k4 | head -1
cluster3-controlplane   78m   0%    902Mi   1%  

student-node ~ ➜  kubectl top node --context cluster4 --no-headers | sort -nr -k4 | head -1
cluster4-controlplane   78m   0%    901Mi   1%    

student-node ~ ➜  
```
Using this, find the node that uses most memory. In this case, it is `cluster3-controlplane` on cluster3.


Save the result in the correct format to the file:
```bash
student-node ~ ➜  echo cluster3,cluster3-controlplane > /opt/high_memory_node 
```

## 6 SECTION: TROUBLESHOOTING

For this question, please set the context to `cluster1` by running:
```bash
kubectl config use-context cluster1
```
A template to create a Kubernetes pod is stored at `/root/red-probe-cka12-trb.yaml` on the `student-node`. However, using this template as-is is resulting in an error.


Fix the issue with this template and use it to create the pod. Once created, watch the pod for a minute or two to make sure its stable i.e, it's not crashing or restarting.


Make sure you do not update the `args:` section of the template.

Solution
**Try to apply the template**
```bash
kubectl apply -f red-probe-cka12-trb.yaml 
```
From the error you can see that the error is for liveness probe, so let's open the template to find out:
```bash
vi red-probe-cka12-trb.yaml
```
Under livenessProbe: you will see the type is httpGet however the rest of the options are command based so this probe should be of exec type.

Change httpGet to exec

**Try to apply the template now**
```bash
kubectl apply -f red-probe-cka12-trb.yaml 
```
Cool it worked, now let's watch the POD status, after few seconds you will notice that POD is restarting. So let's check the logs/events

```bash
kubectl get event --field-selector involvedObject.name=red-probe-cka12-trb
```
You will see an error like:
```bash
21s         Warning   Unhealthy   pod/red-probe-cka12-trb   Liveness probe failed: cat: can't open '/healthcheck': No such file or directory
```
So seems like Liveness probe is failing, lets look into it:
```bash
vi red-probe-cka12-trb.yaml
```
Notice the command `- sleep 3 ; touch /healthcheck; sleep 30;sleep 30000` it starts with a delay of `3` seconds, but the liveness probe `initialDelaySeconds` is set to `1` and `failureThreshold` is also `1`. Which means the POD will fail just after first attempt of liveness check which will happen just after 1 second of pod start. So to make it stable we must increase the `initialDelaySeconds` to at least `5`
```bash
vi red-probe-cka12-trb.yaml
```
Change `initialDelaySeconds` from `1` to `5 `and save apply the changes.
Delete old pod:
```bash
kubectl delete pod red-probe-cka12-trb
```
Apply changes:
```bash
kubectl apply -f red-probe-cka12-trb.yaml
```

## 7 SECTION: TROUBLESHOOTING
For this question, please set the context to `cluster1` by running:
```bash
kubectl config use-context cluster1
```
The `blue-dp-cka09-trb` deployment is having `0` out of `1` pods running. Fix the issue to make sure that pod is up and running.

Solution
**List the pods**
```bash
kubectl get pod
```
Most probably you see `Init:Error` or `Init:CrashLoopBackOff` for the corresponding pod.

**Look into the logs**
```bash
kubectl logs blue-dp-cka09-trb-xxxx -c init-container
```
You will see an error something like
```bash
sh: can't open 'echo 'Welcome!'': No such file or directory
```
**Edit the deployment**
```bash
kubectl edit deploy blue-dp-cka09-trb
```
Under `initContainers:` -> `- command:` add `-c` to the next line of - sh, so final command should look like this
```yaml
   initContainers:
   - command:
     - sh
     - -c
     - echo 'Welcome!'
```
If you will check pod then it must be failing again but with different error this time, let's find that out
```bash
kubectl get event --field-selector involvedObject.name=blue-dp-cka09-trb-xxxxx
```
You will see an error something like
```bash
Warning   Failed      pod/blue-dp-cka09-trb-69dd844f76-rv9z8   Error: failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: error during container init: error mounting "/var/lib/kubelet/pods/98182a41-6d6d-406a-a3e2-37c33036acac/volumes/kubernetes.io~configmap/nginx-config" to rootfs at "/etc/nginx/nginx.conf": mount /var/lib/kubelet/pods/98182a41-6d6d-406a-a3e2-37c33036acac/volumes/kubernetes.io~configmap/nginx-config:/etc/nginx/nginx.conf (via /proc/self/fd/6), flags: 0x5001: not a directory: unknown
```
**Edit the deployment again**

```bash
kubectl edit deploy blue-dp-cka09-trb
```
Under `volumeMounts:` -> - `mountPath: /etc/nginx/nginx.conf` -> `name: nginx-config` add `subPath: nginx.conf` and save the changes.
Finally the pod should be in running state.

## 8 SECTION: TROUBLESHOOTING
For this question, please set the context to `cluster1` by running:

```bash
kubectl config use-context cluster1
```
A pod called `check-time-cka03-trb` is continuously crashing. Figure out what is causing this and fix it.

Make sure that the `check-time-cka03-trb`POD is in `running` state.

This pod prints the current date and time at a pre-defined frequency and saves it to a file. Ensure that it continues this operation once you have fixed it.

Solution
**Look into the POD logs**
```bash
kubectl logs -f check-time-cka03-trb
```
You may not see any logs so look into the kubernetes events for `check-time-cka03-trb` POD

**Look into the POD events**
```bash
kubectl get event --field-selector involvedObject.name=check-time-cka03-trb
```
You will see an error something like:
```bash
Error: failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "/bin/bash": stat /bin/bash: no such file or directory: unknown
```
From the error we can see that its not able to execute `/bin/bash` so let's try `/bin/sh`

**Edit the pod**
```bash
kubectl get pod check-time-cka03-trb -o=yaml > check-time-cka03-trb.yaml
```
** Make the required changes in **`check-time-cka03-trb.yaml` template
```bash
vi check-time-cka03-trb.yaml
```
Under `spec:` -> `containers:` -> `command:` change `/bin/bash` to `/bin/sh `and save the file.
**Delete the old pod.**
```bash
kubectl delete pod check-time-cka03-trb
```
**Apply the updated template**
```bash
kubectl apply -f check-time-cka03-trb.yaml
```

## 9 SECTION: TROUBLESHOOTING
For this question, please set the context to `cluster1` by running:
```bash
kubectl config use-context cluster1
```
There is a Cronjob called `orange-cron-cka10-trb` which is supposed to run every two minutes (i.e 13:02, 13:04, 13:06…14:02, 14:04…and so on). This cron targets the application running inside the `orange-app-cka10-trb` pod to make sure the app is accessible. The application has been exposed internally as a ClusterIP service.


However, this cron is not running as per the expected schedule and is not running as intended.


Make the appropriate changes so that the cronjob runs as per the required schedule and it passes the accessibility checks every-time.

Solution
**Check the cron schedule**
```bash
kubectl get cronjob
```
Make sure the schedule for `orange-cron-cka10-trb` crontjob is set to `*/2 * * * *` if not then edit it.

Also before that look for the issues why this cron is failing

```bash
kubectl logs orange-cron-cka10-trb-xxxx
```
You will see some error like
```bash
curl: (6) Could not resolve host: orange-app-cka10-trb
```
You will notice that the curl is trying to hit `orange-app-cka10-trb` directly but it is supposed to hit the relevant service which is `orange-svc-cka10-trb` so we need to fix the curl command.

**Edit the cronjob**
```bash
kubectl edit cronjob orange-cron-cka10-trb
```
Change schedule `* * * * *` to `*/2 * * * *`
Change command `curl orange-app-cka10-trb` to `curl orange-svc-cka10-trb`
Wait for 2 minutes to run again this cron and it should complete now.

## 10 SECTION: TROUBLESHOOTING

For this question, please set the context to `cluster4` by running:
```bash
kubectl config use-context cluster4
```
There is a pod called `pink-pod-cka16-trb` created in the `default` namespace in `cluster4`. This app runs on port `tcp/5000` and it is exposed to end-users using an ingress resource called `pink-ing-cka16-trb` in such a way that it is supposed to be accessible using the command: `curl http://kodekloud-pink.app` on `cluster4-controlplane` host.


However, this is not working. Troubleshoot and fix this issue, making any necessary to the objects.



`Note:` You should be able to ssh into the `cluster4-controlplane` using `ssh cluster4-controlplane` command.

Solution

**SSH into the `cluster4-controlplane` host and try to access the app.**
```bash
ssh cluster4-controlplane
curl kodekloud-pink.app
```
You must be getting `503 Service Temporarily Unavailabl` error.
Let's look into the service:

```bash
kubectl edit svc pink-svc-cka16-trb
```
Under `ports:` change `protocol: UDP` to `protocol: TCP`

Try to access the app again
```bash
curl kodekloud-pink.app
```
You must be getting `curl: (6) Could not resolve host: example.com` error, from the error we can see that its not able to resolve `example.com` host which indicated that it can be some issue related to the DNS. As we know `CoreDNS` is a DNS server that can serve as the Kubernetes cluster DNS, so it can be something related to `CoreDNS`.

Let's check if we have `CoreDNS` deployment running:

```bash
kubectl get deploy -n kube-system
```
You will see that for `coredns` all relicas are down, you will see `0/0` ready pods. So let's scale up this deployment.
```bash
kubectl scale --replicas=2 deployment coredns -n kube-system
```
Once `CoreDBS` is up let's try to access to app again.
```bash
curl kodekloud-pink.ap
```
It should work now.
```bash

```

```bash

```

```bash

```