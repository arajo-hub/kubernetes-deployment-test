# *Kubernetes Deployment Test*

 [이곳](https://donghoon-khan.github.io/2020/08/10/deploy-spring-boot-application-on-kubernetes/) 참고.



### 🍊 실습

1. 테스트용 프로젝트(본 레포지토리의 프로젝트) 생성

 - 본 레포지토리의 프로젝트는 **JAVA8 MAVEN JAR의 스트링부트앱.**

2. 빌드

  - mvnw는 프로젝트 최상단경로에 위치

````
./mvnw install
````
 - target 폴더에 jar파일 생긴 것 확인

2. Dockerfile 작성

 - 프로젝트 최상단경로에 생성

````
FROM openjdk:8-jdk-alpine
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
````

3. Docker 이미지 만들기

````
docker build -t arajo/kubernetes-deployment-test:latest ./
````

4. 만들어진 이미지 도커 허브로 올리기

 - 경우에 따라 로그인 필요

````
docker push arajo/kubernetes-deployment-test:latest
````

5. 쿠버네티스 클러스터에 배포
````
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-1
spec:
  selector:
    matchLabels:
      type: app
  replicas: 2 # 2개의 파드가 트래픽을 나눠 받는다.
  strategy:
    type: Recreate
  revisionHistoryLimit: 1
  template:
    metadata:
      labels:
        type: app
    spec:
      containers:
      - name: container
        image: arajo/kubernetes-deployment-test:latest
---
apiVersion: v1
kind: Service
metadata:
  name: svc-1
spec:
  selector:
    app: pod
  ports:
    - port: 9000
    targetPort: 8080
  type: LoadBalancer
````

6. 확인

 - Service IP/test로 접근시 Success!가 표시되면 성공
