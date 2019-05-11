---
layout: post
title:  "Django templates"
date:   2019-05-11 13:31:01 +0800
categories: backend
tag: django
---

템플릿 기능에 제한을 둠으로서, 비즈니스 로직을 템플릿 단에 구현함을 방지.
비즈니스 로직은 model에, form/modelform을 통한 유효성 검사 및 저장을 권장

settings.TEMPLATES 설정 리스트
- backend : 템플릿 엔진 지정
- dirs : 경로리스트 (filesystem loader)
- app_dirs : 앱별 templates 경로 추가 여부

각 앱들을 위한 템플릿은 '앱/templates/'경로에 배치  
프로젝트 전반적으로 사용할 템플릿은 DIRS에 명시한 경로에 배치  

## 장고 템플릿 태그/필터
Django Template Tag ```{% 태그명 '인자1' '인자2' %}```와 같은 형식으로 호출
Django Template Filter ```{{ 값|필터1:인자|필터2:인자|필터3}}```   

for/endfor, if/endif, include, load, verbatim등 다양한 빌트인 탬플릿 태그/필터가 제공된다.




## 장고가 템플릿 파일을 찾는 원리(loader)
### 왜 템플릿 파일은 앱이름/templates/앱이름/post_list.html과 같은 구조를 가져야만 하는 지
다양한 템플릿 로더가 있다.
- 파일 시스템 로더
- 앱 디렉토리 로더
- cached 로더

템플릿 로더에게 템플릿을 찾아달라고 요청을 하게된다. 그 대표적인 예로 render라는 함수가 있다. 
ex) ```render(request, 'blog/post_list.html')```
여기서 'blog/post_list.html' 이 부분을 가지고 템플릿을 찾게되는데 먼저 파일 시스템로더, 두번째로 앱 디렉토리 로더를 찾는다. 

settings.TEMPLATES 의 DIRS = [] 설정에 의존한다. 

## 템플릿 상속을 통한 중복 제거
각 뷰에 연결된 템플릿은 독립적으로 동작한다.

#### 기본특징
상속은 여러단계로 이뤄질 수 있고, block에는 이름을 할당해야하며, 이름을 통해 구분한다. 그렇기 때문에 유일해야한다.  
부모템플릿은 자식 템플릿이 비집고 들어올 수 있는 다수의 영역(block)을 정의할 수 있다.  
