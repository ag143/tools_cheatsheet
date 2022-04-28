https://kubernetes.io/docs/reference/kubectl/cheatsheet/
kubectl Cheat Sheet
This page contains a list of commonly used kubectl commands and flags.

Kubectl autocomplete
BASH
source <(kubectl completion bash) # setup autocomplete in bash into the current shell, bash-completion package should be installed first.
echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanently to your bash shell.
You can also use a shorthand alias for kubectl that also works with completion:

alias k=kubectl
complete -F __start_kubectl k
ZSH
source <(kubectl completion zsh)  # setup autocomplete in zsh into the current shell
echo "[[ $commands[kubectl] ]] && source <(kubectl completion zsh)" >> ~/.zshrc # add autocomplete permanently to your zsh shell
A Note on --all-namespaces
Appending --all-namespaces happens frequently enough where you should be aware of the shorthand for --all-namespaces:

kubectl -A

Kubectl context and configuration
Set which Kubernetes cluster kubectl communicates with and modifies configuration information. See Authenticating Across Clusters with kubeconfig documentation for detailed config file information.

kubectl config view # Show Merged kubeconfig settings.

# use multiple kubeconfig files at the same time and view merged config
KUBECONFIG=~/.kube/config:~/.kube/kubconfig2 

kubectl config view

# get the password for the e2e user
kubectl config view -o jsonpath='{.users[?(@.name == "e2e")].user.password}'

kubectl config view -o jsonpath='{.users[].name}'    # display the first user
kubectl config view -o jsonpath='{.users[*].name}'   # get a list of users
kubectl config get-contexts                          # display list of contexts 
kubectl config current-context                       # display the current-context
kubectl config use-context my-cluster-name           # set the default context to my-cluster-name

# add a new user to your kubeconf that supports basic auth
kubectl config set-credentials kubeuser/foo.kubernetes.com --username=kubeuser --password=kubepassword

# permanently save the namespace for all subsequent kubectl commands in that context.
kubectl config set-context --current --namespace=ggckad-s2

# set a context utilizing a specific username and namespace.
kubectl config set-context gce --user=cluster-admin --namespace=foo \
  && kubectl config use-context gce

kubectl config unset users.foo                       # delete user foo

# short alias to set/show context/namespace (only works for bash and bash-compatible shells, current context to be set before using kn to set namespace) 
alias kx='f() { [ "$1" ] && kubectl config use-context $1 || kubectl config current-context ; } ; f'
alias kn='f() { [ "$1" ] && kubectl config set-context --current --namespace $1 || kubectl config view --minify | grep namespace | cut -d" " -f6 ; } ; f'
Kubectl apply
apply manages applications through files defining Kubernetes resources. It creates and updates resources in a cluster through running kubectl apply. This is the recommended way of managing Kubernetes applications on production. See Kubectl Book.

Creating objects
Kubernetes manifests can be defined in YAML or JSON. The file extension .yaml, .yml, and .json can be used.

kubectl apply -f ./my-manifest.yaml            # create resource(s)
kubectl apply -f ./my1.yaml -f ./my2.yaml      # create from multiple files
kubectl apply -f ./dir                         # create resource(s) in all manifest files in dir
kubectl apply -f https://git.io/vPieo          # create resource(s) from url
kubectl create deployment nginx --image=nginx  # start a single instance of nginx

# create a Job which prints "Hello World"
kubectl create job hello --image=busybox:1.28 -- echo "Hello World" 

# create a CronJob that prints "Hello World" every minute
kubectl create cronjob hello --image=busybox:1.28   --schedule="*/1 * * * *" -- echo "Hello World"    

kubectl explain pods                           # get the documentation for pod manifests

# Create multiple YAML objects from stdin
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: busybox-sleep
spec:
  containers:
  - name: busybox
    image: busybox:1.28
    args:
    - sleep
    - "1000000"
---
apiVersion: v1
kind: Pod
metadata:
  name: busybox-sleep-less
spec:
  containers:
  - name: busybox
    image: busybox:1.28
    args:
    - sleep
    - "1000"
EOF

# Create a secret with several keys
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: $(echo -n "s33msi4" | base64 -w0)
  username: $(echo -n "jane" | base64 -w0)
EOF

Viewing, finding resources
# Get commands with basic output
kubectl get services                          # List all services in the namespace
kubectl get pods --all-namespaces             # List all pods in all namespaces
kubectl get pods -o wide                      # List all pods in the current namespace, with more details
kubectl get deployment my-dep                 # List a particular deployment
kubectl get pods                              # List all pods in the namespace
kubectl get pod my-pod -o yaml                # Get a pod's YAML

# Describe commands with verbose output
kubectl describe nodes my-node
kubectl describe pods my-pod

# List Services Sorted by Name
kubectl get services --sort-by=.metadata.name

# List pods Sorted by Restart Count
kubectl get pods --sort-by='.status.containerStatuses[0].restartCount'

# List PersistentVolumes sorted by capacity
kubectl get pv --sort-by=.spec.capacity.storage

