# Devops-Projects-Deploying-Angular-App-on-Kubernetes


![image](https://user-images.githubusercontent.com/96833570/215834567-ec04edd8-9451-4e03-a1f4-849bd419c2ab.png)


![image](https://user-images.githubusercontent.com/96833570/215825767-c28df638-904b-4e71-a2e9-929d09f3254e.png)


## Build the app

`ng build --configuration production --aot`

![image](https://user-images.githubusercontent.com/96833570/215827853-f319d902-1465-421c-a167-e08ca8e62fff.png)


## Start minikube and sync local repos

```
minikube start
eval $(minikube docker-env)
```

## Create the Dockerfile

```
FROM nginx:alpine
COPY /dist/ng-k8s-example /usr/share/nginx/html
```

```
docker build -t ng-k8s-example:1.0 .
```


![image](https://user-images.githubusercontent.com/96833570/215829658-fc4ea5d3-ae0c-4408-9f51-06d6b0df055e.png)

![image](https://user-images.githubusercontent.com/96833570/215829722-fc57628f-00ee-4d5c-96c7-15ae4a61f749.png)


## Create and apply deployment and service files

ng-deployment.yaml file

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ng-deployment
spec:
  selector:
    matchLabels:
      app: ng
  replicas: 4
  template:
    metadata:
      labels:
        app: ng
    spec:
      containers:
      - name: ng
        image: ng-k8s-example:1.0
        resources:
          limits:
            memory: "128Mi"
            cpu: "500m"
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: ng-svc
spec:
  selector:
    app: ng
  ports:
  - port: 80
    targetPort: 80
    nodePort: 32750
  type: NodePort

```

```
kubectl apply -f ng-deployment.yaml
```


![image](https://user-images.githubusercontent.com/96833570/215831331-645dba13-8713-4446-9242-3f23973e9f92.png)


## Accessing the angular app

`minikube service ng-svc --url`


![image](https://user-images.githubusercontent.com/96833570/215831842-b4bc1205-ed6e-454d-bfbb-f88b7a95fc91.png)

![image](https://user-images.githubusercontent.com/96833570/215832471-de88c061-56c3-4ba9-8d78-bab41d97017b.png)


## Validation 
![image](https://user-images.githubusercontent.com/96833570/215832558-62d12880-9b0a-4332-a3c6-47a525e40aa5.png)


## Health check

`minikube dashboard`

![image](https://user-images.githubusercontent.com/96833570/215833294-e11c309a-19cf-4b7f-a7ec-f93fea26da6b.png)
