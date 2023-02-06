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


```

```

helm install longhorn longhorn/longhorn --namespace longhorn-system --create-namespace --version 1.4.0
# or
helm show values longhorn/longhorn > longhorn-values.yaml
helm install longhorn longhorn/longhorn --values longhorn-values.yaml   --namespace longhorn-system --create-namespace --version 1.4.0

```


## Frontend


```
k -n longhorn-system edit svc longhorn-frontend

#or
k -n longhorn-system port-forward svc/longhorn-frontend 8080:80

#or
helm upgrade --install longhorn longhorn/longhorn --values longhorn-values.yaml   -n longhorn-system
```


## Create Volume


Show longhorn storage class
```
k get sc
```



```
# In GUI Create Volume
# Then
k get volumes -A

k get crds | grep longhorn

# Delete the volume again
```



## Test


vi pvc.yaml
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypvc
spec:
  storageClassName: longhorn
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```
k apply -f pvc.yaml




You can use different PVC modes such as ReadWriteOnce or ReadOnlyMany. By default, the storage classes at least support ReadWriteOnce. So, ReadWriteMany is a special feature that uses NFS and is included in Longhorn.


vi pod.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: mypvc
  containers:
    - name: task-pv-container
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/data"
          name: mypd
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