# Get the version label of all pods with label app=cassandra
kubectl get pods --selector=app=cassandra -o \
  jsonpath='{.items[*].metadata.labels.version}'

# Retrieve the value of a key with dots, e.g. 'ca.crt'
kubectl get configmap myconfig \
  -o jsonpath='{.data.ca\.crt}'

# Get all worker nodes (use a selector to exclude results that have a label
# named 'node-role.kubernetes.io/master')
kubectl get node --selector='!node-role.kubernetes.io/master'

# Get all running pods in the namespace
kubectl get pods --field-selector=status.phase=Running

# Get ExternalIPs of all nodes
kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="ExternalIP")].address}'

# List Names of Pods that belong to Particular RC
# "jq" command useful for transformations that are too complex for jsonpath, it can be found at https://stedolan.github.io/jq/
sel=${$(kubectl get rc my-rc --output=json | jq -j '.spec.selector | to_entries | .[] | "\(.key)=\(.value),"')%?}
echo $(kubectl get pods --selector=$sel --output=jsonpath={.items..metadata.name})

# Show labels for all pods (or any other Kubernetes object that supports labelling)
kubectl get pods --show-labels

# Check which nodes are ready
JSONPATH='{range .items[*]}{@.metadata.name}:{range @.status.conditions[*]}{@.type}={@.status};{end}{end}' \
 && kubectl get nodes -o jsonpath="$JSONPATH" | grep "Ready=True"

# Output decoded secrets without external tools
kubectl get secret my-secret -o go-template='{{range $k,$v := .data}}{{"### "}}{{$k}}{{"\n"}}{{$v|base64decode}}{{"\n\n"}}{{end}}'

# List all Secrets currently in use by a pod
kubectl get pods -o json | jq '.items[].spec.containers[].env[]?.valueFrom.secretKeyRef.name' | grep -v null | sort | uniq

# List all containerIDs of initContainer of all pods
# Helpful when cleaning up stopped containers, while avoiding removal of initContainers.
kubectl get pods --all-namespaces -o jsonpath='{range .items[*].status.initContainerStatuses[*]}{.containerID}{"\n"}{end}' | cut -d/ -f3

# List Events sorted by timestamp
kubectl get events --sort-by=.metadata.creationTimestamp

# Compares the current state of the cluster against the state that the cluster would be in if the manifest was applied.
kubectl diff -f ./my-manifest.yaml

# Produce a period-delimited tree of all keys returned for nodes
# Helpful when locating a key within a complex nested JSON structure
kubectl get nodes -o json | jq -c 'paths|join(".")'

# Produce a period-delimited tree of all keys returned for pods, etc
kubectl get pods -o json | jq -c 'paths|join(".")'

# Produce ENV for all pods, assuming you have a default container for the pods, default namespace and the `env` command is supported.
# Helpful when running any supported command across all pods, not just `env`
for pod in $(kubectl get po --output=jsonpath={.items..metadata.name}); do echo $pod && kubectl exec -it $pod -- env; done
Updating resources
kubectl set image deployment/frontend www=image:v2               # Rolling update "www" containers of "frontend" deployment, updating the image
kubectl rollout history deployment/frontend                      # Check the history of deployments including the revision 
kubectl rollout undo deployment/frontend                         # Rollback to the previous deployment
kubectl rollout undo deployment/frontend --to-revision=2         # Rollback to a specific revision
kubectl rollout status -w deployment/frontend                    # Watch rolling update status of "frontend" deployment until completion
kubectl rollout restart deployment/frontend                      # Rolling restart of the "frontend" deployment


cat pod.json | kubectl replace -f -                              # Replace a pod based on the JSON passed into stdin

# Force replace, delete and then re-create the resource. Will cause a service outage.
kubectl replace --force -f ./pod.json

# Create a service for a replicated nginx, which serves on port 80 and connects to the containers on port 8000
kubectl expose rc nginx --port=80 --target-port=8000

# Update a single-container pod's image version (tag) to v4
kubectl get pod mypod -o yaml | sed 's/\(image: myimage\):.*$/\1:v4/' | kubectl replace -f -

kubectl label pods my-pod new-label=awesome                      # Add a Label
kubectl annotate pods my-pod icon-url=http://goo.gl/XXBTWq       # Add an annotation
kubectl autoscale deployment foo --min=2 --max=10                # Auto scale a deployment "foo"
Patching resources
# Partially update a node
kubectl patch node k8s-node-1 -p '{"spec":{"unschedulable":true}}'

# Update a container's image; spec.containers[*].name is required because it's a merge key
kubectl patch pod valid-pod -p '{"spec":{"containers":[{"name":"kubernetes-serve-hostname","image":"new image"}]}}'

# Update a container's image using a json patch with positional arrays
kubectl patch pod valid-pod --type='json' -p='[{"op": "replace", "path": "/spec/containers/0/image", "value":"new image"}]'

# Disable a deployment livenessProbe using a json patch with positional arrays
kubectl patch deployment valid-deployment  --type json   -p='[{"op": "remove", "path": "/spec/template/spec/containers/0/livenessProbe"}]'

