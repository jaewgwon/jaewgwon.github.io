---
title: 도커로 PostgreSQL - 1
categories: [Docker, PostgreSQL, DB]
tags: [docker, postgres, db]
---

## 1. 상황

도커화를 진행하는 중, PostgreSQL을 도커화 하며 API 컨테이너와 연결하는 과정에서  
여러가지 공유할 만한 내용이 많아 포스팅하게 되었다.

## 2. 생각

Dockerfile을 만들 때 postgres의 공식 이미지를 통해 만들면 생각보다 쉽게 작성히 가능해 보였다.
가능하면 스키마나 테스트데이터 등을 SQL파일로 포함해 자동으로 입력시키고 싶다.

## 3. 작업

1. Dockerfile 자체는 몹시 간단하다.  
```docker
FROM postgres:13

RUN mkdir -p /tmp/psql_data/ # host에서 파일을 복사할 디렉토리 만들기

COPY structure.sql /tmp/psql_data/ # 파일 복사
COPY test_data.sql /tmp/psql_data/
```

2. 다음과 같은 방식으로 빌드 및 컨테이너를 실행한다.

구성 및 pwd
- Dockerfile
- *.sql # 추가할 SQL파일

```
$ docker build --tag 이미지이름 .        // 이미지 빌드
$ docker volume create 볼륨이름    // 볼륨 생성; 자동으로 볼륨을 잡기는 하지만 이름이 hashcode라서 보기 불편
```

```
$ docker run -p 5432:5432 --name 컨테이너이름 -e POSTGRES_PASSWORD=비밀번호 -d -v 볼륨이름:/var/lib/postgresql/data 이미지이름
// 실행한다. 이해하기 쉽게 한글로 파라미터를 작성함.
```