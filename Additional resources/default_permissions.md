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

## 📘 Summary

| Permission Class            | Best For                   |
| --------------------------- | -------------------------- |
| `AllowAny`                  | Open APIs                  |
| `IsAuthenticated`           | Private APIs               |
| `IsAdminUser`               | Admin-only access          |
| `IsAuthenticatedOrReadOnly` | Public read, private write |
