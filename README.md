## 安裝 Helm 前置套件
* wget https://storage.googleapis.com/kubernetes-helm/helm-v2.12.2-linux-amd64.tar.gz
* tar -zxvf helm-v2.12.2-linux-amd64.tar.gz
* cd mv linux-amd64/helm /usr/local/bin/helm
* kubectl -n kube-system create sa tiller
* kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
* helm init --service-account tiller
* kubectl create namespace kubeapps

## 使用 Helm 
* 在當前目錄下執行Helm 
    + helm install .

* 列出Helm 
    + helm ls

* 在當前目錄下升級Helm 
    + helm upgrade [release name] . --description [string]

* 查看Helm歷史紀錄 
    + helm history [release name]

* 回滾Helm歷史版號
    + helm rollback [release name] [number]