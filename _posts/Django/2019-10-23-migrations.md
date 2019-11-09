---
layout: post
title:  "Django Migrations"
date:   2019-10-23
categories: Django
tag: [Django, Database]
---
- [Django Migrate](#django-migrate)
- [migrate 정방향, 역방향](#migrate-%ec%a0%95%eb%b0%a9%ed%96%a5-%ec%97%ad%eb%b0%a9%ed%96%a5)
  - [정방향](#%ec%a0%95%eb%b0%a9%ed%96%a5)
  - [역방향](#%ec%97%ad%eb%b0%a9%ed%96%a5)


# Django Migrate
Django model의 변경내역을 데이터베이스의 스키마에 반영하는 기능

# migrate 정방향, 역방향 
## 정방향
```bash 
python manage.py migrate <앱이름>
``` 
미적용 파일 부터 최근 마이그레이션까지 정방향으로 순차적으로 수행 


## 역방향 
```bash 
python manage.py migrate <앱이름> <마이그레이션 이름>
``` 
지정된 마이그레이션이 현재 적용된 마이그레이션보다 이후라면 정방향으로 지정 마이그레이션까지 forward수행

이전이라면, 역방향으로 순차적으로 지정마이그레이션 이전까지 backward수행 

1, 2, 3, 4 마이그레이션이 있고, 4번까지 마이그레이션이 적용된 상태에서 migrate 0002를 하면 4, 3 순서되로 마이그레이션이 취소된다. 최종적으로 2번 마이그레이션이 적용된 상태로 가게된다. 