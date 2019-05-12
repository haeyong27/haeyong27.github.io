---
layout: post
title:  "Form/ModelForm"
date:   2019-05-12 17:31:01 +0800
categories: backend
tag: django
---
## HTML Form
HTML페이지에는 <form></form> 태그를 통해서 입력폼을 구성한다.  
sumbit시 지정 url로 데이터전송을 시도한다. 
```
<form action=""></form>
<a href=""></a>
<img src="" alt="">
```
모두 상대경로에 입각해서 계산한다. 그러므로 아무것도 써주지 않으면 현재페이지를 가리킨다. 

method = POST로 전송할 때 enctype은 multipart/form-data로 지정해줘야한다. 그렇지 않으면 사진을 올렸을 때 파일 명만 전송된다.  

### 장고 뷰에서 인자접근 
- request.GET  
    GET/POST모두 가능  
- request.POST  
    POST요청에서만 가능  
    파일내역 제외한 모든 POST인자 목록
- request.FILES  
    요청 body에서 파일 내역만 파싱한 MultiValueDict객체 


## HttpRequest와 HttpResponse
클라이언트로부터의 모든 요청 내용을 담고 있으며
- 함수 기반 뷰 : 매 요청 시마다 뷰 함수의 첫번째 인자 request로 전달
- 클래스 기반 뷰 : 매 요청 시마다 self.request를 통해 접근

URL의 QueryString은 같은 Key로서 다수 Value지정을 지원


## DJango Style의 Form처리
- 하나의 URL(view)에서 2가지 역할 수행
1. 빈폼을 보여주는 역할
2. 폼을 통해 입력된 값을 검증하고 저장하는 역할