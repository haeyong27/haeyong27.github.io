---
layout: post
title:  "Django ORM retrieve"
date:   2019-11-12
categories: Django
tag: [Django]
---
- [Django ORM (retrieve)](#django-orm-retrieve)
  - [Retrieving specific objects with filters](#retrieving-specific-objects-with-filters)
  - [Lookups that span relationships](#lookups-that-span-relationships)

# Django ORM (retrieve)
[Making queries | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/db/queries/#retrieving-objects)

> To retrieve objects from your database, construct a  [QuerySet](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet)  via a  [Manager](https://docs.djangoproject.com/en/2.2/topics/db/managers/#django.db.models.Manager)  on your model class.
디비에서 객체를 가져오려면 모델의 manager를 통해 queryset을 만들면 된다. 

> A  [QuerySet](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet)  represents a collection of objects from your database. It can have zero, one or many *filters*. Filters narrow down the query results based on the given parameters. In SQL terms, a  [QuerySet](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet)  equates to a SELECT statement, and a filter is a limiting clause such as WHERE or LIMIT.

데이터베이스에서 가져온 객체들의 모음을 표현하는것이 queryset이다. 0개부터 여러개의 filter를 가질 수 있다. 필터는 주어진 파라미터들을 통해 쿼리 결과를 좁혀나간다. queryset은 selete이고, 필터는 where, limit절이라고 생각하면된다. 

> You get a  [QuerySet](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet)  by using your model’s  [Manager](https://docs.djangoproject.com/en/2.2/topics/db/managers/#django.db.models.Manager) . Each model has at least one  [Manager](https://docs.djangoproject.com/en/2.2/topics/db/managers/#django.db.models.Manager) , and it’s called  [objects](https://docs.djangoproject.com/en/2.2/ref/models/class/#django.db.models.Model.objects)  by default. Access it directly via the model class, like so:

각 모델마나 하나 이상의 manager를 가지고 있다. 이름은 기본값으로 objects이다. 모델클레스를 통해 바로 접근할 수 있다. 
```python
>>> Blog.objects
<django.db.models.manager.Manager object at ...>
>>> b = Blog(name='Foo', tagline='Bar')
>>> b.objects
Traceback:
    ...
AttributeError: "Manager isn't accessible via Blog instances."
```
> Managers are accessible only via model classes, rather than from model instances, to enforce a separation between “table-level” operations and “record-level” operations.
managers는 모델 인스턴스가 아닌 모델 클래스를 통해서만 접근할 수 있다. tabel level의 수행과 record level의 수행을 구분하기 위함이다. 

## Retrieving specific objects with filters
> The  [QuerySet](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet)  returned by  [all()](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet.all)  describes all objects in the database table. Usually, though, you’ll need to select only a subset of the complete set of objects.
> To create such a subset, you refine the initial  [QuerySet](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet) , adding filter conditions. The two most common ways to refine a  [QuerySet](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet)  are:

> filter(kwargs)
> Returns a new  [QuerySet](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet)  containing objects that match the given lookup parameters.

> exclude(kwargs)
> Returns a new  [QuerySet](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet)  containing objects that do *not* match the given lookup parameters.


> The lookup parameters (kwargs in the above function definitions) should be in the format described in  [Field lookups](https://docs.djangoproject.com/en/2.2/topics/db/queries/#field-lookups)  below.
loopup 파라미터는 다음과 같은 형식이어야 합니다. 

> Field lookups are how you specify the meat of an SQL WHERE clause. They’re specified as keyword arguments to the  [QuerySet](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet)  methods  [filter()](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet.filter) ,  [exclude()](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet.exclude)  and  [get()](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet.get) .

field lookups는 sql where를 어떻게 구체화 해야하는지에 대한 표준이다. queryset의 filter, exclude, get과 같은 함수에 키워드 인자로 구체화한다. 

> Basic lookups keyword arguments take the form field__lookuptype=value. (That’s a double-underscore). For example:

```python
Entry.objects.filter(pub_date__lte='2006-01-01')
```
translates (roughly) into the following SQL  
SELECT * FROM blog_entry WHERE pub_date <= ‘2006-01-01’;

> The field specified in a lookup has to be the name of a model field. There’s one exception though, in case of a  [ForeignKey](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.ForeignKey)  you can specify the field name suffixed with _id. In this case, the value parameter is expected to contain the raw value of the foreign model’s primary key. For example:
룩업에 명시된 필드는 모델 필드의 이름이어야 한다. 하나의 예외가 있는게 FK인 경우이다. 접두사로 ‘_id’를 붙인다. 이런 경우에는 외래 모델의 프라이머리키의 raw값을 기대한다. 
```python
Entry.objects.filter(blog_id=4)
```

```python
>>> Blog.objects.get(id__exact=14)  # Explicit form
>>> Blog.objects.get(id=14)         # __exact is implied
>>> Blog.objects.get(name__iexact="beatles blog")
```
iexact : A case-insensitive match. So, the query  
Would match a Blog titled ”Beatles Blog”, ”beatles blog”, or even ”BeAtlES blOG”.

## Lookups that span relationships
[Making queries | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/db/queries/#lookups-that-span-relationships)

> Django offers a powerful and intuitive way to “follow” relationships in lookups, taking care of the SQL JOINs for you automatically, behind the scenes. To span a relationship, just use the field name of related fields across models, separated by double underscores, until you get to the field you want.

장고에서는 SQL의 join기능인 관계된 모델들의 필드를 추적하는 강력하고 직관적인 방법이 있다. 

> This example retrieves all Entry objects with a Blog whose name is ’Beatles Blog’:

```python
Entry.objects.filter(blog__name='Beatles Blog')
```

> If you are filtering across multiple relationships and one of the intermediate models doesn’t have a value that meets the filter condition, Django will treat it as if there is an empty (all values are NULL), but valid, object there. All this means is that no error will be raised. For example, in this filter:
 
여러 관계들을 거쳐가며 필터링한 후 결과가 비어있다면 null값을 반환한다. 하지만 유효한 값이다. 에러를 발생시키지 않는다. 
