# Practical-secret-and-configmap
## Create a ConfigMap
```bash
vi configmap.yml
```
```bash
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_MODE: "production"
  APP_COLOR: "pista-green"
```
## Apply configmap file
```bash
kubectl apply -f configmap.yaml
```
## create pod
```bash
vi pod-cm.yml
```
```bash
apiVersion: v1
kind: Pod
metadata:
  name: cm-demo
spec:
  containers:
    - name: demo
      image: busybox
      command: ["sh", "-c", "echo Mode:$APP_MODE Color:$APP_COLOR && sleep 3600"]
      env:
        - name: APP_MODE
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: APP_MODE
        - name: APP_COLOR
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: APP_COLOR
```
## Apply pod.yml file
```bash
kubectl apply -f pod-cm.yaml
```
-------

# Secret practical 

## Create a Secret
```bash
secret.yaml
```
## Encrypt  password
```bash
echo -n "myjls123" | base64
```
```bash
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
type: Opaque
data:
  mysql-root-password: bXlqbHMxMjM=   # echo -n "myjls123" | base64
```
## apply secret file
```bash
kubectl apply -f secret.yaml
```
## create deployment 
```bash
vi mysql-deploy.yml
```
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:8
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: mysql-root-password
          ports:
            - containerPort: 3306
```
## Apply deploy
```bash
kubectl apply -f mysql-deploy.yaml
```
## change the password
```bash
echo -n "NewStrongPass" | base64
```
## Edit secret yml file
```bash
kubectl edit secret <yml file name>
```
```bash
data:
  mysql-root-password:  # edit the myql-root-passsword <echo -n "NewStrongPass" | base64> new encrypted password
```
## Apply secret.yml 
```bash
kubectl apply -f mysql-secret.yaml
```
## Rollout the deployment
```bash
kubectl rollout restart deployment mysql # mandatory to rollout
```
