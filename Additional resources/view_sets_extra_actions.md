# Django REST Framework: ViewSets Extra Actions

## What Are Extra Actions?

In addition to standard actions (`list`, `retrieve`, `create`, etc.), DRF lets you define **custom endpoints** on your `ViewSet` using the `@action` decorator.

These are called **extra actions** and can be either:

* **Detail actions** → operate on a single instance (e.g., `/products/5/publish/`)
* **Non-detail actions** → operate on the whole collection (e.g., `/products/archive_all/`)

---

## Syntax

```python
from rest_framework.decorators import action
from rest_framework.response import Response

class MyViewSet(viewsets.ModelViewSet):
    ...

    @action(detail=True, methods=['post'])  # for detail route
    def publish(self, request, pk=None):
        obj = self.get_object()
        obj.published = True
        obj.save()
        return Response({'status': 'published'})
```

```python
    @action(detail=False, methods=['get'])  # for collection route
    def archived(self, request):
        archived_items = self.queryset.filter(status='archived')
        serializer = self.get_serializer(archived_items, many=True)
        return Response(serializer.data)
```

---

## 📌 Detail vs Non-Detail

| Decorator Example       | URL Pattern            | Use Case                       |
| ----------------------- | ---------------------- | ------------------------------ |
| `@action(detail=True)`  | `/products/5/publish/` | Works on single object (by pk) |
| `@action(detail=False)` | `/products/archived/`  | Works on queryset              |

---

## 📦 Full Example

```python
from rest_framework import viewsets
from rest_framework.decorators import action
from rest_framework.response import Response
from .models import Product
from .serializers import ProductSerializer

class ProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

    @action(detail=True, methods=['post'])
    def publish(self, request, pk=None):
        product = self.get_object()
        product.published = True
        product.save()
        return Response({'status': 'Product published'})

    @action(detail=False, methods=['get'])
    def published(self, request):
        products = Product.objects.filter(published=True)
        serializer = self.get_serializer(products, many=True)
        return Response(serializer.data)
```

Now you’ll have:

* `POST /products/5/publish/`
* `GET /products/published/`
