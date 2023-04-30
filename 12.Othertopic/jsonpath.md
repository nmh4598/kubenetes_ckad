Pre-Requisites – JSON PATH
In the upcoming lecture we will explore some advanced commands with kubectl utility. But that requires JSON PATH. If you are new to JSON PATH queries get introduced to it first by going through the lectures and practice tests available here.

https://kodekloud.com/p/json-path-quiz

Once you are comfortable head back here:

I also have some JSON PATH exercises with Kubernetes Data Objects. Make sure you go through these:

https://mmumshad.github.io/json-path-quiz/index.html#!/?questions=questionskub1

https://mmumshad.github.io/json-path-quiz/index.html#!/?questions=questionskub2

## 1. Get the list of nodes in JSON format and store it in a file at /opt/outputs/nodes.json.

    controlplane ~ ➜  kubectl get nodes -o json > /opt/outputs/nodes.json

## 2. Get the details of the node node01 in json format and store it in the file /opt/outputs/node01.json.

    controlplane ~ ✖ kubectl get node node01 -o json > /opt/outputs/node01.json

## 3. Use JSON PATH query to fetch node names and store them in /opt/outputs/node_names.txt.

Remember the file should only have node names.

    controlplane ~ ➜  kubectl get nodes -o=jsonpath='{.items[*].metadata.name}' > /opt/outputs/node_names.txt

## 4. Use JSON PATH query to retrieve the ``osImages`` of all the nodes and store it in a file`` /opt/outputs/nodes_os.txt``.

The ``osImages`` are under the ``nodeInfo`` section under ``status`` of each node.

    kubectl get nodes -o jsonpath='{.items[*].status.nodeInfo.osImage}' > /opt/outputs/nodes_os.txt

## 5. A kube-config file is present at ``/root/my-kube-config``. Get the user names from it and store it in a file ``/opt/outputs/users.txt.``

Use the command ``kubectl config view --kubeconfig=/root/my-kube-config`` to view the custom kube-config.

    kubectl config view --kubeconfig=my-kube-config -o jsonpath="{.users[*].name}" > /opt/outputs/users.txt

## 6. A set of Persistent Volumes are available. Sort them based on their capacity and store the result in the file /opt/outputs/storage-capacity-sorted.txt.

    kubectl get pv --sort-by=.spec.capacity.storage > /opt/outputs/storage-capacity-sorted.txt

## 7. That was good, but we don't need all the extra details. Retrieve just the first 2 columns of output and store it in ``/opt/outputs/pv-and-capacity-sorted.txt``.

The columns should be named ``NAME`` and ``CAPACITY``. Use the ``custom-columns`` option and remember, it should still be sorted as in the previous question.

    kubectl get pv --sort-by=.spec.capacity.storage -o=custom-columns=NAME:.metadata.name,CAPACITY:.spec.capacity.storage > /opt/outputs/pv-and-capacity-sorted.txt

## 8. Use a JSON PATH query to identify the context configured for the ``aws-user`` in the ``my-kube-config`` context file and store the result in ``/opt/outputs/aws-context-name``.

    kubectl config view --kubeconfig=my-kube-config -o jsonpath="{.contexts[?(@.context.user=='aws-user')].name}" > /opt/outputs/aws-context-name