---
layout: post
title: 데이터베이스와 테이블
date: 2025-04-12 13:55:00 +0900
categories: [SQL, Basics]
tags: [db]
---


## 1.데이터베이스와 테이블

---

### 1-1. 데이터와 데이터베이스의 개념

**데이터** : 모든 기록들 

**데이터베이스** : 데이터가 모여져 있는 공간, 구조적인 방식으로 관리되는 데이터들의 집합

- 데이터베이스의 특징
  - 실시간 접근성 : 즉시 처리하고 응답
  - 지속적인 변화 : 새로운 데이터의 삽입, 삭제, 갱신으로 항상 최신의 데이터 유지
  - 동시 공유 : 다수의 사용자가 동시에 같은 내용에 데이터를 사용할 수 있음.
  - 내용에 대한 참조 : 사용자가 요구하는 데이터 내용으로 데이터를 찾기

**관계형 데이터베이스(RDB, Relational Datebase)**
- 2차원 테이블(행, 열)로 구성되어 데이터를 정의하고 설명하는 모델


---

### 1-2. 테이블

**테이블** : 관계형 데이터베이스에서 자료의 구조를 2차원의 표로 나타낸 것
- 행/열의 형태로 구성 및 관리 가능
- Key를 지정함으로써 원하는 데이터를 빠르고 쉽게 찾아낼 수 있음

---

### 1-3. 스키마와 데이터 타입

**스키마** : 테이블의 뼈대를 구성하며, 테이블에 대한 정보가 들어있음.
- 테이블을 정의할 때, 컬럼별로 저장할 수 있는 타입까지 명시해야 함.

**데이터 타입** : 데이터를 식별하기 위한 분류
- 데이터 타입에 따라 사용하는 SQL 구문이 다름.
