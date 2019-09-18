## 安裝 harbor v1.0.0套件
* git clone -b 1.0.0 https://github.com/goharbor/harbor-helm.git


## 撿查 harbor 套件
* cd harbor-helm/
* git checkout 1.0.0

## 修改 values.yaml 
* type: nodePort
* tls: enabled: false
* externalURL: http://192.168.152.7
* persistence: storageClass: "-"
* persistence: rootdirectory: /data
* auth: realm: "{{ .Values.externalURL }}:30002/service/token?account=admin&client_id=docker&offline_token=true&service=harbor-registry"

## nfs server安裝
* yum install nfs-utils
* systemctl enable rpcbind
* systemctl start rpcbind
* systemctl start nfs
* mkdir /data
* chmod 755 /data
* vi /etc/exports
* /data/     *(rw,sync,no_root_squash,no_all_squash)
* systemctl restart nfs
* showmount -e localhost

## nfs client安裝 (master，node 都要安裝)
* yum install nfs-utils
* systemctl enable rpcbind
* systemctl start rpcbind
* showmount -e 192.168.152.10
* mkdir /data
* mount -t nfs 192.168.152.10:/data /data
* mount

## 創建 PV
```
for i in {1..5}; do
cat <<EOF | kubectl create -f -
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv00${i}
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  nfs:
    path: /data/nfs${i}
    server: 192.168.152.10
EOF
done
```
## 安裝 harbor
* helm install --name harbor-v1 . --wait --timeout 1500 --namespace harbor

## harbor push image
* docker tag junlichung/nginxfinal:vvv 192.168.152.7:30002/chart_repo/junlichung/nginxfinal:vvv
* docker push 192.168.152.7:30002/chart_repo/junlichung/nginxfinal:vvv