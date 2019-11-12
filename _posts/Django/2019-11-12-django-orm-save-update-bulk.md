---
layout: post
title:  "How Django knows to UPDATE vs. INSERT(feat. 효율적인 bulk save, update)"
date:   2019-11-12
categories: Django
tag: [Django]
---


- [How Django knows to UPDATE vs. INSERT](#how-django-knows-to-update-vs-insert)
  - [saving objects](#saving-objects)
    - [Explicitly specifying auto-primary-key values](#explicitly-specifying-auto-primary-key-values)
  - [Updating attributes based on existing fields](#updating-attributes-based-on-existing-fields)
  - [Specifying which fields to save](#specifying-which-fields-to-save)
  - [효율적인 insert와 update를 위한 QuerySet API reference, 효율적인 insert와 update를 위한](#%ed%9a%a8%ec%9c%a8%ec%a0%81%ec%9d%b8-insert%ec%99%80-update%eb%a5%bc-%ec%9c%84%ed%95%9c-queryset-api-reference-%ed%9a%a8%ec%9c%a8%ec%a0%81%ec%9d%b8-insert%ec%99%80-update%eb%a5%bc-%ec%9c%84%ed%95%9c)
    - [bulk_create(),](#bulkcreate)
    - [bulk_update()](#bulkupdate)



# How Django knows to UPDATE vs. INSERT
[Model instance reference | Django documentation | Django](https://docs.djangoproject.com/en/2.2/ref/models/instances/#how-django-knows-to-update-vs-insert)

> You may have noticed Django database objects use the same save() method for creating and changing objects. Django abstracts the need to use INSERT or UPDATE SQL statements. Specifically, when you call save(), Django follows this algorithm:

객체를 생성하거나 변경할 때 둘 다 save()함수를 사용한다. 장고는 insert와 update를 추상화 할 필요가 있다.  

> If the object’s primary key attribute is set to a value that evaluates to True (i.e., a value other than None or the empty string), Django executes an UPDATE.

객체의 PK값이 true로 인식되면 update라고 판단한다. 

> If the object’s primary key attribute is *not* set or if the UPDATE didn’t update anything (e.g. if primary key is set to a value that doesn’t exist in the database), Django executes an INSERT.


> The one gotcha here is that you should be careful not to specify a primary-key value explicitly when saving new objects, if you cannot guarantee the primary-key value is unused. For more on this nuance, see  [Explicitly specifying auto-primary-key values](https://docs.djangoproject.com/en/2.2/ref/models/instances/#explicitly-specifying-auto-primary-key-values)  above and  [Forcing an INSERT or UPDATE](https://docs.djangoproject.com/en/2.2/ref/models/instances/#forcing-an-insert-or-update)  below.

여기서 중요한 점은 객체를 저장할 때 PK값을 명시적으로 하면 안된다는것이다. pk값이 사용되지 않는다는걸 보장할 수 없다면. -> pk값이 데이터베이스에 없으면 insert, 이미 있는것이면 update

> In Django 1.5 and earlier, Django did a SELECT when the primary key attribute was set. If the SELECT found a row, then Django did an UPDATE, otherwise it did an INSERT. The old algorithm results in one more query in the UPDATE case. There are some rare cases where the database doesn’t report that a row was updated even if the database contains a row for the object’s primary key value. An example is the PostgreSQL ON UPDATE trigger which returns NULL. In such cases it is possible to revert to the old algorithm by setting the  [select_on_save](https://docs.djangoproject.com/en/2.2/ref/models/options/#django.db.models.Options.select_on_save)  option to True.

## saving objects
[Model instance reference | Django documentation | Django](https://docs.djangoproject.com/en/2.2/ref/models/instances/#saving-objects)

### Explicitly specifying auto-primary-key values
> If a model has an  [AutoField](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.AutoField)  but you want to define a new object’s ID explicitly when saving, just define it explicitly before saving, rather than relying on the auto-assignment of the ID:
```python
>>> b3 = Blog(id=3, name='Cheddar Talk', tagline='Thoughts on cheese.')
>>> b3.id     # Returns 3.
>>> b3.save()
>>> b3.id     # Returns 3.
```
> If you assign auto-primary-key values manually, make sure not to use an already-existing primary-key value! If you create a new object with an explicit primary-key value that already exists in the database, Django will assume you’re changing the existing record rather than creating a new one.

authofield인 경우에도 명시적으로 id값을 할당하여 pk값을 지정할 수 있다. 주의해야 할 점은 데이터베이스에 이미 있는 pk값이면 수정하게되므로 조심해야한다. 



## Updating attributes based on existing fields

> Sometimes you’ll need to perform a simple arithmetic task on a field, such as incrementing or decrementing the current value. The obvious way to achieve this is to do something like: 
```python
>>> product = Product.objects.get(name='Venezuelan Beaver Cheese')
>>> product.number_sold += 1
>>> product.save()
```

The process can be made robust,  [avoiding a race condition](https://docs.djangoproject.com/en/2.2/ref/models/expressions/#avoiding-race-conditions-using-f) , as well as slightly faster by expressing the update relative to the original field value, rather than as an explicit assignment of a new value. Django provides  [F expressions](https://docs.djangoproject.com/en/2.2/ref/models/expressions/#django.db.models.F)  for performing this kind of relative update. Using  [F expressions](https://docs.djangoproject.com/en/2.2/ref/models/expressions/#django.db.models.F) , the previous example is expressed as:


## Specifying which fields to save

> If save() is passed a list of field names in keyword argument update_fields, only the fields named in that list will be updated. This may be desirable if you want to update just one or a few fields on an object. There will be a slight performance benefit from preventing all of the model fields from being updated in the database. For example:
원하는 필드만 골라서 업데이트할 수 있다. save()에 인자를 전달해서.

```python
product.name = 'Name changed again'
product.save(update_fields=['name'])
```

## 효율적인 insert와 update를 위한 QuerySet API reference, 효율적인 insert와 update를 위한
[QuerySet API reference | Django documentation | Django](https://docs.djangoproject.com/en/dev/ref/models/querysets/#queryset-api-reference)

 
### bulk_create(), 
[QuerySet API reference | Django documentation | Django](https://docs.djangoproject.com/en/dev/ref/models/querysets/#bulk-create)
> This method inserts the provided list of objects into the database in an efficient manner (generally only 1 query, no matter how many objects there are):
객체 리스트를 한번에 저장하는 효율적인 방법

```python
>>> Entry.objects.bulk_create([
...     Entry(headline='This is a test'),
...     Entry(headline='This is only a test'),
... ])
```


### bulk_update()
[QuerySet API reference | Django documentation | Django](https://docs.djangoproject.com/en/dev/ref/models/querysets/#bulk-update)

```python
>>> objs = [
...    Entry.objects.create(headline='Entry 1'),
...    Entry.objects.create(headline='Entry 2'),
... ]
>>> objs[0].headline = 'This is entry 1'
>>> objs[1].headline = 'This is entry 2'
>>> Entry.objects.bulk_update(objs, ['headline'])	
```

```python
s = time.time()
for i in words:
    Author(name=i, email=f'{i}@gmail.com').save()    
print('무식한 방법', time.time() - s, '초')
Author.objects.all().delete()

s = time.time()
l = []
for i in words:
    l.append(Author(name=i, email=f'{i}@gmail.com'))
Author.objects.bulk_create(l)
print('똑똑한 방법', time.time() - s, '초')

>>> 무식한 방법 25.767067193984985 초
>>> 똑똑한 방법 0.6707980632781982 초
```

