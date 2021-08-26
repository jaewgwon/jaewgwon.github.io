---
title: 도커로 Node.js 개발하기
categories: [Node.js, Docker, Backend]
tags: [nodejs, docker, backend, server]
---

## Docker로 node.js 배포하기

일하면서 공부하고 있는 내용들, 새롭게 알게 된 내용들이 많은데 어느 순간부터 정리가 안되는 것 같아 블로깅을 해보기로 결정했다.

1. Dockerfile 작성

참조: [Node.js 웹 앱의 도커라이징](https://nodejs.org/ko/docs/guides/nodejs-docker-webapp/)

공식 사이트에 설명이 잘 나와있어서 어렵지 않게 작성할 수 있었다.

이미 노드로 작성된 앱이 있다고 가정하고,
루트에 다음과 같은 파일을 만들어주었다.

**Dockerfile**
```Go
FROM node:12-alpine
WORKDIR /src
COPY package*.json ./
RUN npm install --silent
COPY . .
CMD [ "npm", "start" ] # 컨테이너가 실행되면 해당 커맨드가 실행 $npm start
EXPOSE 8080 # 외부 사용자가 접근할 시의 포트
```

다음으로, package.json파일의 scripts를 수정한다.  
Dockerfile에서 npm start를 실행하므로 이에 맞춰준다.

**package.json**
```javascript
  "scripts": {
    "start": "nodemon app.js"
  },
```
nodemon을 통해 로컬 환경 개발 시,  
Node.js 코드의 변화를 감지해 도커 컨테이너를 자동으로 재실행하는 것이 가능하다.

nodemon은 다른 npm 라이브러리와 마찬가지로 설치해주면 되겠다.  
`npm i -D nodemon`

이 상태에서 다음과 같은 명령어를 사용하면, 코드의 변화를 실시간으로 감지하는 상태로 도커 컨테이너를 실행시킬 수 있다.

`docker run -p 8080:3000 -v $PWD:/src app`

도커조아..