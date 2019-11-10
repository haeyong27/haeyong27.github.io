---
layout: post
title:  "Django Restframework View"
date:   2019-11-10
categories: Django
tag: [Django, DRF]
---


- [Django Restframework View](#django-restframework-view)
- [GenericAPIView](#genericapiview)
  - [Attributes](#attributes)
- [Mixins](#mixins)
- [Concrete View Classes](#concrete-view-classes)
- [Customizing the generic views](#customizing-the-generic-views)
  - [Creating custom mixins](#creating-custom-mixins)


# Django Restframework View
[Views - Django REST framework](https://www.django-rest-framework.org/api-guide/views/#class-based-views)

> REST framework provides an APIView class, which subclasses Django’s View class.
> APIView classes are different from regular View classes in the following ways:

> - Requests passed to the handler methods will be REST framework’s Request instances, not Django’s HttpRequest instances.
> - Handler methods may return REST framework’s Response, instead of Django’s HttpResponse. The view will manage content negotiation and setting the correct renderer on the response.
> - Any APIException exceptions will be caught and mediated into appropriate responses.
> - Incoming requests will be authenticated and appropriate permission and/or throttle checks will be run before dispatching the request to the handler method.


- handler methods를 통과하는 요청은 Django의 HttpResquest가 아닌 DRF의 리퀘스트 인스턴스가 된다.
- responser도 설정할 수 있다. 
-  요청이 들어오면 이를 handler method로 전달하기 전에 적절한 권한을 부여하고 throttle(호출 횟수 제한) 등을 얼마나 줄지 체크합니다. 

> Using the APIView class is pretty much the same as using a regular View class, as usual, the incoming request is dispatched to an appropriate handler method such as .get() or .post(). Additionally, a number of attributes may be set on the class that control various aspects of the API policy.

APIView는 리퀘스트를 받아 적절한 hadler method( .get() or .post() 등)에 전달하는 일반적인 view와 상당히 유사하다. 

```python
class ListUsers(APIView):
    """
    View to list all users in the system.

    * Requires token authentication.
    * Only admin users are able to access this view.
    """
    authentication_classes = [authentication.TokenAuthentication]
    permission_classes = [permissions.IsAdminUser]

    def get(self, request, format=None):
        """
        Return a list of all users.
        """
        usernames = [user.username for user in User.objects.all()]
        return Response(usernames)
```

> Typically when using the generic views, you’ll override the view, and set several class attributes.

```python
class UserList(generics.ListCreateAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    permission_classes = [IsAdminUser]

    def list(self, request):
        # Note the use of `get_queryset()` instead of `self.queryset`
        queryset = self.get_queryset()
        serializer = UserSerializer(queryset, many=True)
        return Response(serializer.data)

```

# GenericAPIView

[GenericAPIView](https://www.django-rest-framework.org/api-guide/generic-views/#genericapiview)
> This class extends REST framework’s APIView class, adding commonly required behavior for standard list and detail views.
> Each of the concrete generic views provided is built by combining GenericAPIView, with one or more mixin classes.


## Attributes
[Attributes](https://www.django-rest-framework.org/api-guide/generic-views/#attributes)
**Basic settings**:
The following attributes control the basic view behavior.
* queryset - The queryset that should be used for returning objects from this view. Typically, you must either set this attribute, or override the get_queryset() method. If you are overriding a view method, it is important that you call get_queryset() instead of accessing this property directly, as queryset will get evaluated once, and those results will be cached for all subsequent requests.
* serializer_class - The serializer class that should be used for validating and deserializing input, and for serializing output. Typically, you must either set this attribute, or override the get_serializer_class() method.
* lookup_field - The model field that should be used to for performing object lookup of individual model instances. Defaults to ‘pk’. Note that when using hyperlinked APIs you’ll need to ensure that *both* the API views *and* the serializer classes set the lookup fields if you need to use a custom value.
* lookup_url_kwarg - The URL keyword argument that should be used for object lookup. The URL conf should include a keyword argument corresponding to this value. If unset this defaults to using the same value as lookup_field.

queryset : 뷰로부터 객체를 반환한다. queryset 속성을 세팅하거나 get_queryset()를 오버라이드 하는 방법이 있다. 하지만 속성을 직접 접근해서 수정하기 보다는 get_queryset()을 호출해서 오버라이드 하는것이 좋다. 왜냐하면 그 이후에 이루어지는 요청을 캐싱할 수 있기때문이다. 

serializer_class : 유효성검사, input값을 디 시리얼라이징(유저로부터 입력받은것을 django가 더 이해하기 편한 형태로 변환하는 과정), output을 시리얼라이징한다. 

lookup_field : 모델 인스턴스에서 객체를 조회할 때 사용될 필드. 간단하게 말해서 PK(primary key). 기본값은 pk이다. 

이 외에도 [Pagination, Filtering](https://www.django-rest-framework.org/api-guide/generic-views/#attributes) 
 등이 있다.  

 
# Mixins
[Mixins](https://www.django-rest-framework.org/api-guide/generic-views/#mixins)
> The mixin classes provide the actions that are used to provide the basic view behavior. Note that the mixin classes provide action methods rather than defining the handler methods, such as .get() and .post(), directly. This allows for more flexible composition of behavior.
> The mixin classes can be imported from rest_framework.mixins.
mixin classes는 기본 view의 기능을 제공한다. .get() .post()와 같은 handler methods를 직접 정의하기 보다는 action method를 제공하기 때문에 좀더 유연하게 응답을 구성할 수 있다. 


# Concrete View Classes
[Concrete View Classes](https://www.django-rest-framework.org/api-guide/generic-views/#concrete-view-classes)
> The following classes are the concrete generic views. If you’re using generic views this is normally the level you’ll be working at unless you need heavily customized behavior.
> The view classes can be imported from rest_framework.generics.

동작을 커스텀할 경우가 아니라면 일반적으로 사용하게 될 클레스이다. 
예를 들어
1.   [CreateAPIView](https://www.django-rest-framework.org/api-guide/generic-views/#createapiview)
Used for **create-only** endpoints.
Provides a post method handler.
Extends:  [GenericAPIView](https://www.django-rest-framework.org/api-guide/generic-views/#genericapiview) ,  [CreateModelMixin](https://www.django-rest-framework.org/api-guide/generic-views/#createmodelmixin) 

1. [ListAPIView](https://www.django-rest-framework.org/api-guide/generic-views/#listapiview)
Used for **read-only** endpoints to represent a **collection of model instances**.
Provides a get method handler.
Extends:  [GenericAPIView](https://www.django-rest-framework.org/api-guide/generic-views/#genericapiview) ,  [ListModelMixin](https://www.django-rest-framework.org/api-guide/generic-views/#listmodelmixin) 

이외에도 다양하게 있다. 
*  [RetrieveAPIView](https://www.django-rest-framework.org/api-guide/generic-views/#retrieveapiview) 
*  [DestroyAPIView](https://www.django-rest-framework.org/api-guide/generic-views/#destroyapiview) 
*  [UpdateAPIView](https://www.django-rest-framework.org/api-guide/generic-views/#updateapiview) 
*  [ListCreateAPIView](https://www.django-rest-framework.org/api-guide/generic-views/#listcreateapiview) 
*  [RetrieveUpdateAPIView](https://www.django-rest-framework.org/api-guide/generic-views/#retrieveupdateapiview) 
*  [RetrieveDestroyAPIView](https://www.django-rest-framework.org/api-guide/generic-views/#retrievedestroyapiview) 
*  [RetrieveUpdateDestroyAPIView](https://www.django-rest-framework.org/api-guide/generic-views/#retrieveupdatedestroyapiview) 


# Customizing the generic views
[Customizing the generic views](https://www.django-rest-framework.org/api-guide/generic-views/#customizing-the-generic-views)
Often you’ll want to use the existing generic views, but use some slightly customized behavior. If you find yourself reusing some bit of customized behavior in multiple places, you might want to refactor the behavior into a common class that you can then just apply to any view or viewset as needed.


##  Creating custom mixins
[Creating custom mixins](https://www.django-rest-framework.org/api-guide/generic-views/#creating-custom-mixins)
For example, if you need to lookup objects based on multiple fields in the URL conf, you could create a mixin class like the following:

```python
class MultipleFieldLookupMixin(object):
    """
    Apply this mixin to any view or viewset to get multiple field filtering
    based on a `lookup_fields` attribute, instead of the default single field filtering.
    """
    def get_object(self):
        queryset = self.get_queryset()             # Get the base queryset
        queryset = self.filter_queryset(queryset)  # Apply any filter backends
        filter = {}
        for field in self.lookup_fields:
            if self.kwargs[field]: # Ignore empty fields.
                filter[field] = self.kwargs[field]
        obj = get_object_or_404(queryset, **filter)  # Lookup the object
        self.check_object_permissions(self.request, obj)
        return obj
```


