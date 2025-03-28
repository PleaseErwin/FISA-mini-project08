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


## Kubernetes Minikube

### NodePort 서비스
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

<br>

![a11](https://github.com/user-attachments/assets/82187141-fd8b-4172-b077-238f2f589cfc)

<br>
pod들 로그 기록 확인

```
kubectl logs -f app1-deployment-659c6868bc-5g9q2
kubectl logs -f app1-deployment-659c6868bc-bvpwp
```

<br>

![a12](https://github.com/user-attachments/assets/12fe5a6b-82bc-4b0e-9f09-0e09faf30ad1)

<br><br>
![a14](https://github.com/user-attachments/assets/5ebdae64-ae2e-4ecc-b9e6-116169dbfbd5)

<br>

![a16](https://github.com/user-attachments/assets/ba37bf7c-98b4-4c40-b981-17c2ccc98b5b)

로드밸런싱 X

<br><br>

### LoadBalancer 서비스
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
pod들 로그 기록 확인

```
kubectl logs -f app2-deployment-574b8455bf-q6x9p
kubectl logs -f app2-deployment-574b8455bf-t8tms
```

<br>

![a13](https://github.com/user-attachments/assets/98f8070e-3385-4c38-b5ad-e51141640a3f)

<br><br>
![a15](https://github.com/user-attachments/assets/95c117f3-4697-4859-a03f-b9d6e5b2e5da)

<br>

![a17](https://github.com/user-attachments/assets/2a9304a7-c13b-4149-bc5a-571d6fa43cc6)

로드밸런싱 O

