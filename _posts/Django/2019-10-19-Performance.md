---
layout: post
title:  "Performance"
date:   2019-10-19
categories: Django
tag: [Django, performance]
---
- [DJango documentation](#django-documentation)
- [Serialization Performance](#serialization-performance)
- [Optimization](#optimization)


# DJango documentation
[Performance and optimization | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/performance/)



# Serialization Performance
[Improve Serialization Performance in Django Rest Framework | Haki Benita](https://hakibenita.com/django-rest-framework-slow)

summary : 
- UserModelSerializer : 12.818s
- UserReadOnlyModelSerializer : 7.407s
- UserSerializer : 2.101s
- UserReadOnlySerializer : 2.254s
- serialize_user : 0.034s


> 1. serializer is faster than modelserializer.
> 2. marking the fields as readonly didn’t make a significant difference compared to the “regular” serializer.

시리얼라이저는 모델 시리얼 라이저보다 빠르고, Readonly는 성능에 큰 영향을 주지 않는다. 

# Optimization
[Basic Performance Optimization in Django](https://medium.com/@ryleysill93/basic-performance-optimization-in-django-ebd19089a33f)


---
19 Oct 2019 4:35 PM