---
layout: post
title:  "Django Restframework Serializer(ForeignKey, nested objects)"
date:   2019-11-10
categories: Django
tag: [Django, DRF]
---

- [django restframework serializer](#django-restframework-serializer)
  - [Dealing with nested objects](#dealing-with-nested-objects)

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







