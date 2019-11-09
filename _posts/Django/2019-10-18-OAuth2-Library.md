---
layout: post
title:  "Django OAuth2.0 Library(allauth, rest-auth)"
date:   2019-10-01
categories: Django
tag: [Django, auth]
---
- [OAuth2 allauth, rest-auth](#oauth2-allauth-rest-auth)
  - [rest-auth](#rest-auth)
  - [all auth](#all-auth)
  - [JWT Support](#jwt-support)



# OAuth2 allauth, rest-auth

[OAuth 2.0 — OAuth](https://oauth.net/2/)
[Integrate OAuth 2 Into Your Django/DRF Back-end | Toptal](https://www.toptal.com/django/integrate-oauth-2-into-django-drf-back-end)




## rest-auth 
[Introduction — django-rest-auth 0.9.5 documentation](https://django-rest-auth.readthedocs.io/en/latest/introduction.html)

> django-rest-auth, a set of REST API endpoints to handle User Registration and Authentication tasks. By having these API endpoints, your client apps such as AngularJS, iOS, Android, and others can communicate to your Django backend site independently via REST APIs for User Management. 

- User Registration with activation
- Login/Logout

DRF 유저 등록, 로그인, 로그아웃 기능 제공

## all auth 
[Overview — django-allauth 0.32.0 documentation](https://django-allauth.readthedocs.io/en/latest/overview.html#)
- Signup of both local and social accounts
- Connecting more than one social account to a local account

소셜(구글, 페이스북, 카카오 등) 인증기반 유저관리 기능 제공


## JWT Support
[django-rest-auth](https://django-rest-auth.readthedocs.io/en/latest/installation.html#jwt-support-optional)에서는 기본적으로 Token-based authentication를 사용합니다. JWT authentication를 사용하고 싶다면 [djangorestframework-jwt](http://jpadilla.github.io/django-rest-framework-jwt/#overview)를 사용합니다. 
 > Unlike some more typical uses of JWTs, this module only generates authentication tokens that will verify the user who is requesting one of your DRF protected API resources. The actual request parameters themselves are not included in the JWT claims which means they are not signed and may be tampered with. You should only expose your API endpoints over SSL/TLS to protect against content tampering and certain kinds of replay attacks.

```bash
curl -H "Authorization: JWT <your_token>" http://localhost:8000/protected-url/
```

[DRF 공식문서](https://www.django-rest-framework.org/api-guide/authentication/#json-web-token-authentication)에서는 [djangorestframework-simplejwt](https://github.com/davesque/django-rest-framework-simplejwt)를 사용합니다. 
