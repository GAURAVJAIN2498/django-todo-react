# django-todo-react

#INTRODUCTION

prerequisites:

1. Install python3, pip v20, Django, npm and Node.js
## Manual Process
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
   - run these command in ~/backend
   
   backend/settings.py
         # Application definition

       INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'todo',                # add this new
        ]
  9. open todo/models.py add this

    from django.db import models

    # Create your models here.

    class Todo(models.Model):
        title = models.CharField(max_length=120)
        description = models.TextField()
        completed = models.BooleanField(default=False)

        def _str_(self):
            return self.title
9.  

        python manage.py makemigrations todo
        python manage.py migrate todo


10. open todo/admin.py

        from django.contrib import admin
         from .models import Todo

         class TodoAdmin(admin.ModelAdmin):
            list_display = ('title', 'description', 'completed')

         admin.site.register(Todo, TodoAdmin)

11. Create user

        python manage.py createsuperuser
        python manage.py runserver
    
12. Navigate to http://localhost:8000/admin in your web browser.

13. Add backend/settings.py from repo
14. Creating serializers
    
        from rest_framework import serializers
        from .models import Todo

        class TodoSerializer(serializers.ModelSerializer):
            class Meta:
                model = Todo
                fields = ('id', 'title', 'description', 'completed')
15. Add todo/views.py
16. Add backend/urls.py

         python manage.py runserver
    
17. Navigate to http://localhost:8000/admin/api in your web browser

    
# Step 3 — Setting Up the Frontend 
  - run these command in ~/frontend
  - add all the files of frontend
  - # After run the backend server

    

          npx create-react-app frontend
  
18.  

          npm install bootstrap@4.6.0 reactstrap@8.9.0 --legacy-peer-deps
19. Add frontend/src/index.js
    - Add frontend/src/App.js

          mkdir src/components
    - Add frontend/src/components/Modal.js
    - Add frontend/src/App.js

20. start backend

        python manage.py runserver 0.0.0.0:8000  # In Backend
   
21. npm install axios@0.21.1

     - Add frontend/package.json
     - frontend/src/App.js

22. cd ~/frontend

        npm start
    
## Automation

1. setup Jenkins


       sudo apt update
       sudo apt upgrade -y
2. Install docker
     create docker container of jenkins and expose with 8080
3. http://ec2-public-ip:8080

- Get key to open jenkins and do userpass

          sudo cat /var/lib/jenkins/secrets/initialAdminPassword

4. install plugin
   - github plugin
   - github integration plugin

5. Add global credentails of application in ssh username with key
6. create pipeline
7. add gitscm triigger
8. Add pipeline script with SCM give Script with Jenkinsfile
9. create github webhook and give jenkins url

        http://jenkins-public-ip:8080/github-webhook/
10. application/json
11. give [ just can be everything ]
12. install necessary dependencies
13. now create a service file of frontend and backend


/etc/systemd/system/backend.service

     [Unit]
     Description=Django Backend
     After=network.target

     [Service]
     User=root
     WorkingDirectory=/root/django-todo-react/backend
     ExecStart=/usr/local/bin/pipenv run python manage.py runserver 0.0.0.0:8000
     Restart=always
     Environment=PATH=/usr/local/bin:/usr/bin:/bin

     [Install]
     WantedBy=multi-user.target

/etc/systemd/system/frontend.service

    [Unit]
    Description=Django Frontend
    After=network.target

    [Service]
    User=root
    WorkingDirectory=/root/django-todo-react/frontend
    ExecStart=/usr/bin/npm start
    Restart=always
    Environment=PATH=/usr/bin:/bin:/usr/local/bin

    [Install]
    WantedBy=multi-user.target


14. now do change in code by git push
  -> as the code commit pipeline run
    
  -> it can clone the project then create symlink with main project path where i pass application code
  
  -> from new release unlink the last release
  
  -> attach with new release by symlink method
  
  -> from release toproject path and then create and run backend and then create and run frontend
  
  -> remove the unwanted release
