# *Kubernetes Deployment Test*

 [์ด๊ณณ](https://donghoon-khan.github.io/2020/08/10/deploy-spring-boot-application-on-kubernetes/) ์ฐธ๊ณ .



### ๐ ์ค์ต

1. ํ์คํธ์ฉ ํ๋ก์ ํธ(๋ณธ ๋ ํฌ์งํ ๋ฆฌ์ ํ๋ก์ ํธ) ์์ฑ

 - ๋ณธ ๋ ํฌ์งํ ๋ฆฌ์ ํ๋ก์ ํธ๋ **JAVA8 MAVEN JAR์ ์คํธ๋ง๋ถํธ์ฑ.**

2. ๋น๋

  - mvnw๋ ํ๋ก์ ํธ ์ต์๋จ๊ฒฝ๋ก์ ์์น

````
./mvnw install
````
 - target ํด๋์ jarํ์ผ ์๊ธด ๊ฒ ํ์ธ

2. Dockerfile ์์ฑ

 - ํ๋ก์ ํธ ์ต์๋จ๊ฒฝ๋ก์ ์์ฑ

````
FROM openjdk:8-jdk-alpine
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
````

3. Docker ์ด๋ฏธ์ง ๋ง๋ค๊ธฐ

````
docker build -t arajo/kubernetes-deployment-test:latest ./
````

4. ๋ง๋ค์ด์ง ์ด๋ฏธ์ง ๋์ปค ํ๋ธ๋ก ์ฌ๋ฆฌ๊ธฐ

 - ๊ฒฝ์ฐ์ ๋ฐ๋ผ ๋ก๊ทธ์ธ ํ์

````
docker push arajo/kubernetes-deployment-test:latest
````

5. ์ฟ ๋ฒ๋คํฐ์ค ํด๋ฌ์คํฐ์ ๋ฐฐํฌ
````
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-1
spec:
  selector:
    matchLabels:
      type: app
  replicas: 2 # 2๊ฐ์ ํ๋๊ฐ ํธ๋ํฝ์ ๋๋  ๋ฐ๋๋ค.
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

6. ํ์ธ

 - Service IP/test๋ก ์ ๊ทผ์ Success!๊ฐ ํ์๋๋ฉด ์ฑ๊ณต
