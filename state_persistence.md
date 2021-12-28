**Introduction to Docker Storage**
When it comes to storage in Docker , the 2 imp concepts are:
- Storage Drivers plugins
- Volume Driver plugins

Copy on write mechanism in Docker: A new read write layer is created to edit.
Read more [WIP]

To create a new volume in docker:
`docker volume create data_volume`

This will create a new directory under /var/lib/docker/volumes/

We can mount this volume inside the dockers read write layer as:

docker run -v data_volume:/var/lib/mysql/ mysql

This is called Volume Mounting

If you want to mount any other folder outside of /var/lib/docker/voluemes folder,
you must specify the complete path!
for eg: if you want to bind /data/mysql/

`docker run -v /data/mysql:/var/ib/mysql/ mysql`
This is known as bind mounting

Volume Mount:- Mounts a directory from the volumes directory
Bind Mount:- Mounts a directory from any location on the docker host

New way:- `docker run --mount type=bind,source=/data/mysql,target=/var/lib/mysql mysql`

Storage Drivers are used to enable all of these operations- copy on write mechanism , layered architecture, etc
Common Storage Drivers:- AUFS , Overlay , Overlay2, ZFS , Device mapper
Storage Drivers help maintain storage on images and containers

Volumes are provisioned by Volume Drivers
eg:- rexray/ebs for provisioning volume on AWS

**Volumes in Kubernetes**
```yaml
spec:
  containers:
    name: alpine
    image: alpine
    volumeMounts:
    - mountPath: /opt
      name: data-volume
  
  volumes:
    - name: data-volume
      hostPath:
        path: /data
        type: Directory
    
```

**Persistent Volumes**
A Persistent Volume is a cluster wide pool of storage volumes configured by an adminitrator, to be used by user deploying applications on the cluster
The users can now select storage using PVC (Persistent Volume Claims)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-vol1
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 1Gi
  hostPath:
    path: /tmp/data
  persistentVolumeReclaimPolicy: Recycle
```

**Persistent Volume Claims**
PV and PVCs are two seperae objects in kubernetes.
A k8s admin creates a PV and user creates a PVC to use the storage
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mycalaim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```
The control plane can bind PersistentVolumeClaims to matching PersistentVolumes in the cluster.
persistentVolumeReclaimPolicy = Retain , Recycle ,etc
