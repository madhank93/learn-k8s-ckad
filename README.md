# Learn Kubernetes

## What is k8s ?

It is a popular container orchestrator tool.

## Why do you need k8s and what problem does it solves ?

Trends of micro services has increased the usage of containers and to handle these containers efficiently, need an orchestrator tool like k8s. Orchestration tool helps in automating the deployment, managing, scaling, and networking of containers, elf healing, high availability and Automated rollouts and rollbacks.

<div style="text-align: center;">
    <img src="/img/kube-scale.png" alt="kubernetes_scaling" height="400" width="400"/>
</div>

## Kubernetes architecture and basic terminologies

<div style="text-align: center;">
    <img src="/img/kube-arch.png" alt="kubernetes_architecture" height="400" width="400"/>
</div>

Image source - Red hat - [Introduction to Kubernetes architecture](https://www.redhat.com/en/topics/containers/kubernetes-architecture)

- Kubernetes - The whole orchestration system

### Control plane / Master node

- **kube-apiserver** - It's an entry point to interact with Kubernetes API (acts as a gateway). It determines if a request is valid and, if it is, processes it. It can be accessed through the kubectl cmd or kubeadm.

- **kube-scheduler** - It considers the resource needs of a pod, such as CPU or memory, along with the health of the cluster. Then it schedules the pod to an appropriate compute node. Then it passes on the requests to kubelet to execute it.

- **kube-controller-manager** - Detects state changes in the cluster (eg: if pod crashes, detects it and recovers the cluster state). Checks with scheduler and makes sure the correct number of pods is running.

- **etcd** - It is a key-value store database, stores the cluster state. All of the cluster information (eg: new pod created, pod crashed) is stored in the etcd.

### Compute machine / Worker node

- **kubelet** - Kubernetes agent running on nodes, a tiny application that communicates with the control plane & containers vice versa and when the control plane needs something to happen in a node, the kubelet executes the action. It ensures the containers are running in the pod.

- **kube-proxy** - The kube-proxy handles network communications inside or outside of your cluster. It uses operating system’s packet filtering layer if there is one, otherwise , kube-proxy forwards the traffic itself.

- **container runtime** - It is the software responsible for running containers. It can be Docker, Containerd and CRI-O.

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

- **Pod** - basic unit of work. It creates an abstraction over containers, so that it can be replaced with anytime run time containers (eg: docker, cri-o). Many containers can be deployed into the pod, but the best practice is one container per pod. Each pod gets it own IP address and new IP address on every re-creation. It also acts as a load balancer.

- **Service** - it is an abstract way to expose an application running on a set of Pods as a network service. It creates a permanent IP address, lifecycle of pod and service are not connected. Even if the pods crashes and recreated, service IP remains same.

- **Ingress** - it manages external access to the services in a cluster, typically HTTP. It provides load balancing, ssl termination and name based hosting.

- **ConfigMap** - it is used to store non-confidential (external config) data in key-value pairs.

- **Secret** - it is used to store and manage sensitive information (eg: passwords, tokens, and keys), stores in base64 encoded format.

- **Deployment** - describes the desired state of a pod or a replica set, then gradually updates the environment (for example, creating or deleting replicas) until the current state matches the desired state specified in the deployment file. In general we don't work directly with pods, we will create deployments. It is mainly for stateless apps.

- **StatefulSet** - it is used to manage stateful applications with persistent storage (useful for db like mysql, MongoDb ...). It makes sure all the request to db are synchronized so that we can avoid data inconsistency problem. Pod names are persistent and are retained when rescheduled. Storage stays associated with replacement pods. Volumes persist when pods are deleted.

</p>

</details>

---

<details>

  <summary> 2. What is pod ? </summary>

  <p>

![pods](/img/pods.png)

Pod is the smallest object in k8s. Kubernetes doesn't deploy containers directly into the worker node, containers are encapsulated into k8s object known as pods.

For additional instances of the application generally new pod should created altogether rather creating multiple instance of application in the same pod. Pod should contain a single instance of an application. Pod usually has one to one relation with containers.

But helper container are allowed to co-exist in the same pod.

  </p>

</details>

---

<details>

  <summary> 3. How to deploy a pod ? </summary>

  <p>

Syntax:

```console
kubectl run <pod-name> --image=<image-name>
```

Example:

```console
kubectl run nginx --image=nginx
```

  </p>

</details>

---

<details>

  <summary> 4. How to get the list of pods ? </summary>

  <p>
  
  ```console
  kubectl get pods
  kubectl get pods -o wide # for additional information
  ```

  </p>

</details>

---

<details>

  <summary> 5. How to get the detailed description for a pod ? </summary>

  <p>

Syntax:

```console
kubectl describe pod <pod-name>
kubectl describe pods # Describe all pods
kubectl describe pods/<pod-name>
```

Example:

```console
kubectl describe pod nginx
kubectl describe pods/nginx
```

The above commands print a detailed description of the selected resources, including related resources such as events or controllers.

  </p>

</details>

---

<details>

  <summary> 6. How to get all the resources ? </summary>

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

  <summary> 7. How to get logs for a pod ? </summary>

  <p>

Syntax:

```console
kubectl logs <pod-name>
```

Example:

```console
kubectl logs nginx
```

  </p>

</details>

---

<details>

  <summary> 8. How to get an interactive shell inside the pod ? </summary>

  <p>

Syntax:

```console
kubectl exec -it <pod-name> -- <command>
```

Example:

```console
kubectl exec -it nginx -- bin/bash
```

  </p>

</details>

---

<details>

  <summary> 9. What is Imperative vs Declarative style ? </summary>

  <p>

- **Imperative** - uses a sequence of statements to determine how to reach a certain goal. Like using kubectl CLI commands.

- **Declarative** - describe their desired results without explicitly listing commands or steps that must be performed. Like by writing specifications in the YAML files and using `apply` command to get the desired result.

  </p>

</details>

---

<details>

  <summary> 10. What are the main parts of k8s configurations in yaml file ? </summary>

  <p>

Each manifests file needs four parts.

1. `apiVersion` - Which version of the Kubernetes API you're using to create this object (`kubectl api-versions` to list all versions)

2. `kind` - What kind of object you want to create (`kubectl api-resources` to get list of available objects)

3. `metadata` - Data that helps uniquely identify the object, including a name string, UID, and optional namespace

4. `spec` - What state you desire for the object

And there is another part called `status` which will be automatically added by kubernetes.

  </p>

</details>

---

<details>

  <summary> 11. How does a yml file look like for a pod ? </summary>

  <p>

```YAML
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: my-nginx
    type: server
  name: nginx
spec:
  containers:
  - image: nginx-container
    name: nginx
```

Similar yml file can be generated from a cmd line using the following cmd.

```console
kubectl run nginx --image=nginx --dry-run=client -o yaml
```

You can even generate a yml file using the above command

```console
kubectl run nginx --image=nginx --dry-run=client -o yaml > pods.yml
```

  </p>

</details>

---

<details>

  <summary> 12. How to edit a pod configuration ? </summary>

  <p>

Syntax:

```console
kubectl edit pod <pod-name>
```

Example:

```console
kubectl edit pod nginx
```

The above command will open up the pods configurations in vim editor.

  </p>

</details>

---

<details>

  <summary> 13. How to delete a pod ? </summary>

  <p>

Syntax:

```console
kubectl delete pod <pod-name>
kubectl delete pods --all # Delete all pods
kubectl delete pod <pod-name> --now # Delete a pod with minimal delay
```

Example:

```console
kubectl delete pod nginx
```

  </p>

</details>

---

<details>

  <summary> 14. What is Replicaset and the usage of it ? </summary>

  <p>
  
  A ReplicaSet's purpose is to maintain a stable set of replica Pods running at any given time. As such, it is often used to guarantee the availability of a specified number of identical Pods.

It brings up a new instance of a Pod when the existing one crashes or fails, scales it up when the running instances are not matched to the specified number, and deletes the Pods if replicaset is scaled down.

  </p>

</details>

---

<details>
  
  <summary> 15. How does a replicaset config file look like ? </summary>
  
  <p>

```YAML
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-replicaset
spec:
  selector:
    matchLabels:
      type: server # <--- replicaset selector
  replicas: 3
  template:
    metadata:
      name: my-pod
      labels:
        name: pod
        type: server # <--- pod label
    spec:
      containers:
        - name: my-container
          image: nginx
```

`ReplicaSet` selector should match the `Pod` label.

  </p>

</details>

---

<details>

  <summary> 16. How to get list of replicaset ? </summary>

  <p>

Syntax:

```console
kubectl get replicaset
```

  </p>

</details>

---

<details>

  <summary> 17. How to identify list of pods created using replicaset ? </summary>

  <p>

```console
kubectl get pods
```

![pods-created-using-replicaset](/img/pods-created-using-replicaset.png)

  </p>

</details>

---

<details>

  <summary> 18. How to scale a replicaset ? </summary>

  <p>

Syntax:

```console
kubectl scale --replicas=<count> -f <file-name.yml>
kubectl scale replicaset --replicas=<count> <replicaset-name>
```

Example:

```console
kubectl scale --replicas=6 -f k8s-files/replicaset/replicaset.yml
```

**Note**: Scaling pods from cmd will increase the pods count to 6, but count change will take place in the yml file.

  </p>

</details>

---

<details>

  <summary> 19. What is usage of Labels and Selectors ? </summary>

  <p>

Labels - are key/value pairs that are attached to k8s objects, like pods, service, etc. that help to identify that object. Labels can be used to organize and to select subsets of objects.

Selectors - via a label selector, the user can identify a set of objects.

The label selector is the core grouping primitive in Kubernetes.

`Meta data` - contains labels, labels (labels do not provide uniqueness, expect many objects to carry the same label(s)) can be used to organize and to select subsets of objects.

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

  <summary> 20. What is a Deployment in k8s ? </summary>

  <p>
  
  Deployment is an object in k8s, which helps in creating, modifying and managing a set of identical pods. It is a wrapper around Pods and Replicaset. It can scale the no. of pods, rollout updated code or rollback to an earlier version

  </p>

</details>

---

<details>

  <summary> 21. How to create a deployment ? </summary>

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

  <summary> 22. How does a yml file look like for Deployment ? </summary>

  <p>

Below is the nginx deployment config file

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: my-nginx
  name: my-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-nginx
  template:
    metadata:
      labels:
        app: my-nginx
    spec:
      containers:
      - image: nginx
        name: nginx
```

  </p>

</details>

---

<details>

  <summary> 23. How to view the created deployment ? </summary>

  <p>

```console
kubectl get deployments
```

  </p>

</details>

---

<details>

  <summary> 24. How to edit the deployment ? </summary>

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

  <summary> 25. How to delete the deployment ? </summary>

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

Deleting deployment will remove deployment, replicaset, pod and container. `Layers of abstraction is -> Deployment > Replica set > Pod > Container`

  </p>

</details>

---

<details>

  <summary> 26. How to create a deployment and scale it one command ? </summary>

  <p>

Syntax:

```console
kubectl create deployment <deployment-name> --image=<image-name> --replicas=<count>
```

Example:

```console
kubectl create deployment my-nginx --image=nginx --replicas=3
```

  </p>

</details>

---

<details>

  <summary> 27. How to scale using deployment name ? </summary>

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

  <summary> 28. How to dry run a kubernetes commands ? </summary>

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

  <summary> 29. What is a namespace in k8s ? </summary>

  <p>

![kubernetes_namespace](/img/k8s-namespace.png)

Here QA, UAT, and Prod are the namespaces.

**Namespaces** are Kubernetes objects which helps in organizing resources and partitions a single Kubernetes cluster into multiple virtual clusters.

By default when a cluster is created it will create 4 namespaces,

```console
kubectl get namespaces
```

1. default - by default all the resources created will be listed here.
2. kube-node-lease - namespace for the lease objects associated with each node which improves the performance of the node heartbeats as the cluster scales. It help determine the availability of a node.
3. kube-public - place for publicly accessible data.
4. kube-system - place for objects created by Kubernetes systems/control plane.

  </p>

</details>

---

<details>

  <summary> 30. How to create a namespace ? </summary>

  <p>

1. Creating namespace through CLI

Syntax:

```console
kubectl create namespace <namespace-name>
```

Example:

```console
kubectl create namespace my-namespace
```

2. Creating namespace through YAML file

```YAML
apiVersion: v1
kind: Namespace
metadata:
  name: my-namespace
```

  </p>

</details>

---

<details>

  <summary> 31. What is the need for the namespace ? </summary>

  <p>

1. Allowing resources to be grouped and isolates.
2. Avoids naming conflicts.
3. Resource sharing. (some are off limits; eg: ConfigMap in a Cluster-A, can't be accessed from Cluster-B)
4. Enhancing role-based access controls by limiting users and resources (limit usage of CPU, RAM, and Storage) to certain namespaces.

  </p>

</details>

---

<details>

  <summary> 32. Is every objects in k8s can be put under an namespace ? </summary>

  <p>

No. Namespace resources are not themselves in a namespace. And low-level resources, such as nodes and persistentVolumes, are not in any namespace.

To see which Kubernetes resources are and aren't in a namespace:

```console
# In a namespace
kubectl api-resources --namespaced=true

# Not in a namespace
kubectl api-resources --namespaced=false
```

  </p>

</details>

---

<details>

  <summary> 33. How to create a resource under a specified namespace ? </summary>

  <p>

If a namespace is not specified by default all the resources will be created under `default`.

1. From a command line

Syntax:

```console
kubectl create deployment <deployment-name> --image=<image-name> -n=<namespace-name>
kubectl create deployment <deployment-name> --image=<image-name> --namespace=<namespace-name>
```

Example:

```console
kubectl create deployment my-nginx --image=nginx -n=production
```

2.

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: my-nginx
  name: my-nginx
  namespace: production # namespace should be already created
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-nginx
  template:
    metadata:
      labels:
        app: my-nginx
    spec:
      containers:
      - image: nginx
        name: nginx
```

If you try to list all the resource by `kubectl get all` it will not display the above created resource since it lives in another namespace, to list it out, need to switch the default namespace to the newly created namespace.

  </p>

</details>

---

<details>

  <summary> 34. How to switch between namespace ? </summary>

  <p>

Syntax:

```console
kubectl config set-context --current --namespace=<namespace-name>
```

Example:

```console
kubectl config set-context --current --namespace=test-namespace
```

  </p>

</details>

---

<details>

  <summary> 35. How to view resources created specific to a namespace and for overall ? </summary>

  <p>

Syntax:

```console
kubectl get pods --namespace=<namespace-name>
kubectl get pods --all-namespaces # to get all the pods irrespective of namespaces
```

Example:

```console
kubectl get pods --namespace=prod
```

  </p>

</details>

<details>

  <summary> 36. How to access the resources available in other namespaces ? </summary>

  <p>

Even though namespace separates each other, adding the namespace name to the service name provides access to services in any namespace on the cluster.

![accessing-resources-inside-namespace](img/namespace-resource-accessing.png)

Syntax:

```console
<Service Name>.<Namespace Name>
```

Example:

```console
db-service.finance
```

  </p>

</details>

---

<details>

  <summary> 37. How to pass the arguments and commands in k8s ? </summary>

  <p>

The command and arguments that you define in the configuration file override the default command and arguments provided by the container image.

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: command-demo
  labels:
    purpose: demonstrate-command
spec:
  containers:
  - name: command-demo-container
    image: debian
    command: ["printenv"]
    args: ["HOSTNAME", "KUBERNETES_PORT"]
  restartPolicy: OnFailure
```

Running the above config file

```console
kubectl apply -f k8s-files/args-and-commands/commands.yml
```

Result:

```
command-demo
tcp://10.3.240.1:443
```

The command field corresponds to entrypoint in some container runtimes.

| Description                         | Docker field name | Kubernetes field name |
| ----------------------------------- | ----------------- | --------------------- |
| The command run by the container    | Entrypoint        | command               |
| The arguments passed to the command | Cmd               | args                  |

  </p>

</details>

---

<details>

  <summary> 38. How to pass a env variable in k8s ? </summary>

  <p>

Syntax:

```console
kubectl run <pod-name> --image=<image-name> --env="key=value" --env="key=value"
```

Example:

```console
kubectl run nginx --image=nginx --env="DNS_DOMAIN=cluster" --env="POD_NAMESPACE=default"
```

YAML config file.

```YAML
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - env:
    - name: DNS_DOMAIN
      value: cluster
    - name: POD_NAMESPACE
      value: default
    image: nginx
    name: nginx
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```

  </p>

</details>

---

<details>

  <summary> 39. How to easily manage non-confidential key-value details ? What is the use of ConfigMaps ? </summary>

  <p>

A ConfigMap is an API object used to store non-confidential data in key-value pairs. Pods can consume ConfigMaps as environment variables, command-line arguments, or as configuration files in a volume. It allows you to decouple environment-specific configuration from your container images, so that your applications are easily portable.

So it does not provide any secrecy or encryption, so its not suitable for storing passwords or keys.

  </p>

</details>

---

<details>

  <summary> 40. How to create a configmap ? </summary>

  <p>

1. Imperative way of creating a configmap

Syntax:

```console
kubectl create configmap \
    <config-name> --from-literal=<key>=<value>
```

Example:

```console
kubectl create configmap \
    app-config --from-literal=APP_COLOR=blue \
               --from-literal=APP_LOG_LEVEL=verbose
```

2. Creating a config map from a file

Syntax:

```console
kubectl create configmap \
    <config-name> --from-file=<path-to-file>
```

Example:

```console
kubectl create configmap \
    app-config --from-file=k8s-files/ex-4-configmaps/configfile.properties
```

3. Declarative way of creating a configmap

```YAML
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_COLOR: blue
  APP_LOG_LEVEL: verbose
```

  </p>

</details>

---

<details>

  <summary> 41. How to utilize the ConfigMaps with an example ? </summary>

  <p>

`Configmap`

```YAML
kind: ConfigMap
apiVersion: v1
metadata:
  name: test-configmap
data:
  # Configuration Values are stored as key-value pairs
  env.data.name: "test-app"
  env.data.url: "https://test-app.com"
  # File like Keys
  log.properties: |
    log_level=2
    error.color=red
    info.color2=green
```

1.  Configuring all key-value pairs in a ConfigMap as container environment variables

    ```YAML
    apiVersion: v1
    kind: Pod
    metadata:
      name: configmap-pod
    spec:
      containers:
        - name: configmap-busybox
          image: k8s.gcr.io/busybox
          command: [ "/bin/sh", "-c", "env" ]
          envFrom:
            # Loading the Complete ConfigMap
            - configMapRef:
                name: test-configmap # env variable will be set as
      restartPolicy: Never
    ```

2.  Using ConfigMaps in defined env variables and volumes

    ```YAML
    apiVersion: v1
    kind: Pod
    metadata:
      name: test-pod
    spec:
      containers:
        - name: test-container
          image: k8s.gcr.io/busybox
          command: [ "/bin/sh", "-c", "env" ]
          env:
            - name: SPECIAL_LEVEL_KEY # env variable name
              valueFrom:
                configMapKeyRef:
                  name: test-configmap # referring to the ConfigMap name
                  key: special.how # referring to ConfigMap key where the key is associated with the value
            - name: LOG_LEVEL
              valueFrom:
                configMapKeyRef:
                  name: test-configmap
                  key: log_level
          volumeMounts:
            - name: config
              mountPath: "/config"
              readOnly: true
      volumes:
        - name: config
          configMap:
            name: test-configmap
            items:
              - key : "log.properties"
                path: "log.properties"
      restartPolicy: Never
    ```

  </p>

</details>

---

<details>

  <summary> 42. How to easily manage secrets in k8s ? What is the use of Secrets ? </summary>

  <p>
  
  **Secrets** let you store and manage sensitive information, such as passwords, OAuth tokens, and ssh keys.

It can be used in 3 ways,

- As files in a volume mounted on one or more of its containers.
- As container environment variable.
- By the kubelet when pulling images for the Pod.

  </p>

</details>
  
---

<details>

  <summary> 43. What are the ways we can store data in secrets ? </summary>

  <p>
  
  There are two ways we can store data in secrets,
  1. base64 encoded 
  2. plain text - k8s will automatically encode

  </p>

</details>

---

<details>

  <summary> 44. How to create a secrets ? </summary>

  <p>

1. Imperative way of creating a secret

Syntax:

```console
kubectl create secret generic \
    <secret-name> --from-literal=<key>=<value>
```

Example:

```console
kubectl create secret generic \
    app-secret --from-literal=DB_USER=root \
               --from-literal=DB_PASSWORD=admin
```

2. Creating a secret map from a file

Syntax:

```console
kubectl create secret generic \
    <secret-name> --from-file=<path-to-file>
```

Example:

```console
kubectl create secret generic \
    app-secret --from-file=k8s-files/ex-4-configmaps/configfile.properties
```

3. Declarative way of creating a configmap

Convert the plain text password into base64 encoded string

```console
echo -n "password" | base64
```

Convert the base64 encoded string to plain text

```console
echo `echo -n "cGFzc3dvcmQ=" | base64 --decode`
```

```YAML
apiVersion: v1
kind: Secret
metadata:
    name: test-secret
type: Opaque
data:
    username: dXNlcm5hbWU=
    password: cGFzc3dvcmQ=
```

2.  Plain text

```YAML
apiVersion: v1
kind: Secret
metadata:
  name: test-secret2
type: Opaque
stringData:
  user: admin
  password: admin
```

  </p>

</details>

---

<details>

  <summary> 45. How to use secrets with an example ?   </summary>

  <p>

1.  Utilizing as file

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: mysql-client
spec:
  containers:
  - name: mysql
	image: mysql
	command: ["/bin/sh"]
	args: ["-c","mysql -u `cat /mnt/db-creds/user)` -p`cat /mnt/db-creds/password)` -h `cat /mnt/db-creds/host)`"]
	volumeMounts:
	- name: creds
  	  mountPath: "/mnt/db-creds"
  	  readOnly: true
  volumes:
  - name: creds
    secret:
  	secretName: test-secret
```

