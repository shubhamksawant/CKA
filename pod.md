
<details>
<summary>
Senario -
Create a new pod with the nginx image.
</summary>

```bash

controlplane ~ ➜  k run test --image nginx
pod/test created

controlplane ~ ➜  k get po
NAME            READY   STATUS    RESTARTS   AGE
newpods-c4qf2   1/1     Running   0          2m43s
newpods-gmswx   1/1     Running   0          2m43s
newpods-tt4lm   1/1     Running   0          2m43s
test            1/1     Running   0          6s

  ```
</details>


<details>
<summary>
Senario -
Which nodes are these pods placed on?
</summary>

```bash

controlplane ~ ➜  k get po -o wide
NAME            READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
newpods-c4qf2   1/1     Running   0          3m33s   10.42.0.10   controlplane   <none>           <none>
newpods-gmswx   1/1     Running   0          3m33s   10.42.0.11   controlplane   <none>           <none>
newpods-tt4lm   1/1     Running   0          3m33s   10.42.0.9    controlplane   <none>           <none>
test            1/1     Running   0          56s     10.42.0.12   controlplane   <none>           <none>
  ```
</details>




<details>
<summary>
Senario -
  What does the READY column in the output of the kubectl get pods command indicate?

  ```bash

controlplane ~ ➜  k get po
NAME            READY   STATUS             RESTARTS   AGE
newpods-c4qf2   1/1     Running            0          4m6s
newpods-gmswx   1/1     Running            0          4m6s
newpods-tt4lm   1/1     Running            0          4m6s
test            1/1     Running            0          89s
webapp          1/2     ImagePullBackOff   0          10s


  ```
</summary>

```bash
running container / total container

  ```
</details>




<details>
<summary>
Senario -
Delete the webapp Pod.

</summary>

```bash

controlplane ~ ➜  k delete pod webapp
pod "webapp" deleted
  ```
</details>




<details>
<summary>
Senario -
Now change the image on this pod to redis.
</summary>

```bash

controlplane ~ ➜  k edit pod redis
pod/redis edited
  ```
</details>




<details>
<summary>
Senario -

</summary>

```bash

  ```
</details>




<details>
<summary>
Senario -

</summary>

```bash

  ```
</details>




<details>
<summary>
Senario -

</summary>

```bash

  ```
</details>




<details>
<summary>
Senario -

</summary>

```bash

  ```
</details>




<details>
<summary>
Senario -

</summary>

```bash

  ```
</details>




<details>
<summary>
Senario -

</summary>

```bash

  ```
</details>




<details>
<summary>
Senario -

</summary>

```bash

  ```
</details>




<details>
<summary>
Senario -

</summary>

```bash

  ```
</details>




<details>
<summary>
Senario -

</summary>

```bash

  ```
</details>




<details>
<summary>
Senario -

</summary>

```bash

  ```
</details>





