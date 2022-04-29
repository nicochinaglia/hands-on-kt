# hands-on-kt

## Pré-requisitos

```bash
docker  20.10.14
kind    0.12.0
helm    3.8.0
kubectl v1.21.12
```

```bash
docker run hello-world
```

```bash
kind create cluster
```

```bash
kubectl config get-contexts
kubectl get namespaces
kubectl get configmaps
```

## Helm Charts

### First Template

```bash
# Create a Folder for Helm Charts
mkdir charts/

# Create a "mychart" Helm Chart
helm create charts/mychart

# remove files
rm -rf charts/mychart/charts
rm -rf charts/mychart/templates/deployment.yaml
rm -rf charts/mychart/templates/hpa.yaml
rm -rf charts/mychart/templates/ingress.yaml
rm -rf charts/mychart/templates/NOTES.txt
rm -rf charts/mychart/templates/service.yaml
rm -rf charts/mychart/templates/serviceaccount.yaml
rm -rf charts/mychart/templates/tests
rm -rf charts/mychart/values.yaml

# Show mychart structure
find charts/mychart -type f

# Show Template
helm template mychart-example charts/mychart

# Generate a new values.yaml file
cat <<EOF > charts/mychart/values.yaml
config:
  replicas: 3
  timeout: 360
EOF

# Generate a new configmap.yaml file
cat <<EOF > charts/mychart/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config-map
data:
  replicas: {{ .Values.config.replicas | quote }}
  timeout: {{ .Values.config.timeout | quote }}
EOF

# Generate a new NOTES.txt file
cat <<EOF > charts/mychart/templates/NOTES.txt
The config will have {{ .Values.config.replicas | quote }} replicas and a timeout of {{ .Values.config.timeout | quote }} seconds.
EOF

# Show Template
helm template mychart-example charts/mychart
```

### Install

```bash
# Install Helm Chart
helm install mychart-example charts/mychart

# List Helm Releases
helm list

# Check the ConfigMap
kubectl get configmap my-config-map -o yaml

# Unistall Helm Release
helm uninstall mychart-example

# List Helm Releases
helm list
```

### Upgrade a Helm Release

```bash
# Check for ConfigMaps
kubectl get configmaps

# Install
helm install mychart-example charts/mychart
helm list # take a look on the last column
kubectl get configmaps

# Change Chart Version
grep "^appVersion" charts/mychart/Chart.yaml

sed -i 's/appVersion:.*/appVersion: 1.0.0/g' charts/mychart/Chart.yaml

grep "^appVersion" charts/mychart/Chart.yaml

# Upgrade
helm upgrade --install mychart-example charts/mychart
helm list #  take a look on the third and last column
```

### Parameters

```bash
# Passing parameters
helm upgrade \
  --install mychart-example charts/mychart \
  --set "config.replicas=10"

kubectl get configmap my-config-map -o yaml

mkdir -p ${HOME}/trash

cat <<EOF > ${HOME}/trash/values-extra.yaml
config:
  replicas: 15
EOF

cat ${HOME}/trash/values-extra.yaml

helm upgrade \
  --install mychart-example charts/mychart \
  --values ${HOME}/trash/values-extra.yaml

kubectl get configmap my-config-map -o yaml

# Unistall Helm Release
helm uninstall mychart-example
```

## Packaging

```bash
export GITHUB_USER="smsilva"
export GITHUB_REPOSITORY="hands-on-kt"
```

### Package Helm Chart

```bash
mkdir -p releases/

helm package \
  charts/mychart \
  --destination releases/
```

### Generate Helm Index File

```bash
helm repo index \
  releases/ \
  --url https://raw.githubusercontent.com/${GITHUB_USER?}/${GITHUB_REPOSITORY?}/main/releases
```

## Usage Example

```bash
helm repo add \
  mylocalrepo https://raw.githubusercontent.com/${GITHUB_USER?}/${GITHUB_REPOSITORY?}/main/releases

helm repo update mylocalrepo
helm search repo mylocalrepo

helm upgrade \
  --install \
  --namespace mychart \
  --create-namespace \
  mychart mylocalrepo/mychart

kubectl \
  --namespace mychart \
  get configmap
```
