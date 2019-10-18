---
layout: post
title:  "Django Model"
date:   2019-10-19
categories: Django
tag: [Django, model]
---

- [Django Model](#django-model)
- [Overview](#overview)
- [Relationships](#relationships)
  - [Many-to-one relationships](#many-to-one-relationships)
  - [Many-to-many relationships](#many-to-many-relationships)
  - [One-to-one relationships](#one-to-one-relationships)
- [Meta options](#meta-options)
- [Model methods](#model-methods)
- [Model inheritance](#model-inheritance)
  - [Abstract base classes](#abstract-base-classes)

# Django Model
참고 : https://docs.djangoproject.com/en/2.2/topics/db/models/

# Overview
> A model is the single, definitive source of information about your data. It contains the essential fields and behaviors of the data you’re storing. Generally, each model maps to a single database table.

모델은 데이터에 대한 정보 소스 입니다. 필드와 데이터의 동작이 포함되어 있습니다. 일반적으로 각 모델은 하나의 데이터베이스 테이블에 매칭됩니다. 

* Each model is a Python class that subclasses  [django.db.models.Model](https://docs.djangoproject.com/en/2.2/ref/models/instances/#django.db.models.Model) .
* Each attribute of the model represents a database field.
* With all of this, Django gives you an automatically-generated database-access API; see  [Making queries](https://docs.djangoproject.com/en/2.2/topics/db/queries/) .

```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```

* The name of the table, **myapp_person**, is automatically derived from some model metadata but can be overridden. See  [Table names](https://docs.djangoproject.com/en/2.2/ref/models/options/#table-names)  for more details.
* An **id** field is added automatically, but this behavior can be overridden. See  [Automatic primary key fields](https://docs.djangoproject.com/en/2.2/topics/db/models/#automatic-primary-key-fields) .
* The **CREATE TABLE** SQL in this example is formatted using PostgreSQL syntax, but it’s worth noting Django uses SQL tailored to the database backend specified in your  [settings file](https://docs.djangoproject.com/en/2.2/topics/settings/) .

앱 이름이 myapp, 모델 이름이 person이라면 데이터 베이스에 저장되는 테이블에 이름은 mysqpp_person이 됩니다. id 필드는 자동으로 추가되며 오버라이드 할 수 있습니다. 

# Relationships
## Many-to-one relationships
> To define a many-to-one relationship, use  [django.db.models.ForeignKey](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.ForeignKey) . You use it just like any other  [Field](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.Field)  type: by including it as a class attribute of your model.
>  [ForeignKey](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.ForeignKey)  requires a positional argument: the class to which the model is related.

```python
from django.db import models

class Manufacturer(models.Model):
    # ...
    pass

class Car(models.Model):
    manufacturer = models.ForeignKey(Manufacturer, on_delete=models.CASCADE)
    # ...
```

> [ForeignKey](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.ForeignKey)  fields accept a number of extra arguments which are explained in  [the model field reference](https://docs.djangoproject.com/en/2.2/ref/models/fields/#foreign-key-arguments) . These options help define how the relationship should work; all are optional.

## Many-to-many relationships
For example, if a **Pizza** has multiple **Topping** objects – that is, a **Topping** can be on multiple pizzas and each **Pizza** has multiple toppings – here’s how you’d represent that:
```python
from django.db import models

class Topping(models.Model):
    # ...
    pass

class Pizza(models.Model):
    # ...
    toppings = models.ManyToManyField(Topping)
```

## One-to-one relationships
> This is most useful on the primary key of an object when that object “extends” another object in some way.
> For example, if you were building a database of “places”, you would build pretty standard stuff such as address, phone number, etc. in the database. Then, if you wanted to build a database of restaurants on top of the places, instead of repeating yourself and replicating those fields in the **Restaurant** model, you could make **Restaurant** have a  [OneToOneField](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.OneToOneField)  to **Place** (because a restaurant “is a” place; in fact, to handle this you’d typically use  [inheritance](https://docs.djangoproject.com/en/2.2/topics/db/models/#model-inheritance) , which involves an implicit one-to-one relation).
> As with  [ForeignKey](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.ForeignKey) , a  [recursive relationship](https://docs.djangoproject.com/en/2.2/ref/models/fields/#recursive-relationships)  can be defined and  [references to as-yet undefined models](https://docs.djangoproject.com/en/2.2/ref/models/fields/#lazy-relationships)  can be made.

```python
from django.db import models

class Place(models.Model):
    name = models.CharField(max_length=50)
    address = models.CharField(max_length=80)

    def __str__(self):
        return "%s the place" % self.name

class Restaurant(models.Model):
    place = models.OneToOneField(
        Place,
        on_delete=models.CASCADE,
        primary_key=True,
    )
    serves_hot_dogs = models.BooleanField(default=False)
    serves_pizza = models.BooleanField(default=False)

    def __str__(self):
        return "%s the restaurant" % self.place.name
```
place라는 데이터 베이스를 만들 때 주소나 전화번호 같은것으로 특정지을 수 있지만, Restaurant과 같은 모델을 하나 더 만들어 place를 표현할 수 있다. 

# Meta options
> Give your model metadata by using an inner class Meta, like so:

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

메타데이터 모델은 “필드가 아닌 모든”것입니다.  정렬 순서라든가, 데이터베이스 테이블 이름, 사람이 읽기 편한 이름 등등.

# Model methods
참고 : [Models | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/db/models/#model-methods)

> **objects**
> The most important attribute of a model is the  [Manager](https://docs.djangoproject.com/en/2.2/topics/db/managers/#django.db.models.Manager) . It’s the interface through which database query operations are provided to Django models and is used to  [retrieve the instances](https://docs.djangoproject.com/en/2.2/topics/db/queries/#retrieving-objects)  from the database. If no custom **Manager** is defined, the default name is  [objects](https://docs.djangoproject.com/en/2.2/ref/models/class/#django.db.models.Model.objects) . Managers are only accessible via model classes, not the model instances.


# Model inheritance
## Abstract base classes
> Abstract base classes are useful when you want to put some common information into a number of other models. You write your base class and put **abstract=True** in the  [Meta](https://docs.djangoproject.com/en/2.2/topics/db/models/#meta-options)  class. This model will then not be used to create any database table. Instead, when it is used as a base class for other models, its fields will be added to those of the child class.
An example:

```python
from django.db import models

class CommonInfo(models.Model):
    name = models.CharField(max_length=100)
    age = models.PositiveIntegerField()

    class Meta:
        abstract = True		
        ordering = ['name']

class Student(CommonInfo):
    home_group = models.CharField(max_length=5)
```

> The **Student** model will have three fields: **name**, **age** and **home_group**. The **CommonInfo** model cannot be used as a normal Django model, since it is an abstract base class. 

Student 모델은 3개의 필드를 가지게 됩니다. name, age, home_group.
CommonInfo 모델은 일반적인 장고 모델처럼 사용될 수 없습니다. 추상화 기반의 클래스 이기 때문입니다. 

 

---
19 Oct 2019 12:08 AM