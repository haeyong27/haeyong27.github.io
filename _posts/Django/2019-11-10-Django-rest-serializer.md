---
layout: post
title:  "Django Restframework Serializer"
date:   2019-11-10
categories: Django
tag: [Django, DRF]
---

- [django restframework serializer](#django-restframework-serializer)
  - [Dealing with nested objects](#dealing-with-nested-objects)
  - [Dealing with multiple objects](#dealing-with-multiple-objects)
  - [ModelSerializer](#modelserializer)

# django restframework serializer  
[Serializers - Django REST framework](https://www.django-rest-framework.org/api-guide/serializers/)

> Serializers allow complex data such as querysets and model instances to be converted to native Python datatypes that can then be easily rendered into JSON, XML or other content types. Serializers also provide deserialization, allowing parsed data to be converted back into complex types, after first validating the incoming data.

시리얼라이저는 장고의 쿼리셋이나 모델의 인스턴스와 같은 복잡한 데이터를 python의 데이터 타입으로 변환하여 json, XML과 같은 타입으로 변환을 도와줍니다. 유효성을 검사한 뒤, 반대 작업인 deserialze와 다시 복잡한 데이터 형태로 파싱을 도와주기도 합니다. 

> serialize, save, validation, partial update와 같은 기본적인 기능은 넘어간다. 

## Dealing with nested objects
 [Dealing with nested objects](https://www.django-rest-framework.org/api-guide/serializers/#dealing-with-nested-objects)   
개인적으로 사이드 프로젝트를 하면서 이 부분에 대한 지식이 없어 이 키워드를 찾는데 오래걸렸다. Foreign Key관계에 있는 모델을 만들 때 유용하게 쓰이는 기능이다. 예를들어서 Song이라는 모델과 Archive라는 노래를 담는 보관함이 있다고 하자. 
```python
class Song(models.Model):
		title = PK
		singer = char
		created_at = date
		...

class Archive(models.Model):
		song = FK(Song)
		...
```

Django models.py에 모델을 이렇게 정의했고, 시리얼라이저도 같은 구조였다. 나의 프로젝트의 클라이언트(Vue.js)에서 Archive를 읽어왔을 때 Archive.song을 읽어오면 song object의 string값만 전달 받아 Song의 필드들(title, singer, created_at 등)의 값을 읽어올 수 없었다. 이 문제는 Archive의 song 필드에 song serializer를 대입해주면 해결된다. 

아래는 내 경우와 다른경우이고, 공식문서에 있는 코드를 가져왔다. 
```python
class UserSerializer(serializers.Serializer):
    email = serializers.EmailField()
    username = serializers.CharField(max_length=100)

class CommentSerializer(serializers.Serializer):
    user = UserSerializer()
    content = serializers.CharField(max_length=200)
    created = serializers.DateTimeField()
```


> The previous examples are fine for dealing with objects that only have simple datatypes, but sometimes we also need to be able to represent more complex objects, where some of the attributes of an object might not be simple datatypes such as strings, dates or integers.


##  Dealing with multiple objects
[Dealing with multiple objects](https://www.django-rest-framework.org/api-guide/serializers/#dealing-with-multiple-objects) 

> The Serializer class can also handle serializing or deserializing lists of objects.
> To serialize a queryset or list of objects instead of a single object instance, you should pass the many=True flag when instantiating the serializer. You can then pass a queryset or list of objects to be serialized.

object를 serialize, deserialize하는것 이외에도 쿼리셋도 같은 작업을 할 수 있다. many=True 옵션을 주면된다.

```python
queryset = Book.objects.all()
serializer = BookSerializer(queryset, many=True)
serializer.data
# [
#     {'id': 0, 'title': 'The electric kool-aid acid test', 'author': 'Tom Wolfe'},
#     {'id': 1, 'title': 'If this is a man', 'author': 'Primo Levi'},
#     {'id': 2, 'title': 'The wind-up bird chronicle', 'author': 'Haruki Murakami'}
# ]
```

## ModelSerializer
[ModelSerializer](https://www.django-rest-framework.org/api-guide/serializers/#modelserializer)

> Often you’ll want serializer classes that map closely to Django model definitions.
> The ModelSerializer class provides a shortcut that lets you automatically create a Serializer class with fields that correspond to the Model fields.
> **The**ModelSerializer**class is the same as a regular**Serializer**class, except that**:
* It will automatically generate a set of fields for you, based on the model.
* It will automatically generate validators for the serializer, such as unique_together validators.
* It includes simple default implementations of .create() and .update().
Declaring a ModelSerializer looks like this:

모델시리얼라이저는 Serializer와 동일한 기능을하고 추가적으로 편리한 기능을 제공한다. 모델만 입력해주면 자동으로 모든 필드에 대해 처리를 해준다. 하지만 웬만하면 사용하지 않을것이다. 성능의 저하를 가져온다.  
참고글 : [Improve Serialization Performance in Django Rest Framework | Haki Benita](https://hakibenita.com/django-rest-framework-slow)





