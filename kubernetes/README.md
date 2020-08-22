### What is Kubernetes?
Kubernetes is an open-source container management platform for deploying and managing containerized workloads. 

#### Pods
These are the smallest unit of application in Kubernetes. Pods represent a single, isolated instance of an application and the resources needed to execute it, each having their own IP address. Pods are made up of one or more containers that work together and share a life cycle on the same node; each pod could be composed of a single container or multiple containers, depending on its complexity. This can be advantageous as all containers within the same pod can communicate without the need for additional setup from the user.

#### Services
Services are an abstraction that sit one level above pods, acting as a director between individual pods and the outside world. As pods are isolated instances, they normally would not have access to one another. Services fix this problem by defining a policy to access, an entry condition of sorts, for a select set of pods decided by a selector. Once this is done, pods in this service’s set can communicate freely until the policy to access is changed.

#### Deployments
Our final component of Kubernetes, Deployments, takes the hassle out of upgrading pods. Normally, when upgrading a system, one would have to shut off all old instances then reboot the system using the upgraded instances, resulting in a period of downtime. Kubernetes’ deployments allow us to sidestep this problem by allowing for “rolling updates” during which pods are taken down one-by-one, upgraded to the new version, and verified as functional before moving onto the next.


#### Minikube
The first of our additional tools, Minikube creates a virtual testing ground for all your containerized programs. When run, Minikube creates a Virtual Machine (VM) on your computer which will simulate the behavior of a physical system without the risk of making unwanted changes to your machine.

This VM is a simple single-node cluster, meaning that it behaves like a group of computers with only one machine hooked up. This simplified cluster form and simulated behavior make Minikube the perfect development and testing environment for unfinished programs or for simply learning container-based programming.

#### kubectl
For our next tool, we have kubectl, a command-line application to manage Kubernetes clusters. In many ways, the kubectl command is its own coding language with exclusive syntax and complexity. While difficult to pick up due to its extensiveness, kubectl offers unmatched control to developers, making it the most popular cluster manipulation tool for Kubernetes out there right now. Combined with Minikube, these two tools are essential for those seeking to dive into Kubernetes.

#### Powerful Features of Kubernetes
* Self-healing capabilities to maintain “desired state” conditions
* Autoscaling so you can automatically change the number of running containers, based on CPU utilization or other application-provided metrics.
* Extendable to additional functionalities with loadable modules
* Management of storage available to applications within the cluster
* Allows the storage of confidential information via secret labeling
* Easy access to logs and background information
* Automated load balancing across nodes
* Optimization of component placement for more efficient hardware optimization
* Node capability management, so that components with specialized node requirements (such as apps requiring GPU hardware) are run on nodes with the required capabilities
* Scalability via replica controllers and placement policies
* Customization of automated rollouts and rollback policies

#### Creating a Kubernetes Cluster
Minikube users, enter:
```
minikube start
```
For Docker Desktop, your cluster has begun automatically.

Now to test that our cluster was made correctly, enter:
```
kubectl get svc
```
The following table will print. Yours may look slightly different, but the two leftmost fields, Kubernetes and clusterIP, will be the same.
```
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   6s
```
This shows us that the default service, Kubernetes, is currently running. As this service is always initialized at cluster startup, we know the cluster is running as expected!

#### Deploying an app
Now we’ll create our first deployment. For this, we’ll be pulling a test image from the Google Container Registry (GCR) called hello-node. Creating this deployment will come with one pod built-in.

To do this, paste the following line:
```
kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```
Now, to check that this deployment is running correctly, enter:
```
kubectl get deployments
```
```
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
hello-node   1/1     1            1           170m
```
This will print a list of the deployments currently running on this cluster. As we’ve only created the one, you’ll only see hello-node listed. Congratulations, you’ve deployed your app!

#### Exploring your app
While we’ve got it, might as well look around! We’ll use the get command to see that all the parts we learned about above are now present and working on our app!

First, let’s look at our nodes, enter:
```
kubectl get nodes
```
```
NAME             STATUS   ROLES    AGE    VERSION
docker-desktop   Ready    master   1d2h   v1.15.5
```
This will list the names, status, and roles of all the nodes in our cluster. Since our cluster is just a single device test environment, there will be only one.

Now we’ll look at our pods, enter:
```
kubectl get pods
```
Again, a table will print with some useful information.
```
NAME                          READY   STATUS    RESTARTS   AGE
hello-node-55b49fb9f8-wwpwr   1/1     Running   0          36s
```
Here, it’s important to note that our pod has only one instance, shown by the 1/1 in the ready column. We can also see that it is currently running from the status column, letting us know that it has not failed.

