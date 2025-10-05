# 🔐 Django REST Framework: Default Permissions

## What Are Permissions?

**Permissions** control **who can access** or **modify** which parts of your API. They sit *on top of authentication* to enforce rules like:

* "Only authenticated users can write."
* "Only admins can delete."
* "Anyone can read."

---

## Setting Default Permissions

You can define default permissions globally in your **`settings.py`** file:

```python
# settings.py

REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',  # Only authenticated users
    ]
}
```

This ensures all your views will use this permission unless explicitly overridden.

---

## 🧰 Built-in Permission Classes

| Class                       | Description                                     |
| --------------------------- | ----------------------------------------------- |
| `AllowAny`                  | No restrictions; all users allowed              |
| `IsAuthenticated`           | Only logged-in users allowed                    |
| `IsAdminUser`               | Only admin users allowed                        |
| `IsAuthenticatedOrReadOnly` | Authenticated users can write; others read-only |
| `DjangoModelPermissions`    | Enforces Django model-level permissions         |
| `DjangoObjectPermissions`   | Adds object-level permission support            |

---

## ✅ Examples

### 1. `IsAuthenticated`

Only allows logged-in users:

```python
from rest_framework.permissions import IsAuthenticated
from rest_framework.views import APIView

class MyView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        return Response({"message": "Hello, authenticated user!"})
```

---

### 2. `AllowAny`

Completely open access:

```python
from rest_framework.permissions import AllowAny

class PublicView(APIView):
    permission_classes = [AllowAny]

    def get(self, request):
        return Response({"message": "Public access!"})
```

---

### 3. `IsAuthenticatedOrReadOnly`

Unauthenticated users can read, but not write:

```python
from rest_framework.permissions import IsAuthenticatedOrReadOnly

class ReadOnlyOrWriteView(APIView):
    permission_classes = [IsAuthenticatedOrReadOnly]

    def get(self, request):
        return Response({"message": "Read-only if not logged in"})

    def post(self, request):
        return Response({"message": "Only logged-in users can POST"})
```

---

### 4. `IsAdminUser`

Only users with **admin privileges** (`is_staff=True`) can access the view:

```python
from rest_framework.permissions import IsAdminUser

class AdminOnlyView(APIView):
    permission_classes = [IsAdminUser]

    def get(self, request):
        return Response({"message": "Welcome, admin user!"})
```

This is useful for admin dashboards or management endpoints that should not be visible to normal users.

---

### 5. `DjangoModelPermissions`

Enforces **Django’s built-in model-level permissions** (`add`, `change`, `delete`, `view`).

Users must have specific permissions assigned via Django’s admin or group settings.

```python
from rest_framework.permissions import DjangoModelPermissions
from rest_framework.viewsets import ModelViewSet
from myapp.models import Product
from myapp.serializers import ProductSerializer

class ProductViewSet(ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    permission_classes = [DjangoModelPermissions]
```

🔹 Example rules enforced:

* User must have `myapp.view_product` to list or retrieve.
* User must have `myapp.add_product` to create.
* User must have `myapp.change_product` to update.
* User must have `myapp.delete_product` to delete.

---

### 6. `DjangoObjectPermissions`

Adds **object-level permissions** (per individual record), using Django’s object permission system (like from `django-guardian`).

```python
from rest_framework.permissions import DjangoObjectPermissions
from rest_framework.viewsets import ModelViewSet
from myapp.models import Document
from myapp.serializers import DocumentSerializer

class DocumentViewSet(ModelViewSet):
    queryset = Document.objects.all()
    serializer_class = DocumentSerializer
    permission_classes = [DjangoObjectPermissions]
```

🔹 This checks **permissions on each object** (e.g., `view_document`, `change_document` on specific instances).

🔸 To use this, you’ll need a backend that supports object-level permissions — such as the `django-guardian` package.

---

## 📘 Summary

| Permission Class            | Best For                   |
| --------------------------- | -------------------------- |
| `AllowAny`                  | Open APIs                  |
| `IsAuthenticated`           | Private APIs               |
| `IsAdminUser`               | Admin-only access          |
| `IsAuthenticatedOrReadOnly` | Public read, private write |
