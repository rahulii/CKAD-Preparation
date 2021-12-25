
Security in Docker
By defualt every process in container is run as root user.
Docker implemnents a set of security features that limits the ability of root user within the container! 
So the root user within the container is not same as within the host.
Root user and the processes run by the root user has unrestricted access to the files , reboting the host , etc.
To see the full capabilities of a root user:- /usr/include/linux/capabilities.h

By default docker runs the container with limited set of capabilities.
--cap-add to add more capabilities to docker user
eg : docker run --cap-add MAC_ADMIN ubuntu
all priviliges: --privilliged flag

**Kubernetes Security**
To apply security context at a pod level , use
```yaml
spec:
  securityContext:
    runAsUser: 1000
    
    capabilities:
      add: ["MAC_ADMIN"]
```

**Resource Requirements**
Every pod consumes a set of resources - vCPU , memory and disk space
K8s scheduler decides which node a pod goes to .
By default: cpu: 0.5 and memory: 256Mi (Mebibyte)
When a scheduler tries to place a pod on the node it checks if the sufficient amount of these are available
pod.yaml
```yaml
spec:
  resources:
    requests:
      memory: "1Gi"
      cpu: 1
     limits:
      memory: "2Gi"
      cpu: 2
```

1 Gb = 1k MB
1 Gi = 1024 Mi


**Service Account**

There are 2 types of user in k8s: User Account and Service Account
Service Accounts are used by applications to interact with k8s api server, for eg Prometheus uses Service Account to poll k8s
In order for any application to talk to kube-api-server it needs to be authenticated. For authentication , we use service accounts

By default a pod mounts a default service account.
To use different service account , use
```yaml
serviceAccountName: dashboard-sa
```
 **Taints and Tolerations**
 Taints and Tolerations are used to restrict , what pods can be scheduled on which nodes
 Taints are applied to Nodes
 Tolerations are applied to pods
      
`kubectl taint nodes  node-name key=value:taint-effect`
taint-effect: what happens to this pod , if they DO NOT Tolerate this taint
NoSchedule - scheduler will not schedule any new pods that do not tolerate the taint.
PreferNoSchedule - scheduler will try to not schedule
NoExecute - scheduler will not schedule any new pods that do not tolerate the taint and Existing pods that do not tolerate the taint will also be evicted
eg: kubectl taint nodes node-1 app=blue:NoSchedule

To apply tolerations to pods:
```yaml
spec:
  tolerations:
    - key: "app"
      operator: "Equal"
      value: "blue"
      effect: NoSchedule
```
Taints and Tolerations only restrict the pods , but it does not gurantee that the pods will be scheduled on a particular node.

**node Selectors**
When we want to place a pod on a particular node , one of the ways is to use nodeSelectors
```yaml
spec:
  nodeSelector:
    size: Large
```
where size=Large is a label on a node
How to label a node?
k label node <node_name> size=Large

The scheduler uses this labels to identify and match the nodes

Node Selectors cannot be used , when our requirement is much more complex
For eg: place a pod on Large OR Medium Nodes , or NOT on small Nodes

For this Node Affinity and Anti-Affinity features were introduced.

**Node Affinity**
The Primary feature of node affinity features are to ensure that the pods are hosted on a particular nodes
Node Affinity Types: describe the behaviour of a scheduler wrt to a pod 
 - requiredDuringSchedulingIgnoredDuringExecution
 - preferredDuringSchedulingIgnoredDuringExecution

```yaml
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
         - matchExpressions:
          - key: size
            operator: In
            values: 
            - Large
            
```
Other Operators: NotIn , Exists
