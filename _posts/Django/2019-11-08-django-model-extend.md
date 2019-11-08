---
layout: post
title:  "Django Model Extend"
date:   2019-11-08
categories: Django
tag: [Django]
---



- [django user extend](#django-user-extend)
  - [Extending the existing User model](#extending-the-existing-user-model)
- [Substituting a custom User model](#substituting-a-custom-user-model)
  - [Using a custom user model when starting a project](#using-a-custom-user-model-when-starting-a-project)
- [Referencing theUsermodel](#referencing-theusermodel)
- [Specifying a custom user model](#specifying-a-custom-user-model)

# django user extend
## Extending the existing User model
[Customizing authentication in Django | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/auth/customizing/#extending-the-existing-user-model)

> There are two ways to extend the default  [User](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User)  model without substituting your own model. If the changes you need are purely behavioral, and don’t require any change to what is stored in the database, you can create a  [proxy model](https://docs.djangoproject.com/en/2.2/topics/db/models/#proxy-models)  based on  [User](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User) . This allows for any of the features offered by proxy models including default ordering, custom managers, or custom model methods.
> If you wish to store information related to User, you can use a  [OneToOneField](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.OneToOneField)  to a model containing the fields for additional information. This one-to-one model is often called a profile model, as it might store non-auth related information about a site user. For example you might create an Employee model:

User 모델을 확장하는 방법은 2가지가 있다. 저장 되어있는 데이터베이스를 변경할 필요 없이 함수같은 동작의 변경에만 관심있다면 proxy model이 적합하다. 정렬방법, custom manages, user model methods 등. 두번 째로 User모델과 관련된 추가적인 정보를 저장하고 싶다면 OneToOne필드가 좋다.  보통 profile model이라고 일컬어진다. 

```python
from django.contrib.auth.models import User

class Employee(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    department = models.CharField(max_length=100)
```

> Assuming an existing Employee Fred Smith who has both a User and Employee model, you can access the related information using Django’s standard related model conventions:

```python
u = User.objects.get(username='fsmith')
freds_department = u.employee.department
```


# Substituting a custom User model
[Customizing authentication in Django | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/auth/customizing/#substituting-a-custom-user-model)

> Some kinds of projects may have authentication requirements for which Django’s built-in  [User](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User)  model is not always appropriate. For instance, on some sites it makes more sense to use an email address as your identification token instead of a username.
> Django allows you to override the default user model by providing a value for the  [AUTH_USER_MODEL](https://docs.djangoproject.com/en/2.2/ref/settings/#std:setting-AUTH_USER_MODEL)  setting that references a custom model:

장고 빌트인 User모델이 적합하지 않은 경우들도 많다. user를 식별할 토큰으로 username 대신 email이 필요할수도 있다. 장고에서는 AUTH_USER_MODEL을 통해 override할 수 있다. 

settings.py 의 AUTH_USER_MODEL
```python
AUTH_USER_MODEL = 'myapp.MyUser'
```
Default: ’auth.User’


You cannot change the AUTH_USER_MODEL setting during the lifetime of a project (i.e. once you have made and migrated models that depend on it) without serious effort. It is intended to be set at the project start, and the model it refers to must be available in the first migration of the app that it lives in. See  [Substituting a custom User model](https://docs.djangoproject.com/en/2.2/topics/auth/customizing/#auth-custom-user)  for more details.


## Using a custom user model when starting a project
> If you’re starting a new project, it’s highly recommended to set up a custom user model, even if the default  [User](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User)  model is sufficient for you. This model behaves identically to the default user model, but you’ll be able to customize it in the future if the need arises:

지금 당장은 장고에서 기본으로 제공하는 USER모델로 충분하다 하더라도 아래와 같이 코드를 작성하는것이 좋다. 추후에 커스텀하기 편하다. 

```python
from django.contrib.auth.models import AbstractUser
class User(AbstractUser):
    pass
```

# Referencing theUsermodel
> If you reference  [User](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User)  directly (for example, by referring to it in a foreign key), your code will not work in projects where the  [AUTH_USER_MODEL](https://docs.djangoproject.com/en/2.2/ref/settings/#std:setting-AUTH_USER_MODEL)  setting has been changed to a different user model.

> Instead of referring to  [User](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User)  directly, you should reference the user model using django.contrib.auth.get_user_model(). This method will return the currently active user model – the custom user model if one is specified, or  [User](https://docs.djangoproject.com/en/2.2/ref/contrib/auth/#django.contrib.auth.models.User)  otherwise.

USER모델을 직접 참조하기 보다는 'get_user_model()' 함수를 이용해서 참조하는것이 좋다. 

> When you define a foreign key or many-to-many relations to the user model, you should specify the custom model using the  [AUTH_USER_MODEL](https://docs.djangoproject.com/en/2.2/ref/settings/#std:setting-AUTH_USER_MODEL)  setting. For example:

user 모델을 재정의 하거나 FK, MTM관계를 가지고 있는것으로 정의했다면 settings.py에 있는 AUTH_USER_MODEL값을 설정해줘야한다.  사용방법은 아래와 같다. 

```python
from django.conf import settings
from django.db import models

class Article(models.Model):
    author = models.ForeignKey(
        settings.AUTH_USER_MODEL,
        on_delete=models.CASCADE,
    )
```


#  Specifying a custom user model
> Keeping all user related information in one model removes the need for additional or more complex database queries to retrieve related models. On the other hand, it may be more suitable to store app-specific user information in a model that has a relation with your custom user model. That allows each app to specify its own user data requirements without potentially conflicting or breaking assumptions by other apps. It also means that you would keep your user model as simple as possible, focused on authentication, and following the minimum requirements Django expects custom user models to meet.

하나의 모델에 유저 모델을 모두 유지한다면 추가적인 DB쿼리는 필요가 없다. 하지만 유저정보를 다른 관계에 있는 커스텀 유저모델에 저장하는것이 좋다. 다른앱들과 충돌없이 각 앱의 특성에 맞게 구체화할 수 있기 때문이다. 

> The easiest way to construct a compliant custom user model is to inherit from  [AbstractBaseUser](https://docs.djangoproject.com/en/2.2/topics/auth/customizing/#django.contrib.auth.models.AbstractBaseUser) .  [AbstractBaseUser](https://docs.djangoproject.com/en/2.2/topics/auth/customizing/#django.contrib.auth.models.AbstractBaseUser)  provides the core implementation of a user model, including hashed passwords and tokenized password resets. You must then provide some key implementation details:

```python
class MyUser(AbstractBaseUser):
    identifier = models.CharField(max_length=40, unique=True)
    ...
    USERNAME_FIELD = 'identifier'
```


