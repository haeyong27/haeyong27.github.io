---
layout: post
title:  "Django Rest Framework Serializers"
date:   2019-10-19
categories: Django
tag: [Django, DRF]
---
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

## queryset
> We can also serialize querysets instead of model instances. To do so we simply add a many=True flag to the serializer arguments.

모델 인스턴스 대신 쿼리셋을 직렬화 할 수 있습니다. many옵션을 True로 설정해주면 됩니다.

```python
serializer = SnippetSerializer(Snippet.objects.all(), many=True)
serializer.data
```

# ModelSerializers
- An automatically determined set of fields.
- Simple default implementations for the create() and update() methods.

```python
class SnippetSerializer(serializers.ModelSerializer):
    class Meta:
        model = Snippet
        fields = [‘id’, ‘title’, ‘code’, ‘linenos’, ‘language’, ‘style’]
```

ModelSerializer는 serializer에서 직접 작성해줘야하는 save, update같은 함수를 자동으로 만들어줍니다. 또한 메타클레스에 필드명만 입력해줘도 자동으로 serializer에서 정의하는 필드들을 완성해줍니다. 

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



---
19 Oct 2019 3:38 PM