# Add a new element to a positional array
kubectl patch sa default --type='json' -p='[{"op": "add", "path": "/secrets/1", "value": {"name": "whatever" } }]'
Editing resources
Edit any API resource in your preferred editor.

kubectl edit svc/docker-registry                      # Edit the service named docker-registry
KUBE_EDITOR="nano" kubectl edit svc/docker-registry   # Use an alternative editor
Scaling resources
kubectl scale --replicas=3 rs/foo                                 # Scale a replicaset named 'foo' to 3
kubectl scale --replicas=3 -f foo.yaml                            # Scale a resource specified in "foo.yaml" to 3
kubectl scale --current-replicas=2 --replicas=3 deployment/mysql  # If the deployment named mysql's current size is 2, scale mysql to 3
kubectl scale --replicas=5 rc/foo rc/bar rc/baz                   # Scale multiple replication controllers
Deleting resources
kubectl delete -f ./pod.json                                      # Delete a pod using the type and name specified in pod.json
kubectl delete pod unwanted --now                                 # Delete a pod with no grace period
kubectl delete pod,service baz foo                                # Delete pods and services with same names "baz" and "foo"
kubectl delete pods,services -l name=myLabel                      # Delete pods and services with label name=myLabel
kubectl -n my-ns delete pod,svc --all                             # Delete all pods and services in namespace my-ns,
# Delete all pods matching the awk pattern1 or pattern2
kubectl get pods  -n mynamespace --no-headers=true | awk '/pattern1|pattern2/{print $1}' | xargs  kubectl delete -n mynamespace pod
Interacting with running Pods
kubectl logs my-pod                                 # dump pod logs (stdout)
kubectl logs -l name=myLabel                        # dump pod logs, with label name=myLabel (stdout)
kubectl logs my-pod --previous                      # dump pod logs (stdout) for a previous instantiation of a container
kubectl logs my-pod -c my-container                 # dump pod container logs (stdout, multi-container case)
kubectl logs -l name=myLabel -c my-container        # dump pod logs, with label name=myLabel (stdout)
kubectl logs my-pod -c my-container --previous      # dump pod container logs (stdout, multi-container case) for a previous instantiation of a container
kubectl logs -f my-pod                              # stream pod logs (stdout)
kubectl logs -f my-pod -c my-container              # stream pod container logs (stdout, multi-container case)
kubectl logs -f -l name=myLabel --all-containers    # stream all pods logs with label name=myLabel (stdout)
kubectl run -i --tty busybox --image=busybox:1.28 -- sh  # Run pod as interactive shell
kubectl run nginx --image=nginx -n mynamespace      # Start a single instance of nginx pod in the namespace of mynamespace
kubectl run nginx --image=nginx                     # Run pod nginx and write its spec into a file called pod.yaml
--dry-run=client -o yaml > pod.yaml

kubectl attach my-pod -i                            # Attach to Running Container
kubectl port-forward my-pod 5000:6000               # Listen on port 5000 on the local machine and forward to port 6000 on my-pod
kubectl exec my-pod -- ls /                         # Run command in existing pod (1 container case)
kubectl exec --stdin --tty my-pod -- /bin/sh        # Interactive shell access to a running pod (1 container case) 
kubectl exec my-pod -c my-container -- ls /         # Run command in existing pod (multi-container case)
kubectl top pod POD_NAME --containers               # Show metrics for a given pod and its containers
kubectl top pod POD_NAME --sort-by=cpu              # Show metrics for a given pod and sort it by 'cpu' or 'memory'
Copy files and directories to and from containers
kubectl cp /tmp/foo_dir my-pod:/tmp/bar_dir            # Copy /tmp/foo_dir local directory to /tmp/bar_dir in a remote pod in the current namespace
kubectl cp /tmp/foo my-pod:/tmp/bar -c my-container    # Copy /tmp/foo local file to /tmp/bar in a remote pod in a specific container
kubectl cp /tmp/foo my-namespace/my-pod:/tmp/bar       # Copy /tmp/foo local file to /tmp/bar in a remote pod in namespace my-namespace
kubectl cp my-namespace/my-pod:/tmp/foo /tmp/bar       # Copy /tmp/foo from a remote pod to /tmp/bar locally
Note: kubectl cp requires that the 'tar' binary is present in your container image. If 'tar' is not present,kubectl cp will fail. For advanced use cases, such as symlinks, wildcard expansion or file mode preservation consider using kubectl exec.
tar cf - /tmp/foo | kubectl exec -i -n my-namespace my-pod -- tar xf - -C /tmp/bar           # Copy /tmp/foo local file to /tmp/bar in a remote pod in namespace my-namespace
kubectl exec -n my-namespace my-pod -- tar cf - /tmp/foo | tar xf - -C /tmp/bar    # Copy /tmp/foo from a remote pod to /tmp/bar locally
Interacting with Deployments and Services
kubectl logs deploy/my-deployment                         # dump Pod logs for a Deployment (single-container case)
kubectl logs deploy/my-deployment -c my-container         # dump Pod logs for a Deployment (multi-container case)

