---
layout: post
title:  "Django ORM many to many save, retrieve"
date:   2019-11-13
categories: Django
tag: [Django]
---


# django orm many to many field create, save
[Many-to-many relationships | Django documentation | Django](https://docs.djangoproject.com/en/2.2/topics/db/examples/many_to_many/)

```python
from django.db import models

class Publication(models.Model):
    title = models.CharField(max_length=30)

    class Meta:
        ordering = ['title']

    def __str__(self):
        return self.title

class Article(models.Model):
    headline = models.CharField(max_length=100)
    publications = models.ManyToManyField(Publication)

    class Meta:
        ordering = ['headline']

    def __str__(self):
        return self.headline
```

```python
>>> p1 = Publication(title='The Python Journal')
>>> p1.save()
>>> p2 = Publication(title='Science News')
>>> p2.save()
>>> p3 = Publication(title='Science Weekly')
>>> p3.save()
>>> a1 = Article(headline='Django lets you build Web apps easily')
>>> a1.publications.add(p1)
Traceback (most recent call last):
...
ValueError: "<Article: Django lets you build Web apps easily>" needs to have a value for field "id" before this many-to-many relationship can be used.
```

many to many 필드는 .add()로 추가해야한다. 하지만 저장하기 전에는 add()를 할 수 없다. 시도하면 위와같은 에러가 뜬다. 

```python
>>> a1.save()
>>> a1.publications.add(p1)
```

이렇게 해야한다. 

```python
>>> a2.publications.add(p1, p2)
>>> a2.publications.add(p3)
>>> a2.publications.add(p3)
```

객체를 풀어서 넣어줘야한다. 리스트로 넣어주면 인식을 못한다. 같은걸 두번 넣어도 두번 저장되지 않는다. 


```python
>>> p2.article_set.all()
<QuerySet [<Article: NASA uses Python>]>
>>> p1.article_set.all()
<QuerySet [<Article: Django lets you build Web apps easily>, <Article: NASA uses Python>]>
>>> Publication.objects.get(id=4).article_set.all()
<QuerySet [<Article: NASA uses Python>]>
```

article에는 여러개의 publications가 있다. 그래서 article instance에서 바로 publication.all() 하면 publications의 리스트를 가져올 수 있다. 반대로 publications에 속하는 article을 가져오려면 뒤에 _set을 붙여줘야한다. 예를들어서 p1.article_set.all() 이렇식으로 할 수 있다.  아래코드로 연습했다. 

```python
from loremipsum import get_paragraph
import requests
import time
import random 

for i in Author._meta.fields:
    print(i)
print()
for i in Blog._meta.fields:
    print(i)
print()
for i in Entry._meta.fields:
    print(i)
print()

# word_site = "http://svnweb.freebsd.org/csrg/share/dict/words?view=co&content-type=text/plain"
# response = requests.get(word_site)
# words = response.content.splitlines()
# words = [x.decode('utf-8') for x in words]

#Authors 이름을 0~9로 10개의 객체 생성
l = []
for i in range(10):
    l.append(Author(name=i, email=f'{i}@gmail.com'))
Author.objects.bulk_create(l)

#Blog 는 a, b, c, d로 4개의 객체 생성
for i in ['a', 'b', 'c', 'd']:
    Blog(name=i).save()

    
#many to many관계에 있는 entry의 author
entry_authors = [set([random.randint(0, 9) for _ in range(random.randint(1, 9))]) for _ in range(1000)]


#랜덤으로 만든 authors set의 list로 entry만듬 
for i in range(10000):
    temp = Entry(blog = b[random.randint(0, 3)])
    temp.save()
    temp.authors.add(*[a[x] for x in entry_authors[random.randint(0, 999)]])
    
b = Blog.objects.all()
e = Entry.objects.all()
a = Author.objects.all()

print(Author.objects.all())
print(Blog.objects.all())
print(Entry.objects.all())


# b.delete()
# e.delete()
# a.delete()
```


```python
>>> e[0].authors.all()
<QuerySet [<Author: 0>, <Author: 2>]>
>>> len(a[i].entry_set.all())
<QuerySet [<Entry: Entry object (2158)>, <Entry: Entry object (2165)>, <Entry: Entry object (2168)>, <Entry: Entry object (2172)>, <Entry: Entry object (2173)>, <Entry: Entry object (2175)>, <Entry: Entry object (2176)>, <Entry: Entry object (2180)>, <Entry: Entry object (2182)>, <Entry: Entry object (2184)>, <Entry: Entry object (2185)>, <Entry: Entry object (2186)>, <Entry: Entry object (2187)>, <Entry: Entry object (2188)>, <Entry: Entry object (2191)>, <Entry: Entry object (2197)>, <Entry: Entry object (2199)>, <Entry: Entry object (2200)>, <Entry: Entry object (2201)>, <Entry: Entry object (2203)>, '...(remaining elements truncated)...']>
```
