# Simple Flask Demo Application

This project demonstrates a basic Flask application to showcase the framework's features with minimal complexity. Below are step-by-step instructions for building, running, and deploying the Flask app.

---

## Application Overview
The Flask application is a simple **Task Manager** that allows users to:
- View a list of tasks.
- Add a new task.
- Delete an existing task.

---

## Prerequisites

### Install Required Tools:
- Python 3.8+
- Pip package manager

### Install Flask:
```bash
pip install flask
```

### Directory Structure:
```
simple_flask_app/
├── app.py
├── templates/
│   ├── index.html
├── static/
│   ├── style.css
├── requirements.txt
```

---

## Step 1: Set Up the Flask Application

1. **Create Application Folder:**
   ```bash
   mkdir simple_flask_app
   cd simple_flask_app
   ```

2. **Set Up Virtual Environment (Optional):**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install Flask:**
   ```bash
   pip install flask
   ```

4. **Create `app.py` File:**
   ```python
   from flask import Flask, render_template, request, redirect, url_for

   app = Flask(__name__)

   # In-memory storage for tasks
   tasks = []

   @app.route("/")
   def index():
       return render_template("index.html", tasks=tasks)

   @app.route("/add", methods=["POST"])
   def add_task():
       task = request.form.get("task")
       if task:
           tasks.append(task)
       return redirect(url_for("index"))

   @app.route("/delete/<int:task_id>")
   def delete_task(task_id):
       if 0 <= task_id < len(tasks):
           tasks.pop(task_id)
       return redirect(url_for("index"))

   if __name__ == "__main__":
       app.run(debug=True)
   ```

5. **Add Requirements File:**
   ```bash
   pip freeze > requirements.txt
   ```

---

## Step 2: Create Templates and Static Files

1. **Create `templates/index.html`:**
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Task Manager</title>
       <link rel="stylesheet" type="text/css" href="/static/style.css">
   </head>
   <body>
       <h1>Task Manager</h1>
       <form action="/add" method="post">
           <input type="text" name="task" placeholder="Enter a task">
           <button type="submit">Add Task</button>
       </form>
       <ul>
           {% for task in tasks %}
           <li>
               {{ loop.index }}. {{ task }}
               <a href="/delete/{{ loop.index0 }}">Delete</a>
           </li>
           {% endfor %}
       </ul>
   </body>
   </html>
   ```

2. **Create `static/style.css`:**
   ```css
   body {
       font-family: Arial, sans-serif;
       margin: 20px;
   }

   h1 {
       color: #333;
   }

   ul {
       list-style-type: none;
       padding: 0;
   }

   li {
       margin: 5px 0;
   }

   a {
       margin-left: 10px;
       color: red;
       text-decoration: none;
   }

   a:hover {
       text-decoration: underline;
   }
   ```

---

## Step 3: Run the Flask Application

1. **Run the Application:**
   ```bash
   python app.py
   ```

2. **Access the Application:**
   Open [http://127.0.0.1:5000](http://127.0.0.1:5000) in your browser.

---

## Step 4: Deployment (Optional)

### Deploy to Heroku:
1. **Create a `Procfile`:**
   ```
   web: python app.py
   ```

2. **Add `requirements.txt`:**
   Ensure it includes `Flask`.

3. **Initialize Git Repository:**
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   ```

4. **Deploy:**
   - Install Heroku CLI and log in.
   - Create a Heroku app and push the repository.

---

## Conclusion
This simple Flask application provides a basic Task Manager with functionality to add and delete tasks. The project is easy to extend with additional features such as a database or user authentication.