kubectl port-forward svc/my-service 5000                  # listen on local port 5000 and forward to port 5000 on Service backend
kubectl port-forward svc/my-service 5000:my-service-port  # listen on local port 5000 and forward to Service target port with name <my-service-port>

kubectl port-forward deploy/my-deployment 5000:6000       # listen on local port 5000 and forward to port 6000 on a Pod created by <my-deployment>
kubectl exec deploy/my-deployment -- ls                   # run command in first Pod and first container in Deployment (single- or multi-container cases)
Interacting with Nodes and cluster
kubectl cordon my-node                                                # Mark my-node as unschedulable
kubectl drain my-node                                                 # Drain my-node in preparation for maintenance
kubectl uncordon my-node                                              # Mark my-node as schedulable
kubectl top node my-node                                              # Show metrics for a given node
kubectl cluster-info                                                  # Display addresses of the master and services
kubectl cluster-info dump                                             # Dump current cluster state to stdout
kubectl cluster-info dump --output-directory=/path/to/cluster-state   # Dump current cluster state to /path/to/cluster-state

# If a taint with that key and effect already exists, its value is replaced as specified.
kubectl taint nodes foo dedicated=special-user:NoSchedule
Resource types
List all supported resource types along with their shortnames, API group, whether they are namespaced, and Kind:

kubectl api-resources
Other operations for exploring API resources:

kubectl api-resources --namespaced=true      # All namespaced resources
kubectl api-resources --namespaced=false     # All non-namespaced resources
kubectl api-resources -o name                # All resources with simple output (only the resource name)
kubectl api-resources -o wide                # All resources with expanded (aka "wide") output
kubectl api-resources --verbs=list,get       # All resources that support the "list" and "get" request verbs
kubectl api-resources --api-group=extensions # All resources in the "extensions" API group
Formatting output
To output details to your terminal window in a specific format, add the -o (or --output) flag to a supported kubectl command.

Output format	Description
-o=custom-columns=<spec>	Print a table using a comma separated list of custom columns
-o=custom-columns-file=<filename>	Print a table using the custom columns template in the <filename> file
-o=json	Output a JSON formatted API object
-o=jsonpath=<template>	Print the fields defined in a jsonpath expression
-o=jsonpath-file=<filename>	Print the fields defined by the jsonpath expression in the <filename> file
-o=name	Print only the resource name and nothing else
-o=wide	Output in the plain-text format with any additional information, and for pods, the node name is included
-o=yaml	Output a YAML formatted API object
Examples using -o=custom-columns:

# All images running in a cluster
kubectl get pods -A -o=custom-columns='DATA:spec.containers[*].image'

# All images running in namespace: default, grouped by Pod
kubectl get pods --namespace default --output=custom-columns="NAME:.metadata.name,IMAGE:.spec.containers[*].image"

 # All images excluding "k8s.gcr.io/coredns:1.6.2"
kubectl get pods -A -o=custom-columns='DATA:spec.containers[?(@.image!="k8s.gcr.io/coredns:1.6.2")].image'

# All fields under metadata regardless of name
kubectl get pods -A -o=custom-columns='DATA:metadata.*'
More examples in the kubectl reference documentation.

Kubectl output verbosity and debugging
Kubectl verbosity is controlled with the -v or --v flags followed by an integer representing the log level. General Kubernetes logging conventions and the associated log levels are described here.

Verbosity	Description
--v=0	Generally useful for this to always be visible to a cluster operator.
--v=1	A reasonable default log level if you don't want verbosity.
--v=2	Useful steady state information about the service and important log messages that may correlate to significant changes in the system. This is the recommended default log level for most systems.
--v=3	Extended information about changes.
--v=4	Debug level verbosity.
--v=5	Trace level verbosity.
--v=6	Display requested resources.
--v=7	Display HTTP request headers.
--v=8	Display HTTP request contents.
--v=9	Display HTTP request contents without truncation of contents.
What's next 
  
  https://www.bluematador.com/learn/kubectl-cheatsheet
  
  Kubectl Command Cheatsheet
Kubectl is the command line configuration tool for Kubernetes that communicates with a Kubernetes API server. Using Kubectl allows you to create, inspect, update, and delete Kubernetes objects. This cheatsheet will serve as a quick reference to make commands on many common Kubernetes components and resources. You can use the full command for an object on things like pod(s) or the shortcode variation mentioned in parantheses in the heading of each section. They will all generate the same outcome. You'll also want to be sure to follow up most of the commands with the specific <name> of the resource you are managing.

Cluster Management
Display endpoint information about the master and services in the cluster

kubectl cluster-info
 

Display the Kubernetes version running on the client and server

kubectl version
 

Get the configuration of the cluster

kubectl config view
 

List the API resources that are available

kubectl api-resources
 

List the API versions that are available

kubectl api-versions
 

List everything

kubectl get all --all-namespaces
 

Daemonsets
Shortcode = ds

List one or more daemonsets

kubectl get daemonset
 

Edit and update the definition of one or more daemonset

kubectl edit daemonset <daemonset_name>
 

