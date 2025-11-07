# Spark on K(S

# Operator
helm repo add spark-operator https://kubeflow.github.io/spark-operator
helm repo update

helm install spark-operator spark-operator/spark-operator \
  --namespace spark-operator \
  --create-namespace


kubectl get pods -n spark-operator

# ServiceAccount
kubectl create serviceaccount spark -n default
kubectl create rolebinding spark-role-default --clusterrole=edit --serviceaccount=default:spark -n default

# Test
kubectl apply -f spark-pi.yaml

# Out the k8s
kubectl apply -f spark-token.yaml
