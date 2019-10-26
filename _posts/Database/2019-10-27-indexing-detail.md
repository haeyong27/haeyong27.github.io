---
layout: post
title:  "Indexing detail"
date:   2019-10-27
categories: Database
tag: [Database, Youtube, DataStructure]
---

- [Indexing detail](#indexing-detail)
  - [인덱스 1](#%ec%9d%b8%eb%8d%b1%ec%8a%a4-1)
  - [인덱스 2](#%ec%9d%b8%eb%8d%b1%ec%8a%a4-2)
  - [이것이 MySQL이다 09. MySQL 인덱스(2) - YouTube](#%ec%9d%b4%ea%b2%83%ec%9d%b4-mysql%ec%9d%b4%eb%8b%a4-09-mysql-%ec%9d%b8%eb%8d%b1%ec%8a%a42---youtube)
  - [B-Tree Tutorial - An Introduction to B-Trees - YouTube](#b-tree-tutorial---an-introduction-to-b-trees---youtube)

# Indexing detail
## 인덱스 1
[이것이 MySQL이다 09. MySQL 인덱스(1) - YouTube](https://www.youtube.com/watch?v=aTOFBD52060&list=PLVsNizTWUw7HhYtI-4GGmlJ5yxNdwNI_X&index=29)

9:39 인덱스의 종류
클러스터링 : 영어사전 (알파벳순으로 책 내용전체가 정렬이 되어있다), 그렇기 때문에 테이블당 한 개만 생성가능 
보조인덱스 : 책 맨 뒤에 있는 특정 단어들이 위치해 있는 페이지를 알려주는 ‘찾아보기’ 기능, 테이블에 여러개 보조 인덱스를 설정 가능 

12:00 자동으로 생성되는 인덱스
PK를 지정하면 PK를 기준으로 클러스터형 인덱스가 생성된다.

16:11 테이블 생성시 제약 조건으로 PK를 지정하면 클러스터형 인덱스가, unique를 사용하면 보조 인덱스가 생성된다. 

21:40 unique + not null = primary key

## 인덱스 2
[이것이 MySQL이다 09. MySQL 인덱스(2) - YouTube](https://www.youtube.com/watch?v=qW9X4w_SnT4&list=PLVsNizTWUw7HhYtI-4GGmlJ5yxNdwNI_X&index=30)
---
내부 작동 B-tree
[B-Tree Tutorial - An Introduction to B-Trees - YouTube](https://www.youtube.com/watch?v=C_q5ccN84C8)
---
8:12 클러스터형 인덱스와 보조인덱스의 구조 

9:50 pk(primary key), 클러스터형
데이터 페이지(리프 페이지) 또한 인덱스 페이지가된다. 영어사전은 ‘찾아보기’가 따로 없는걸 생각해보면 이해하기 쉽다. 

12:07 유니크, 보조인덱스 
데이터 영역은 클러스터형과 달리 따로 정렬되지 않는다.

14:47 검색 예시 

16:07 클러스터형과 비 클러스터형의 비교 
클러스터형의 장점 : 범위검색에 좋다. 