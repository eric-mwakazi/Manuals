# INSTALLING HELM, GRAFANA, LOKI and ALLOY in K8S
## HELM INSTALL
```bash
 ## Install Helm 3
 curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
 get-helm-3 && rm get-helm-3
 # Verify Helm installation
 helm version
```
 ## PVC INSTALL
```bash 
kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml
```
## 🚀 Step 1: Add the Grafana Helm repository
```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```
## 📦 Step 2: Deploy Loki
```bash
helm upgrade --install loki grafana/loki-stack \
  --namespace observability --create-namespace \
  --set promtail.enabled=false \
  --set loki.persistence.enabled=true \
  --set loki.persistence.size=3Gi \
  --set loki.persistence.storageClassName=local-path \
  --set grafana.enabled=false
```
## ⚙️ Step 3: Deploy Alloy (Grafana Agent successor)
```bash
helm upgrade --install alloy grafana/alloy \
  --namespace observability \
  --set controller.type=daemonset \
  --set logs.enabled=true \
  --set logs.receivers.k8slogs=true \
  --set logs.exporters.loki.endpoint=http://loki:3100/loki/api/v1/push
```
## 📊 Step 4: Deploy Grafana
```bash
helm upgrade --install grafana grafana/grafana \
  --namespace observability --create-namespace \
  --set adminPassword='grafana' \
  --set service.type=NodePort \
  --set persistence.enabled=true \
  --set persistence.size=5Gi \
  --set persistence.storageClassName=local-path \
  --set securityContext.runAsUser=472 \
  --set securityContext.fsGroup=472
```
### 🔍 Optional: Verify after deploy
```bash
kubectl get pods -n observability
kubectl get svc -n observability
```
## 📌 Step 5: Add Loki as a data source in Grafana
You can automate this or add it via the UI:

Go to [http://your-node-ip:grafana-nodeport](http://your-node-ip:grafana-nodeport) (replace `grafana-nodeport` with the actual NodePort assigned to Grafana).

Settings → Data Sources → Add Loki

URL: http://loki:3100