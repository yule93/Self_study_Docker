# Self_study_Docker

## 1. 설치

https://www.docker.com/products/docker-desktop

여기서 자신의 OS에 맞는 버전으로 설치하면 Docker Desktop 앱이 설치된다. 이 때, 만약 도커 컨테이너 만드는 게 실패한다면 알럿 메세지를 따라 WSL을 설치해주면 된다.


## 2. 테스트&확인용 명령어

<img src="https://i.imgur.com/fgwpc4n.jpg" title="source: imgur.com" width = "50%" height = "" />

[1-1: Hello Docker World!]


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
4) 
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
VueJS의 루트 주소에 Dockerfile 이라는 이름의 파일 확장명이 없는 파일을 만들어야 한다. 내용은 아래와 같이 만들며, cmd에서 파일을 생성하는 명령어는 `copy con Dockerfile`로 만들면 된다.
```Shell
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

Dockerfile을 만든 후, 루트 디렉터리에서 그대로 `docker build . -t front:0.1`를 shell 명령어로 실행하면 관통 프로젝트의 FE가 이미지화가 되어 도커 저장소에 만들어진다. `docker images` 명령어로 잘 만들어진 게 확인이 됐다면 이름 혹은 이미지 아이디를 사용해 `docker run -it -p 80:80 --rm front:0.1`를 실행하면 80 포트에 배포된 FE에 접근이 가능하다.

---
## Spring Boot (Backend)

1) 기본 세팅 사항
Spring Boot를 jar 파일로 압축해 배포하기 위해서는 **Java와 Maven Wrapper가 기본적으로 깔려있어야** 하는데, 래퍼를 깔기 위해서는 Maven을 다시 설치해야 했다.
![Maven 설치](https://i.imgur.com/dOCodm5.png)

설치 후, `mvn -N io.takari:maven:wrapper` 명령어를 사용해 .mvn 디렉토리, mvnw, mvnw.cmd 파일을 생성하고 이 때 두 파일의 역할은 다음과 같다.
- mvnw : mvnw 는 Maven 대신에 사용되는 실행 가능한 unix shell script
- mvwn.cmd : mvnw 의 윈도우 배치 버전 shell script

.mvn directory 에는 wrapper directory 가 생성되며 그 안에는 **MavenWrapperDownloader.java , maven-wrapper.jar , maven-wrapper.properties** 으로 총 3개의 파일이 존재한다.
- MavenWrapperDownloader.java : 이 파일을 컴파일 및 실행하여 Maven이 다운로드 된다.
- maven-wrapper.jar : wrapper shell scripts 로 부터 maven 을 실행하고 다운로드 하는데 사용 된다.
- maven-wrapper.properties : **Maven 이 존재하지 않는 경우 다운로드하기 위한 URL을 명시하기 위해서** 사용 된다.
이 파일들을 통해서 Maven이 존재하지 않아도 특정 버전과 classpath를 가지고 공통된 Maven 을 다운로드 및 사용할 수 있게 된다.

이후, **Dockerfile(확장자 없음)을 루트 파일에 만들어야 하는데** 다음과 같은 내용으로 작성하면 된다.
```text
FROM openjdk:8-jdk-alpine
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```
[참고 자료: 스프링 공식 사이트](https://spring.io/guides/gs/spring-boot-docker/)

mvnw 파일이 모두 완성되면 `java -jar target\backend-0.0.1-SNAPSHOT.jar` shell 명령어를 사용해, 관통 프로젝트의 backend 부분을 .jar 파일로 만들 수 있다.

위에 제시된 **jar, Dockerfile**을 만든 후에 backend의 루트 디렉터리에서 shell 명령어로 `docker build . -t back:0.1`을 실행하면 이미지화 되고, `docker images` 명령어로 이미지가 정상적으로 만들어진 것을 확인할 수 있다.
이미지 아이디 혹은 이름을 붙인 back:0.1로 `docker run -it -p 8080:8080 --rm back:0.1` shell 스크립트를 실행하면 로컬호스트이 8080번 포트에 자동 배포가 이뤄졌음을 알 수 있다.