2.  using it through env variables

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: test-secret2
              key: username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: test-secret2
              key: password
```

3. Using it as a multiple env variables

```YAML
apiVersion: v1
kind: Pod
metadata:
    name: configmap-pod
spec:
    containers:
    - name: configmap-busybox
        image: k8s.gcr.io/busybox
        command: [ "/bin/sh", "-c", "env" ]
        envFrom:
        # Loading the Complete secret
        - secretRef:
            name: test-configmap
    restartPolicy: Never
```

  </p>

</details>

---

<details>

  <summary> 46. How to run PODS as a specific user and add capabilities ? </summary>

  <p>
  
  1. Specific user at container level

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: web-pod
spec:
  containers:
  - image: ubuntu
    name: ubuntu
    command: ["sleep", "3600"]
    securityContext:
        runAsUser: 1000
        capabilities: ["MAC_ADMIN"]
  restartPolicy: Never
```

2. Specific user at pods level (if an is user specified at pod level, all the container in the pod will be run as that user)

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: web-pod
spec:
  securityContext:
    runAsUser: 1000
  containers:
  - image: ubuntu
    name: ubuntu
    command: ["sleep", "3600"]
  - image: busybox
    name: busybox
    securityContext:
        runAsUser: 2000 # overriding the pod level user
        capabilities: ["MAC_ADMIN"]
  restartPolicy: Never
