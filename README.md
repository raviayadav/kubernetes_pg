# kubernetes_pg

* Kubectl is kubernetes command line. Minikube is like a mini k8s which has a docker installed in it so that you may interact with docker command line to the docker daemon inside the minikube as the docker daemon on windows is not very smooth to work with yet and one might face issues.
* Type minikube docker-env, it wil give you the commands you need to use to make the connection between docker cmmnd line on ur pc and minikube docker daemon.

* kubectl cluster-info
* kubectl get nodes
* kubectl get all
* minikube ip
* docker container run -p 8080:80 -d richardchesterwood/k8s-fleetman-webapp-angular:release0-5 ->> 8080 is the port exposed of cotainer, 80 is the port nginx is working on in container, -d is used to run in background and not hang the terminal. Connect using minikubeip:8080 from ur browser
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
* kubectl apply -f serviceConfig-fileName.yaml // creates the service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: fleetman-webapp

spec:
  # This defines which pods are going to be represented by this Service
  # The service becomes a network endpoint for either other services
  # or external users to connect to (browser)
  # Accept traffic on port and forward that traffic to targetPort
  # If the type is clusterIP then the service is only accessible from inside the cluster and not by external things like browsers. Usecase - microservices
  # In this case, we will use type as nodePort which allows external access to the nodes. The nodePort value under ports should be greater than 30000 upto 32k
  # We can use an ingress service if we don't want the restriction of port numbers
  selector:
    app: webapp

  ports:
    - name: http
      port: 80
      nodePort: 30080
      
  type: NodePort
```
* The above configurations will not work though. Still, we need to use labels in both the pod and service configurations
```yaml
# In the service.yaml
  selector:
    mylabel: webapp

# In the pod.yaml
metadata: 
  name: webapp
  labels:
    mylabel: webapp
```
* Now we will be able to access through minikube-ip:30080
* This proves that labels can be arbitrary. However just to maintain consistency, we will rename the label key in the first-pod.yaml to app and in the service.yaml file we will rename the selector key to app as well.
* We use release key in yaml files to configure releases under the label.
* Three dashes in yaml is the document seperator
* We can make multiple pods from the same file using document seperator as shown below
```yaml
apiVersion: v1
kind: Pod
metadata: 
  name: webapp
  labels:
    app: webapp
    release: "0"
spec:
  containers:
  - name: webapp
    image: richardchesterwood/k8s-fleetman-webapp-angular:release0

---

apiVersion: v1
kind: Pod
metadata: 
  name: webapp-release-0-5
  labels:
    app: webapp
    release: "0-5"
spec:
  containers:
  - name: webapp
    image: richardchesterwood/k8s-fleetman-webapp-angular:release0-5

```
* Below is the relese config for our service file

```yml
apiVersion: v1
kind: Service
metadata:
  name: fleetman-webapp

spec:
  # This defines which pods are going to be represented by this Service
  # The service becomes a network endpoint for either other services
  # or external users to connect to (browser)
  # Accept traffic on port and forward that traffic to targetPort
  # If the type is clusterIP then the service is only accessible from inside the cluster and not by external things like browsers. Usecase - microservices
  # In this case, we will use type as nodePort which allows external access to the nodes. The nodePort value under ports should be greater than 30000
  # We can use an ingress service if we don't want the restriction of port numbers
  selector:
    app: webapp
    release: "0"

  ports:
    - name: http
      port: 80
      nodePort: 30080
      
  type: NodePort
```
* We can check what release the current service is pointing to by using 
* kubectl describe service service-name-here
* Then change the release in service.yaml to the release we want to go live on.
* kubectl apply -f service-filename.yaml
* kubectl get pods
* kubectl get po
* kubectl get po --show-labels
* kubectl get po --show-labels -l release=0 //notice l for selecting