Delete a daemonset

kubectl delete daemonset <daemonset_name>
 

Create a new daemonset

kubectl create daemonset <daemonset_name>
 

Manage the rollout of a daemonset

kubectl rollout daemonset
 

Display the detailed state of daemonsets within a namespace

kubectl describe ds <daemonset_name> -n <namespace_name>
 

Deployments
Shortcode = deploy

List one or more deployments

kubectl get deployment
 

Display the detailed state of one or more deployments

kubectl describe deployment <deployment_name>
 

Edit and update the definition of one or more deployment on the server

kubectl edit deployment <deployment_name>
 

Create one a new deployment

kubectl create deployment <deployment_name>
 

Delete deployments

kubectl delete deployment <deployment_name>
 

See the rollout status of a deployment

kubectl rollout status deployment <deployment_name>
 

Events
Shortcode = ev

List recent events for all resources in the system

kubectl get events
 

List Warnings only

kubectl get events --field-selector type=Warning
 

List events but exclude Pod events

kubectl get events --field-selector involvedObject.kind!=Pod
 

Pull events for a single node with a specific name

kubectl get events --field-selector involvedObject.kind=Node, involvedObject.name=<node_name>
 

Filter out normal events from a list of events

kubectl get events --field-selector type!=Normal
 

Logs
Print the logs for a pod

kubectl logs <pod_name>
 

Print the logs for the last hour for a pod

kubectl logs --since=1h <pod_name>
 

Get the most recent 20 lines of logs

kubectl logs --tail=20 <pod_name>
 

Get logs from a service and optionally select which container

kubectl logs -f <service_name> [-c <$container>]
 

Print the logs for a pod and follow new logs

kubectl logs -f <pod_name>
 

Print the logs for a container in a pod

kubectl logs -c <container_name> <pod_name>
 

Output the logs for a pod into a file named ‘pod.log’

kubectl logs <pod_name> pod.log
 

View the logs for a previously failed pod

kubectl logs --previous <pod_name>
 

For logs we also recommend using a tool developed by Johan Haleby called Kubetail. This is a bash script that will allow you to get logs from multiple pods simultaneously. You can learn more about it at its Github repository. Here are some sample commands using Kubetail.

 

Get logs for all pods named with pod_prefix

kubetail <pod_prefix>
 

Include the most recent 5 minutes of logs

kubetail <pod_prefix> -s 5m
 

Manifest Files 
Another option for modifying objects is through Manifest Files. We highly recommend using this method. It is done by using yaml files with all the necessary options for objects configured. We have our yaml files stored in a git repository, so we can track changes and streamline changes.

 

Apply a configuration to an object by filename or stdin. Overrides the existing configuration.

kubectl apply -f manifest_file.yaml
 

Create objects

kubectl create -f manifest_file.yaml
 

Create objects in all manifest files in a directory

kubectl create -f ./dir
 

Create objects from a URL

kubectl create -f ‘url’
 

Delete an object

kubectl delete -f manifest_file.yaml
 

Namespaces
Shortcode = ns

Create namespace <name>

kubectl create namespace <namespace_name>
 

List one or more namespaces

kubectl get namespace <namespace_name>
 

Display the detailed state of one or more namespace

kubectl describe namespace <namespace_name>
 

Delete a namespace

kubectl delete namespace <namespace_name>
 

Edit and update the definition of a namespace

kubectl edit namespace <namespace_name>
 

Display Resource (CPU/Memory/Storage) usage for a namespace

kubectl top namespace <namespace_name>
 

Nodes
Shortcode = no

Update the taints on one or more nodes

kubectl taint node <node_name>
 

List one or more nodes

kubectl get node
 

Delete a node or multiple nodes

kubectl delete node <node_name>
 

Display Resource usage (CPU/Memory/Storage) for nodes

kubectl top node
 

Resource allocation per node

kubectl describe nodes | grep Allocated -A 5
 

Pods running on a node

kubectl get pods -o wide | grep <node_name>
 

Annotate a node

kubectl annotate node <node_name>
 

Mark a node as unschedulable

kubectl cordon node <node_name>
 

Mark node as schedulable

kubectl uncordon node <node_name>
 

Drain a node in preparation for maintenance

kubectl drain node <node_name>
 

Add or update the labels of one or more nodes

kubectl label node
 

Pods
Shortcode = po

List one or more pods

kubectl get pod
 

Delete a pod

kubectl delete pod <pod_name>
 

Display the detailed state of a pods

kubectl describe pod <pod_name>
 

Create a pod

kubectl create pod <pod_name>
 

Execute a command against a container in a pod

kubectl exec <pod_name> -c <container_name> <command>
 

Get interactive shell on a a single-container pod

kubectl exec -it <pod_name> /bin/sh
 

Display Resource usage (CPU/Memory/Storage) for pods

kubectl top pod
 

Add or update the annotations of a pod

kubectl annotate pod <pod_name> <annotation>
 

Add or update the label of a pod

kubectl label pod <pod_name>
 

Replication Controllers
Shortcode = rc

