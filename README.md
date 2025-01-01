# Flask and Django Combined Demo Application

This project combines a Flask and Django application into a single application to demonstrate their capabilities while minimizing redundancy. Below are step-by-step instructions for building, running, and deploying this combined application.

---

## Application Overview
The application will have the following features:
1. **Flask Component:** Handles a basic API for a "Task Manager" with CRUD operations.
2. **Django Component:** Implements a basic web interface to display and manage tasks.

---

## Prerequisites

### Install Required Tools:
- Python 3.8+
- Pip package manager
- Virtual environment tools (e.g., `venv` or `virtualenv`)

### Install Required Packages:
```
pip install flask django flask-restful
```

### Directory Structure:
```
combined_app/
├── flask_app/
│   ├── app.py
│   ├── models.py
│   ├── requirements.txt
├── django_app/
│   ├── manage.py
│   ├── tasks/
│   │   ├── models.py
│   │   ├── views.py
│   │   ├── admin.py
│   │   ├── urls.py
│   ├── settings.py
│   ├── urls.py
├── README.md
```

---

## Step 1: Set Up Flask Application

1. **Create Flask Application Folder:**
   ```
   mkdir combined_app/flask_app
   cd combined_app/flask_app
   ```

2. **Set Up Virtual Environment:**
   ```
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install Flask and Dependencies:**
   ```
   pip install flask flask-restful
   ```

4. **Create `app.py` File:**
   ```python
   from flask import Flask, jsonify, request
   from flask_restful import Resource, Api

   app = Flask(__name__)
   api = Api(app)

   # In-memory data storage
   tasks = []

   class Task(Resource):
       def get(self):
           return jsonify(tasks)

       def post(self):
           task = request.json
           tasks.append(task)
           return jsonify(task), 201

       def delete(self):
           task_id = request.json.get('id')
           global tasks
           tasks = [task for task in tasks if task['id'] != task_id]
           return '', 204

   api.add_resource(Task, '/tasks')

   if __name__ == "__main__":
       app.run(debug=True)
   ```

5. **Run Flask Application:**
   ```
   python app.py
   ```
   Open [http://127.0.0.1:5000/tasks](http://127.0.0.1:5000/tasks) to test.

6. **Add Requirements File:**
   ```
   pip freeze > requirements.txt
   ```

---

## Step 2: Set Up Django Application

1. **Create Django Application Folder:**
   ```
   cd ..  # Back to combined_app directory
   django-admin startproject django_app
   cd django_app
   python manage.py startapp tasks
   ```

2. **Configure `settings.py`:**
   - Add `tasks` to the `INSTALLED_APPS` list:
     ```python
     INSTALLED_APPS = [
         ...
         'tasks',
     ]
     ```

3. **Create Task Model in `tasks/models.py`:**
   ```python
   from django.db import models

   class Task(models.Model):
       title = models.CharField(max_length=200)
       description = models.TextField()
       completed = models.BooleanField(default=False)

       def __str__(self):
           return self.title
   ```

4. **Register Model in `tasks/admin.py`:**
   ```python
   from django.contrib import admin
   from .models import Task

   admin.site.register(Task)
   ```

5. **Migrate Database:**
   ```
   python manage.py makemigrations
   python manage.py migrate
   ```

6. **Create Views in `tasks/views.py`:**
   ```python
   from django.shortcuts import render
   from .models import Task

   def task_list(request):
       tasks = Task.objects.all()
       return render(request, 'tasks/task_list.html', {'tasks': tasks})
   ```

7. **Create URLs in `tasks/urls.py`:**
   ```python
   from django.urls import path
   from . import views

   urlpatterns = [
       path('', views.task_list, name='task_list'),
   ]
   ```

8. **Include Task URLs in `django_app/urls.py`:**
   ```python
   from django.contrib import admin
   from django.urls import path, include

   urlpatterns = [
       path('admin/', admin.site.urls),
       path('tasks/', include('tasks.urls')),
   ]
   ```

9. **Create Template in `tasks/templates/tasks/task_list.html`:**
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Task List</title>
   </head>
   <body>
       <h1>Tasks</h1>
       <ul>
           {% for task in tasks %}
               <li>{{ task.title }}: {{ task.description }}</li>
           {% endfor %}
       </ul>
   </body>
   </html>
   ```

10. **Run Django Application:**
    ```
    python manage.py runserver
    ```
    Open [http://127.0.0.1:8000/tasks/](http://127.0.0.1:8000/tasks/) to test.

---

## Step 3: Combine Flask and Django

1. **Run Flask and Django Applications Together:**
   - Start the Flask app (`python flask_app/app.py`).
   - Start the Django app (`python manage.py runserver`).

2. **Integrate Flask API into Django Frontend:**
   - Use JavaScript in `task_list.html` to fetch data from the Flask API.
   - Example:
     ```html
     <script>
         fetch('http://127.0.0.1:5000/tasks')
             .then(response => response.json())
             .then(data => console.log(data));
     </script>
     ```

---

## Step 4: Deployment

### Deploy Flask App
1. **Containerize Flask App (Optional):**
   - Create a `Dockerfile` for Flask:
     ```dockerfile
     FROM python:3.8-slim
     WORKDIR /app
     COPY . .
     RUN pip install -r requirements.txt
     CMD ["python", "app.py"]
     ```
   - Build and run the Docker container.

2. **Deploy on Cloud (AWS/Google Cloud/Azure):**
   - Use services like AWS Elastic Beanstalk or Google App Engine.

### Deploy Django App
1. Use a cloud platform like **Heroku**, **AWS**, or **Google Cloud**.
2. Add a production-ready database (e.g., PostgreSQL).
3. Configure `ALLOWED_HOSTS` and `DEBUG` settings for production.

---

## Conclusion
This combined Flask and Django application demonstrates the strengths of both frameworks. Flask serves as a lightweight API for task management, while Django provides a robust frontend for interacting with the data. This setup showcases the flexibility of combining these two frameworks effectively.
