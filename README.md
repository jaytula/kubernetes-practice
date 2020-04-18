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

## Troubleshooting

- On Windows 10, Docker Desktop automatically started `minikube` so the above command would fail. Stopping Docker Desktop appears to help.
- Also try removing `.minikube` folder.  In Powershell `Remove-Item .\.minikube\ -recurse`