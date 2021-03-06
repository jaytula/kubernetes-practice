# kubernetes-practice

| Title | Link |
| --- | --- |
| Home Page | https://kubernetes.io |
| Learn Kubernetes Basics | https://kubernetes.io/docs/tutorials/kubernetes-basics/ |

## Terms

- The **Master** coordinates the cluster
- **Nodes** are the workers that run applications
- A **Node** is a VM or a physical computer that servers as a worker machine in a Kubernetes cluster
- Each **Node** has a **Kubelet**, which is an agent for managing the node and communication with the Kubernetes **Master**
- **Nodes** communicate with the **Master** using the **Kubernetes API**

## Using Minikube to Create a Cluster

### Module 1: Create a Kubernetes cluster

#### Cluster up and running

Check that it is properly installed

```shell
minikube version
```

Start the cluster, by running *minikube start* command

```shell
minikube start
```

#### Cluster version

Displays both the Client and Server version of `kubectl`

```shell
kubectl version
```

#### Cluster details

View cluster details with

```shell
kubectl cluster-info
```

Show all nodes that can be used to host our applications

```shell
kubectl get nodes
```

### Module 2: Deploy an App

#### Using kubectl to Create a Deployment

Check that `kubectl` is configured to talk to your cluster:

```shell
kubectl version
```

View nodes in the cluster with:

```shell
kubectl get nodes
```

#### Deploy our app

Create deployment

```shell
kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1
```

List your deployments

```shell
kubectl get deployments
```

#### View our app

Create a proxy in one terminal.  

```shell
kubectl proxy
```

We now have a connection between the host and the Kubernetes cluster:

```shell
curl http://localhost:8001/version
```

Getting the POD_NAME and saving into a env variable:

```shell
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
```

### Module 3: Explore Your App

#### Check application configuration

List existing pods:

```shell
kubectl get pods
```

Show detailed information of pods:

```shell
kubectl describe pods
```

#### Show the app in the terminal

Save POD_NAME to env var:

```shell
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
```

To see output of our application:

```shell
curl http://localhost:8081/api/v1/namespaces/default/pods/$POD_NAME/proxy/
```

#### View the container logs

Retrieve logs for a containers with the Pod:

```shell
kubectl logs $POD_NAME
```

#### Executing command on the container

List env variables

```shell
kubectl exec $POD_NAME env
```

Start bash session in the Pod's container

```shell
kubectl exec -ti $POD_NAME bash
```

View source code of app:

```shell
cat server.js
```

Check application is up:

```shell
curl http://localhost:8080
```

### Module 4: Expose Your App Publicly

#### Create a new service

List the current services

```shell
kubectl get services
```

Create a new service with `expose`

```shell
kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080
```

Check services again

```shell
kubectl get services
```

Find out what port was exposed

```shell
kubectl describe services/kubernetes-bootcamp
```

Create environment variable NODE_PORT

```shell
export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template --template '{{(index .spec.ports).nodePort}}')
```

Test app exposed outside of the cluster using curl

```shell
curl $(minikube ip):$NODE_PORT
```

#### Using Labels

View label with

```shell
kubectl describe deployment
```

Use label to query list of Pods with `-l` option

```shell
kubectl get pods -l run=kubernetes-bootcamp
```

Similarly with `get services`

```shell
kubectl get services -l run=kubernetes-bootcamp
```

Apply new label

```shell
kubectl label pod $POD_NAME app=v1
```

Check new label applied

```shell
kubectl describe pods $POD_NAME
```

Get pod by label:

```shell
kubectl get pods -l app=v1
```

#### Deleting a service

Delete a service

```shell
kubectl delete service -l run=kubernetes-bootcamp
```

Confirm service is deleted:

```shell
kubectl get services
```

Confirm route no longer exposed

```shell
curl $(minikube ip):$NODE_PORT
```

Confirm unexposed port is still running

```shell
kubectl exec -ti $POD_NAME curl localhost:8080
```

### Module 5: Scale Your App

#### Scaling a deployment

```shell
kubectl get deployments
```

To see ReplicaSet created by the Deployment

```shell
kubectl get rs
```

Scale to 4 replicas

```shell
kubectl scale deployments/kubernetesbootcamp --replicas=4
```

Check to see if number of pods changed

```shell
kubectl get pods -o wide
```

Check the deployments event logs

```shell
kubectl describe deployments/kubernetes-bootcamp
```

#### Loading Balancing

Find exposed IP and Port

```shell
kubectl services/kubernetes-bootcamp
```

Save NodePort to env var

```shell
export NODE_PORT=$(kubectl get services -o go-template --template '{{(index .spec.ports 0).nodePort}}')
```

Demonstrate that we hit a different random pod:

```shell
curl $(minikube ip):$NODE_PORT
```

#### Scale Down

Scale down to 2 replicas

```shell
kubectl scale deployments/kubernetes-bootcamp --replicas=2
```

Check that change was applied

```shell
kubectl get deployments
```

Check that 2 pods were terminated

```shell
kubectl get pods -o wide
```

### Update Your App

#### Update the version of the app

Get deployments

```shell
kubectl get deployments
```

List your pods

```shell
kubectl get pods
```

View current image version of your app

```shell
kubectl describe pods
```

Update your app to version 2 with set image

```shell
kubectl set image deployments/kubernets-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
```

Check status of pods

```shell
kubectl get pods
```

#### Verify Update

Use describe services to find Running App IP and Port

```shell
kubectl describe services/kubernetes-bootcamp
```

Export NODE_PORT

```shell
export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template --template='{{(index .spec.ports 0).nodePort}}')
```

Check with curl

```shell
curl $(minikube ip):$NODE_PORT
```

Update can be confirmed with `roolout status` command

```shell
kubectl rollout status deployments/kubernetes-bootcamp
```

View current image version of the app:

```shell
kubectl describe pods
```

#### Rollback an update

Deploy another update

```shell
kubectl set image deployments/kubenetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v10
```

See status of deployments:

```shell
kubectl get deployments
```

And something is wrong... we do not have the desired number of pods

```shell
kubectl get pods
```

Get more info:

```shell
kubectl describe pods
```

Undo rollout:

```shell
kubectl rollout undo deployments/kubernetes-bootcamp
```

Check pods

```shell
kubectl get pods
```

Check image deployed

```shell
kubectl describe pods
```


## Troubleshooting

- On Windows 10, Docker Desktop automatically started `minikube` so the above command would fail. Stopping Docker Desktop appears to help.
- Also try removing `.minikube` folder.  In Powershell `Remove-Item .\.minikube\ -recurse`