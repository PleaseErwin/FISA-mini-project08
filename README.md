# Java App Loadbalance with Kubernetes Minikube

### Gradle build -> jar 파일 생성
![a1](https://github.com/user-attachments/assets/fb150a35-7078-4738-8fce-ae5606256860)
<br><br>

### Dockerfile로 Docker 이미지 생성

Dockerfile
```
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY app.jar app.jar

# JVM 최적화 옵션 추가
ENTRYPOINT ["java", "-XX:+UnlockExperimentalVMOptions", "-XX:+UseContainerSupport", "-XX:MaxRAMPercentage=75.0", "-jar", "app.jar"]
```
<br>

Docker 이미지 생성<br>
![a2](https://github.com/user-attachments/assets/d7e08ea6-5f41-4c56-9be6-a64faff93f80)

<br>

### Docker hub에 이미지 push

Docker 로그인<br>
![a3](https://github.com/user-attachments/assets/2861a9c9-b0f5-49bb-9e57-b424d108de34)


만들어진 이미지에 version 태그 적용<br>
![a6](https://github.com/user-attachments/assets/5fa851f1-0255-4db8-b0c3-49c105830f37)


이미지 목록<br>
![a5](https://github.com/user-attachments/assets/621f039a-3bd1-4f96-a8aa-b69bd056b3c4)

Docker hub에 이미지 업로드<br>
![a7](https://github.com/user-attachments/assets/c2ec7f4f-402a-4de1-a802-fa71772ece0e)


![a8](https://github.com/user-attachments/assets/6e797cf2-497b-46a7-ae3a-c81df0192bd6)


<br><br>


### Minikube

<details>
<summary> mission1.yaml - NodePort </summary>

```
# mission1.yaml - NodePort

apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: javaapp1
  template:
    metadata:
      labels:
        app: javaapp1
    spec:
      containers:
      - name: javaapp1
        image: pleaseerwin/javaapp:7.7
        ports:
        - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: javaapp1-service
spec:
  selector:
    app: javaapp1
  ports:
    - protocol: TCP
      port: 8088
      targetPort: 8070
      nodePort: 30080
  type: NodePort
```

</details>

yaml 파일을 실행시켜 클러스터 구성<br>
![a9](https://github.com/user-attachments/assets/b955c077-70b3-49fa-aa3d-a77b3ad97bc9)

<br><br>

<details>
<summary> mission2.yaml - LoadBalancer </summary>

```
# mission2.yaml - LoadBalancer

apiVersion: apps/v1
kind: Deployment
metadata:
  name: app2-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: javaapp2
  template:
    metadata:
      labels:
        app: javaapp2
    spec:
      containers:
      - name: javaapp2
        image: pleaseerwin/javaapp:7.7
        ports:
        - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: javaapp2-service
spec:
  type: LoadBalancer
  selector:
    app: javaapp2
  ports:
    - protocol: TCP
      port: 8088
      targetPort: 8070
```

</details>

yaml 파일을 실행시켜 클러스터 구성<br>
![a10](https://github.com/user-attachments/assets/4bef7d9c-199f-482d-8103-e0d407b633fb)


<br>