As a result, the get pods command is useful when troubleshooting a more complex system as it allows you to see which of your many pods is malfunctioning.

If you need more information, you can also use the describe command for specific pods, nodes or even deployments to see a plethora of information available on each resource.

Let’s try it for our deployment, enter:
```
kubectl describe deployment hello-node
```
You’ll not recognize most of this information if you’re just starting out, however for the experienced Kubernetes user, this ready access to information is one of the aspects which makes Kubernetes so appealing. With both get and describe commands, all the information needed to troubleshoot a failure is just a command-line away!

#### Exposing your app publicly
Up until now, our app has been fully isolated in our test environment. With real programs, you’ll almost always need to have your app send web requests to other outside web apps. To do this, we’ll use the expose command which will create a new service instance with the same name as our deployment and will automatically define the port configuration to allow a connection. As part of the command, we define which port the service should listen on. In this case, we’ll use port 8080.

To try this yourself, enter:
```
kubectl expose deployment hello-node --type=LoadBalancer --port=8080
```
To see this service in action, we can once again enter:
```
kubectl get svc
```
```
NAME       TYPE         CLUSTER-IP     EXTERNAL-IP  PORT(S)         AGE
hello-node LoadBalancer 10.108.188.234 localhost    8080:32505/TCP  7s
kubernetes ClusterIP    10.96.0.1      <none>       443/TCP         1h
```

Here we see that where we once had only one service, we now have two, the original kubernetes and new service hello-node. Notice how the latter differs in both the EXTERNAL-IP and the PORT(S) field due to it being public. NodePorts are the published IP addresses for external users to access the services.

Now that our app is exposed, we can access the web application running inside the hello-node pod to print a message.

To do this, enter:
```
curl http:///localhost:8080 
```

#### Scale your app
While our app now works fully and is reachable by external sources, what happens if our pod fails? Or what happens if the number of requests reaching our program suddenly spikes?

To protect against these cases, we need to scale up our app; creating more instances of our hello-node pod to delineate requests to or to step in if a pod fails. The best part is, with Kubernetes this takes but a single command to create and will do the above jobs automatically from then on!

To scale up our app, enter:
```
kubectl scale --replicas=3 deployment/hello-node
```

This will set our program to maintain a state of three running instances of the hello-node pod rather than just one.

To check this, enter:
```
kubectl get deployment hello-node
```
This should print a screen like so:
```
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
hello-node   3/3     3            3           135m
```
Notice the three pods under this deployment listed in the ready column.

Or to see the pods independently you can enter:
```
kubectl get pods
```
```
NAME                          READY   STATUS    RESTARTS   AGE
hello-node-55b49fb9f8-fxjnj   1/1     Running   0          2m34s
hello-node-55b49fb9f8-jlfwq   1/1     Running   0          2m34s
hello-node-55b49fb9f8-zhf9f   1/1     Running   0          136m
```

#### Updating your app
Finally, we know that no app rules forever; eventually, a new version will replace the old. In our case, say a new version of our hello-node pod is created and we want to use it to replace all of our old pods. Well as we discussed previously, Kubernetes has us covered as we can make this upgrade with no downtime using a rolling update.

As there is no new version of hello-node, this would give an error message if entered.

However, if there were an updated version, you would apply it by entering:
```
kubectl set image deployments/hello-node hello-node=myContainers/hello-node:v2
```
To break this down, we first mark that we’d like to set a new image, then specify the type we’re going to adapt, deployments, then which deployment, hello-node. After the first mention of our deployment, we’ve told the command what we’d like to edit, at which point we then provide the new image, myContainers/hello-node:v2.

Kubernetes will then take each of our old pods down one by one and replace them with our upgraded version. This takes a moment and does get longer depending on the number of pods which must be upgraded. To check if the upgrade rollout is successful, we would enter:
```
kubectl rollout status deployments/hello-node
```

With that, we’ve finished our creation and exploration of a Kubernetes basic program. To conclude your masterpiece, now simply enter the two lines:
```
kubectl delete service hello-node
kubectl delete deployment hello-node
```

#### More Reference :

https://www.educative.io/blog/kubernetes-tutorial?affiliate_id=5082902844932096&utm_source=google&utm_medium=cpc&utm_campaign=platform2&utm_content=ad-1-dynamic&gclid=Cj0KCQjw4f35BRDBARIsAPePBHyn4WBwIWcvxSajjJgm85CiOI_zwm-VE_PvjgiuG7tMsXom8Zb0gYYaAspgEALw_wcB


#### Pods vs Deployment
Deployment - Maintains a set of identical pods, ensuring that they have the correct config and that the right number of them exist.
Pods:

