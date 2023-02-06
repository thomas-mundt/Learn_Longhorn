# Learn Longhorn

## Installing Longhorn with ReadWriteMany mode

```
sudo apt update
sudo apt upgrade


sudo apt install -y open-isci
sudo apt install -y nfs-common

sudo systemctl status iscsid
```


## Helm

```
helm repo add longhorn https://charts.longhorn.io
helm repo update

helm install longhorn longhorn/longhorn --namespace longhorn-system --create-namespace --version 1.4.0
```





```
kubectl apply -f https://raw.githubusercontent.com/longhorn/longhorn/v1.3.1/deploy/longhorn.yaml
```

vi pvc.yaml
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: longhorn-volv-pvc
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: longhorn
  resources:
    requests:
      storage: 2Gi
```
kubectl create -f pvc.yaml

You can use different PVC modes such as ReadWriteOnce or ReadOnlyMany. By default, the storage classes at least support ReadWriteOnce. So, ReadWriteMany is a special feature that uses NFS and is included in Longhorn.


vi pod.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: volume-test
  namespace: default
spec:
  containers:
  - name: volume-test
    image: nginx:stable-alpine
    imagePullPolicy: IfNotPresent
    volumeMounts:
    - name: volv
      mountPath: /data
    ports:
    - containerPort: 80
  volumes:
  - name: volv
    persistentVolumeClaim:
      claimName: longhorn-volv-pvc
```
kubectl create -f pod.yaml


## Using Longhorn UI

```
kubectl port-forward svc/longhorn-frontend -n longhorn-system 8080:80
```
http://localhost:8080

https://longhorn.io/docs/1.3.1/deploy/accessing-the-ui.



## Troubleshooting Longhorn

Check whether the Longhorn pods are running successfully
```
kubectl get pods –n longhonr-system
```

Check whether the PV was created
```
kubectl get pv
```

Check whether the PVC was created
```
kubectl get pvc
```


