# Instructions to start my first django project

## Configure Python and Django environments
- Create your python environment and start django
    ```zsh
    conda create --prefix ./.wttd python=3.9 django 
    conda activate .wttd/
    django-admin startproject eventex .
    ```


- See all the available django's command
    ```zsh
    python manage.py
    ```

- Create an alias to manage.py so we can call it from any activated /.env project
>I am using conda/miniconda. if you have virtualenv just use $VIRTUAL_ENV instead.
    ```zsh
    alias manage='python $CONDA_DEFAULT_ENV/../manage.py'
    ```

- Run the DJANGO server to check if everthing is running
    ```zsh
    manage runserver
    ```

- Open webbrowser and type:
    ```
    http://127.0.0.1:8000/
    The install worked successfully! Congratulations!
    ```

- Lets create the first DJANGO APP
    ```zsh
    cd ~/wttd/eventex
    manage startapp core
    ```

- Now you will see the following:

    ```zsh
    cd ..
    tree eventex

    ├── __init__.py
    ├── __pycache__
    │   ├── __init__.cpython-39.pyc
    │   ├── settings.cpython-39.pyc
    │   ├── urls.cpython-39.pyc
    │   └── wsgi.cpython-39.pyc
    ├── asgi.py
    ├── core
    │   ├── __init__.py
    │   ├── admin.py
    │   ├── apps.py
    │   ├── migrations
    │   │   └── __init__.py
    │   ├── models.py
    │   ├── tests.py
    │   └── views.py
    ├── settings.py
    ├── urls.py
    ```

- On PYCHARM,

1. Open project: ~/wttd
2. Show the python interpreter
3. Open ~/wttd/eventex/settings.py
4. Find and add the following:
    ```python
       INSTALLED_APPS = [
       'django.contrib.admin',
       'django.contrib.auth',
       'django.contrib.contenttypes',
       'django.contrib.sessions',
       'django.contrib.messages',
       'django.contrib.staticfiles',
    -->'eventex.core',
       ]
    ```

5. Save the file.
6. Create the root PATH. 
    ```python
    #open ~/wttd/eventex/urls.py
    #Add the following:

    import eventex.core.views

    path('', eventex.core.views.home),
    ```

7. Open  ~/wttd/eventex/core/views.py
8. Add the following code inside views.py file
    ```python
    from django.shortcuts import render


    def home(request):
        return render(request, 'index.html')
    ```
9. Save the file
10. Create a folder `templates` directory on ~/wttd/eventex/core/
11. Create a file `index.html`  on ~/wttd/eventex/core/templates/
12. Add the following text inside index.html

    ```html
    <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>Title</title>
        </head>
        <body>
            <h1> Welcome to the Eventex!</h1>
        </body>
        </html>
    ```

13. On terminal, run:
    ```zsh
    manage runserver
    ```

