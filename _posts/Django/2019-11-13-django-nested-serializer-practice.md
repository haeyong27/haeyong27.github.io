---
layout: post
title:  "Django Restframework nested serializer 연습"
date:   2019-11-13
categories: Django
tag: [Django, DRF]
---



# django restframework nested serializer (연습)
[Serializers - Django REST framework](https://www.django-rest-framework.org/api-guide/serializers/#specifying-nested-serialization)
다음과 같은 model과 serializer가 있다. 
```python
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
    created_at = models.DateField(auto_now=True)
    updated_at = models.DateField(auto_now_add=True)
    authors = models.ManyToManyField(Author)

    class Meta():
        ordering = ['-created_at']
```
```python
class Authorserialzier(serializers.ModelSerializer):
    class Meta:
        model = Author
        fields = '__all__'


class Entryserialzier(serializers.ModelSerializer):

    class Meta:
        model = Entry
        fields = '__all__'
        # depth = 1
```

FK를 사용하는 경우에 serializer의 meta에서 depth를 설정해주지 않으면, id값을 가져와서 출력해준다. 아무런 의미 없는 정보이다. 
```json
{
    "count": 1000,
    "next": "http://127.0.0.1:8000/tt/entry/?limit=2&offset=2",
    "previous": null,
    "results": [
        {
            "id": 33232,
            "created_at": "2019-11-13",
            "updated_at": "2019-11-13",
            "blog": 71,
            "authors": [
                51125,
                51128,
                51131,
                51132,
                51133
            ]
        },
        {
            "id": 33233,
            "created_at": "2019-11-13",
            "updated_at": "2019-11-13",
            "blog": 69,
            "authors": [
                51130,
                51131,
                51132
            ]
        }
    ]
}
```

해결방법은 depth값을 설정해주면된다.(참고 : [Serializers - Django REST framework](https://www.django-rest-framework.org/api-guide/serializers/#specifying-nested-serialization))   
 여기서는 한번만 들어가면 되니까 1로해준다. 여러번 파고들어가면 그에맞는 정수를 입력해주면 된다. 
depth = 1일 때 결과는 다음과 같다. 

```json
{
    "count": 1000,
    "next": "http://127.0.0.1:8000/tt/entry/?limit=2&offset=2",
    "previous": null,
    "results": [
        {
            "id": 33232,
            "created_at": "2019-11-13",
            "updated_at": "2019-11-13",
            "blog": {
                "id": 71,
                "name": "c",
                "tagline": ""
            },
            "authors": [
                {
                    "id": 51125,
                    "name": "0",
                    "email": "0@gmail.com"
                },
                {
                    "id": 51128,
                    "name": "3",
                    "email": "3@gmail.com"
                },
                {
                    "id": 51131,
                    "name": "6",
                    "email": "6@gmail.com"
                },
                {
                    "id": 51132,
                    "name": "7",
                    "email": "7@gmail.com"
                },
                {
                    "id": 51133,
                    "name": "8",
                    "email": "8@gmail.com"
                }
            ]
        },
        {
            "id": 33233,
            "created_at": "2019-11-13",
            "updated_at": "2019-11-13",
            "blog": {
                "id": 69,
                "name": "a",
                "tagline": ""
            },
            "authors": [
                {
                    "id": 51130,
                    "name": "5",
                    "email": "5@gmail.com"
                },
                {
                    "id": 51131,
                    "name": "6",
                    "email": "6@gmail.com"
                },
                {
                    "id": 51132,
                    "name": "7",
                    "email": "7@gmail.com"
                }
            ]
        }
    ]
}
```



