---
layout: post
title:  "Django Authentication"
date:   2019-10-18
categories: django
tag: [django, auth]
---

- [django user](#django-user)
- [overview](#overview)
- [Django authentication system](#django-authentication-system)
  - [User](#user)
  - [Authentication in Web requests](#authentication-in-web-requests)
    - [How to log a user in](#how-to-log-a-user-in)

# django user
[User authentication in Django | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/auth/)

> Django comes with a user authentication system. It handles user accounts, groups, permissions and cookie-based user sessions.  

장고는 사용자 인증 시스템과 함께 제공된다. 사용자 계정, 그룹, 권한, 쿠기기반의 사용자 세션등을 다룬다. 

# overview
> The Django authentication system handles both authentication and authorization. Briefly, authentication verifies a user is who they claim to be, and authorization determines what an authenticated user is allowed to do. Here the term authentication is used to refer to both tasks.

- authentication : 사용자 인증
- authorization : 권한 부여  
여기서 사용하는 authentication 라는 용어는 두가지 일 모두를 가리킨다. 

> The authentication system in Django aims to be very generic and doesn’t provide some features commonly found in web authentication systems. Solutions for some of these common problems have been implemented in third-party packages:


* Password strength checking
* Throttling of login attempts
* Authentication against third-parties (OAuth, for example)
* Object-level permissions

장고의 인증 시스템은 매우 일반적인 기능을 제공하지만 일부 기능을 제공하지 않습니다. 이에 대한 대안으로 third-party 패키지가 있습니다. 비밀번호 강도, 로그인 시도 제한, OAuth같은 인증 시스템 등 

**1.** **’django.contrib.auth’** contains the core of the authentication framework, and its default models.
**2.** **’django.contrib.contenttypes’** is the Django  [content type system](https://docs.djangoproject.com/en/2.2/ref/contrib/contenttypes/) , which allows permissions to be associated with models you create.

With these settings in place, running the command **manage.py migrate** creates the necessary database tables for auth related models and permissions for any models defined in your installed apps.

인증과 관련된 모델과 권한들을 생성하기 위해서 manage.py migrate 명령 수행이 필요합니다. 


# Django authentication system
[Using the Django authentication system | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/auth/default/)

## User
> objects


 [User](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User)  objects are the core of the authentication system. They typically represent the people interacting with your site and are used to enable things like restricting access, registering user profiles, associating content with creators etc. Only one class of user exists in Django’s authentication framework, i.e.,  ['superusers'](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User.is_superuser)  or admin  ['staff'](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User.is_staff)  users are just user objects with special attributes set, not different classes of user objects.
The primary attributes of the default user are:
*  [username](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User.username) 
*  [password](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User.password) 
*  [email](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User.email) 
*  [first_name](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User.first_name) 
*  [last_name](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User.last_name) 
See the  [full API documentation](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User)  for full reference, the documentation that follows is more task oriented.

```python
from django.contrib.auth.models import User
user = User.objects.create_user('john', 'lennon@thebeatles.com', 'johnpassword')
```

## Authentication in Web requests
> Django uses  [sessions](https://docs.djangoproject.com/en/2.2/topics/http/sessions/)  and middleware to hook the authentication system into  [request objects](https://docs.djangoproject.com/en/2.2/ref/request-response/#django.http.HttpRequest) .


장고는 세션과 미들웨어를 사용하여 인증시스템과 request 객체를 연결합니다. 

> These provide a  [request.user](https://docs.djangoproject.com/en/2.2/ref/request-response/#django.http.HttpRequest.user)  attribute on every request which represents the current user. If the current user has not logged in, this attribute will be set to an instance of  [AnonymousUser](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.AnonymousUser) , otherwise it will be an instance of  [User](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User) .
> You can tell them apart with  [is_authenticated](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User.is_authenticated) , like so:


매 request요청마다 request.user에 현재 유저에 대한 정보가 담겨있습니다. 로그인이 되어있으면 User 인스턴스를, 로그인이 되어있지 않으면 AnonymousUser 인스턴스를 반환합니다. 

### How to log a user in
> To log a user in, from a view, use  [login()](https://docs.djangoproject.com/en/2.2/topics/auth/default/#django.contrib.auth.login) . It takes an  [HttpRequest](https://docs.djangoproject.com/en/2.2/ref/request-response/#django.http.HttpRequest)  object and a  [User](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User)  object.  [login()](https://docs.djangoproject.com/en/2.2/topics/auth/default/#django.contrib.auth.login)  saves the user’s ID in the session, using Django’s session framework.
> Note that any data set during the anonymous session is retained in the session after a user logs in.
> This example shows how you might use both  [authenticate()](https://docs.djangoproject.com/en/2.2/topics/auth/default/#django.contrib.auth.authenticate)  and  [login()](https://docs.djangoproject.com/en/2.2/topics/auth/default/#django.contrib.auth.login) :


```python
from django.contrib.auth import authenticate, login

def my_view(request):
    username = request.POST['username']
    password = request.POST['password']
    user = authenticate(request, username=username, password=password)
    if user is not None:
        login(request, user)
        # Redirect to a success page.
        ...
    else:
        # Return an 'invalid login' error message.
        ...
```
POST를 통해 입력받은 username과 password를 사용하여 사용자 검증을 하고 통과한다면 이전에 받은 HttpRequest와 user정보를 login함수에 인자로 전달합니다. 


---
17 Oct 2019 7:59 PM