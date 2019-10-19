---
layout: post
title:  "OAuth2.0 Library(allauth, rest-auth)"
date:   2019-10-01
categories: Django
tag: [Django, auth]
---
- [시작](#%ec%8b%9c%ec%9e%91)
- [django library](#django-library)
  - [rest-auth](#rest-auth)
  - [all auth](#all-auth)
  - [실패이유 이걸 꼭 등록해야됨.](#%ec%8b%a4%ed%8c%a8%ec%9d%b4%ec%9c%a0-%ec%9d%b4%ea%b1%b8-%ea%bc%ad-%eb%93%b1%eb%a1%9d%ed%95%b4%ec%95%bc%eb%90%a8)



OAuth2 allauth, rest-auth
## 시작
[OAuth 2.0 — OAuth](https://oauth.net/2/)
[Integrate OAuth 2 Into Your Django/DRF Back-end | Toptal](https://www.toptal.com/django/integrate-oauth-2-into-django-drf-back-end)


[Host](https://terms.naver.com/entry.nhn?docId=3477684&cid=58439&categoryId=58439)
> 인터넷 상에 연결된 개별적인 컴퓨터를 지칭하는 말이다. 인터넷에 연결되어 있고, 개별적인 IP 주소를 가지는 시스템을 호스트라고 한다.


## django library
### rest-auth 
[Introduction — django-rest-auth 0.9.5 documentation](https://django-rest-auth.readthedocs.io/en/latest/introduction.html)
django-rest-auth, a set of REST API endpoints to handle User Registration and Authentication tasks. By having these API endpoints, your client apps such as AngularJS, iOS, Android, and others can communicate to your Django backend site independently via REST APIs for User Management. 

- User Registration with activation
- Login/Logout

### all auth 
[Overview — django-allauth 0.32.0 documentation](https://django-allauth.readthedocs.io/en/latest/overview.html#)
- Signup of both local and social accounts
- Connecting more than one social account to a local account


```javascript
this*.$axios.post*(‘http://127.0.0.1:8000/rest-auth/google/‘, { code: authCode, redirect_uri : ‘http://127.0.0.1:8000/accounts/google/login/callback/‘})
```


[Installation — django-rest-auth 0.9.5 documentation](https://django-rest-auth.readthedocs.io/en/latest/installation.html)
```python
class GoogleLogin(SocialLoginView):
    adapter_class = GoogleOAuth2Adapter
    client_class = OAuth2Client
    callback_url = ‘http://localhost:3000’
```
    
### 실패이유  이걸 꼭 등록해야됨.
 [Django: SocialApp matching query does not exist](https://stackoverflow.com/questions/15409366/django-socialapp-matching-query-does-not-exist) 