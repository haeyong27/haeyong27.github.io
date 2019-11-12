---
layout: post
title:  "Django ORM create, save"
date:   2019-11-12
categories: Django
tag: [Django]
---




- [Django ORM (create, save)](#django-orm-create-save)
  - [Model](#model)
  - [Making Queries](#making-queries)
    - [Creating objects](#creating-objects)
      - [Overriding predefined model methods](#overriding-predefined-model-methods)
      - [Overridden model methods are not called on bulk operations](#overridden-model-methods-are-not-called-on-bulk-operations)
    - [Saving changes to objects](#saving-changes-to-objects)
    - [Saving ForeignKey and ManyToManyField fields](#saving-foreignkey-and-manytomanyfield-fields)
      - [Foreign Key](#foreign-key)
      - [ManyToMany](#manytomany)

# Django ORM (create, save)
[Models and databases | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/db/)
> A model is the single, definitive source of data about your data. It contains the essential fields and behaviors of the data you’re storing. Generally, each model maps to a single database table.

모델은 데이터의 필드와 동작을 가지고있다. 모델은 데이터 베이스의 각 테이블과 매칭된다. 

## Model
## Making Queries
> Once you’ve created your  [data models](https://docs.djangoproject.com/en/2.2/topics/db/models/) , Django automatically gives you a database-abstraction API that lets you create, retrieve, update and delete objects. This document explains how to use this API. Refer to the  [data model reference](https://docs.djangoproject.com/en/2.2/ref/models/)  for full details of all the various model lookup options.

일단 데이터 모델을 생성하면 장고에서 자동으로 데이터베이스 추상화 API를 제공한다. 이는 객체를 생성, 조회, 업데이트, 삭제를 할 수 있다. 

> Throughout this guide (and in the reference), we’ll refer to the following models, which comprise a Weblog application:

```python
from django.db import models

class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()

    def __str__(self):
        return self.name

class Author(models.Model):
    name = models.CharField(max_length=200)
    email = models.EmailField()

    def __str__(self):
        return self.name

class Entry(models.Model):
    blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
    headline = models.CharField(max_length=255)
    body_text = models.TextField()
    pub_date = models.DateField()
    mod_date = models.DateField()
    authors = models.ManyToManyField(Author)
    number_of_comments = models.IntegerField()
    number_of_pingbacks = models.IntegerField()
    rating = models.IntegerField()

    def __str__(self):
        return self.headline

```


### Creating objects
> To represent database-table data in Python objects, Django uses an intuitive system: A model class represents a database table, and an instance of that class represents a particular record in the database table.
> To create an object, instantiate it using keyword arguments to the model class, then call  [save()](https://docs.djangoproject.com/en/2.2/ref/models/instances/#django.db.models.Model.save)  to save it to the database.
> Assuming models live in a file **mysite/blog/models.py**, here’s an example:

키워드 인자를 사용하여 인스턴스화한 뒤 save()를 호출하여 디비에 저장한다.
```python
>>> from blog.models import Blog
>>> b = Blog(name='Beatles Blog', tagline='All the latest Beatles news.')
>>> b.save()
```

> This performs an **INSERT** SQL statement behind the scenes. Django doesn’t hit the database until you explicitly call  [save()](https://docs.djangoproject.com/en/2.2/ref/models/instances/#django.db.models.Model.save) .

이는 insert SQL을 수행한다. save를 호출하기 전까지 데이터베이스와 통신하지 않는다. 

#### Overriding predefined model methods
[Models | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/db/models/#overriding-predefined-model-methods)

> There’s another set of  [model methods](https://docs.djangoproject.com/en/2.2/ref/models/instances/#model-instance-methods)  that encapsulate a bunch of database behavior that you’ll want to customize. In particular you’ll often want to change the way  [save()](https://docs.djangoproject.com/en/2.2/ref/models/instances/#django.db.models.Model.save)  and  [delete()](https://docs.djangoproject.com/en/2.2/ref/models/instances/#django.db.models.Model.delete)  work.

```python
class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()

    def save(self, *args, **kwargs):
        do_something()
        super().save(*args, **kwargs)  # Call the "real" save() method.
        do_something_else()
```

> It’s important to remember to call the superclass method – that’s that **super().save(*args, **kwargs)** business – to ensure that the object still gets saved into the database. If you forget to call the superclass method, the default behavior won’t happen and the database won’t get touched.

super().save()를 호출하지 않으면 원래 save()가 제대로 작동하지 않는다. 꼭 하길.

#### Overridden model methods are not called on bulk operations
> Note that the  [delete()](https://docs.djangoproject.com/en/2.2/ref/models/instances/#django.db.models.Model.delete)  method for an object is not necessarily called when  [deleting objects in bulk using a QuerySet](https://docs.djangoproject.com/en/2.2/topics/db/queries/#topics-db-queries-delete)  or as a result of a  [cascading delete](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.ForeignKey.on_delete) . To ensure customized delete logic gets executed, you can use  [pre_delete](https://docs.djangoproject.com/en/2.2/ref/signals/#django.db.models.signals.pre_delete)  and/or  [post_delete](https://docs.djangoproject.com/en/2.2/ref/signals/#django.db.models.signals.post_delete)  signals.
> Unfortunately, there isn’t a workaround when  [creating](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet.bulk_create)  or  [updating](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet.update)  objects in bulk, since none of  [save()](https://docs.djangoproject.com/en/2.2/ref/models/instances/#django.db.models.Model.save) ,  [pre_save](https://docs.djangoproject.com/en/2.2/ref/signals/#django.db.models.signals.pre_save) , and  [post_save](https://docs.djangoproject.com/en/2.2/ref/signals/#django.db.models.signals.post_save)  are called.

하지만 대량의 작업에서는 재정의된 함수가 호출되지 않는다. 가능하면 커스텀하지 말아야겠다. 



### Saving changes to objects
[Making queries | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/db/queries/#saving-changes-to-objects)

> To save changes to an object that’s already in the database, use  [save()](https://docs.djangoproject.com/en/2.2/ref/models/instances/#django.db.models.Model.save) .
> Given a **Blog** instance **b5** that has already been saved to the database, this example changes its name and updates its record in the database:

디비에 있는 객체를 바꿀 때 save()사용.
```python
>>> b5.name = 'New name'
>>> b5.save()
```

> This performs an **UPDATE** SQL statement behind the scenes. Django doesn’t hit the database until you explicitly call  [save()](https://docs.djangoproject.com/en/2.2/ref/models/instances/#django.db.models.Model.save) .
update역할을 한다. 


### Saving ForeignKey and ManyToManyField fields
[Making queries | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/db/queries/#saving-foreignkey-and-manytomanyfield-fields)


#### Foreign Key
> Updating a  [ForeignKey](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.ForeignKey)  field works exactly the same way as saving a normal field – simply assign an object of the right type to the field in question. This example updates the **blog** attribute of an **Entry** instance **entry**, assuming appropriate instances of **Entry** and **Blog** are already saved to the database (so we can retrieve them below):

FK update도 일반적인 update와 동일하다. 다음은 Entry 인스턴스의 blog 속성을 업데이트하는 예이다. 
```python
>>> from blog.models import Blog, Entry
>>> entry = Entry.objects.get(pk=1)
>>> cheese_blog = Blog.objects.get(name="Cheddar Talk")
>>> entry.blog = cheese_blog
>>> entry.save()
```

#### ManyToMany
Updating a  [ManyToManyField](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.ManyToManyField)  works a little differently – use the  [add()](https://docs.djangoproject.com/en/2.2/ref/models/relations/#django.db.models.fields.related.RelatedManager.add)  method on the field to add a record to the relation. This example adds the **Author** instance **joe** to the **entry** object:  

M2M는 조금 다르다. add()를 사용한다. 
```python
>>> from blog.models import Author
>>> joe = Author.objects.create(name="Joe")
>>> entry.authors.add(joe)
```
자세한건 여기서 : [Related objects reference | Django documentation | Django](https://docs.djangoproject.com/en/2.2/ref/models/relations/#related-objects-reference)

> To add multiple records to a  [ManyToManyField](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.ManyToManyField)  in one go, include multiple arguments in the call to  [add()](https://docs.djangoproject.com/en/2.2/ref/models/relations/#django.db.models.fields.related.RelatedManager.add) , like this:  


M2M필드에서 여러 인스턴스를 한번에 저장하려면 다음과같이 

```python
>>> john = Author.objects.create(name="John")
>>> paul = Author.objects.create(name="Paul")
>>> george = Author.objects.create(name="George")
>>> ringo = Author.objects.create(name="Ringo")
>>> entry.authors.add(john, paul, george, ringo)
```