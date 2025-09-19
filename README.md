# django-todo-react

#INTRODUCTION

prerequisites

1. Install python3, pip v20, Django, npm and Node.js

2. Setting up the Backend
   
         mkdir django-todo-react && cd django-todo-react
         pip install pipenv
         pipenv shell
3. Setup Django

         pipenv install django
         django-admin startproject backend
         cd backend
4. Start New Application called todo

         python manage.py startapp todo
         python manage.py migrate
         python manage.py runserver 0.0.0.0:8000
   
5. Navigate to http://localhost:8000 in your web browser
6.             http://ec2-public-ip:8000

7. Register todo Application
   backend/settings.py
         # Application definition

       INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'todo', ## add this new
        ]
  8. 
