---
layout: post
title:  "Database Terminology"
date:   2019-10-25
categories: Database
tag: [Database, terminology]
---

# DB Term
## Relation
### meaning
 [Understanding the Meaning of a Database Relation](https://www.lifewire.com/relation-definition-1019260)
> A common term used in database design is a “relational database”—but a database relation is not the same thing and does not imply, as its name suggests, a relationship between tables. Rather, a database relation simply refers to an individual table in a relational database.

일반적으로 데이터베이스를 설계하는데 사용되는 관계형 데이터베이스(relational database)라는 용어는 테이블 사이의 관계입니다. 그에반해 database relation은 관계형 데이터베이스에 있는 각각의 테이블을 의미합니다. 

### Relation  schema
[릴레이션 스키마](https://terms.naver.com/entry.nhn?docId=3431143&cid=58430&categoryId=58430&expCategoryId=58430)
- 스키마(schema)은 릴레이션의 논리적 구조이다.
- attribute, 속성, columns

### Relation instance
[릴레이션 인스턴스](https://terms.naver.com/entry.nhn?docId=3431144&cid=58430&categoryId=58430&expCategoryId=58430)
- 인스턴스(instance)는 릴레이션에 존재하는 투플들의 집합이다.
- [릴레이션 스키마](https://terms.naver.com/entry.nhn?docId=3431143&ref=y) 에서 정의하는 각  [속성](https://terms.naver.com/entry.nhn?docId=3431117&ref=y) 에 대응하는 실제 값
- tuple, record


## KEY
### Super key
[수퍼키](https://terms.naver.com/entry.nhn?docId=3431148&cid=58430&categoryId=58430&expCategoryId=58430)
유일성을 만족하는 속성들의 집합. 
속성이 몇 개로 구성되어있든 상관없다. 유일성만 만족한다면 

### Candidate key
[후보키](https://terms.naver.com/entry.nhn?docId=3431149&cid=58430&categoryId=58430&expCategoryId=58430)
각 tuple의 유일성을 보장하기 위해 필요한 최소한의 속성들로 구성된 집합. 그러므로 수퍼키들 중 최소성을 만족하는 것이 후보키가 된다. 

### Primary key(PK)
[기본키](https://terms.naver.com/entry.nhn?docId=3431150&cid=58430&categoryId=58430&expCategoryId=58430)
하나의 테이블 안에 여러개의 후보키가 존재할 수 있다. 하지만 여러 후보키를 모두 사용할 필요는 없고 데이터베이스 사용 환경을 고려하여 하나를 골라 PK로 설정한다.

### Foreign key(FK)
특정 릴레이션(A table)의 속성이 다른 릴레이션(B table)의 기본키가 되는 키, 다시말해 다른 릴레이션(B table)의 기본키를 참조하는 속성의 집합. 
