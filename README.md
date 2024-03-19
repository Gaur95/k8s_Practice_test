# k8s_Practice_test

Practical test -
Our pizza restaurant is working on a new web application designed to run in a Kubernetes cluster. The development team has designed a web service which serves a list of pizza toppings. The team has provided a set of specifications which the pizza topping web service needs in order to run.

We have been asked to create a deployment that meets the app's specifications. Then, we need to expose the application using a NodePort service. This setup should meet the following criteria:

+ All objects should be in the pizza namespace. 
+ The deployment should be named pizza-deployment.
+ The deployment should have 3 replicas.
+ The deployment's pods should have one container using the aakashgaur57/pizza-service image with the tag 1.12.0.
+ Run the container with the command nginx.
+ Run the container with the arguments "-g", "daemon off;".
+ The pods should expose port 80 to the cluster.
+ The pods should be configured to periodically check the /healthz endpoint on port 8081, and restart automatically if the request fails.
+ The pods should not receive traffic from the service until the / endpoint on port 80 responds successfully.
+ The service should be named pizza-service.
+ The service should forward traffic to port 80 on the pods.
+ The service should be exposed externally by listening on port 30080 on each node.
## Deployement.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pizza-deployment
  namespace: pizza
spec:
  replicas: 3
  selector:
    matchLabels:
      app: pizza
  template:
    metadata:
      labels:
        app: pizza
    spec:
      containers:
      - name: pizza-container
        image: aakashgaur57/pizza-service:1.12.0
        command: ["nginx"]
        args: ["-g", "daemon off;"]
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 15
          periodSeconds: 10
        livenessProbe:
          httpGet:
            path: /healthz
            port: 8081
          initialDelaySeconds: 15
          periodSeconds: 10
```
## service.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: pizza-service
  namespace: pizza
spec:
  selector:
    app: pizza
  type: NodePort
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30080
```
