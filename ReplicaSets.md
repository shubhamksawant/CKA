
<details>
<summary>
Senario -
How about now? How many ReplicaSets do you see? How many PODs are DESIRED in the new-replica-set? What is the image used to create the pods in the new-replica-set?

```bash

controlplane ~ ➜  k get rs
NAME              DESIRED   CURRENT   READY   AGE
new-replica-set   4         4         0       6s


controlplane ~ ➜  k describe rs new-replica-set
Name:         new-replica-set
Namespace:    default
Selector:     name=busybox-pod
Labels:       <none>
Annotations:  <none>
Replicas:     4 current / 4 desired
Pods Status:  0 Running / 4 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  name=busybox-pod
  Containers:
   busybox-container:
    Image:      busybox777
    Port:       <none>
    Host Port:  <none>
    Command:
      sh
      -c
      echo Hello Kubernetes! && sleep 3600
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Events:
  Type    Reason            Age   From                   Message
  ----    ------            ----  ----                   -------
  Normal  SuccessfulCreate  88s   replicaset-controller  Created pod: new-replica-set-m5qdk
  Normal  SuccessfulCreate  88s   replicaset-controller  Created pod: new-replica-set-6w4gf
  Normal  SuccessfulCreate  88s   replicaset-controller  Created pod: new-replica-set-n4f9t
  Normal  SuccessfulCreate  88s   replicaset-controller  Created pod: new-replica-set-4hcb4

</summary>
1
4
busybox777

  ```
</details>




<details>
<summary>
Senario -
Create a ReplicaSet using the replicaset-definition-1.yaml file located at /root/.

```bash
apiVersion: v1
kind: ReplicaSet
metadata:
  name: replicaset-1
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx
  ```
</summary>

```bash

controlplane ~ ➜  k apply -f replicaset-definition-1.yaml 
replicaset.apps/replicaset-1 created

controlplane ~ ➜  cat replicaset-definition-1.yaml 
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-1
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx
  ```
</details>




<details>
<summary>
Senario -
Fix the issue in the replicaset-definition-2.yaml file and create a ReplicaSet using it.
```bash
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-2
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
  ```
</summary>

```bash
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-2
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: nginx
  template:
    metadata:
      labels:
        tier: nginx
    spec:
      containers:
      - name: nginx
        image: nginx

controlplane ~ ➜  k apply -f replicaset-definition-2.yaml 
replicaset.apps/replicaset-2 created
  ```
</details>




<details>
<summary>
Senario -
Delete the two newly created ReplicaSets - replicaset-1 and replicaset-2
</summary>

```bash

controlplane ~ ➜  k delete rs replicaset-1
replicaset.apps "replicaset-1" deleted

controlplane ~ ➜  k delete rs replicaset-2
replicaset.apps "replicaset-2" deleted
  ```
</details>




<details>
<summary>
Senario -
Scale the ReplicaSet to 5 PODs.
Use kubectl scale command or edit the replicaset using kubectl edit replicaset.
</summary>

```bash

controlplane ~ ✖ kubectl scale replicaset.apps/new-replica-set --replicas=5
replicaset.apps/new-replica-set scaled
  ```
</details>




<details>
<summary>
Senario -
Now scale the ReplicaSet down to 2 PODs.


Use the kubectl scale command or edit the replicaset using kubectl edit replicaset.
</summary>

```bash
controlplane ~ ➜  kubectl scale replicaset.apps/new-replica-set --replicas=2
replicaset.apps/new-replica-set scaled
  ```
</details>

