# Persistent Volumes Example

File **create_storage.yml**

	kind: PersistentVolume
	apiVersion: v1
	metadata:
	  name: it-vol-storage
	  labels:
	    type: local
	spec:
	  storageClassName: manual
	  capacity:
	    storage: 1Gi
	  accessModes:
	    - ReadWriteOnce
	  hostPath:
	    path: "/tmp/data"
	---
	apiVersion: v1
	kind: PersistentVolumeClaim
	metadata:
	  name:  it-vol-storage-1
	spec:
	  storageClassName: manual
	  accessModes:
	    - ReadWriteOnce
	  resources:
	    requests:
	      storage: 100Mi
	  volumeName: it-vol-storage


File **use_storage.yml**

	apiVersion: v1
	kind: Pod
	metadata:
	  name: examplestorage
	spec:
	  containers:
	  - image:  mycluster.icp:8500/default/ibm-rpta:9.2.1
	    name: ptasnikstorage
	    volumeMounts:
	    - mountPath: /testvol
	      name: testvol
	    command:
	    - /bin/bash
	    - -c
	    - TMP_ID=$(date '+%Y%m%d-%H%M') && touch /testvol/${TMP_ID}.result && sleep 31536000
	  volumes:
	  - name: testvol
	    persistentVolumeClaim:
	      claimName: it-vol-storage-1

File **clean_storage.yml**

	apiVersion: v1
	kind: Pod
	metadata:
	  name: ptasnikcleanstorage
	spec:
	  containers:
	  - image:  mycluster.icp:8500/default/ibm-rpta:9.2.1
	    name: ptasnikcleanstorage
	    volumeMounts:
	    - mountPath: /testvol
	      name: testvol
	    command:
	    - /bin/bash
	    - -c
	    - rm -rf /testvol/*
	  volumes:
	  - name: testvol
	    persistentVolumeClaim:
	      claimName: it-vol-storage-1

The following key concepts
* The volume works differently than docker volumes
* The volume DOES **not** map local file storage into the pod
  * You must use _kubectl cp_ to initially populate the PersistentVolume
  * You must use _kubectl cp_ to get results out of the PersistentVolume
* The _persistence_ is across the pod life-cycle.  So you only need to populate it one time. 
* In real-world examples, say on Amazon/Azure the mappings are much simpler

## Typical Flow

Define and pre-populate the persistent volume 

	kubectl create -f create_storage.yml
	"it-vol-storage" created
	"it-vol-storage-1" created
	
	kubectl create -f use_storage.yml
	pod "examplestorage" created
	
	kubectl cp input.txt examplestorage:/testvol
	kubectl exec -it examplestorage -- ls -la /testvol
	total 4
	drwxr-xr-x. 2 root root 51 Jul 19 20:13 .
	drwxr-xr-x. 1 root root 32 Jul 19 20:12 ..
	-rw-r--r--. 1 root root  0 Jul 19 20:12 20180719-2012.result
	-rw-rw-r--. 1 1002 1002 10 Jul 19 18:20 input.txt
	
	kubectl delete -f use_storage.yml
	pod "examplestorage" deleted
	
	kubectl get pv it-vol-storage
	NAME             CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS    CLAIM                      STORAGECLASS   REASON    AGE
	it-vol-storage   1Gi        RWO            Retain           Bound     default/it-vol-storage-1   manual                   3m
	
	kubectl get pvc it-vol-storage-1
	NAME               STATUS    VOLUME           CAPACITY   ACCESS MODES   STORAGECLASS   AGE
	it-vol-storage-1   Bound     it-vol-storage   1Gi        RWO            manual         3m
	
the volume persists across multiple create/delete cycles of the pods

	kubectl create -f use_storage.yml
	pod "examplestorage" created
	
	kubectl delete -f use_storage.yml
	pod "examplestorage" deleted
	sleep 65
	
	kubectl create -f use_storage.yml
	pod "examplestorage" created
	
	kubectl delete -f use_storage.yml
	pod "examplestorage" deleted
	sleep 65
	
	kubectl create -f use_storage.yml
	pod "examplestorage" created
	
	kubectl exec -it examplestorage -- ls -la /testvol
	total 4
	drwxr-xr-x. 2 root root 135 Jul 19 20:22 .
	drwxr-xr-x. 1 root root  32 Jul 19 20:22 ..
	-rw-r--r--. 1 root root   0 Jul 19 20:12 20180719-2012.result
	-rw-r--r--. 1 root root   0 Jul 19 20:18 20180719-2018.result
	-rw-r--r--. 1 root root   0 Jul 19 20:20 20180719-2020.result
	-rw-r--r--. 1 root root   0 Jul 19 20:22 20180719-2022.result
	-rw-rw-r--. 1 1002 1002  10 Jul 19 18:20 input.txt

to copy files out use kubectl cp

	kubectl cp examplestorage:/testvol/20180719-2022.result .
	tar: Removing leading `/' from member names      // ignore this message
	
	ls
	20180719-2022.result  clean_storage.yml  create_storage.yml  input.txt  use_storage.yml

to 'clean' the persistent volume simple execute a command inside a pod

	kubectl create -f clean_storage.yml
	pod "ptasnikcleanstorage" created
	
	kubectl delete -f clean_storage.yml
	pod "ptasnikcleanstorage" deleted
	
	kubectl exec -it examplestorage -- ls -la /testvol
	total 0
	drwxr-xr-x. 2 root root  6 Jul 19 20:25 .
	drwxr-xr-x. 1 root root 32 Jul 19 20:22 ..

The path inside the pods /testvol is now empty.  To shutdown things

	kubectl delete -f use_storage.yml
	pod "examplestorage" deleted
	
	kubectl delete -f create_storage.yml
	persistentvolume "it-vol-storage" deleted
	persistentvolumeclaim "it-vol-storage-1" deleted




