# Self_study_Docker

## 1. 설치

https://www.docker.com/products/docker-desktop

여기서 자신의 OS에 맞는 버전으로 설치하면 Docker Desktop 앱이 설치된다. 이 때, 만약 도커 컨테이너 만드는 게 실패한다면 알럿 메세지를 따라 WSL을 설치해주면 된다.


## 2. 테스트&확인용 명령어

1) 도커 버전 확인하기
```Shell
docker -v
```
2) 테스트용 Hello world 도커 컨테이너 실행
```Shell
docker run hello-world
```
<img src = "https://user-images.githubusercontent.com/31907528/147330870-d6d940ea-d59b-4c48-a101-9952b39e1474.jpg" style="width: 50%; height:" />
3) 컨테이너 조회
```Shell
docker ps -a
```
4) 컨테이너 삭제
```Shell
docker rm [컨테이너 ID 또는 NAME]
```
5) 도커 이미지 조회
```Shell
docker images
```
6) 도커 이미지 삭제
```Shell
docker rmi [이미지 ID 또는 이미지명: TAG명]
```

## 3. Dockerization Vue js
[공식 문서 참고](https://kr.vuejs.org/v2/cookbook/dockerize-vuejs-app.html)
1) Dockerfile 만들기
VueJS의 루트 주소에 Dockerfile 이라는 이름의 파일 확장명이 없는 파일을 만들어야 한다. 내용은 아래와 같이 만들며, cmd에서 파일을 생성하는 명령어는 ```copy con Dockerfile```로 만들면 된다.
```text
FROM node:lts-alpine
# install simple http server for serving static content
RUN npm install -g http-server
# make the 'app' folder the current working directory
WORKDIR /app
# copy both 'package.json' and 'package-lock.json' (if available)
COPY package*.json ./
# install project dependencies leaving out dev dependencies
RUN npm install --production
# copy project files and folders to the current working directory (i.e. 'app' folder)
COPY . .
# build app for production with minification
RUN npm run build
EXPOSE 8080
CMD [ "http-server", "dist" ]
```