List the replication controllers

kubectl get rc
 

List the replication controllers by namespace

kubectl get rc --namespace=”<namespace_name>”
 

ReplicaSets
Shortcode = rs

List ReplicaSets

kubectl get replicasets
 

Display the detailed state of one or more ReplicaSets

kubectl describe replicasets <replicaset_name>
 

Scale a ReplicaSet

kubectl scale --replicas=[x] 
 

Secrets
Create a secret

kubectl create secret
 

List secrets

kubectl get secrets
 

List details about secrets

kubectl describe secrets
 

Delete a secret

kubectl delete secret <secret_name>
 

Services
Shortcode = svc

List one or more services

kubectl get services
 

Display the detailed state of a service

kubectl describe services
 

Expose a replication controller, service, deployment or pod as a new Kubernetes service

kubectl expose deployment [deployment_name]
 

Edit and update the definition of one or more services

kubectl edit services
 

Service Accounts
Shortcode = sa

List service accounts

kubectl get serviceaccounts
 

Display the detailed state of one or more service accounts

kubectl describe serviceaccounts
 

Replace a service account

kubectl replace serviceaccount
 

Delete a service account

kubectl delete serviceaccount <service_account_name>
 

StatefulSet
Shortcode = sts

List StatefulSet

kubectl get statefulset
 

Delete StatefulSet only (not pods)

kubectl delete statefulset/[stateful_set_name] --cascade=false
 

Common Options
In Kubectl you can specify optional flags with commands. Here are some of the most common and useful ones.

 

-o Output format. For example if you wanted to list all of the pods in ps output format with more information.

kubectl get pods -o wide 
 

-n Shorthand for --namespace. For example, if you’d like to list all the Pods in a specific Namespace you would do this command:

kubectl get pods --namespace=[namespace_name]
 

kubectl get pods -n=[namespace_name]
 

-f Filename, directory, or URL to files to use to create a resource. For example when creating a pod using data in a file named newpod.json.

kubectl create -f ./newpod.json
 

-l Selector to filter on, supports ‘=’, ‘==’, and ‘!=’.

 

Help for kubectl
  https://github.com/dennyzhang/cheatsheet-kubernetes-A4
  1 Kubectl Kubernetes CheatSheet

linkedin
github
slack


PRs Welcome
PDF Link: cheatsheet-kubernetes-A4.pdf, Category: Cloud
Blog URL: https://cheatsheet.dennyzhang.com/cheatsheet-kubernetes-A4
Related posts: Kubectl CheatSheet, Kubernetes Yaml, #denny-cheatsheets
File me Issues or star this repo.

1.1 Common Commands
Name	Command
Run curl test temporarily	kubectl run --generator=run-pod/v1 --rm mytest --image=yauritux/busybox-curl -it
Run wget test temporarily	kubectl run --generator=run-pod/v1 --rm mytest --image=busybox -it wget
Run nginx deployment with 2 replicas	kubectl run my-nginx --image=nginx --replicas=2 --port=80
Run nginx pod and expose it	kubectl run my-nginx --restart=Never --image=nginx --port=80 --expose
Run nginx deployment and expose it	kubectl run my-nginx --image=nginx --port=80 --expose
List authenticated contexts	kubectl config get-contexts, ~/.kube/config
Set namespace preference	kubectl config set-context <context_name> --namespace=<ns_name>
List pods with nodes info	kubectl get pod -o wide
List everything	kubectl get all --all-namespaces
Get all services	kubectl get service --all-namespaces
Get all deployments	kubectl get deployments --all-namespaces
Show nodes with labels	kubectl get nodes --show-labels
Get resources with json output	kubectl get pods --all-namespaces -o json
Validate yaml file with dry run	kubectl create --dry-run --validate -f pod-dummy.yaml
Start a temporary pod for testing	kubectl run --rm -i -t --image=alpine test-$RANDOM -- sh
kubectl run shell command	kubectl exec -it mytest -- ls -l /etc/hosts
Get system conf via configmap	kubectl -n kube-system get cm kubeadm-config -o yaml
Get deployment yaml	kubectl -n denny-websites get deployment mysql -o yaml
Explain resource	kubectl explain pods, kubectl explain svc
Watch pods	kubectl get pods -n wordpress --watch
Query healthcheck endpoint	curl -L http://127.0.0.1:10250/healthz
Open a bash terminal in a pod	kubectl exec -it storage sh
Check pod environment variables	kubectl exec redis-master-ft9ex env
Enable kubectl shell autocompletion	echo "source <(kubectl completion bash)" >>~/.bashrc, and reload
Use minikube dockerd in your laptop	eval $(minikube docker-env), No need to push docker hub any more
Kubectl apply a folder of yaml files	kubectl apply -R -f .
Get services sorted by name	kubectl get services –sort-by=.metadata.name
Get pods sorted by restart count	kubectl get pods –sort-by=’.status.containerStatuses[0].restartCount’
List pods and images	kubectl get pods -o=’custom-columns=PODS:.metadata.name,Images:.spec.containers[*].image’
List all container images	list-all-images.sh
kubeconfig skip tls verification	skip-tls-verify.md
Ubuntu install kubectl	=”deb https://apt.kubernetes.io/ kubernetes-xenial main”=
Reference	GitHub: kubernetes releases
Reference	minikube cheatsheet, docker cheatsheet, OpenShift CheatSheet
1.2 Check Performance
Name	Command
Get node resource usage	kubectl top node
Get pod resource usage	kubectl top pod
Get resource usage for a given pod	kubectl top <podname> --containers
List resource utilization for all containers	kubectl top pod --all-namespaces --containers=true
1.3 Resources Deletion
Name	Command
Delete pod	kubectl delete pod/<pod-name> -n <my-namespace>
Delete pod by force	kubectl delete pod/<pod-name> --grace-period=0 --force
Delete pods by labels	kubectl delete pod -l env=test
Delete deployments by labels	kubectl delete deployment -l app=wordpress
Delete all resources filtered by labels	kubectl delete pods,services -l name=myLabel
Delete resources under a namespace	kubectl -n my-ns delete po,svc --all
Delete persist volumes by labels	kubectl delete pvc -l app=wordpress
Delete state fulset only (not pods)	kubectl delete sts/<stateful_set_name> --cascade=false

