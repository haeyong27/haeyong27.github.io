---
layout: post
title:  "Database Indexing"
date:   2019-10-15
categories: Database
tag: [Database]
---
<iframe width="560" height="315" src="https://www.youtube.com/embed/uO8tL0okg7Q" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

인덱스가 생성되면 테이블과 매핑된 또다른 테이블이 하나 생성된다. 
테이블이 하나 더 생성됐는데 어떻게 더 빨라지나요 ?
인덱스 컬럼을 기준으로 소팅이 되어 저장이 되어있다. 보통 테이블은 물리적으로 흩어져 저장이 되어있다.  특정 조건의 데이터를 찾으려면 테이블 풀 스캔을 해야한다. 데이터 양이 방대해진다면 시간이 많이 걸린다. 인덱스가 테이블과 매핑되어있다. 인덱스에서 데이터들을 먼저 찾은 다음 매핑된 테이블로 가서 나머지 데이터들을 꺼내오는 방식이다. 


2:38 어떤 컬럼을 인덱스로 설정해야 효율적일까 ?
특정조건부터 스캔을 한다. 
1. 조건(where)절에 자주 등장하는 컬럼을 인덱스로 설정하면 효율적 
2. order by 절에 자주 등장하는 컬럼을 인덱스로 지정, 인덱스는 소팅이되어 저장되어있기 때문에 인덱스에서 바로 꺼내서 사용할 수 있음
3. 여러 컬럼을 조합해서 만들기도 함 

3:35 인덱스의 단점
인덱스는 오브젝트이다. select는 빨라질지 몰라도 insert나 update는 느려진다. 정렬이 된 상태로 저장되어야 하기 때문에 어느자리에 insert할지 찾아서 저장해야한다. 또한 테이블만 하는것이 아니라 인덱스도 똑같이 해줘야하기 때문에. 

4:09 결합인덱스
결합하는 컬럼들의 순서가 중요. where절에서 equal 조건으로 많이 쓰이는 컬럼, 분별력이 높은 컬럼들이 앞으로 오는게 효율적. 예를들어 성별보다는 아이디처럼 분별력이 높은것. -> 결합인덱스에서 컬럼의 분별력과 선두 위치 여부는 상관이 없습니다. 오히려 분별력이 낮은 컬럼이 선두에 위치하는 것이 index skip scan 측면에선 더 유리

 5:05 인덱스 스캔방식 
index range scan : 어떤 시작점 부터 특정 범위를 스캔하는 방식 
index full scan, index skip scan , index fast full scan 등등이 있다. 

5:25 인덱스를 탄다고 무조건 빨라지지는 않음, 손익분기점이 있다. 
테이블이 가지고 있는 전체 데이터 양의 10에서 15프로 일때 효율적이고 그 이상일 떈 풀스캔이 더 빠르다. 