---
layout: post
title:  "Django Rest Framework performance"
date:   2019-10-19
categories: Django
tag: [Django, performance]
---

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

1. serializer is faster than modelserializer.
2. marking the fields as readonly didn’t make a significant difference compared to the “regular” serializer.

---
19 Oct 2019 4:35 PM