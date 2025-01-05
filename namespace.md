
<details>
<summary>
Senario -
how to check How many Namespaces exist on the system?
</summary>

```bash
controlplane ~ ➜  k get ns
NAME              STATUS   AGE
default           Active   7m20s
dev               Active   36s
finance           Active   36s
kube-node-lease   Active   7m20s
kube-public       Active   7m20s
kube-system       Active   7m20s
manufacturing     Active   36s
marketing         Active   36s
prod              Active   36s
research          Active   36s

  ```
</details>




<details>
<summary>
Senario -
how to check  How many pods exist in the research namespace?
</summary>

```bash

controlplane ~ ➜  k get po -n research
NAME    READY   STATUS             RESTARTS      AGE
dna-1   0/1     CrashLoopBackOff   3 (37s ago)   82s
dna-2   0/1     CrashLoopBackOff   3 (36s ago)   82s

  ```
</details>




<details>
<summary>
Senario -
Create a POD in the finance namespace.
</summary>

```bash

controlplane ~ ➜  k run redis --image=redis --namespace=finance
pod/redis created

controlplane ~ ➜  k get po -n finance
NAME      READY   STATUS    RESTARTS   AGE
payroll   1/1     Running   0          2m43s
redis     1/1     Running   0          9s

  ```
</details>




<details>
<summary>
Senario -
how to check  Which namespace has the blue pod in it?
</summary>

```bash

controlplane ~ ➜  k get all -A
NAMESPACE       NAME                                          READY   STATUS             RESTARTS      AGE
dev             pod/redis-db                                  1/1     Running            0             4m26s
finance         pod/payroll                                   1/1     Running            0             4m26s
finance         pod/redis                                     1/1     Running            0             112s
kube-system     pod/coredns-5dd589bf46-rk57z                  1/1     Running            0             11m
kube-system     pod/helm-install-traefik-6w7nb                0/1     Completed          1             11m
kube-system     pod/helm-install-traefik-crd-f5crz            0/1     Completed          0             11m
kube-system     pod/local-path-provisioner-846b9dcb6c-cr67c   1/1     Running            0             11m
kube-system     pod/metrics-server-5dc58b587c-rd8j4           1/1     Running            0             11m
kube-system     pod/svclb-traefik-cf7fe512-q7v6w              2/2     Running            0             10m
kube-system     pod/traefik-7f4b44bf74-rjbfx                  1/1     Running            0             10m
manufacturing   pod/red-app                                   1/1     Running            0             4m26s
marketing       pod/blue                                      1/1     Running            0             4m26s
marketing       pod/redis-db                                  1/1     Running            0             4m26s
research        pod/dna-1                                     0/1     CrashLoopBackOff   5 (77s ago)   4m26s
research        pod/dna-2                                     0/1     CrashLoopBackOff   5 (84s ago)   4m26s

NAMESPACE       NAME                      TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                      AGE
default         service/kubernetes        ClusterIP      10.43.0.1       <none>           443/TCP                      11m
dev             service/db-service        ClusterIP      10.43.25.65     <none>           6379/TCP                     4m26s
finance         service/payroll-service   NodePort       10.43.182.217   <none>           8080:30083/TCP               4m26s
kube-system     service/kube-dns          ClusterIP      10.43.0.10      <none>           53/UDP,53/TCP,9153/TCP       11m
kube-system     service/metrics-server    ClusterIP      10.43.186.28    <none>           443/TCP                      11m
kube-system     service/traefik           LoadBalancer   10.43.169.205   192.168.227.67   80:31048/TCP,443:32625/TCP   10m
manufacturing   service/red-service       NodePort       10.43.52.216    <none>           8080:30080/TCP               4m26s
marketing       service/blue-service      NodePort       10.43.105.161   <none>           8080:30082/TCP               4m26s
marketing       service/db-service        NodePort       10.43.67.189    <none>           6379:32546/TCP               4m26s

NAMESPACE     NAME                                    DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
kube-system   daemonset.apps/svclb-traefik-cf7fe512   1         1         1       1            1           <none>          10m

NAMESPACE     NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   deployment.apps/coredns                  1/1     1            1           11m
kube-system   deployment.apps/local-path-provisioner   1/1     1            1           11m
kube-system   deployment.apps/metrics-server           1/1     1            1           11m
kube-system   deployment.apps/traefik                  1/1     1            1           10m

NAMESPACE     NAME                                                DESIRED   CURRENT   READY   AGE
kube-system   replicaset.apps/coredns-5dd589bf46                  1         1         1       11m
kube-system   replicaset.apps/local-path-provisioner-846b9dcb6c   1         1         1       11m
kube-system   replicaset.apps/metrics-server-5dc58b587c           1         1         1       11m
kube-system   replicaset.apps/traefik-7f4b44bf74                  1         1         1       10m

NAMESPACE     NAME                                 STATUS     COMPLETIONS   DURATION   AGE
kube-system   job.batch/helm-install-traefik       Complete   1/1           18s        11m
kube-system   job.batch/helm-install-traefik-crd   Complete   1/1           15s        11m

  ```
</details>




<details>
<summary>
Senario -
What DNS name should the application pod use to access the database db-service in the dev namespace?
</summary>

```bash
Since the blue application and the db-service are in different namespaces. In this case, we need to use the service name along with the namespace to access the database. The FQDN (fully Qualified Domain Name) for the db-service in this example would be db-service.dev.svc.cluster.local.

Note: You can also access it using the service name and namespace like this: db-service.dev
  ```
</details>

