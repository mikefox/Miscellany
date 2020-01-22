# DD Example
This serves as an example project to run to generate metrics, traces, and logs within Datadog.

# Setup / Prerequisites
- [docker](https://docs.docker.com/install/)
- [minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)
- Start Minikube
  - From this directory run:
  ```
  minikube start
  eval $(minikube docker-env)
  ```
  - This will start minikube and also set the docker environment to the one minikube offers instead of the one that you may already have installed in your local environment.

# Run the example app
## Build Docker Image
```
docker build -t sample_flask:latest ./raw_files/flask/
docker build -t sample_postgres:latest ./raw_files/postgres/
```

## Put API Key secret in minikube
Replace `API_KEY` with your actual API Key from your Datadog Account.
```
kubectl create secret generic datadog-api --from-literal=token=API_KEY
```

## Deploy to minikube
- Run:
  ```
  kubectl apply -f postgres_deployment.yaml -f flask_deploy.yaml -f datadog-agent.yaml -f kubernetes
  ```

## Confirm it works & generate traffic
- Grab the `CLUSTER-IP` for flaskapp:
  - `kubectl get services`
  - Example output:
  ```
  ⇒  kubectl get services
  NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
  flaskapp     ClusterIP   10.104.65.215   <none>        5005/TCP   3m11s
  kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP    32m
  postgres     ClusterIP   10.99.143.193   <none>        5432/TCP   3m11s
  ```
- `minikube ssh`
- Hit the flash endpoints:
  ```
  curl http://CLUSTER-IP:5005/
  curl http://CLUSTER-IP:5005/bad
  curl http://CLUSTER-IP:5005/query
  curl http://CLUSTER-IP:5005/log
  ```

### Observe in Datadog
In a few minutes, you should be able to see metrics, traces, hosts, logs, etc:
- [metric summary page](https://app.datadoghq.com/metric/summary)
- [host map](https://app.datadoghq.com/infrastructure/map)
- [Traces](https://app.datadoghq.com/apm/search)
- [Logs](https://app.datadoghq.com/logs)
- [processes](https://app.datadoghq.com/process)
- [Postgres OOTB Dashboard](https://app.datadoghq.com/screen/integration/235/)
- etc...