```

  </p>

</details>

---

<details>

  <summary> 47. What is Service Accounts in k8s ? </summary>

  <p>

There 2 types of account in k8s,

1.  User account - is used by humans, authenticated by the apiserver, performing some administrative tasks (for example, using kubectl) or a developer accessing the cluster to deploy an applications
2.  Service account - processes in containers inside pods can also contact the apiserver

  </p>

</details>

---

<details>

  <summary> 48. How to create a service account ? </summary>

  <p>

Syntax:

```console
kubectl create serviceaccount <service-account-name>
```

Example:

```console
kubectl create serviceaccount custom-dashboard
```

To get the list of service accounts

```console
kubectl get serviceaccount
```

  </p>

</details>

---

<details>

  <summary> 49. What is resource ? And how does a resource allocation takes place in k8s ? </summary>

  <p>

When a pod is created, optionally each resources needed for a container can be specified. The most common resources are 1. CPU and 2. Memory (RAM).

When a container is specified with the resource `request`, scheduler uses these information to decide on which node to place the pod. When a container is specified with the resource `limit`, kubelet enforces the container to use only the allowed limit.

If the node has not sufficient resources to place a pod then scheduler avoids that node and instead places that pod in the node which has the resources available.

If all the nodes available in the cluster does not have sufficient resources, k8s holds scheduling the pods and it will be in the pending state.

  </p>

</details>

---

<details>

  <summary> 50. What is the difference between `Resource` and `Limit` ? </summary>

  <p>

It's possible (and allowed) for a container to use more resource than its `request` for that resource specifies. However, a container is not allowed to use more than its resource `limit`.

If a pod tries to exceed a resource beyond it's limit, incase of CPU k8s throttles it and incase of RAM it terminates the pod.

  </p>

</details>

---

<details>

  <summary> 51. How to specify the resource and limit ? </summary>

  <p>

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
  - name: app
    image: images.my-company.example/app:v4
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
  - name: log-aggregator
    image: images.my-company.example/log-aggregator:v6
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

1 G (Gigabyte) = 1,000,000,000 bytes
1 M (Megabyte) = 1,000,000 bytes
1 K (kilobyte) = 1,000 bytes

1 Gi (Gibibyte) = 1,073,741,824 bytes
1 Mi (Mebibyte) = 1,048,576 bytes
1 Ki (kibibyte) = 1,024 bytes

  </p>

</details>

---

<details>

  <summary> 52. What is mean by taints and toleration ? </summary>

  <p>

`Taints` and `Tolerations` allow the node to control which pods should or should not be places on it. A taint allows a node to refuse pod to be scheduled unless that pod has a matching toleration.

You apply taints to a node through the node specification (NodeSpec) and apply tolerations to a pod through the pod specification (PodSpec). A taint on a node instructs the node to repel all pods that do not tolerate the taint.

Taints and tolerations are only meant to restrict node from accepting certain pods but it does not guarantee that a pod with tolerations is always placed on a tainted node.

  </p>

</details>

---

<details>

  <summary> 53. How to add a taints and tolerations ? </summary>

  <p>

1. Adding taints to the node

Syntax:

```console
kubectl taint nodes node1 key1=value1:NoSchedule
```

Example:

```
kubectl taint nodes node1 app=blue:NoSchedule
```

Note: If you are using minikube, follow this guide to setup multi node (https://minikube.sigs.k8s.io/docs/tutorials/multi_node/)

2. Adding tolerations to the pod

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  tolerations:
  - key: "app"
    operator: "Equal"
    value: "blue"
    effect: "NoSchedule"
```

  </p>

