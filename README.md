# Airflow-ETL
Deploy Apache Airflow workflow with Helm Chart.
## Requirements

- Kubectl
- Helm
- Kind
- Docker

### Kind Installation
```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.15.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```
## Setup
```bash
# Create a kubernetes cluster of 1 control plane and 3 worker nodes
kind create cluster --name airflow-cluster --config kind-cluster.yaml
```

```bash
# Check the cluster info
kubectl cluster-info
```

```bash
# Check the nodes with kubectl
kubectl get nodes -o wide
```

```bash
# Add the official repository of the Airflow Helm Chart
helm repo add apache-airflow https://airflow.apache.org
```

```bash
# Update the repo
helm repo update
```

```bash
# Create namespace airflow
kubectl create namespace airflow
```

```bash
# Check the namespace 
kubectl get namespaces
```

```bash
# Install the Airflow Helm Chart
helm install airflow apache-airflow/airflow --namespace airflow --debug
```

```bash
# Get pods
kubectl get pods -n airflow
```

```bash
# Check release
helm ls -n airflow
```

```bash
# If for some reasons the release is stuck in pending-install or timed out
# Resinstall the chart
helm delete airflow --namespace airflow
helm install airflow apache-airflow/airflow --namespace airflow --debug —timeout 10m0s
```

```bash
# Port forward 8080:8080
kubectl port-forward svc/airflow-webserver 8080:8080 -n airflow --context kind-airflow-cluster
```
### Debug
```bash
# 1. Delete the Helm release
helm delete airflow --namespace airflow

# 2. Check your PODs
kubectl get pods -n airflow

# 3. If airflow-migrations is in ContainerCreating state delete it
kubectl get jobs -n airflow
kubectl delete jobs <pods_name_of_airflow_migrations> -n airflow

# 4. Install the chart again
helm install airflow apache-airflow/airflow --namespace airflow --debug --timeout 10m0s
```
### Get Values
Get the configuration file of the Helm chart make changes to Airflow instance in deployment.

````bash
# Get the Chart values
helm show values apache-airflow/airflow > values.yaml
```