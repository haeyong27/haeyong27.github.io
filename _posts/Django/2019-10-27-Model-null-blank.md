---
layout: post
title:  "Django Model option null, blank(string-based fields)"
date:   2019-10-27
categories: Django
tag: [Django, Database]
---

- [documentation](#documentation)
  - [null](#null)
  - [blank](#blank)
- [stackoverflow](#stackoverflow)
  - [1](#1)
  - [2](#2)
    - [null=False, blank=False](#nullfalse-blankfalse)
    - [null=True, blank=True](#nulltrue-blanktrue)
    - [null=False, blank=True](#nullfalse-blanktrue)
    - [null=True, blank=False](#nulltrue-blankfalse)


# documentation
## null
[Model field reference | Django documentation | Django](https://docs.djangoproject.com/en/2.2/ref/models/fields/#null)

>*If True, Django will store empty values as NULL in the database. Default is False.*

>*Avoid using null on string-based fields such as CharField and TextField. If a string-based field has null=True, that means it has two possible values for “no data”: NULL, and the empty string. In most cases, it’s redundant to have two possible values for “no data;” the Django convention is to use the empty string, not NULL. One exception is when a CharField has both unique=True and blank=True set. In this situation, null=True is required to avoid unique constraint violations when saving multiple objects with blank values.*

string-based fields인 charfield와 textfiled는 null을 사용하는것을 피해야한다. 만약 null값이 true라면 두가지 가능한 경우가 있다. null값과 ‘’ 빈 문자열. 장고에서는 널 값이 아닌 빈 문자열로 인식하는것이 convention 이다. 하나의 예외가 있다면 charfield가 unique=true, blank=true일 때 이다. 이 상황에서 빈 값이 있는 여러개의 객채를 저장할 때 unique constraint violations를 피하고 싶다면 null=true로 줘야한다. 

---

PRIMARY KEY 제약 조건과 달리 UNIQUE 제약 조건에서는 NULL 값이 허용된다.. 단 UNIQUE 제약 조건에서 사용되는 다른 값과 마찬가지로 Null 값도 열당 하나만 허용된다.. UNIQUE 제약 조건은 FOREIGN KEY 제약 조건에서 참조할 수 있다.

출처 : [UNIQUE 제약 조건 및 CHECK 제약 조건 Microsoft Docs](https://docs.microsoft.com/ko-kr/sql/relational-databases/tables/unique-constraints-and-check-constraints?view=sql-server-ver15#Unique)

---



>*For both string-based and non-string-based fields, you will also need to set blank=True if you wish to permit empty values in forms, as the null parameter only affects database storage (see blank).*

어떤 경우에서든 빈 값을 입력받고 싶다면 blank=True로 설정해줘야한다. 


## blank

[Model field reference](https://docs.djangoproject.com/en/2.2/ref/models/fields/#blank)

>*If True, the field is allowed to be blank. Default is False.*

>*Note that this is different than null. null is purely database-related, whereas blank is validation-related. If a field has blank=True, form validation will allow entry of an empty value. If a field has blank=False, the field will be required.*

blank는 null과는 다르다. null은 database와 관련된것에 비해 blank는 유효성을 검사하는 용도이다. blank=true라면 빈값에 대해서 form validation을 통과할 것이다.

# stackoverflow

## 1
[python - differentiate null=True, blank=True in django - Stack Overflow](https://stackoverflow.com/questions/8609192/differentiate-null-true-blank-true-in-django)

>*CHAR and TEXT types are never saved as NULL by Django, so null=True is unnecessary. However, you can manually set one of these fields to None to force set it as NULL. If you have a scenario where that might be necessary, you should still include null=True.*

```python
models.DateTimeField(blank=True) #*raises IntegrityError if blank*

models.DateTimeField(null=True) #*NULL allowed, but must be filled out in a form*
```

>*Obviously those two options don’t make logical sense to use (though, there might be a use case for null=True, blank=False if you want a field to always be required in forms, but optional when dealing with an object through something like the shell.)*


```python
models.CharField(blank=True) #*No problem, blank is stored as ''*

models.CharField(null=True) #*NULL allowed, but will never be set as NULL*
```

>*CHAR and TEXT types are never saved as NULL by Django, so null=True is unnecessary. However, you can manually set one of these fields to None to force set it as NULL. If you have a scenario where that might be necessary, you should still include null=True.*


장고에서 char, text는 절대 null로 저장되지 않는다. 강제로 null로 지정해야 한다면 null=true로 지정해야한다.

## 2

### null=False, blank=False 
> This is the default configuration and means that the value is required in all circumstances.


### null=True, blank=True
> This means that the field is optional in all circumstances. (As noted below, though, this is not the recommended way to make string-based fields optional.)

string-based fields에는 적합하지 않은 옵션이다. 

### null=False, blank=True
> This means that the form doesn’t require a value but the database does. There are a number of use cases for this:

form에 값을 입력하지 않지만 데이터베이스에서는 값이 필요한 경우이다. 몇몇 사용예는 다음과 같다. 

> The most common use is for optional string-based fields. As noted in the documentation, the Django idiom is to use the empty string to indicate a missing value. If NULL was also allowed you would end up with two different ways to indicate a missing value.

장고에서는 빈 문자열로 누락된 값을 나타낸다. 만약 null이 허용된다면 결측값에 대해 두가지 의미를 가지게 된다. 모호해지게된다. 


> Another common situation is that you want to calculate one field automatically based on the value of another (in your save() method, say). You don’t want the user to provide the value in a form (hence blank=True), but you do want the database to enforce that a value is always provided (null=False).

유저가 입력하는 것이 아니라 개발자가 원하는 값으로 넣고 싶은경우.
(ex. 글 작성한 시간, 회원가입 날짜)

> Another use is when you want to indicate that a ManyToManyField is optional. Because this field is implemented as a separate table rather than a database column, null is meaningless. The value of blank will still affect forms, though, controlling whether or not validation will succeed when there are no relations.


### null=True, blank=False
> This means that the form requires a value but the database doesn’t. This may be the most infrequently used configuration, but there are some use cases for it:

이런 경우는 잘 없다. 

> It’s perfectly reasonable to require your users to always include a value even if it’s not actually required by your business logic. After all, forms are only one way of adding and editing data. You may have code that is generating data which doesn’t need the same stringent validation that you want to require of a human editor.

> Another use case that I’ve seen is when you have a ForeignKey for which you don’t wish to allow cascade deletion. That is, in normal use the relation should always be there (blank=False), but if the thing it points to happens to be deleted, you don’t want this object to be deleted too. In that case you can use null=True and on_delete=models.SET_NULL to implement a simple kind of soft deletion.

