---
layout: post
title:  "Django Restframework Serializers Overview"
date:   2019-10-19
categories: Django
tag: [Django, DRF]
---
- [Introduction](#introduction)
- [Serializer class](#serializer-class)
  - [Dealing with multiple objects](#dealing-with-multiple-objects)
- [ModelSerializers](#modelserializers)
- [Writing regular Django views using our Serializer](#writing-regular-django-views-using-our-serializer)

# Introduction
참조 : [Django Rest Framework Serialization](https://www.django-rest-framework.org/tutorial/1-serialization/#tutorial-1-serialization) 

# Serializer class
> The first thing we need to get started on our Web API is to provide a way of serializing and deserializing the snippet instances into representations such as json.

Web API를 시작할 때 알아야 할 부분은 모델 인스턴스를 json과 같은 형식으로 표현할 줄 알아야 합니다. 이를 직렬화 비직렬화 라고 합니다. 

```python
from rest_framework import serializers
from snippets.models import Snippet, LANGUAGE_CHOICES, STYLE_CHOICES


class SnippetSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(required=False, allow_blank=True, max_length=100)
    code = serializers.CharField(style={'base_template': 'textarea.html'})
    linenos = serializers.BooleanField(required=False)
    language = serializers.ChoiceField(choices=LANGUAGE_CHOICES, default='python')
    style = serializers.ChoiceField(choices=STYLE_CHOICES, default='friendly')

    def create(self, validated_data):
        """
        Create and return a new `Snippet` instance, given the validated data.
        """
        return Snippet.objects.create(**validated_data)

    def update(self, instance, validated_data):
        """
        Update and return an existing `Snippet` instance, given the validated data.
        """
        instance.title = validated_data.get('title', instance.title)
        instance.code = validated_data.get('code', instance.code)
        instance.linenos = validated_data.get('linenos', instance.linenos)
        instance.language = validated_data.get('language', instance.language)
        instance.style = validated_data.get('style', instance.style)
        instance.save()
        return instance
```



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

# ModelSerializers


[ModelSerializer](https://www.django-rest-framework.org/api-guide/serializers/#modelserializer)


- An automatically determined set of fields.
- Simple default implementations for the create() and update() methods.

```python
class SnippetSerializer(serializers.ModelSerializer):
    class Meta:
        model = Snippet
        fields = [‘id’, ‘title’, ‘code’, ‘linenos’, ‘language’, ‘style’]
```

ModelSerializer는 serializer에서 직접 작성해줘야하는 save, update같은 함수를 자동으로 만들어준다. 또한 메타클레스에 필드명만 입력해줘도 자동으로 serializer에서 정의하는 필드들을 완성해준다. 


> Often you’ll want serializer classes that map closely to Django model definitions.
> The ModelSerializer class provides a shortcut that lets you automatically create a Serializer class with fields that correspond to the Model fields.
> **The**ModelSerializer**class is the same as a regular**Serializer**class, except that**:
* It will automatically generate a set of fields for you, based on the model.
* It will automatically generate validators for the serializer, such as unique_together validators.
* It includes simple default implementations of .create() and .update().
Declaring a ModelSerializer looks like this:

모델시리얼라이저는 Serializer와 동일한 기능을하고 추가적으로 편리한 기능을 제공한다. 모델만 입력해주면 자동으로 모든 필드에 대해 처리를 해준다. 하지만 웬만하면 사용하지 않을것이다. 성능의 저하를 가져온다.  

- [Improve Serialization Performance in Django Rest Framework](https://hakibenita.com/django-rest-framework-slow)
- [serializer의 성능](https://haeyong27.github.io/django/2019/10/19/Performance/)에 관한 글 참고



# Writing regular Django views using our Serializer
```python
@csrf_exempt
def snippet_list(request):
    """
    List all code snippets, or create a new snippet.
    """
    if request.method == 'GET':
        snippets = Snippet.objects.all()
        serializer = SnippetSerializer(snippets, many=True)
        return JsonResponse(serializer.data, safe=False)

    elif request.method == 'POST':
        data = JSONParser().parse(request)
        serializer = SnippetSerializer(data=data)
        if serializer.is_valid():
            serializer.save()
            return JsonResponse(serializer.data, status=201)
        return JsonResponse(serializer.errors, status=400)
```
