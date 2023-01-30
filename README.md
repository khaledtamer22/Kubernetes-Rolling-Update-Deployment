# What's the Rolling Update Deployment strategy in kubernetes?

Rolling updates incrementally replace your resource's Pods with new ones, which are then scheduled on nodes with available resources. Rolling updates are designed to update your workloads without downtime. The following objects represent Kubernetes workloads.

# In this example we will implement the Rolling Update deployment on a node js application

# Let's start

# 1- Create `deployment.yml` file and make sure to define your `strategy type` to be `RollingUpdate` 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: RollingUpdate-deployment
  labels:
    app: helloserver
spec:
  replicas: 8
  selector:
    matchLabels:
      app: helloserver
  strategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: helloserver
    spec:
      containers:
      - name: helloserver
        image: khaledeltaweel/k8s-web-server
        # image: khaledeltaweel/k8s-web-server:1.0
        ports:
        - containerPort: 3000
```
Create the deployment using the command 

`kubectl apply -f deployment.yml`

# 2- Create  `service.yml` file

```
apiVersion: v1
kind: Service
metadata:
  name: RollingUpdate
spec:
  selector:
    app: helloserver
  ports:
    - port: 80
      targetPort: 3000
  type: NodePort
```

Create the service using the command 

`kubectl apply -f service.yml`

# Now we have created a normal application and you can run the application using the command

`minikube service RollingUpdate`

The web browser will open and the application  will be running on ip looks like this ip `172.26.177.67:30374`

# 3- Start the rolling update deployment strategy process

We used the image `khaledeltaweel/k8s-web-server` in the `deployment.yml` file, so we will change the image version in the `Dockerfile` to be `khaledeltaweel/k8s-web-server:1.0` and then run the deployment again using the command

`kubectl apply -f deployment.yml`

# What will happen when we run this above command?

- Pods will be replaced one by one without any cluster downtime
- A minor performance reduction will happed during the update process (One active pod short of the desired number of pods)

# Optional

If you would like to see the update process you can run the command `kubectl rollout status deployment/RollingUpdate-deployment` after running the new deployment immediately