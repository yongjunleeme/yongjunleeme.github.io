---
layout  : wiki
title   : 
summary : 
date    : 2020-03-11 13:09:01 +0900
updated : 2020-03-15 18:26:44 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 두서없이 코멘트 기

- pr 메시지 날리듯이 commit 메시지 써야
- `urls.py`을 먼저 보면서 앱 구조를 파악
- 앱 생성 기준 - 독립적인 모듈인지
- url은 동사가 아닌 명사로 - 의도한 리소스를 명시 예) 서치가 아니라 프로덕트
- 엔드포인트는 웹페이지와 상관 없다. 디자인과 페이지가 바뀐다고 해도 엔드포인트는 바뀌지 않아야 한다.
- 코드 한 줄 70자 넘어가면 다음줄로 가독성을 위해
- 하드코딩 노노
- 에러나지 않은 코드라면 같은 변수이름 사용해도 된다. 가독성 확보를 위해.
- 가독성 실패하면 애초부터 꽝된다.
- 상품이 계속 쌓일 것 같으면 중간테이블에 수량 컬럼을 만들어서 저장
- 단위가 있는 데이터 저장하는 필드 char가 아니라 integer로
- commit -m으로 쓰지 않고 commit으로


## 코드

```python
class ProductSearchView(View):
    filter = {
	        "menu"    : "menu_name",
	        "product" : "name_kr__icontains",
	        "tag"     : "tag__name",
	        "allergy" : "allergy__name__in"
    }
    def get(self, request):
        menu    = request.GET.get('menu', None)
        product = request.GET.get('product', None)
        tag     = request.GET.get('tag', None) 
        allergy = request.GET.getlist('allergy', None)
        tags    = Tag.objects.values('name')
    data = {filter[option] : request.GET[option] for option in request.GET if option in filter}
        
        products      = Product.objects.filter(**data).distinct()
        products_data = [
            {   
                'id'       : product.id,
                'name'     : product.name_kr,
                'thumbnail': product.thumbnail,
                'tags'     : list(tags.filter(product__id = product.id))
            } for product in products
        ]
        return JsonResponse({'count': len(products), 'data': products_data}, status = 200)

```

- distinct()- 중복제거

```python
class StoreMainView(View):
    MAIN_STORE = "SEOUL"
    def get(self, request):
        name   = request.GET.get('name', 'SEOUL')
        limit  = request.GET.get('limit', 49)
        stores = STORES.order_by('distance').filter(name = name)[:limit]

        return JsonResponse({'stores':list(stores)}, status = 200)

```

- 재사용할 것 같은 코드 제이슨으로 만들어서 응답?

```python
colors = [{
            "color_id"  : product_color.color.id,
            "color_name": product_color.color.name
} for product_color in ProductColor.objects.filter(product_id = product_id).select_related('color').all()]
```

- 가독성

```python
ordered_product_list = (
                Product
                .objects
                .prefetch_related('product_like')
                .annotate(like_count = Count('product_like'))
                .order_by('-like_count')[:36]
            )
```

- 가독성


```python
def check_login(func):
    def wrapper(self, request, *args, **kwargs):
        access_token = request.headers.get('Authorization', None)

        if access_token:
            try:
                payload      = jwt.decode(access_token, SECRET_KEY, algorithm = 'HS256')
                user         = User.objects.get(login_id = payload['login_id'])
                request.user = user

            except jwt.exceptions.DecodeError:
                return JsonResponse({"message": "INVALID_TOKEN" }, status = 400)
            except User.DoesNotExist:
                return JsonResponse({"message": "INVALID_USER" }, status = 400)
            except KeyError:
                JsonResponse({"message": "INVALID_KEY" }, status = 400)
        else:
            request.user = None

        return func(self, request, *args, **kwargs)
    return wrapper
```

- 데코레이터 - 강제성, 재사용성, 스파게티 코드 방지

```python
user_match = Q(.,..) & Q(..)
            follower = Follower.objects.filter(user_match)

            if follower.exists():
                follower.delete()

followee = User.objects.get(id = followee_id)
            request.user.follow_relation.add(followee)
```
