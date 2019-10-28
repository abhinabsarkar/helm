# Hello world Helm chart

In this article, we are going to deploy an application on kubernetes docker desktop cluster using Helm. 
Refer the following for:
* [Helm Architecture](/architecture/helm-readme.md)
* [Install instrunctions & software versions used in this article](/install/readme.md)
* [Dockerized Python application used in this article](/src/hello-py/readme.md)

## Helm Chart Structure
With the create command, Helm provides a predetermined structure to ensure a standard (similar to Ansible). These files are auto-generated. Helm uses YAML format for configuration files.
```cmd
HELLO-HELM-CHART
│   Chart.yaml
│   values.yaml
├───charts
└───templates
    │   deployment.yaml
    │   ingress.yaml
    │   NOTES.txt
    │   service.yaml
    │   serviceaccount.yaml
    │   _helpers.tpl
    └───tests
            test-connection.yaml
```

Create a helm chart
```cmd
helm create hello-helm-chart
```

Make the following changes in values.yaml for image and service resources. The complete values.yaml can be seen [here](/src/hello-helm-chart/values.yaml). Note that the port 5000 is used because that's the port exposed in the hello-py container. Kubernetes ServiceTypes allow you to specify what kind of Service you want. The default is ClusterIP.

Type values and their behaviors are:

* ClusterIP: Exposes the Service on a cluster-internal IP. Choosing this value makes the Service only reachable from within the cluster. This is the default ServiceType.
* NodePort: Exposes the Service on each Node’s IP at a static port (the NodePort). A ClusterIP Service, to which the NodePort Service routes, is automatically created. You’ll be able to contact the NodePort Service, from outside the cluster, by requesting http://<Node_IP>:<Node_Port>
* LoadBalancer: Exposes the Service externally using a cloud provider’s load balancer. NodePort and ClusterIP Services, to which the external load balancer routes, are automatically created.

  ![Alt text](/images/service-type.jpg)
> You can also use Ingress to expose your Service. Ingress is not a Service type, but it acts as the entry point for your cluster. It lets you consolidate your routing rules into a single resource as it can expose multiple services under the same IP address.

```yaml
replicaCount: 3

image:
  repository: abhinabsarkar/abs-hello-py
  tag: alpine3.7

service:
  type: LoadBalancer
  port: 80
  targetPort: 5000 
```

Make the following change in deployment.yaml since the container port exposed is 5000 in the python application
```yaml
      ports:
        - name: http
          containerPort: 5000
```

> Optional: Check if the chart is well formed or not by running the following command from the hello-helm-chart folder
```cmd
C:\Abhinab\helm\src\hello-helm-chart>helm lint
```
Package the helm chart 
```cmd
C:\Abhinab\helm\src>helm package hello-helm-chart --debug
```

Install the package, which in turn will deploy the services to the cluster
```cmd
C::\Abhinab\helm\src>helm install hello-helm-chart-0.1.0.tgz --name hello-py
NAME:   hello-py
LAST DEPLOYED: Mon Oct 28 17:38:37 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Deployment
NAME                       READY  UP-TO-DATE  AVAILABLE  AGE
hello-py-hello-helm-chart  0/3    3           0          0s

==> v1/Pod(related)
NAME                                        READY  STATUS             RESTARTS  AGE
hello-py-hello-helm-chart-658fff89bb-5srd2  0/1    ContainerCreating  0         0s
hello-py-hello-helm-chart-658fff89bb-9dk2w  0/1    ContainerCreating  0         0s
hello-py-hello-helm-chart-658fff89bb-stx89  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                       TYPE          CLUSTER-IP     EXTERNAL-IP  PORT(S)       AGE
hello-py-hello-helm-chart  LoadBalancer  10.98.228.181  localhost    80:30371/TCP  0s

==> v1/ServiceAccount
NAME                       SECRETS  AGE
hello-py-hello-helm-chart  1        0s

NOTES:
1. Get the application URL by running these commands:
     NOTE: It may take a few minutes for the LoadBalancer IP to be available.
           You can watch the status of by running 'kubectl get --namespace default svc -w hello-py-hello-helm-chart'
  export SERVICE_IP=$(kubectl get svc --namespace default hello-py-hello-helm-chart --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
  echo http://$SERVICE_IP:80
```

The install command creates a deployment in the kubernetes cluster, the hello-py application is scaled to three pods and a service gets created to expose the deployment on the LoadBalancer IP on port 80. Also, a service account is created and the NOTES.txt file tells us how to access the pod.

The pods deployed into the kubernetes cluster can be viewed by running the below commmand.
```cmd
C:\Abhinab\helm\src>kubectl get pod
NAME                                         READY   STATUS    RESTARTS   AGE
hello-py-hello-helm-chart-658fff89bb-5srd2   1/1     Running   0          8m22s
hello-py-hello-helm-chart-658fff89bb-9dk2w   1/1     Running   0          8m22s
hello-py-hello-helm-chart-658fff89bb-stx89   1/1     Running   0          8m22s
``` 

Browse the application on http://localhost:80 and it should show the below page.

![Alt text](/images/hello-py.jpg)

>If the service type used is ClusterIP, the app 'hello-py' will only be exposed internally within the cluster. To browse the application then, either use the kubectl proxy or use the port-forward command of the kubectl cli to forward one of the local ports (in this case, say port 9000) to the pod which is listening on port 5000.
```cmd
C:\Abhinab\helm\src>kubectl port-forward hello-py-hello-helm-chart-86db4f9bdc-mxr97 9000:5000
Forwarding from 127.0.0.1:9000 -> 5000
Forwarding from [::1]:9000 -> 5000
```

To delete the application deployed, run either of the below commands based on requirement. When you do helm delete $RELEASE_NAME it deletes all resources but keeps the record with $RELEASE_NAME in case you want to rollback. You can see removed releases via helm ls -a . Whereas helm delete --purge $RELEASE_NAME removes records and make that name free to be reused for another installation.
```cmd
# Deletes all resources but keeps the record with $RELEASE_NAME in case you want to rollback
helm delete hello-py
# Removes records and make that name free to be reused for another installation
helm delete --purge hello-py
```