</details>

---

<details>

  <summary> 54. How to remove the taint from the node ? </summary>

  <p>

To remove the added taints from the node add `minus` symbol in the end

Syntax:

```console
kubectl taint nodes node1 key1=value1:NoSchedule-
```

Example:

```console
kubectl taint nodes node1 app=blue:NoSchedule-
```

  </p>

</details>

---

<details>

  <summary> 55. How to assign pods to a specific node ? </summary>

  <p>

We can add a constraints at a Pod level so that it will run only on a particular set of Node(s). The recommended approach is to add `label` to the node and `nodeSelectors` to the pod for facilitate the selection.

  </p>

</details>

---

<details>

  <summary> 56. How to add a label to the node ? </summary>

  <p>

Syntax:

```console
kubectl label nodes <node-name> <label-key>=<label-value>
```

Example:

```console
kubectl label nodes node01 size=large
```

  </p>

</details>

---

<details>

  <summary> 57. How to select a specific node for placing a pod ? </summary>

  <p>

Select a specific node by using `nodeSelector`

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    size: large
```

  </p>

</details>

---

<details>

  <summary> 58. What is the limitations of using `nodeSelector` ? And how to overcome it ? </summary>

  <p>

Following logical expression type of selection cannot be achieved using `nodeSelector`, for this one has to go for `Node affinity`.

- size: Large or Medium
- size: Not Small

  </p>

</details>

---

<details>

  <summary> 59. What is node affinity ? </summary>

  <p>
  
It allows you to constrain which nodes your pod is eligible to be scheduled on, based on labels on the node. And it allows the constraint more expressive.

Available types:

- Hard type : requiredDuringSchedulingIgnoredDuringExecution (if node didn't match pod will not be placed)
- Soft type : preferredDuringSchedulingIgnoredDuringExecution (even if node didn't match pod will be placed in anyone of the node)

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    env: staging
spec:
  containers:
    - name: node-affinity
      image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: size
                operator: In
                values:
                  - Large
                  - Medium
```

  </p>

