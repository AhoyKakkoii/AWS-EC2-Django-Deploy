## How To Host Django React App in Local

Note:
 - OS: Ubuntu 20.0.4, running in my raspberry pi
 - Server Tool: Apache2
 - Reference:https://blog.learningdollars.com/2020/01/16/how-to-host-a-django-api-using-mod-wsgi-serving-a-react-frontend-on-an-apache-server-on/
 
 Steps:
 
 1. Prepare a Django React Package, can follow most steps on: https://github.com/AhoyKakkoii/react-django-init-deploy
 2. Install packages in ubuntu:
`sudo apt-get install python3-pip apache2 libapache2-mod-wsgi-py3`
 3. git clone Django React Package
 4. cd into root `cd react-django-init-app/react_app`
 5. activate virtual env: `virtualenv venv && source venv/bin/activate`
 6. install dependencies: `pip install -r requirements.txt`
 7. deactivate: `deactivate`
 8. configure apache2: 
 `sudo nano /etc/apache2/sites-available/000-default.conf`
 9. add following:
 ```
<Directory /home/[user]/react-django-init-app/react_app/react_app>
        <Files wsgi.py>
            Require all granted
        </Files>
</Directory>

WSGIDaemonProcess react_app python-home=/home/[user]/react-django-init-app/react_app/venv python-path=/home/[user]/react-django-init-app/react_app
WSGIProcessGroup react_app
WSGIScriptAlias /api /home/[user]/react-django-init-app/react-app/react-app/wsgi.py
``` 
10.  set privilleges:
`chmod 664 ~/ldeproject/djangoapache/db.sqlite3`
`sudo chown :www-data ~/ldeproject/djangoapache/db.sqlite3`
`sudo chown :www-data ~/ldeproject/djangoapache`
11. restart apache2:
`sudo systemctl restart apache2`
12. set cors:
`source venv/bin/activate && pip install django-cors-headers`
13. add to settings.py
```
INSTALLED_APPS = [
    ...
    'corsheaders',
   ...
]

MIDDLEWARE = [  # Or MIDDLEWARE_CLASSES on Django < 1.10
    ...
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    ...
]

CORS_ORIGIN_WHITELIST = [
    '*',
]
```
14. install react frontend: `create-react-app frontend`
15. extra apache2 configuration: `nano public/.htaccess`
16. add:
```
Options -MultiViews
   RewriteEngine On
   RewriteCond %{REQUEST_FILENAME} !-f
   RewriteRule ^ index.html [QSA,L]
```
17. `npm run build`
18. serve static files:
```
[sudo] rm /var/www/html/index.html
sudo cp -r ~/ldeproject/reactapache/apachereactapp/build/* /var/www/html/
sudo systemctl restart apache2
```
20. configure router's port forwarding, open port 80 for http
21. search current external IP in browser and see the app running
