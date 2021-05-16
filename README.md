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

  ![kubernetes_objects](/img/kube-kube-obj.jpeg) ![kubernetes_objects](/img/kube-kube-objects.png)

  Image source of [Kubernetes object](https://tsuyoshiushio.medium.com/kubernetes-in-three-diagrams-6aba8432541c)

  <p>
  
    **Pod** - basic unit of work. It creates an abstraction over containers, so that it can be replaced with anytime run time 
    containers (eg: docker, cri-o). Many containers can be deployed into the pod, but the best practice is one container per pod. 
    Each pod gets it own IP address and new IP address on every re-creation. It also acts as a load balancer.

    **Service** - it is an abstract way to expose an application running on a set of Pods as a network service. It creates a 
    permanent IP address, lifecycle of pod and service are not connected. Even if the pods crashes and recreated, service IP remains 
    same.

    **Ingress** - it manages external access to the services in a cluster, typically HTTP. It provides load balancing, ssl 
    termination and name based hosting.

    **ConfigMap** - it is used to store non-confidential (external config) data in key-value pairs.

    **Secret** - it is used to store and manage sensitive information (eg: passwords, tokens, and keys), stores in base64 encoded 
    format.

    **Deployment** - describes the desired state of a pod or a replica set, then gradually updates the environment (for example, 
    creating or deleting replicas) until the current state matches the desired state specified in the deployment file. In general we 
    don't work directly with pods, we will create deployments. It is mainly for stateless apps.

    **StatefulSet** - it is used to manage stateful applications with persistent storage (useful for db like mysql, MongoDb ...). It makes sure all the request to db are synchronized so that we can avoid data inconsistency problem. Pod names are persistent and are retained when rescheduled. Storage stays associated with replacement pods. Volumes persist when pods are deleted

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