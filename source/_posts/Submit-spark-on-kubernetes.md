---
layout: posts
title: Submit spark on kubernetes
date: 2021-03-13 14:56:48
tags: spark
categories:
- bigdata
- spark
---

**Attention**: Please make sure you local machine can be accessed by the `pod` in kubernetes cluster. Because once you have submitted the job from scala shell, the executor will need to communicate with `driver node` to fetch the source data.

## Command

```
./bin/spark-shell 
  --master k8s://https://sh.tinkmaster.tech:18002  
  --conf spark.kubernetes.container.image=tinkmaster/tinkmaster-docker-spark-3.1.1-hadoop3.2-with-spark-r:alpha 
  --conf spark.kubernetes.namespace=spark 
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark-sa 
  --conf spark.kubernetes.driver.volumes.hostPath.hostpath-pv.mount.path=/opt/mount-files
  --conf spark.kubernetes.driver.volumes.hostPath.hostpath-pv.mount.readOnly=false
  --conf spark.kubernetes.driver.volumes.hostPath.hostpath-pv.options.path=/host-mount-files-path
```

`--master`: set the cluster manager
`spark.kubernetes.container.image`: tell driver to using which image to launch executor
`spark.kubernetes.container.namespace`: which namespace to run pod
`spark.kubernetes.authenticate.driver.serviceAccountName`: you'd better create an role to authorize executor pod to create other resouces like configMap and service.
`spark.kubernetes.driver.volumes.hostPath.hostpath-pv.mount.path`: thepath in executor pods mapping to the host path in host machine
`spark.kubernetes.driver.volumes.hostPath.hostpath-pv.mount.readOnly`: grant write access to the 
`spark.kubernetes.driver.volumes.hostPath.hostpath-pv.options.path`: the path in host machine you wanna mount


## About `hostPath` volume

Before creating a local persistent volume, you have to create a `local-storage` class. Explanation here: [kubernetes storage class](https://kubernetes.io/docs/concepts/storage/storage-classes/#local)

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
```

Then, now you can create the persistent volume to use the path in different pod.

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: hostpath-pv
spec:
  capacity:
    storage: 20Gi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-storage
  local:
    path: /mnt/disks/ssd1
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - minikube
```

Referred article:
* [kubernetes storage class](https://kubernetes.io/docs/concepts/storage/storage-classes/#local)
* [kubernetes pvc](https://kubernetes.io/docs/concepts/storage/volumes/#hostpath)