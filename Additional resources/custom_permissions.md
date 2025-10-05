# 🛡️ Django REST Framework: Custom Permissions

## Why Use Custom Permissions?

Built-in permissions like `IsAuthenticated` or `IsAdminUser` are great, but sometimes you need more control, like:

* "Only the owner of an object can edit it."
* "Only users from a certain group can delete."
* "Only users with verified email can post."

That's where **custom permission classes** come in.

---

## How to Create a Custom Permission

Create a custom permission by subclassing `BasePermission` and overriding the `has_permission()` and/or `has_object_permission()` methods.

---

### ✅ Example 1: Only Staff Can Access

```python
from rest_framework.permissions import BasePermission

class IsStaffUser(BasePermission):
    def has_permission(self, request, view):
        return request.user and request.user.is_staff
```

Apply it to a view:

```python
class StaffOnlyView(APIView):
    permission_classes = [IsStaffUser]

    def get(self, request):
        return Response({"message": "Hello, staff!"})
```

---

### ✅ Example 2: Only Owner Can Edit

```python
class IsOwner(BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.owner == request.user
```

Use this in a detail view or `ModelViewSet`:

```python
class ArticleViewSet(viewsets.ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    permission_classes = [IsOwner]
```

DRF will automatically call `has_object_permission()` for detail views (like `retrieve`, `update`, `destroy`).

---

### ✅ Example 3: Check Custom User Attribute

```python
class IsVerifiedUser(BasePermission):
    def has_permission(self, request, view):
        return request.user.is_authenticated and request.user.is_verified
```

---

### ✅ Example 4: Only Read Access for Non-Admins

```python
class ReadOnlyOrAdmin(BasePermission):
    def has_permission(self, request, view):
        # SAFE_METHODS = ['GET', 'HEAD', 'OPTIONS']
        if request.method in ('GET', 'HEAD', 'OPTIONS'):
            return True
        return request.user and request.user.is_staff
```

This allows anyone to view (`GET`), but only admins can modify data (`POST`, `PUT`, `DELETE`).

---

### ✅ Example 5: Only Members of a Specific Group Can Access

```python
class IsInEditorsGroup(BasePermission):
    def has_permission(self, request, view):
        return (
            request.user 
            and request.user.is_authenticated 
            and request.user.groups.filter(name='Editors').exists()
        )
```

Use this when only users from a particular group (e.g., `"Editors"`) should be allowed to perform actions on the endpoint.

---

## 🔁 Method Reference

| Method                    | When It's Called                          |
| ------------------------- | ----------------------------------------- |
| `has_permission()`        | On general access (list/create requests)  |
| `has_object_permission()` | On detail object (retrieve/update/delete) |

---

## 💡 Tips

* Use `has_permission()` for general access rules.
* Use `has_object_permission()` when access depends on a specific object.
* Chain multiple permission classes using a list: all must pass.
