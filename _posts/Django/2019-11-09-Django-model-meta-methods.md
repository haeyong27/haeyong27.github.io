---
layout: post
title:  "Django Model Meta, attribute, methods (해시태그 기능을 위한 공부)"
date:   2019-11-09
categories: Django
tag: [Django]
---

- [Django model - Meta, attribute, methods](#django-model---meta-attribute-methods)
  - [1. Meta](#1-meta)
  - [2. Model attributes (Manager)](#2-model-attributes-manager)
    - [Custom managers](#custom-managers)
      - [Adding extra manager methods](#adding-extra-manager-methods)
  - [3. Model methods](#3-model-methods)
  - [결론](#%ea%b2%b0%eb%a1%a0)
    - [요약](#%ec%9a%94%ec%95%bd)
    - [내 프로젝트에 적용](#%eb%82%b4-%ed%94%84%eb%a1%9c%ec%a0%9d%ed%8a%b8%ec%97%90-%ec%a0%81%ec%9a%a9)

# Django model - Meta, attribute, methods



## 1. Meta
[Models | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/db/models/#meta-options)

```python
from django.db import models

class Ox(models.Model):
    horn_length = models.IntegerField()

    class Meta:
        ordering = ["horn_length"]
        verbose_name_plural = "oxen"
```

> Model metadata is “anything that’s not a field”, such as ordering options ( [ordering](https://docs.djangoproject.com/en/2.2/ref/models/options/#django.db.models.Options.ordering) ), database table name ( [db_table](https://docs.djangoproject.com/en/2.2/ref/models/options/#django.db.models.Options.db_table) ), or human-readable singular and plural names ( [verbose_name](https://docs.djangoproject.com/en/2.2/ref/models/options/#django.db.models.Options.verbose_name)  and  [verbose_name_plural](https://docs.djangoproject.com/en/2.2/ref/models/options/#django.db.models.Options.verbose_name_plural) ). None are required, and adding **class Meta** to a model is completely optional.
> A complete list of all possible **Meta** options can be found in the  [model option reference](https://docs.djangoproject.com/en/2.2/ref/models/options/) .
> 
모델 메타데이터는 필드가 아닌 모든것을 뜻합니다. 예를들어 정렬옵션, db table이름, 사람이 읽기 편한 이름 등이 있습니다. 필수는 아니고 옵션입니다. 관련 옵션들은 [여기](https://docs.djangoproject.com/en/2.2/ref/models/options/)에 있습니다. 

## 2. Model attributes (Manager)
[Models | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/db/models/#model-attributes)

**objects**
> The most important attribute of a model is the  [Manager](https://docs.djangoproject.com/en/2.2/topics/db/managers/#django.db.models.Manager) . It’s the interface through which database query operations are provided to Django models and is used to  [retrieve the instances](https://docs.djangoproject.com/en/2.2/topics/db/queries/#retrieving-objects)  from the database. If no custom **Manager** is defined, the default name is  [objects](https://docs.djangoproject.com/en/2.2/ref/models/class/#django.db.models.Model.objects) . Managers are only accessible via model classes, not the model instances.

모델의 가장 중요한 속성중 하나는 [Manager](https://docs.djangoproject.com/en/2.2/topics/db/managers/#django.db.models.Manager) 입니다. 이 인터페이스를 통해 Django models로 쿼리작업이 이루어지고 데이터베이스로부터 인스턴스를 받아올 수 있습니다. Manager를 따로 정해주지 않으면 디폴트 이름값은 objects입니다. models instances가 아닌 model class를 통해서만 접근할 수 있습니다. 

### Custom managers
[Managers | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/db/managers/#custom-managers)

> There are two reasons you might want to customize a **Manager**: to add extra **Manager** methods, and/or to modify the initial **QuerySet** the **Manager** returns.

커스텀하는 이유
1. manager methods 추가
2. initail queryset 수정 

#### Adding extra manager methods
[Managers | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/db/managers/#adding-extra-manager-methods)



> Adding extra **Manager** methods is the preferred way to add “table-level” functionality to your models. (For “row-level” functionality – i.e., functions that act on a single instance of a model object – use  [Model methods](https://docs.djangoproject.com/en/2.2/topics/db/models/#model-methods) , not custom **Manager** methods.)
> A custom **Manager** method can return anything you want. It doesn’t have to return a **QuerySet**.
> For example, this custom **Manager** offers a method **with_counts()**, which returns a list of all **OpinionPoll** objects, each with an extra **num_responses** attribute that is the result of an aggregate query:

```python
from django.db import models

class PollManager(models.Manager):
    def with_counts(self):
        from django.db import connection
        with connection.cursor() as cursor:
            cursor.execute("""
                SELECT p.id, p.question, p.poll_date, COUNT(*)
                FROM polls_opinionpoll p, polls_response r
                WHERE p.id = r.poll_id
                GROUP BY p.id, p.question, p.poll_date
                ORDER BY p.poll_date DESC""")
            result_list = []
            for row in cursor.fetchall():
                p = self.model(id=row[0], question=row[1], poll_date=row[2])
                p.num_responses = row[3]
                result_list.append(p)
        return result_list

class OpinionPoll(models.Model):
    question = models.CharField(max_length=200)
    poll_date = models.DateField()
    objects = PollManager()

class Response(models.Model):
    poll = models.ForeignKey(OpinionPoll, on_delete=models.CASCADE)
    person_name = models.CharField(max_length=50)
    response = models.TextField()

```


## 3. Model methods
[Models | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/db/models/#model-methods)

> Define custom methods on a model to add custom “row-level” functionality to your objects. Whereas  [Manager](https://docs.djangoproject.com/en/2.2/topics/db/managers/#django.db.models.Manager)  methods are intended to do “table-wide” things, model methods should act on a particular model instance.
> This is a valuable technique for keeping business logic in one place – the model.
> For example, this model has a few custom methods:

모델의 객체에서 row level 수준으로 동작할 custom methods를 정의할 수 있다. Manager methods는 table wide인 반면 model methodssms 특정 모델 인스턴스에서 작동한다. 이는 비즈니스 로직에서 중요한 기술이다. 

```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    birth_date = models.DateField()

    def baby_boomer_status(self):
        "Returns the person's baby-boomer status."
        import datetime
        if self.birth_date < datetime.date(1945, 8, 1):
            return "Pre-boomer"
        elif self.birth_date < datetime.date(1965, 1, 1):
            return "Baby boomer"
        else:
            return "Post-boomer"

    @property
    def full_name(self):
        "Returns the person's full name."
        return '%s %s' % (self.first_name, self.last_name)
```




## 결론
### 요약
1. model attribute (manager) : 하나의 모델, 하나의 테이블, 모델의 인스턴스에 국한된 속성
```python
Book.dahl_objects.all()
Book.dahl_objects.filter(title='Matilda')
Book.dahl_objects.count()
```

2. model method : low level부터 접근가능, 다른 모델과 연계해서 사용할 수 있다. 
```python
class Person(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    birth_date = models.DateField()

    def baby_boomer_status(self):
        "Returns the person's baby-boomer status."
        import datetime
        if self.birth_date < datetime.date(1945, 8, 1):
            return "Pre-boomer"
        elif self.birth_date < datetime.date(1965, 1, 1):
            return "Baby boomer"
        else:
            return "Post-boomer"
```


### 내 프로젝트에 적용
내가 작업하고 있는 인스타그램 클론프로젝트의 Django : models.py의 일부분이다. 
```python
class Post(models.Model):
		...
    content = models.TextField(blank=True)
    tag_set = models.ManyToManyField("Tag")

class Tag(models.Model):
    name = models.CharField(max_length=100, unique=True)
```


내가 구현하려 했던 기능은 
1. Post를 작성하고 저장하면서 내용에서 해시태그를 추출
2. 추출한 태그를 자동으로 Post table의 tag_set 필드에 저장. 

model methods만 활용하면 충분하다고 판단했고 간략하게 다음과 같이 코딩할 예정이다.

```python
class Post(models.Model):
		...
    content = models.TextField(blank=True)
    tag_set = models.ManyToManyField("Tag")

	  def set_tags(self):
		  tag_set = self.content에서 tag 추출
		  t = Tag.objects.get_or_create(name=tag_set)
		  self.tag_set.add(t)
```



#study/programming/django
9 Nov 2019 3:51 PM
