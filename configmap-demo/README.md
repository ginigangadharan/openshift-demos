# ConfigMap Demo

```bash
## create an app
$ oc new-app --name mynodejs \
  nodejs:10~https://github.com/ginigangadharan/openshift-demos \
  --context-dir configmap-demo

## Expose the App
$ oc expose svc mynodejs
$ oc get route

## create a configmap
$ oc create configmap nodejs-message \
  --from-literal APP_MSG="This is a nodejs App"


## create a secret
$ oc create secret generic nodejs-secret \
  --from-file nodejs-app/sec 

$ oc create secret generic mysql \
  --from-literal user=myuser --from-literal password=redhat123 \
  --from-literal database=test_secrets --from-literal hostname=mysql

## new app
$ oc new-app --name mysql \
 --docker-image registry.access.redhat.com/rhscl/mysql-57-rhel7:5.7-47
## set it
$ oc set env bc/mynodejs --from configmap/nodejs-message

$ oc set env deployment/mysql --prefix MYSQL_ \
 --from secret/mysql

$ oc set volume bc/mynodejs \
> --add -t secret -m /opt/app-root/secure \
> --name myappsec-vol --secret-name nodejs-secret
error: the  mynodejs is not a pod or does not hav
```

```bash
$ oc describe configmap/app-details
Name:         app-details
Namespace:    mywebapp
Labels:       <none>  
Annotations:  <none>  

Data
====
APP_MESSAGE:
----
This is a Test App    
APP_VERSION:
----
1.0
Events:  <none>
```

```bash
$ oc describe secret/app-secret
Name:         app-secret
Namespace:    mywebapp
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
USERNAME:  5 bytes
PASSWORD:  8 bytes
```

```bash
$ oc describe dc/mywebapp
Name:           mywebapp
Namespace:      mywebapp
Created:        17 minutes ago
Labels:         <none>
Annotations:    <none>
Latest Version: 3
Selector:       app=hello-openshift
Replicas:       1
Triggers:       Config
Strategy:       Rolling
Template:
Pod Template:
  Labels:       app=hello-openshift
  Containers:
   hello-openshift:
    Image:      openshift/hello-openshift
    Port:       8080/TCP
    Host Port:  0/TCP
    Environment:
      APP_MESSAGE:      <set to the key 'APP_MESSAGE' of config map 'app-details'>      Optional: false
      APP_VERSION:      <set to the key 'APP_VERSION' of config map 'app-details'>      Optional: false
    Mounts:
      /opt/secure from myappsec-vol (rw)
  Volumes:
   myappsec-vol:
    Type:       Secret (a volume populated by a Secret)
    SecretName: app-secret
    Optional:   false

Deployment #3 (latest):
        Created:        10 seconds ago
        Status:         Running
        Replicas:       1 current / 1 desired
Deployment #2:
        Name:           mywebapp-2
        Created:        14 minutes ago
        Status:         Complete
        Replicas:       1 current / 1 desired
        Selector:       app=hello-openshift,deployment=mywebapp-2,deploymentconfig=mywebapp
        Labels:         openshift.io/deployment-config.name=mywebapp
        Pods Status:    1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Deployment #1:
        Created:        17 minutes ago
        Status:         Complete
        Replicas:       0 current / 0 desired

Events:
  Type          Reason                  Age     From                            Message
  ----          ------                  ----    ----                            -------
  Normal        DeploymentCreated       17m     deploymentconfig-controller     Created new replication controller "mywebapp-1" for version 1
  Normal        DeploymentCreated       14m     deploymentconfig-controller     Created new replication controller "mywebapp-2" for version 2
  Normal        DeploymentCreated       10s     deploymentconfig-controller     Created new replication controller "mywebapp-3" for version 3
```  

