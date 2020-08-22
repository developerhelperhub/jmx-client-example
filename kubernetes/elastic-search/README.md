
#### Start Minikube 
Kubernetes start on the docker driver
```
minikube start --driver=docker --memory=6g --cpus=4 
```

Note: clean the minikube cluster
```
minikube delete
```

#### Apply the deployment YAML file
```
kubectl apply -f deployment.yml
```

#### List the all objects which are running 
```
kubectl get all
```

The below details will be shown
```
NAME                                    READY   STATUS    RESTARTS   AGE
pod/my-elasticsearch-7f4656b6c8-ntb5z   1/1     Running   0          91s

NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
service/kubernetes         ClusterIP   10.96.0.1       <none>        443/TCP             2m54s
service/my-elasticsearch   ClusterIP   10.99.195.184   <none>        9200/TCP,9300/TCP   91s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-elasticsearch   1/1     1            1           91s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/my-elasticsearch-7f4656b6c8   1         1         1       91s
```

#### Display the Cluster IP
```
kubectl get svc
```

```
NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
kubernetes         ClusterIP   10.96.0.1       <none>        443/TCP             4m31s
my-elasticsearch   ClusterIP   10.99.195.184   <none>        9200/TCP,9300/TCP   3m8s
```

#### Port forwarding to 9200
```
kubectl port-forward service/my-elasticsearch 9200:9200
```
We can access the elastic search 9200 port through browser
```
http://localhost:9200
```

we can use kubectl proxy to test it – this will open a local TCP port to the API-server and then we can use it to access our elastic search.
```
kubectl proxy --port=8080
```

We can access the elastic search 8080 port through browser
```
http://localhost:8080/api/v1/namespaces/default/services/my-elasticsearch
```

#### References:
https://rtfm.co.ua/en/kubernetes-clusterip-vs-nodeport-vs-loadbalancer-services-and-ingress-an-overview-with-examples/