Runs a single set of containers
Good for one-off dev purposes
Rarely used directly in production
Deployment:

Runs a set of identical pods
Monitors the state of each pod, updating as necessary
Good for dev
Good for production

#### Install minikube
```
brew install minikube
```

#### Minikube start with docker & clean
```
minikube start --driver=docker --memory=6g --cpus=4
```

the easiest would probably be delete the machine and start again with a fresh node
```
minikube delete
```

#### Port forwarding
```
kubectl port-forward service/nginx-service 7000:80
```

### Volumes
A Kubernetes [volume](https://kubernetes.io/docs/concepts/storage/volumes/), on the other hand, has an explicit lifetime - the same as the Pod that encloses it. Consequently, a volume outlives any Containers that run within the Pod, and data is preserved across Container restarts. Of course, when a Pod ceases to exist, the volume will cease to exist, too. Perhaps more importantly than this, Kubernetes supports many types of volumes, and a Pod can use any number of them simultaneously.


#### Types of Volumes
Kubernetes supports several types of Volumes:
* awsElasticBlockStore
* azureDisk
* emptyDir
* gcePersistentDisk
* local
* persistentVolumeClaim
* [secret](https://kubernetes.io/docs/concepts/configuration/secret/)

#### Persistent Volumes
Managing storage is a distinct problem from managing compute instances. The PersistentVolume subsystem provides an API for users and administrators that abstracts details of how storage is provided from how it is consumed. To do this, we introduce two new API resources: PersistentVolume and PersistentVolumeClaim.

*A PersistentVolume (PV)* is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes. It is a resource in the cluster just like a node is a cluster resource. PVs are volume plugins like Volumes, but have a lifecycle independent of any individual Pod that uses the PV. This API object captures the details of the implementation of the storage, be that NFS, iSCSI, or a cloud-provider-specific storage system.

*A PersistentVolumeClaim (PVC)* is a request for storage by a user. It is similar to a Pod. Pods consume node resources and PVCs consume PV resources. Pods can request specific levels of resources (CPU and Memory). Claims can request specific size and access modes (e.g., they can be mounted ReadWriteOnce, ReadOnlyMany or ReadWriteMany, see AccessModes).


### Deployment Example
Everyone running applications on Kubernetes cluster uses a deployment.

It’s what you use to scale, roll out, and roll back versions of your applications.

With a deployment, you tell Kubernetes how many copies of a Pod you want running. The deployment takes care of everything else.

#### What is a Deployment?
A deployment is an object in Kubernetes that lets you manage a set of identical pods.

Without a deployment, you’d need to create, update, and delete a bunch of pods manually.

With a deployment, you declare a single object in a YAML file. This object is responsible for creating the pods, making sure they stay up to date, and ensuring there are enough of them running

You can also easily autoscale your applications using a Kubernetes deployment.

```yaml
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: nginx-deployment
spec:
  # A deployment's specification really only 
  # has a few useful options
  
  # 1. How many copies of each pod do we want?
  replicas: 3

  # 2. How do want to update the pods?
  strategy: Recreate

  # 3. Which pods are managed by this deployment?
  selector:
    # This must match the labels we set on the pod!
    matchLabels:
      deploy: example
  
  # This template field is a regular pod configuration 
  # nested inside the deployment spec
  template:
    metadata:
      # Set labels on the pod.
      # This is used in the deployment selector.
      labels:
        deploy: example
    spec:
      containers:
        - name: nginx
          image: nginx:1.7.9
```
First, the replicas key sets the number of instances of the pod that the deployment should keep alive.

Next, we use a label selector to tell the deployment which pods are part of the deployment. This essentially says "all the pods matching these labels are grouped in our deployment."

After that, we have the template object.

This is interesting. It’s essentially a pod template jammed inside our deployment spec. When the deployment creates pods, it will create them using this template!

So everything under the template key is a regular pod specification.

In this case, the deployment will create pods that run nginx-hostname and with the configured labels.

#### Deployment vs service
A deployment is used to keep a set of pods running by creating pods from a template.

A service is used to allow network access to a set of pods.

Both services and deployments choose which pods they operate on using labels and label selectors. This is where the overlap is.

You can use deployments independent of services. You can use services independent of deployments. They just go together really nicely!

This lets you do canary deployments. You add a new deployment version of a pod and run it alongside your existing deployment, but have both deployments handle requests to the service. Once deployed, you can verify the new deployment on a small proportion of the service's traffic, and gradually scale up the new deployment while decreasing the old one.

#### Reference
https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-deployment-tutorial-example-yaml.html