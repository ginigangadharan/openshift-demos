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

