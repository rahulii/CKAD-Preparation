
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



      
