# Getting Started

#### 1. docker build and create image
```
$ docker build -t learn/spring-boot-test:latest .
```

#### 2. apply and port-forwarding
```
$ kubectl apply -f k8s-deployment.yaml
$ kubectl apply -f k8s-service.yaml

$ kubectl port-forward service/spring-boot-test-loadbalancer-service 8080:8080
```

#### Dockerfile
```
# For Java 11
FROM adoptopenjdk/openjdk11:alpine-jre

# Refer to Maven build -> finalName
ARG JAR_FILE=target/spring-boot-test.jar

# cd /opt/app
WORKDIR /opt/app

EXPOSE 8081

# cp target/spring-boot-web.jar /opt/app/app.jar
COPY ${JAR_FILE} app.jar

# java -jar /opt/app/app.jar
ENTRYPOINT ["java","-jar","app.jar"]
```

#### k8s-deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: spring-boot-test-deployment # Name of deployment
  labels:
    app: spring-boot-test
spec:
  replicas: 2      # Number of pods
  selector:        # Pod selector for deployment
    matchLabels:
      app:  spring-boot-test
  template:
    metadata:
      labels:
        app:  spring-boot-test
    spec:
      containers:
        - name: spring-boot-test-container
          imagePullPolicy: IfNotPresent
          image: learn/spring-boot-test:latest   #image name
          ports:
            - containerPort: 8080
```
#### k8s-service.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: spring-boot-test-loadbalancer-service  # Name of service
spec:
  type: LoadBalancer
  #externalIPs:
  #  - 192.168.0.10
  selector:
    app: spring-boot-test
  ports:
    - protocol: TCP
      port: 8080                          # Pod port
      targetPort: 8080                    # Container port
```

![Screenshot](https://github.com/OzgurAkinci/spring-boot-kubernetes-and-docker/blob/main/git_resources/pods.png)?raw=true)

#### other
```
$ kubectl get all
$ kubectl get services
$ kubectl get pods
$ kubectl get deployments
$ kubectl scale deployment spring-boot-example --replicas=3
$ kubectl delete --all pods
$ kubectl delete --all deployments
$ kubectl delete --all namespaces
$ kubectl delete daemonsets,replicasets,services,deployments,pods,rc,ingress --all --all-namespaces

```
