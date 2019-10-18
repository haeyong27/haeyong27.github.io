---
layout: post
title:  "django start"
date:   2019-05-09 13:31:01 +0800
categories: django
tag: django
---
## 장고의 주요 기능
- Views : HTTP 요청 처리  
- Models : 데이터베이스와의 인터페이스  
- Templates : 문자열 조합을 용이하게  
- Admin : 데이터베이스 레코드 관리 UI  
- Loggin : 메세지 로깅  
- Static files : 정적인 파일 관리  
- Messages framework : 메세지 노출   


## models/views 모듈을 팩키지로 전환 ?
- 모듈 : 파이썬 소스코드 파일
- 팩키지 : 파이썬 소스코드 디렉토리
- 1안) 다음 기본 구성을
- shop/models.py 내 Item모델, Review모델
- 2안) 앱 복잡도에 따라, 다음과 같이 변경해보실 수도 있습니다.
- shop/models/
- item.py 내 Item모델
- review.py 내 Review모델  
- \__init__.py 내에서는 from .item import * 와 같이 임포트하게되면, 모델 외부에
서는 (1안)과 동일하게 사용할 수 있습니다.


## 장고쉘
python manage.py shell

```pip install django-extensions```


## 장고 모델 (ORM)
object relational mapping
데이터 베이스 :RDBMS, NoSQL
파일 : 로컬, 외부 정적 스토리지
캐시서버 : memcached, redis

모델명과 DB 테이블 명
DB 테이블명 : 디폴트 "앱이름_모델명"
예  
blog 앱 
Post 모델 -> 'blog_post'
Comment 모델 -> 'blog_comment'

## 모델을 통한 데이터 조회
ModelCls.objects.all() -> QuerySet 
순회가능한 객체이다. 
Chaining을 지원한다.
• QueyrSet은 Lazy한 특성
• QuerySet을 만드는 동안에는 DB접근을 하지 않습니다.
• 실제로 데이터가 필요한 시점에 접근을 합니다.


## 모델을 통한 데이터 생성/수정/삭제

post = Post.objects 모델메니저
post.create(field1=valeu1, field2=value2)
**{dict} 형태로 unpack하여 인자를 전달할 수 있다.


## Django Form
Model 클래스와 유사하다
- 입력폼 HTML 생성
- 입력폼 값 검증(Validation)
- 검증을 통과한 값들을 사전타입으로 제공

### Form처리
폼 처리 시에 같은 URL에서 GET/POST로 나눠 처리한다
- GET
입력폼을 보여준다
- POST
데이터를 입력받아 유효성 검증과정을 거친다.
성공시 : 데이터를 저장하고 SUCCESS URL로 이동
실패시 : 오류메세지와 입력폼을 다시 보여줌