</details>

---

<details>

  <summary> 60. What is the purpose of the multi container pods ? </summary>

  <p>

- The main purpose of a multi-container Pod is to facilitate support co-located, co-managed helper processes for a primary application. For example, providing logging service to the web server.
- Multi containers in a pod share same network namespace, shared volumes, and the same IPC namespace so it possible for these containers to efficiently communicate, ensuring data locality.
- This allows to manage several tightly coupled application containers as a single unit.
- All the containers in the pod share same lifecycle.

  </p>

</details>

---

<details>

  <summary> 61. What are some of the design patterns for Multi container pods ? </summary>

  <p>

//TODO: Need to add examples for below design pattern with diagrams and explanation

1. **Sidecar pattern**

![side-car](img/side-car.png)

A Sidecar container is a second container added to the Pod definition. It is placed in the same Pod so that it can use the same resources being used by the main container.

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: webserver
spec:
  volumes:
    - name: shared-logs
      emptyDir: {}
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx
    - name: sidecar-container
      image: busybox
      command:
        [
          "sh",
          "-c",
          "while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done",
        ]
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx
```

And access the log by executing the following command

```console
kubectl exec webserver -c sidecar-container -- bin/cat /var/log/nginx/error.log
kubectl exec webserver -c sidecar-container -- bin/cat /var/log/nginx/access.log
```

For more information refer following article,

[1] [Sidecar pattern](https://www.magalix.com/blog/the-sidecar-pattern)

2. **Adapter design pattern**
3. **Ambassador design pattern**

  </p>

</details>

---

<details>

  <summary> 62. How does multi container pod communication happens ? </summary>

  <p>

There are three ways that containers in the pod communicate with each other.

1. Shared volumes,
2. Inter-process communications (IPC), and
3. Inter-container network communication.

For more information refer following article,

[1] [Multi-container pods and container communication](https://www.mirantis.com/blog/multi-container-pods-and-container-communication-in-kubernetes/)

  </p>

</details>

---

<details>

  <summary> 63. How to maintain the order of container creation in terms of multi-container pods ?  </summary>

  <p>

Init containers can be use to maintain the order of the execution. Containers specified in the initContainers section will run before starting main container in the pod. Init container executes sequentially and each of the init containers must succeed before the next can run.

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
    - name: myapp-container
      image: busybox:1.28
      command: ["sh", "-c", "echo The app is running! && sleep 3600"]
  initContainers:
    - name: init-myservice
      image: busybox:1.28
      command:
        [
          "sh",
          "-c",
          "until nslookup myservice.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for myservice; sleep 2; done",
        ]
    - name: init-mydb
      image: busybox:1.28
      command:
        [
          "sh",
          "-c",
          "until nslookup mydb.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for mydb; sleep 2; done",
        ]
```