14. Open a browser and type:  http://127.0.0.1:8000/
    ```
    You should be able to see `Welcome to the Eventex!
    ```

------
## Working on the web page design
1. Download `landingpage` folder and paste it into ~/wttd
2. On Pycharm, cp all folders and files, except for `index.html`, and paste into a **new directory**
called `~/wttd/core/static/`

3. Make sure that `Search for references` and `Open moved files in editor` are unselected
4. cp `index.html` from `~/wttd/landingpage` and paste on:
`~/wttd/eventex/core/templates`

5. Fix the paths on the `templates/index.html`
    - include the following on the top of the page
    `{% load static %}`

    - Now do the following:
    ```html
    <!-- Favicon -->
    <link rel="shortcut icon" href="{%static 'img/favicon.ico' %}">
    ```

    - Find and replace all, by using regex:
    ```
    FROM: (src|href)="((img|css|js).*?)"
    TO: $1="{% static '$2' %}"
    ```
-----
- Deploying eventex on HEROKU

1. Open www.heroku.com and create your account

2. Install heroku by typing:
    ```
    brew install heroku/brew/heroku
    ```

3. Log in by typing:
    ```
    heroku login
    ```

4. Check you heroku version
    ```zsh
    heroku --version
    ```
5. Install python-decouple.
    ```zsh
    # On terminal ,type
    conda install python-decouple
    ```

6. Open `~/wttd/eventex/settings.py`, and add the following
    ```python
    from decouple import config
    ```

7. Add the following:
    ```python
    SECRET_KEY = config('SECRET_KEY')
    ```
8. Copy the SECRECT_KEY = `xxxxxxxxxxxxxx` and paste on:`~/wttd/.env`
    >remove the space and the ` `` `
    -->`SECRET_KEY=xxxxx`

9. Delete the `old SECRECT_KEY='xxxxx'` variable from the the file settings.py
10. Do the same with debug on settings.py
 
    ```python
    DEBUG = config('DEBUG', default=False, cast=bool)
    
    # cp DEBUG=True  and paste on:`~/wttd/.env` 
    # Delete DEBUG = True from settings file
    ```
11. Open terminal, and type:
    ```zsh
    conda install dj-database-url
    ```
12. Open settings.py, and add the following:
    ```python
    from dj_database_url import parse as dburl
    ```
13. On settings.py, look for DATABASES={ ... and add the following:
    ```python
    default_dburl = 'sqlite:///' + str(BASE_DIR / 'db.sqlite3')
    DATABASES = {
            'default': config('DATABASE_URL', default=default_dburl, cast=dburl),
    }
    ```

14. On settings.py, add the following:
    `ALLOWED_HOSTS = ['*']`

15. On settings.py, configure STATIC_ROOT variable:
    ```python
    STATIC_ROOT = str(BASE_DIR / 'staticfiles')
    ```

16. On terminal, install:
    ```zsh
    conda install dj-static
    ```
    
17. Navigate and open `~/wttd/eventex/wsgi.py`, and add the following:        
    ```python
    import os
    from dj_static import Cling
    from django.core.wsgi import get_wsgi_application

    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'eventex.settings')

    application = Cling(get_wsgi_application())
    ```

18. Install the following:
    ```zsh
    # Webserver that receive request
    pip install gunicorn

    # Driver of database
    conda install psycopg2-binary
    ```

19. Lets register all the dependecies
    ```
    pip list --format=freeze  > requirements.txt
    ```
20. Create a new text file on `~/wttd/Profile` , and type:
    ```
    web: gunicorn eventex.wsgi --log-file -
    ```
----
## Create a git repository
1. Navigate to ~/wttd
2. on terminal, type:
    ```zsh
    git init
    ```
3. Lets ignore some files
    ```zsh
    vi .gitignore
    ```
4. Add .gitignore
    ```zsh
    git add .gitignore
    
    # add the following inside .gitignore
   .env
    .idea
    *.sqlite3
    *pyc
    __pycache__
 
    ```
5. Add the rest of the files
    ```zsh
    git add .
    git commit -S -am 'import project'
    ```
---
## Deploying on heroku

1. On terminal, type
    ```zsh
    # Change fernandopacheco with your own name
    heroku apps:create eventex-fernandopacheco
    ```

2. Check if git knows about our heroku
    ```zsh
    git remote -v

    heroku  https://git.heroku.com/eventex-fernandopacheco.git (fetch)
    heroku  https://git.heroku.com/eventex-fernandopacheco.git (push)
    ```

3. Open it
    ```zsh
    heroku open
    ```
4. Lets config heroku environment
    ```zsh
    heroku config:set SECRET_KEY=''
    #ps: type cat .env on terminal and add the secret key inside the heroku command

    heroku config:set DEBUG=True
    ```

5. Push your project to heroku
    ```zsh
    git push heroku main --force
    ```
    - if fails because of setuptools, try:                                                                                   
    ```zsh
    pip install setuptools --upgrade     
    pip list --format=freeze > requirements.txt  
    
    # run the following again:
    git push heroku main --force 
    ```