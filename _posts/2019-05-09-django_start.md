---
layout: post
title:  "django start"
date:   2019-05-09 13:31:01 +0800
categories: django
tag: django
---

## 개요 
웹 서비스, 앱 서버, 챗봇 서비스 
웹 서비스를 만들 때 마다 반복되는것들을 표준화 해서 묶어놓은것 
모든 언어 마다 웹 프레임워크가 존재한다. 

django는 백엔드 개발에 필요한 거의 모든 기능을 제공한다

### django의 강점
- python 생태계
- 풀스탤 웹 프레임워크
- 10년동안 성숙

### 웹 서비스 개발 파트
- 백엔드 개발
- 서버운영 
- 프론트엔드 개발
- 앱 개발

## 환경세팅

[아나콘다 설치 팁](https://docs.anaconda.com/anaconda/user-guide/faq/)


To see if the conda installation of Python is in your PATH variable:

- On macOS and Linux, open the terminal and run—```echo $PATH```  
- On Windows, open an Anaconda Prompt and run—```echo %PATH%```

To see which Python installation is currently set as the default:

- On macOS and Linux, open the terminal and run—```which python```  
- On Windows, open an Anaconda Prompt and run—```where python```  
  
To see which packages are installed in your current conda environment and their version numbers, in your terminal window or an Anaconda Prompt, run ```conda list```  

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