---
layout: post
title:  "다양한 구동환경을 위한 settings / requirements.txt 분기"
date:   2019-05-11 13:31:01 +0800
categories: django
tag: setting
---

실행환경 별로 필요한 라이브러리가 다를 수 있다.

- 공통
- 개발용
- 배포용(AWS)
- 배포용(Heroku)
- 서비스 2.0 개발용

## settings란?  
다양한 프로젝트 설정을 담는 파이썬 소스파일  
장고 앱, DB 설정, 캐시 설정  
디폴트는 (django/conf/global_settings.py)를 기본으로 하고, 지정 settings를 통해 필요한 설정을 재정의한다. 

os.environ.setdefualt(key, default=None)  
os.environ는 dict과 유사한 인터페이스이다.  
dict.setdefault(key, default=None)  
```
if 'DJANGO_SETTINGS_MODULE' not in os.environs:
    os.environs['DJANGO_SETTINGS_MODULE']='askcompnay.settings'
```

manage.py, wsgi.py에 있다. 

이또한 settings폴더를 만들고, \__init__.py를 집어넣은다음, prod.py, common.py 등을 만들어서 사용할 수 있다. 주의할점은 manage.py, wsgi.py에 os.environ.setdefualt값을 잘 정해줘야 오류없이 실행시킬 수 있다. 