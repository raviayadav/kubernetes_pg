# kubernetes_pg

* kubectl cluster-info
* kubectl get nodes
* kubectl get all
* minikube ip
* 
```sh
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.34.0.3    <none>        443/TCP   46h
```
* In the above output, the kubectl sends all these cli commands to this rest endpoint(cluster-ip) of our minikube.
* kubectl apply -f podConfig-filename.yaml // creates the pod
```yaml
apiVersion: v1
kind: Pod
metadata: 
  name: webapp
spec:
  containers:
  - name: webapp
    image: path-to-your-image
```
* Pods are not visible outside the kubernetes cluster.
* kubectl describe podname
* kubectl exec podname ls // lets you execute the command on the pod
* kubectl exec -it podname sh // to interact with terminal we need to use the it flag
* from the shell, check if the app is deployed. wget http://localhost:80
* services can access pods through the labels defined in the pods.