This example defines a simple Pod that has two init containers. The first waits for myservice, and the second waits for mydb. Once both init containers complete, the Pod runs the app container from its spec section.

  </p>

</details>

---

<details>

  <summary> 64. What are the pod status ? </summary>

  <p>

| Value     | Description                                                                                                                                                                                                                                                        |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Pending   | The Pod has been accepted by the Kubernetes cluster, but one or more of the containers has not been set up and made ready to run. This includes time a Pod spends waiting to be scheduled as well as the time spent downloading container images over the network. |
| Running   | The Pod has been bound to a node, and all of the containers have been created. At least one container is still running, or is in the process of starting or restarting.                                                                                            |
| Succeeded | All containers in the Pod have terminated in success, and will not be restarted.                                                                                                                                                                                   |
| Failed    | All containers in the Pod have terminated, and at least one container has terminated in failure. That is, the container either exited with non-zero status or was terminated by the system.                                                                        |
| Unknown   | For some reason the state of the Pod could not be obtained. This phase typically occurs due to an error in communicating with the node where the Pod should be running.                                                                                            |

  </p>

</details>

---

<details>

  <summary> 65. What are the pod conditions ? </summary>

  <p>
  
A Pod has a PodStatus, which has an array of PodConditions through which the Pod has or has not passed:

`PodScheduled`: the Pod has been scheduled to a node.

`ContainersReady`: all containers in the Pod are ready.

`Initialized`: all init containers have started successfully.

`Ready`: the Pod is able to serve requests and should be added to the load balancing pools of all matching Services.

  </p>

</details>

---

<details>

  <summary> 66. What are the types of health checks available in k8s ? </summary>

  <p>
<div style="text-align: center;">
    <img src="img/liveness.gif" alt="liveness" width="200" class="center"/>
</div>