1.4 Log & Conf Files
Name	Comment
Config folder	/etc/kubernetes/
Certificate files	/etc/kubernetes/pki/
Credentials to API server	/etc/kubernetes/kubelet.conf
Superuser credentials	/etc/kubernetes/admin.conf
kubectl config file	~/.kube/config
Kubernetes working dir	/var/lib/kubelet/
Docker working dir	/var/lib/docker/, /var/log/containers/
Etcd working dir	/var/lib/etcd/
Network cni	/etc/cni/net.d/
Log files	/var/log/pods/
log in worker node	/var/log/kubelet.log, /var/log/kube-proxy.log
log in master node	kube-apiserver.log, kube-scheduler.log, kube-controller-manager.log
Env	/etc/systemd/system/kubelet.service.d/10-kubeadm.conf
Env	export KUBECONFIG=/etc/kubernetes/admin.conf
1.5 Pod
Name	Command
List all pods	kubectl get pods
List pods for all namespace	kubectl get pods -all-namespaces
List all critical pods	kubectl get -n kube-system pods -a
List pods with more info	kubectl get pod -o wide, kubectl get pod/<pod-name> -o yaml
Get pod info	kubectl describe pod/srv-mysql-server
List all pods with labels	kubectl get pods --show-labels
List all unhealthy pods	kubectl get pods –field-selector=status.phase!=Running –all-namespaces
List running pods	kubectl get pods –field-selector=status.phase=Running
Get Pod initContainer status	kubectl get pod --template '{{.status.initContainerStatuses}}' <pod-name>
kubectl run command	kubectl exec -it -n “$ns” “$podname” – sh -c “echo $msg >>/dev/err.log”
Watch pods	kubectl get pods -n wordpress --watch
Get pod by selector	kubectl get pods –selector=”app=syslog” -o jsonpath=’{.items[*].metadata.name}’
List pods and images	kubectl get pods -o=’custom-columns=PODS:.metadata.name,Images:.spec.containers[*].image’
List pods and containers	-o=’custom-columns=PODS:.metadata.name,CONTAINERS:.spec.containers[*].name’
Reference	Link: kubernetes yaml templates
1.6 Label & Annotation
Name	Command
Filter pods by label	kubectl get pods -l owner=denny
Manually add label to a pod	kubectl label pods dummy-input owner=denny
Remove label	kubectl label pods dummy-input owner-
Manually add annotation to a pod	kubectl annotate pods dummy-input my-url=https://dennyzhang.com
1.7 Deployment & Scale
Name	Command
Scale out	kubectl scale --replicas=3 deployment/nginx-app
online rolling upgrade	kubectl rollout app-v1 app-v2 --image=img:v2
Roll backup	kubectl rollout app-v1 app-v2 --rollback
List rollout	kubectl get rs
Check update status	kubectl rollout status deployment/nginx-app
Check update history	kubectl rollout history deployment/nginx-app
Pause/Resume	kubectl rollout pause deployment/nginx-deployment, resume
Rollback to previous version	kubectl rollout undo deployment/nginx-deployment
Reference	Link: kubernetes yaml templates, Link: Pausing and Resuming a Deployment

