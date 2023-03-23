## list of tools
Docker desktop https://github.com/rancher-sandbox/rancher-desktop/releases (Set container engine to docker)
```
brew install kind
brew install kubectx
brew install derailed/k9s/k9s
brew install helm
```

## CLONE GITHUB REPO
```
git clone git@github.com:levplotkin/airflow-local-dev.git
```

## CREATE KUBE CLUSTER
```
kind create cluster --name airflow-cluster --config kind-cluster.yaml
```

## CREATE AIRFLOW NAMESPACE
```
kubectl create namespace airflow
```

## CREATE WEBSERVER SECRET (FERNET KEY)
```
kubectl -n airflow create secret generic my-webserver-secret --from-literal="webserver-secret-key=$(python3 -c 'import secrets; print(secrets.token_hex(16))')"
```

## CREATE PERSISTENT VOLUME AND CLAIM FOR DAGS
```
kubectl apply -f dags_volume.yaml
```

## FETCH LATEST HELM CHART VERSION AND INSTALL AIRFLOW
```
helm repo add apache-airflow https://airflow.apache.org
helm repo update
helm search repo airflow
helm install airflow apache-airflow/airflow --namespace airflow --debug -f values.yaml
```

## set port forwarding
```
kubectl port-forward svc/airflow-webserver 8080:8080 -n airflow 
```

## web ui
http://0.0.0.0:8080/login/

Login with admin/admin

## troubleshooting
if admin does not exist
open ssh to airflow-webserver

### get pod
```
kubectl exec -it $(kubectl get pod -n airflow | grep airflow-webserver) -n airflow -- bash

```

### add admin
```
airflow users create \
          --username admin \
          --firstname FIRST_NAME \
          --lastname LAST_NAME \
          --role Admin \
          --email admin@example.org \
          --password admin
```

## test
copy test/dags_volume.yaml to dags/

check the te dag in ui

## clean up
```
kind delete clusters "airflow-cluster"
```

## used materials

https://medium.com/go-city/deploying-apache-airflow-on-kubernetes-for-local-development-8e958675585d
https://medium.com/everything-full-stack/airflow-on-k8s-for-local-development-5c3ad0ab8e7d

https://www.youtube.com/watch?v=lKL7DMIfMyc
https://www.youtube.com/watch?v=AjBADrVQJv0&t=306s

https://github.com/marclamberti/webinar-airflow-chart
https://github.com/gocityengineering/airflow-local-setup