Image source - [Google cloud blog](https://cloud.google.com/blog/products/containers-kubernetes/kubernetes-best-practices-setting-up-health-checks-with-readiness-and-liveness-probes)

1. **Liveness probes** - allows to check if app is alive. The kubelet agent that runs on each node uses the liveness probes to ensure that the containers are running as expected. Many applications running for long periods of time eventually transition to broken states, and cannot recover except by being restarted. Kubernetes provides liveness probes to detect and remedy such situations.

<div style="text-align: center;">
    <img src="img/readiness.gif" alt="readiness" width="200" class="center"/>
</div>

2. **Readiness probes** - The kubelet uses readiness probes to know when a container is ready to start accepting traffic. A Pod is considered ready when all of its containers are ready. If a readiness probe fails, Kubernetes will stop routing traffic to the pod until the probe passes again.

3. **Startup probes** - It is used to determine whether the container has started. If such a probe is configured, it disables liveness and readiness checks until it succeeds, making sure those probes don't interfere with the application startup. This can be used to adopt liveness checks on slow starting containers, avoiding them getting killed by the kubelet before they are up and running.

  </p>

</details>

---

<details>

  <summary> 67. How to perform different types of health checks ? </summary>

  <p>

At some point, an application may crash. Or an application might be still in the initialization stage and not yet ready to receive and process requests. Using probes (testing), we can determine whether the container is crashed or still running.

There is 3 ways to do it.

1. Sending an HTTP request
2. Running a command
3. Opening a TCP socket


1. Checking liveness and start-up probes using HTTP request

```YAML
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-http
spec:
  containers:
    - name: liveness
      image: k8s.gcr.io/liveness
      args:
        - /server
      livenessProbe:
        httpGet:
          path: /healthz
          port: 8080
          httpHeaders:
            - name: Custom-Header
              value: Awesome
        initialDelaySeconds: 3
        periodSeconds: 3
      startupProbe:
        httpGet:
          path: /healthz
          port: 8080
        failureThreshold: 30
        periodSeconds: 10
```

`livenessProbe` For the first 10 seconds that the container is alive, the /healthz handler returns a status of 200. After that, the handler returns a status of 500. The kubelet starts performing health checks 3 seconds after the container starts. So the first couple of health checks will succeed. But after 10 seconds, the health checks will fail, and the kubelet will kill and restart the container.

`startupProbes` legacy applications that might require an additional startup time on their first initialization. In such cases, it can be tricky to set up liveness probe parameters without compromising the fast response to deadlocks that motivated such a probe. setting up a startup probe with the same command, HTTP or TCP check, with a failureThreshold * periodSeconds long enough to cover the worse case startup time. The application will have a maximum of 5 minutes (30 * 10 = 300s) to finish its startup. Once the startup probe has succeeded once, the liveness probe takes over to provide a fast response to container deadlocks. If the startup probe never succeeds, the container is killed after 300s and subject to the pod's restartPolicy

Complete [source code](https://github.com/kubernetes/kubernetes/blob/master/test/images/agnhost/liveness/server.go)

```go
http.HandleFunc("/healthz", func(w http.ResponseWriter, r *http.Request) {
    duration := time.Now().Sub(started)
    if duration.Seconds() > 10 { // <-- After 10 secs it throw an error and the kubelet will kill and restart the container.
        w.WriteHeader(500)
        w.Write([]byte(fmt.Sprintf("error: %v", duration.Seconds())))
    } else {
        w.WriteHeader(200)
        w.Write([]byte("ok"))
    }
})
```

2. Checking liveness and readiness probe using TCP request

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: goproxy
  labels:
    app: goproxy
spec:
  containers:
    - name: goproxy
      image: k8s.gcr.io/goproxy:0.1
      ports:
        - containerPort: 8080
      readinessProbe:
        tcpSocket:
          port: 8080
        initialDelaySeconds: 5
        periodSeconds: 10
      livenessProbe:
        tcpSocket:
          port: 8080
        initialDelaySeconds: 15
        periodSeconds: 20
```

This example uses both readiness and liveness probes. The kubelet will send the first readiness probe 5 seconds after the container starts. This will attempt to connect to the goproxy container on port 8080. If the probe succeeds, the Pod will be marked as ready. The kubelet will continue to run this check every 10 seconds.

In addition to the readiness probe, this configuration includes a liveness probe. The kubelet will run the first liveness probe 15 seconds after the container starts. Similar to the readiness probe, this will attempt to connect to the goproxy container on port 8080. If the liveness probe fails, the container will be restarted.

3. Checking liveness using command

```YAML
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
    - name: liveness
      image: k8s.gcr.io/busybox
      args:
        - /bin/sh
        - -c
        - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
      livenessProbe:
        exec:
          command:
            - cat
            - /tmp/healthy
        initialDelaySeconds: 5
        periodSeconds: 5
```

For the first 30 seconds of the container's life, there is a /tmp/healthy file. So during the first 30 seconds, the command cat /tmp/healthy returns a success code. After 30 seconds, cat /tmp/healthy returns a failure code. So the containers have been killed and recreated.

  </p>

</details>

---

<details>

  <summary>   </summary>

  <p>
  
  </p>

</details>

---

<details>

  <summary>   </summary>

  <p>
  
  </p>

</details>

---

<details>

  <summary>   </summary>

  <p>
  
  </p>

</details>

---

<details>

  <summary> 10. What are the different types of Services available ? </summary>

  <p>

**Service** - provides the stable address for a pod(s).

- **ClusterIP** - It is the default service type. Exposes the Service on a cluster-internal IP. Only reachable within cluster. Pods can reach service on apps port number.

- **NodePort** - Exposes the Service on each Node's IP at a static port (the NodePort). High port allocated on each node. You'll be able to contact the NodePort Service, from outside the cluster, by requesting <NodeIP>:<NodePort>

```YAML
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app: MyApp
  ports:
      # By default and for convenience, the `targetPort` is set to the same value as the `port` field.
    - port: 80
      targetPort: 80
      # Optional field
      # By default and for convenience, the Kubernetes control plane will allocate a port from a range (default: 30000-32767)
      nodePort: 30007
```

- **LoadBalancer** - Exposes the Service externally using a cloud provider's load balancer. NodePort and ClusterIP Services, to which the external load balancer routes, are automatically created.

```YAML
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
  clusterIP: 10.0.171.239
  type: LoadBalancer
status:
  loadBalancer:
    ingress:
    - ip: 192.0.2.127
```

- **ExternalName** - Maps the Service to the contents of the externalName field (e.g. foo.bar.example.com), by returning a CNAME record with its value. No proxying of any kind is set up.

```YAML
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ExternalName
  externalName: my.database.example.com
```

  </p>

</details>

---

<details>

  <summary> 11. How does the ClusterIP works ? </summary>

  <p>

It is the default service type. Exposes the Service on a cluster-internal IP. Only reachable within cluster (nodes and pods). Pods can reach service on apps port number.

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

4.  Get the pods list

```console
kubectl get pods
```

5.  Get into the shell for one of the pod

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

Exposes the Service on each Node's IP at a static port (the NodePort). High port allocated on each node. You'll be able to contact the NodePort Service, from outside the cluster, by requesting <NodeIP>:<NodePort>

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

  <summary> 19. How to create nginx deployment and service using declarative approach ?  </summary>

  <p>

Use the `apply` command

```console
kubectl apply -f ./k8s-files/ex-1-nginx/nginx-deploy.yml
```

  </p>

</details>

---

<details>

  <summary> 20. How does Port, TargetPort, ContainerPort, and NodePort works ? </summary>

  <p>

- **Port** - The port of this service. Other pods in the cluster that may need to access the service will just use port.

- **TargetPort** - it forwards the traffic to `ContainerPort` (where its listening). Also, if targetPort is not set, it will default to the same value as port

- **ContainerPort** - port on which the app can be reached out inside the container.

- **NodePort** - makes the service visible outside the Kubernetes cluster by the node’s IP address and the port number

Flow - Traffic comes in on `NodePort` , forwards to `Port` on the service which then routes to `TargetPort` on the pod(s) and in turn it routes to `ContainerPort` (if TargetPort and ContainerPort matches).

1. Create deployment and service

```console
kubectl apply -f ./k8s-files/ex-2-ports/deployment.yml
kubectl apply -f ./k8s-files/ex-2-ports/service.yml
```

2. To test usage of ports, create a ubuntu pod with interactive shell and install curl

```console
kubectl run -i --tty ubuntu --image=ubuntu --restart=Never -- sh
apt-get update; apt-get install curl # to install curl
```

3. Access nginx using the Port from within the cluster

```console
curl nginx-service:8080 # `hello-world` is the service name and `8080` is the port mentioned in the service.
```

4. Access nginx using the Port from outside the cluster

```console
kubectl describe pod nginx-deploy # this will list the NodeIP (Node: minikube/192.168.64.2)
```

Result:

The above command will fetch you the NodeIP and NodePort is have already mentioned in the `service.yml` file.

```
Name:         nginx-deploy-756d646fff-8848w
Namespace:    default
Priority:     0
Node:         minikube/192.168.64.2 # <=== NodeIP
Start Time:   Sat, 22 May 2021 22:13:20 +0530
Labels:       app=hello-world
pod-template-hash=756d646fff
...
...
```

5. You can access it from the browser using `NodeIP:NodePort` in this case `192.168.64.2:31234`.

  </p>

</details>

---

<details>

  <summary> 28. What is Ingress ? </summary>

  <p>

![kubernetes_ingress](img/k8s-ingress.png)

**Ingress** exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource (e.g. load balancing, SSL termination,path-based routing, protocol).

The advantage of an Ingress over a LoadBalancer is that an Ingress can consolidate routing rules in a single resource to expose multiple services.

Example:

```YAML
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: dashboard-ingress
  namespace: kubernetes-dashboard
spec:
  rules:
  - host: my-app.com # Valid domain address, map domain name to IP address of the entry node and any incoming request must be forwarded to internal service
    http:
      paths:  # Incoming urls matching the path are forwarded to the backend.
      - backend:
          serviceName: my-internal-service # service name and port should correspond to the name of internal service
          servicePort: 80
```

  </p>

</details>

---

<details>

  <summary> 29. What is ingress controller ? What is the use of it ? </summary>

  <p>

![kubernetes_ingress_controller](img/k8s-ingress-controller.png)

An Ingress controller is responsible for fulfilling the Ingress, by evaluating all the rules, managing re-directions, acts as an entrypoint to the cluster.

There many Ingress controller are available, HAProxy Ingress, NGINX Ingress Controller, Traefik, and AKS (azure).

  </p>

</details>

---

<details>

  <summary>  30. How does ingress works in practically ? </summary>

  <p>

1. Enable ingress addons in minikube

```console
minikube addons enable ingress
```

Which automatically starts k8s nginx implementation of Ingress controller.

2. Now we are going to route incoming request to minikube k8s dashboard(right now it is not accessible to outside cluster)

![kubernetes_namespace](img/k8s-ns.png)

If you don't see the kubernetes dashboard, execute `minikube dashboard`

```console
kubectl apply -f ./k8s-files/ex-3-ingress/ingress.yml
```

```console
kubectl get ingress -n kubernetes-dashboard # get the address
```

Result:

```
NAME                CLASS    HOSTS           ADDRESS        PORTS   AGE
dashboard-ingress   <none>   dashboard.com   192.168.64.2   80      84s
```

```
# if Nginx Ingress: service “ingress-nginx-controller-admission” not found; execute the below command
# kubectl delete -A ValidatingWebhookConfiguration ingress-nginx-admission
```

3. Map the IP address to domain name (doing it locally)

```console
sudo vi /etc/hosts
```

And configure the IP address and domain name

![domain_mapping](img/k8s-ingress-ex1.png)

4. Go to browser and type dashboard.com, it will re-direct to the kubernetes dashboard.

Note: After exercise don't forget to remove domain mapping from `/etc/hosts` otherwise it will always try to reach that IP address

  </p>

</details>

---

<details>

  <summary> 31. What happens if an incoming request not mapped to any of the backend service ? </summary>

  <p>

Kubernetes has the default backend running.

Syntax:

```console
kubectl describe ingress <ingress-name> -n <namespace>
```

Example:

```console
kubectl describe ingress dashboard-ingress -n kubernetes-dashboard
```

w.r.t to previous example `dashboard.com/eat` will result in `404 page not found`

  </p>

</details>

---

<details>

  <summary> 32. What are the different use cases of ingress ? </summary>

  <p>

1. Multiple path for same host

```YAML
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: simple-example
spec:
  rules:
  - host: foo.bar.com
    http:
      paths:
      - path: /foo
        pathType: Prefix
        backend:
          service:
            name: service1
            port:
              number: 4200
      - path: /bar
        pathType: Prefix
        backend:
          service:
            name: service2
            port:
              number: 8080
```

2. Multiple sub-domains or domains

```YAML
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: name-virtual-host-ingress
spec:
  rules:
  - host: foo.app.com
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: service1
            port:
              number: 80
  - host: bar.app.com
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: service2
            port:
              number: 80
```

3. Configuring TLS (Transport Layer Security)

```YAML
apiVersion: v1
kind: Secret
metadata:
  name: test-secret-tls
  namespace: default
data:
  tls.crt: base64 encoded cert
  tls.key: base64 encoded key
type: kubernetes.io/tls
```

  </p>

</details>

---

<details>

  <summary> 38. Why do we need to persist data in k8s ? </summary>

  <p>

Data/files in a container are ephemeral (lasts only for a short period of time), once the container crashes or removed, data (ex: mysql data or logs of the server) inside the container will lost. To avoid such scenario, data must be persisted.

  </p>

</details>

---

<details>

  <summary>   </summary>

  <p>
  
  </p>

</details>

---

<details>

  <summary>   </summary>

  <p>
  
  </p>

</details>

---

<details>

  <summary>   </summary>

  <p>
  
  </p>

</details>

---

<details>

  <summary>   </summary>

  <p>
  
  </p>

</details>

---

<details>

  <summary>   </summary>

  <p>
  
  </p>

</details>

---

## Resources

### Video series

- [Kubernetes Tutorial for Beginners - Tech with Nana](https://www.youtube.com/watch?v=X48VuDVv0do&t=2s)

- [Udemy - Docker tutorial with Kubernetes - Bret Fisher](https://www.udemy.com/course/docker-mastery/)

- [Udemy - CKAD - Mumshad Mannambeth](https://www.udemy.com/course/certified-kubernetes-application-developer/learn/lecture/12321104#content)

### Articles

- [100 days of k8s - Anais Urlichs](https://100daysofkubernetes.io/)

- [Kubernetes handbook](https://www.freecodecamp.org/news/the-kubernetes-handbook/)

- [Kubernetes official doc](https://kubernetes.io/docs/home/)

- [Collection of Kubernetes tutorial](https://www.aquasec.com/cloud-native-academy/kubernetes-101/kubernetes-tutorials/)

- [Matthew Palmer articles on k8s](https://matthewpalmer.net/kubernetes-app-developer/articles/)

- [Kubernetes articles](https://www.magalix.com/hs-search-results?term=kubernetes&type=SITE_PAGE&type=BLOG_POST&type=LISTING_PAGE)

- [Collabnix k8s](https://collabnix.github.io/kubelabs/)

- [CKAD exercises](https://github.com/dgkanatsios/CKAD-exercises)

- [CKAD prep notes](https://github.com/twajr/ckad-prep-notes)