1.8 Quota & Limits & Resource
Name	Command
List Resource Quota	kubectl get resourcequota
List Limit Range	kubectl get limitrange
Customize resource definition	kubectl set resources deployment nginx -c=nginx --limits=cpu=200m
Customize resource definition	kubectl set resources deployment nginx -c=nginx --limits=memory=512Mi
Reference	Link: kubernetes yaml templates
1.9 Service
Name	Command
List all services	kubectl get services
List service endpoints	kubectl get endpoints
Get service detail	kubectl get service nginx-service -o yaml
Get service cluster ip	kubectl get service nginx-service -o go-template=’{{.spec.clusterIP}}’
Get service cluster port	kubectl get service nginx-service -o go-template=’{{(index .spec.ports 0).port}}’
Expose deployment as lb service	kubectl expose deployment/my-app --type=LoadBalancer --name=my-service
Expose service as lb service	kubectl expose service/wordpress-1-svc --type=LoadBalancer --name=ns1
Reference	Link: kubernetes yaml templates
1.10 Secrets
Name	Command
List secrets	kubectl get secrets --all-namespaces
Generate secret	echo -n 'mypasswd', then redirect to base64 --decode
Get secret	kubectl get secret denny-cluster-kubeconfig
Get a specific field of a secret	kubectl get secret denny-cluster-kubeconfig -o jsonpath=”{.data.value}”
Create secret from cfg file	kubectl create secret generic db-user-pass –from-file=./username.txt
Reference	Link: kubernetes yaml templates, Link: Secrets
1.11 StatefulSet
Name	Command
List statefulset	kubectl get sts
Delete statefulset only (not pods)	kubectl delete sts/<stateful_set_name> --cascade=false
Scale statefulset	kubectl scale sts/<stateful_set_name> --replicas=5
Reference	Link: kubernetes yaml templates
1.12 Volumes & Volume Claims
Name	Command
List storage class	kubectl get storageclass
Check the mounted volumes	kubectl exec storage ls /data
Check persist volume	kubectl describe pv/pv0001
Copy local file to pod	kubectl cp /tmp/my <some-namespace>/<some-pod>:/tmp/server
Copy pod file to local	kubectl cp <some-namespace>/<some-pod>:/tmp/server /tmp/my
Reference	Link: kubernetes yaml templates
1.13 Events & Metrics
Name	Command
View all events	kubectl get events --all-namespaces
List Events sorted by timestamp	kubectl get events –sort-by=.metadata.creationTimestamp
1.14 Node Maintenance
Name	Command
Mark node as unschedulable	kubectl cordon $NODE_NAME
Mark node as schedulable	kubectl uncordon $NODE_NAME
Drain node in preparation for maintenance	kubectl drain $NODE_NAME
1.15 Namespace & Security
Name	Command
List authenticated contexts	kubectl config get-contexts, ~/.kube/config
Set namespace preference	kubectl config set-context <context_name> --namespace=<ns_name>
Switch context	kubectl config use-context <context_name>
Load context from config file	kubectl get cs --kubeconfig kube_config.yml
Delete the specified context	kubectl config delete-context <context_name>
List all namespaces defined	kubectl get namespaces
List certificates	kubectl get csr
Check user privilege	kubectl –as=system:serviceaccount:ns-denny:test-privileged-sa -n ns-denny auth can-i use pods/list
Check user privilege	kubectl auth can-i use pods/list
Reference	Link: kubernetes yaml templates
1.16 Network
Name	Command
Temporarily add a port-forwarding	kubectl port-forward redis-134 6379:6379
Add port-forwarding for deployment	kubectl port-forward deployment/redis-master 6379:6379
Add port-forwarding for replicaset	kubectl port-forward rs/redis-master 6379:6379
Add port-forwarding for service	kubectl port-forward svc/redis-master 6379:6379
Get network policy	kubectl get NetworkPolicy
1.17 Patch
Name	Summary
Patch service to loadbalancer	kubectl patch svc $svc_name -p ‘{“spec”: {“type”: “LoadBalancer”}}’
1.18 Extenstions
Name	Summary
Enumerates the resource types available	kubectl api-resources
List api group	kubectl api-versions
List all CRD	kubectl get crd
List storageclass	kubectl get storageclass

1.19 Components & Services
1.19.1 Services on Master Nodes
Name	Summary
kube-apiserver	API gateway. Exposes the Kubernetes API from master nodes
etcd	reliable data store for all k8s cluster data
kube-scheduler	schedule pods to run on selected nodes
kube-controller-manager	Reconcile the states. node/replication/endpoints/token controller and service account, etc
cloud-controller-manager	
1.19.2 Services on Worker Nodes
Name	Summary
kubelet	A node agent makes sure that containers are running in a pod
kube-proxy	Manage network connectivity to the containers. e.g, iptable, ipvs
Container Runtime	Kubernetes supported runtimes: dockerd, cri-o, runc and any OCI runtime-spec implementation.
1.19.3 Addons: pods and services that implement cluster features
Name	Summary
DNS	serves DNS records for Kubernetes services
Web UI	a general purpose, web-based UI for Kubernetes clusters
Container Resource Monitoring	collect, store and serve container metrics
Cluster-level Logging	save container logs to a central log store with search/browsing interface
1.19.4 Tools
Name	Summary
kubectl	the command line util to talk to k8s cluster
kubeadm	the command to bootstrap the cluster
kubefed	the command line to control a Kubernetes Cluster Federation
Kubernetes Components	Link: Kubernetes Components
1.20 More Resources
License: Code is licensed under MIT License.

https://kubernetes.io/docs/reference/kubectl/cheatsheet/

https://codefresh.io/kubernetes-guides/kubernetes-cheat-sheet/
  
  
  
