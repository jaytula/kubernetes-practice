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


## Troubleshooting

- On Windows 10, Docker Desktop automatically started `minikube` so the above command would fail. Stopping Docker Desktop appears to help.
- Also try removing `.minikube` folder.  In Powershell `Remove-Item .\.minikube\ -recurse`