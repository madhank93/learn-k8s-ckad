# Learn Kubernetes

## What is K8s ?

It is a popular container orchestrator tool.

## Why do you need K8s and what problem does it solves ?

Trends of micro services has increased the usage of containers and to handle these containers efficiently, need an 
orchestrator tool like k8s. Orchestration tool helps in automating the deployment, managing, scaling, and networking of containers, 
elf healing, high availability and Automated rollouts and rollbacks.

![kubernetes_scaling](/img/kube-scale.png)
## Kubernetes architecture and basic terminologies

![kubernetes_architecture](/img/kube-arch.png)

Image source - Red hat - [Introduction to Kubernetes architecture](https://www.redhat.com/en/topics/containers/kubernetes-architecture)

* Kubernetes - The whole orchestration system

### Control plane / Master node

* **kube-apiserver** - It's an entry point to interact with Kubernetes API (acts as a gateway). It determines if a request is valid 
and, if it is, processes it. It can be accessed through the kubectl cmd or kubeadm.

* **kube-scheduler** - It considers the resource needs of a pod, such as CPU or memory, along with the health of the cluster. Then it 
schedules the pod to an appropriate compute node. Then it passes on the requests to kubelet to execute it.

* **kube-controller-manager** - Detects state changes in the cluster (eg: if pod crashes, detects it and recovers the cluster state). 
Checks with scheduler and makes sure the correct number of pods is running.

* **etcd** - It is a key-value store database, stores the cluster state. All of the cluster information (eg: new pod created, pod 
crashed) is stored in the etcd.

### Compute machine / Worker node

* **kubelet** - Kubernetes agent running on nodes, a tiny application that communicates with the control plane & containers vice versa
and when the control plane needs something to happen in a node, the kubelet executes the action. It ensures the containers are 
running in the pod.

* **kube-proxy** - The kube-proxy handles network communications inside or outside of your cluster. It uses operating system’s packet 
filtering layer if there is one, otherwise , kube-proxy forwards the traffic itself.

* **container runtime** - It is the software responsible for running containers. It can be Docker, Containerd and CRI-O.

## Q&A

<details>

  <summary> 1. What are the main components of kubernetes ? </summary>

  &nbsp;

  <p align="center">
    <img alt="Kubernetes Object" src="/img/kube-obj.jpeg" width="45%">
      &nbsp; &nbsp; &nbsp; &nbsp;
    <img alt="Kubernetes Object" src="/img/kube-objects.png" width="45%">
  </p>

  Image source of [Kubernetes object](https://tsuyoshiushio.medium.com/kubernetes-in-three-diagrams-6aba8432541c)

  <p>

* **Pod** - basic unit of work. It creates an abstraction over containers, so that it can be replaced with anytime run 
time containers (eg: docker, cri-o). Many containers can be deployed into the pod, but the best practice is one container per pod.
Each pod gets it own IP address and new IP address on every re-creation. It also acts as a load balancer.

* **Service** - it is an abstract way to expose an application running on a set of Pods as a network service. It 
creates a permanent IP address, lifecycle of pod and service are not connected. Even if the pods crashes and recreated, service 
IP remains same.

* **Ingress** - it manages external access to the services in a cluster, typically HTTP. It provides load balancing, 
ssl termination and name based hosting.

* **ConfigMap** - it is used to store non-confidential (external config) data in key-value pairs.

* **Secret** - it is used to store and manage sensitive information (eg: passwords, tokens, and keys), stores in 
base64 encoded format.

* **Deployment** - describes the desired state of a pod or a replica set, then gradually updates the environment (for 
example, creating or deleting replicas) until the current state matches the desired state specified in the deployment file. In 
general we don't work directly with pods, we will create deployments. It is mainly for stateless apps.

* **StatefulSet** - it is used to manage stateful applications with persistent storage (useful for db like mysql, 
MongoDb ...). It makes sure all the request to db are synchronized so that we can avoid data inconsistency problem. Pod names are 
persistent and are retained when rescheduled. Storage stays associated with replacement pods. Volumes persist when pods are 
deleted.

</p>

</details>

---

<details>

  <summary> 2. How to deploy an nginx ? </summary>

  <p>

Syntax:

```console
kubectl create deployment <deployment-name> --image=<image-name>
```

Example:

```console
kubectl create deployment my-nginx --image=nginx
```

```
Layers of abstraction -> Deployment > Replica set > Pod > Container
```

Result:

![k8s_deployment_result](/img/kube-res.png)

Pod name is the combination of = deployment-name + replica set ID + its own ID

  </p>

</details>

---

<details>

  <summary> 3. How to get all the resources in k8s ?  </summary>

  <p>

  Syntax:

  ```console
  kubectl get pods # List all pods
  kubectl get deployments # List all deployments
  kubectl get all # List all resources

  kubectl get pods -o wide # List all pods with more information
  ```

Add `-o wide` to the command to get more info.

  </p>

</details>

---

<details>

  <summary> 4. How to edit the deployment ? </summary>

  <p>

Syntax:

```console
kubectl edit deployment <deployment-name>
```

The above command will open up the auto-generated config file.

  </p>

</details>

---

<details>

  <summary> 5. How to get logs for the pod ? </summary>

  <p>

Syntax:

```console
kubectl logs <pod-name>
```

Example:

```console
kubectl logs my-nginx-6b74b79f57-hmlqd
```

  </p>

</details>

---

<details>

  <summary> 6. How to get an interactive shell inside the pod ? </summary>

  <p>

Syntax:

```console
kubectl exec -it <pod-name> -- <command>
```

Example:

```console
kubectl exec -it my-nginx-6b74b79f57-hmlqd -- bin/bash
```

  </p>

</details>

---

<details>

  <summary> 7. How to scale replicaset using deployment name ? </summary>

  <p>

Syntax:

```console
kubectl scale <deployment-name> --replicas=<count>
```
Example:

```console
kubectl scale deploy/my-nginx --replicas=2
```

`deploy/my-nginx` is a short form of `deployment my-nginx`

  </p>

</details>

---

<details>

  <summary> 8. How to get the detailed description for a specific pod ? </summary>

  <p>

Syntax:

```console
kubectl describe <pod-name>
```

Example:

```console
kubectl describe pod/apache-deploy-7dfb754b6b-qllfq
```

The above commands print a detailed description of the selected resources, including related resources such as events or controllers.

  </p>

</details>

---

<details>

  <summary> 9. How to delete an deployment ? </summary>

  <p>

Syntax:

```console
kubectl delete deployment <deployment-name>
```

Example:

```console
kubectl delete deployment deploy/my-nginx
```

`deploy/my-nginx` is a short form of `deployment my-nginx`

Deleting deployment will remove deployment, replicaset, pod and container. `Layers of abstraction is -> Deployment > Replica set > 
Pod > Container`

  </p>

</details>

---

<details>

  <summary> 10. What are the different types of Services available ? </summary>

  <p>

**Service** - provides the stable address for a pod(s).

* ClusterIP - It is the default service type. Exposes the Service on a cluster-internal IP. Only reachable within cluster. Pods can 
reach service on apps port number.

* NodePort - Exposes the Service on each Node's IP at a static port (the NodePort). High port allocated on each node. You'll be able 
to contact the NodePort Service, from outside the cluster, by requesting <NodeIP>:<NodePort>

* LoadBalancer - Exposes the Service externally using a cloud provider's load balancer. NodePort and ClusterIP Services, to which the 
external load balancer routes, are automatically created.
  
* ExternalName -

  </p>

</details>

---

<details>

  <summary> 11. How does the ClusterIP works ? </summary>

  <p>

  It is the default service type. Exposes the Service on a cluster-internal IP. Only reachable within cluster (nodes and pods). Pods 
  can reach service on apps port number.

  1. Create an deployment

  ```console
  kubectl create deployment hello-node --image=k8s.gcr.io/echoserver:1.4
  ```

  2. Scale it two.

  ```console
  kubectl scale deploy/hello-node --replicas=2
  ```

  3. Create a service
   
   ```console
   kubectl expose deployment hello-node --port=8080
   ```

   4. Get the pods list
   
   ```console
   kubectl get pods
   ```

   5. Get into the shell for one of the pod

  ```console
  kubectl exec -it pod/hello-node-7567d9fdc9-qxtjt -- bin/bash
  ```

  6. Access the other pod

  ```console
  curl hello-node:8080
  ```

  </p>

</details>

---

<details>

  <summary> 12. How does the NodePort service works ? </summary>

  <p>

Exposes the Service on each Node's IP at a static port (the NodePort). High port allocated on each node. You'll be able 
to contact the NodePort Service, from outside the cluster, by requesting <NodeIP>:<NodePort>

1. Create an deployment

```console
  kubectl create deployment hello-node --image=k8s.gcr.io/echoserver:1.4
```

2. Create a service

```console
kubectl expose deployment hello-node --port=8080 --type=NodePort
```

3. Open the exposed service in minikube

```console
minikube service hello-node
```

  </p>

</details>

---

<details>

  <summary> 13. What is Imperative vs Declarative style ? </summary>

  <p>

* **Imperative** - uses a sequence of statements to determine how to reach a certain goal. Like using kubectl CLI commands.

* **Declarative** - describe their desired results without explicitly listing commands or steps that must be performed. Like by 
writing specifications in the YAML files and using `apply` command to get the desired result.

  </p>

</details>

---

<details>

  <summary> 14. What are the main parts of k8s configurations in yaml file ? </summary>

  <p>

  Each manifests file needs four parts. 
  
  1. `apiVersion` - Which version of the Kubernetes API you're using to create this object (`kubectl api-versions` to list all 
  versions)
  
  2. `kind` - What kind of object you want to create (`kubectl api-resources` to get list of available objects)
   
  3. `metadata` - Data that helps uniquely identify the object, including a name string, UID, and optional namespace

  4. `spec` - What state you desire for the object

  And there is another part called `status` which will be automatically added by kubernetes.

  </p>

</details>

---

<details>

  <summary> 15. What are the ways to get list of available keys for k8s object/kind ? </summary>

  <p>

  To get all the keys for a specific object/kind.

  ```console
  kubectl explain services --recursive
  ```

  To get the keys for a specific resource, and also with what value type it supports.

  ```console
  kubectl explain services.spec

  kubectl explain services.spec.type # specific to a single key.
  ```

  </p>

</details>

---

<details>

  <summary> 16. How to dry run a kubernetes commands ? </summary>

  <p>

The main usage of dry run is used to check syntactical errors and the preview of the object that would be sent to your cluster.

Syntax: 

```console
kubectl create deployment nginx --image=nginx --dry-run=client
kubectl create deployment nginx --image=nginx --dry-run=server
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml # to get the result template
kubectl apply -f app.yml --dry-run
kubectl apply -f app.yml --dry-run=server
```

  </p>

</details>

---

<details>

  <summary> 17. What is the use of a `diff` command ? </summary>

  <p>

`kubectl diff` shows the differences (changes made) between the current "live" object and the new "dry-run" object.

Syntax:

```console
kubectl diff -f some-resources.yaml
```

  </p>

</details>

---

<details>

  <summary> 18. What is labels & selectors ? How the connection is being established using it ? </summary>

  <p>

In yaml file of k8s contains, 

`Meta data` - contains labels, labels (labels do not provide uniqueness, expect many objects to carry the same label(s)) can be used 
to organize and to select subsets of objects.

`spec` - contains selector, selectors depend on labels to select a group of resources such as pods.

`Example template`

```YAML
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: label-demo
  labels:
    environment: production
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: App1
   template:
     metadata:
       labels:
         environment: production
         app: nginx
     spec:
       containers:
         - name: nginx
           image: nginx:1.7.9
```

  </p>

</details>

---

<details>

  <summary>  </summary>

  <p>


  </p>

</details>

---

## Resources:

### Video series

* [Kubernetes Tutorial for Beginners - Tech with Nana](https://www.youtube.com/watch?v=X48VuDVv0do&t=2s)

* [Udemy - Docker tutorial with Kubernetes - Bret Fisher](https://www.udemy.com/course/docker-mastery/)

* [Udemy - CKAD - Mumshad Mannambeth](https://www.udemy.com/course/certified-kubernetes-application-developer/learn/lecture/12321104#content)

### Articles

* [Kubernetes handbook](https://www.freecodecamp.org/news/the-kubernetes-handbook/)

* [Kubernetes official doc](https://kubernetes.io/docs/home/)

* [Collection of Kubernetes tutorial](https://www.aquasec.com/cloud-native-academy/kubernetes-101/kubernetes-tutorials/)