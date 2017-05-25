# multi-tier-kubernetes
Angular front end on nginx, SpringBoot APIs and MongoDB

## Preparation

First need to get the following tools installed:

* Install Kubernetes

* Install Minikube

* Install Docker

## Application packaging and deployment

### Project Structure

The frontend application is contained within the folder `front-end` and the backend SpringBoot project is 
contained within `back-end`. The kubernetes `yaml` files for both front end and the backend are inside
the folder `k8s`.

### MongoDB

We will use the official `mongo` image from dockerhub and run a mongodb `service` in our kubernetes cluster. This
`service` will be used by the `backend` as data storage.

The `mongo.yaml` defines a `ReplicationController` which ensures that there is always one instance of `mongo`
running in our cluster. This mongodb `pod` is listening on container port `27017` and the data storage path is `/data/db`
within the container which is mounted on host at `/data/storage/mongodb`.
 
The mongodb `pod` above is exposed as a service within our kubernetes cluster as a service with name `mongo-service` as
described in `mongo.yaml`.

### SpringBoot Service

### Angular FrontEnd
 
### nginx Reverse Proxy
 
## Complete Deployment

* `minikube start` to start the local kubernetes cluster which takes a while to get the cluster ready.
   ```
        arun:k8s achalise$ minikube start
        There is a newer version of minikube available (v0.19.0).  Download it here:
        https://github.com/kubernetes/minikube/releases/tag/v0.19.0
        
        To disable this notification, run the following:
        minikube config set WantUpdateNotification false
        Starting local Kubernetes cluster...
        Starting VM...
        SSH-ing files into VM...
        Setting up certs...
        Starting cluster components...
        Connecting to cluster...
        Setting up kubeconfig...
        Kubectl is now configured to use the cluster.
        arun:k8s achalise$ 

   ```

* Deploy and start up the `mongo` service using the descriptor file `mongo.yaml`

   ```
    arun:k8s achalise$ kubectl create -f mongo.yaml 
    replicationcontroller "mongo-controller" created
    service "mongo-service" created
    arun:k8s achalise$ 
   ```

    Check that the pod and service are created and running successfully:
    
    ```
    arun:k8s achalise$ kubectl get pods 
    NAME                     READY     STATUS    RESTARTS   AGE
    mongo-controller-srq9f   1/1       Running   0          8m
    

    arun:k8s achalise$ kubectl get services
    NAME            CLUSTER-IP   EXTERNAL-IP   PORT(S)     AGE
    kubernetes      10.0.0.1     <none>        443/TCP     27d
    mongo-service   10.0.0.111   <none>        27017/TCP   8m
    arun:k8s achalise$ 

    ```
    Check the logs to make sure mongodb started successfully:
    
    `kubectl logs mongo-controller-srq9f` in my case. Run command with the correct `pod` name.
    
* Deploy and start up the SpringBoot service
    ```
    arun:k8s achalise$ kubectl create -f backend.yaml 
    deployment "backend" created
    service "backend" created
    
    arun:k8s achalise$ kubectl get pods
    NAME                       READY     STATUS    RESTARTS   AGE
    backend-2415081020-tmx44   1/1       Running   0          51s
    mongo-controller-srq9f     1/1       Running   0          21m
    
    
    arun:k8s achalise$ kubectl get services
    NAME            CLUSTER-IP   EXTERNAL-IP   PORT(S)     AGE
    backend         10.0.0.88    <none>        8080/TCP    1m
    kubernetes      10.0.0.1     <none>        443/TCP     27d
    mongo-service   10.0.0.111   <none>        27017/TCP   21m
    arun:k8s achalise$     
    
    ```

* Deploy and start the Front End
  ``` 
  arun:k8s achalise$ kubectl create -f front-end.yaml 
  deployment "frontend" created
  service "frontend" created
  arun:k8s achalise$ 
  
  arun:k8s achalise$ kubectl get pods
  NAME                        READY     STATUS    RESTARTS   AGE
  backend-2415081020-tmx44    1/1       Running   0          8m
  frontend-3251706051-p7770   1/1       Running   0          31s
  mongo-controller-srq9f      1/1       Running   0          29m
  arun:k8s achalise$ 
  
  arun:k8s achalise$ kubectl get services
  NAME            CLUSTER-IP   EXTERNAL-IP   PORT(S)          AGE
  backend         10.0.0.88    <none>        8080/TCP         9m
  frontend        10.0.0.86    <nodes>       8888:32318/TCP   52s
  kubernetes      10.0.0.1     <none>        443/TCP          27d
  mongo-service   10.0.0.111   <none>        27017/TCP        29m
  arun:k8s achalise$ 

  ```  

* Access the application from outside the cluster
  
  ``` 
  arun:k8s achalise$ minikube service frontend
  Opening kubernetes service default/frontend in default browser...
  arun:k8s achalise$ 
  
  ```  
  To just list the url of the service:
  ``` 
  arun:k8s achalise$ minikube service frontend --url
  http://192.168.99.100:32318
  arun:k8s achalise$ 
  
  ```
    
### Conclusion

### Useful Docker and Kubernetes commands 


### Create the front end

First install `angular cli` gloablly with `npm install -g @angular/cli`.
Now generate a new project with angular cli by executing
`ng new front-end`. We have named our application `front-end`.

Create a `Dockerfile` using `nginx` image to deploy our angular app.


To remove all stopped containers
`docker rm $(docker ps -a -q)`
