# Spark on K8S

# Operator
```bash
helm repo add spark-operator https://kubeflow.github.io/spark-operator
helm repo update

helm install spark-operator spark-operator/spark-operator \
  --namespace spark-operator \
  --create-namespace
```

```bash
kubectl get pods -n spark-operator
```

# ServiceAccount
```bash
kubectl create serviceaccount spark -n default
kubectl create rolebinding spark-role-default --clusterrole=edit --serviceaccount=default:spark -n default
```

# Test
```bash
kubectl apply -f spark-pi.yaml
kubectl logs spark-pi-example-driver
```

# Out the k8s

```bash
kubectl apply -f spark-token.yaml
TOKEN=$(kubectl get secret spark-user-token -o jsonpath='{.data.token}' | base64 --decode)
```


```bash
./bin/spark-shell  \
  --master k8s://https://192.168.122.146:6443 \
  --deploy-mode client \
  --conf spark.kubernetes.container.image=apache/spark:latest \
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
  --conf spark.kubernetes.executor.deleteOnTermination=true \
  --conf spark.kubernetes.trust.certificates=true \
  --conf spark.kubernetes.authenticate.oauthToken="$TOKEN"
```
