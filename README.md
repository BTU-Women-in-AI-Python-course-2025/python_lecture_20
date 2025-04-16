# Django Rest Framework

- **Default Permissions** - https://www.django-rest-framework.org/api-guide/permissions/:
  - Control access to your API endpoints.
- **Custom Permissions** - https://www.django-rest-framework.org/api-guide/permissions/#custom-permissions:
  - Define and apply custom permissions to control access to specific parts of your API, ensuring security and privacy
- **ViewSet Extra Actions** - https://www.django-rest-framework.org/api-guide/viewsets/#marking-extra-actions-for-routing
  
### 📚 **Student Task: Add Permissions and a Custom Action to a ViewSet**

1. **Use an existing ViewSet** (e.g., `ArticleViewSet`).

2. **Create a custom permission** (`IsAuthor`) that only allows the author of an article to update or delete it.

3. **Apply the custom permission** to your ViewSet (e.g., `ArticleViewSet`).

4. **Add an extra action** e.g., to the ArticleViewSet called `publish` (use `@action`) that updates a `published` field to `True`.

---

#### 🔍 Example Output:
- `PUT /articles/3/` → only works if the logged-in user is the article’s author.
- `POST /articles/3/publish/` → sets `published = True` for that article.
