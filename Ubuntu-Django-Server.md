## Host a Django backend, React frontend server on Ubuntu
Notes:

 - server: apache2
 - reference: [link](https://www.digitalocean.com/community/tutorials/how-to-serve-django-applications-with-apache-and-mod_wsgi-on-ubuntu-16-04#configure-a-python-virtual-environment)
 - OS: ubuntu 20.0.4, works on raspberry pi.

Steps:
1. install packages:
```
sudo apt-get install python3-pip apache2 libapache2-mod-wsgi-py3
```
2. create virtual env and activate:
```
mkdir ~/django-react-app
cd ~/django-react-app
virtualenv env
source env/bin/activate
```
3. create django app:
```
pip install django
django-admin.py startproject myproject .
```
4. configure settings, add/edit following to myproject/settings.py
```
ALLOWED_HOSTS = ["*"]

import os
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
```
5. initialize app
```
cd myproject
./manage.py makemigrations
./manage.py migrate
```
6. create frontend app
```
sudo npm -i -g create-react-app
create-react-app frontend
cd frontend && npm run build
cd .. && ./manage.py collectstatic
```
7. configure frontend app, add following to myproject/views.py
```
from django.shortcuts import render
from django.http import HttpResponse
from django.views.generic import View
from django.views.decorators.csrf import csrf_exempt
from django.conf import settings

import logging
import os

class FrontendAppView(View):
    """
    Serves the compiled frontend entry point (only works if you have run `yarn
    run build`).
    """
    def get(self, request):
            print (os.path.join(settings.REACT_APP_DIR, 'build', 'index.html'))
            try:
                with open(os.path.join(settings.REACT_APP_DIR, 'build', 'index.html')) as f:
                    return HttpResponse(f.read())
            except FileNotFoundError:
                logging.exception('Production build of app not found')
                return HttpResponse(
                    """
                    This URL is only used when you have built the production
                    version of the app. Visit http://localhost:3000/ instead, or
                    run `yarn run build` to test the production version.
                    """,
                    status=501,
                )
```
8. configure django-react, add/edit folloing to myproject/settings.py
```
REACT_APP_DIR = os.path.join(BASE_DIR, 'frontend')
STATICFILES_DIRS = [
    os.path.join(REACT_APP_DIR, 'build', 'static'),
]
```

9. confgure django-react, add/edit following to myproject/urls.py
```
from django.conf.urls import url
from django.contrib import admin
from django.urls import path
from myproject.views import FrontendAppView
urlpatterns = [
    path('admin/', admin.site.urls),
    url(r'^', FrontendAppView.as_view())
]
```
10. test server locally, run following then open the ubuntu url (http://ip:8000) in browser
```
sudo ufw allow 8000
./manage.py runserver 0.0.0.0:8000
```
11.  deactivate
```
deactivate
```
12.configure apache2, add/edit following to /etc/apache2/sites-available/000-default.conf, change ubuntu to your username
```
<VirtualHost *:80>
    . . .

    Alias /static /home/ubuntu/django-react-app/myproject/static
    <Directory /home/ubuntu/django-react-app/myproject/static>
        Require all granted
    </Directory>

    <Directory /home/ubuntu/django-react-app/myproject/myproject>
        <Files wsgi.py>
            Require all granted
        </Files>
    </Directory>

    WSGIDaemonProcess myproject python-home=/home/ubuntu/django-react-app/env python-path=/home/ubuntu/django-react-app
    WSGIProcessGroup myproject
    WSGIScriptAlias / /home/ubuntu/django-react-app/myproject/myproject/wsgi.py

</VirtualHost>
```

13.  give permissions and restart apache2
```
chmod 664 ~/django-react-app/myproject/db.sqlite3
sudo chown :www-data ~/django-react-app/myproject/db.sqlite3
sudo chown :www-data ~/django-react-app/myproject
sudo ufw delete allow 8000
sudo ufw allow 'Apache Full'
sudo systemctl restart apache2
```
