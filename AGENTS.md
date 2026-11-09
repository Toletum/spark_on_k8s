# spark_on_k8s

Notas y manifiestos para ejecutar Apache Spark sobre Kubernetes con el operador de Spark y `spark-shell`.

## Visibilidad y rama

- Público. Rama por defecto: `main`.

## Stack

- Kubernetes, Helm (chart `spark-operator` de Kubeflow), `kubectl`.
- Imagen de Spark: `apache/spark:latest` (sin versión fijada).
- Spark 3.5.1 (solo en las etiquetas `version` de `spark-pi.yaml`).

## Estructura

- `README.md`: guía paso a paso (instalar el operador, ServiceAccount, prueba, obtener token y lanzar `spark-shell`).
- `spark-pi.yaml`: `SparkApplication` de ejemplo (`SparkPi`) en el namespace `default`.
- `spark-token.yaml`: `Secret` de tipo `kubernetes.io/service-account-token` asociado a la ServiceAccount `spark`.

## Comandos

Del README (no ejecutados):

- Instalar operador: `helm repo add spark-operator https://kubeflow.github.io/spark-operator`, `helm repo update`, `helm install spark-operator spark-operator/spark-operator --namespace spark-operator --create-namespace`.
- ServiceAccount: `kubectl create serviceaccount spark -n default` y `kubectl create rolebinding spark-role-default --clusterrole=edit --serviceaccount=default:spark -n default`.
- Prueba: `kubectl apply -f spark-pi.yaml` y `kubectl logs spark-pi-example-driver`.
- Token: `kubectl apply -f spark-token.yaml` y luego leer el token con `kubectl get secret ... -o jsonpath=...`. No mostrar ni guardar el valor.
- Lanzar `spark-shell` contra el API server (`--master k8s://https://<API_SERVER>:6443`).

## Configuración

- Variables/placeholders del README: `<API_SERVER>` (URL del API server de Kubernetes) y `K8S_NS` (namespace de destino).
- Token: se lee de la Secret `spark-user-token`; nunca escribirlo en el repo.

## Convenciones para agentes

- Mantener los manifiestos YAML con la versión de la API del operador (`sparkoperator.k8s.io/v1beta2`).
- No añadir tokens, IPs reales ni credenciales a los ficheros ni al README. Usar placeholders.
- Coherencia de versiones: si se cambia la imagen, actualizar también las etiquetas `version`.

## Estado

- No hay tests ni CI; el README es un runbook manual.
- Riesgo de seguridad: el README pasa el token con `--conf spark.kubernetes.authenticate.oauthToken="$TOKEN"` (queda en el historial de la shell y en la lista de procesos) y usa `spark.kubernetes.trust.certificates=true` (no verifica el certificado del API server).
- `spark-pi.yaml`: la imagen es `latest` mientras las etiquetas dicen 3.5.1; hay que fijar una versión.
- `spark-pi.yaml`: `spark.driver.allowMultipleContexts` no parece necesario para SparkPi (no verificado).
- Sin `.gitignore`.
