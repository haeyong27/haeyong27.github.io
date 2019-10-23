---
layout: post
title:  "Model"
date:   2019-10-19
categories: Django
tag: [Django, model]
---

Lookups that span relationships
Django offers a powerful and intuitive way to “follow” relationships in lookups, taking care of the SQL JOINs for you automatically, behind the scenes. To span a relationship, just use the field name of related fields across models, separated by double underscores, until you get to the field you want.

This example retrieves all Entry objects with a Blog whose name is 'Beatles Blog':

>>> Entry.objects.filter(blog__name='Beatles Blog')
This spanning can be as deep as you’d like.

It works backwards, too. To refer to a “reverse” relationship, just use the lowercase name of the model.

This example retrieves all Blog objects which have at least one Entry whose headline contains 'Lennon':

>>> Blog.objects.filter(entry__headline__contains='Lennon')
If you are filtering across multiple relationships and one of the intermediate models doesn’t have a value that meets the filter condition, Django will treat it as if there is an empty (all values are NULL), but valid, object there. All this means is that no error will be raised. For example, in this filter:

Blog.objects.filter(entry__authors__name='Lennon')
(if there was a related Author model), if there was no author associated with an entry, it would be treated as if there was also no name attached, rather than raising an error because of the missing author. Usually this is exactly what you want to have happen. The only case where it might be confusing is if you are using isnull. Thus:

Blog.objects.filter(entry__authors__name__isnull=True)
will return Blog objects that have an empty name on the author and also those which have an empty author on the entry. If you don’t want those latter objects, you could write:

Blog.objects.filter(entry__authors__isnull=False, entry__authors__name__isnull=True)