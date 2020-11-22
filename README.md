# AWS EC2 Django Deployment

Notes:
* Using OS ubuntu 20.0.4 in EC2
* Using AWS student account
* Reference: https://www.youtube.com/watch?v=u0oEIqQV_-E

Steps:
1. Go to aws and create an EC2 instance
  * select ubuntu 20.0.4
  * useful rules: http, https for hosting
  * bind a ssh key pair, create and save or use exist
  
2. Before deployment
  * Disable debug mode and allow hosts in Django app settings.py
  * check deploy requirements `python manage.py check --deploy`
  * follow steps in console
  
3. Run EC2 Instence
  * connect SSH: select running instance and select connect->ssh client, copy command go to ssh key directory and paste run command
  * initialize: `sudo apt-get update && sudo apt-get upgrade -y`
  * install: `sudo apt-get install python3-venv -y`
  * create virtual env: `python3 -m venv env`
  * activate virtual env: `source env/bin/activate`
  * create django app: `pip3 install django`
  * get app from local, use git clone or ftp(setup needed)
  * install: `pip3 install gunicorn`
  * install: `sudo apt-get install nginx -y`
  * enable ec2 security groups inbound rules: http, https, port8000
  * test to tell gunicorn to server django by run wsgi.py: `gunicorn --bind 0.0.0.0:8000 react_app.wsgi:application`
  * go to ec2 url:8000
  * to make sure gunicorn runs in background and runs automatically, use supervisor
  * install: `sudo apt-get install -y supervisor`
  * edit configuration: `sudo nano /etc/supervisor/conf.d/gunicorn.conf`
  * add following && save:
  ```
  [program:gunicorn]
  directory=/home/ubuntu/react_app
  command=/home/ubuntu/env/bin/gunicorn --workers 3 --bind unix:/home/ubuntu/react_app/app.sock react_app.wsgi:application
  autostart=true
  autorestart=true
  stderr_logfile=/var/log/gunicorn/gunicorn.err.log
  stdout_logfile=/var/log/gunicorn/gunicorn.out.log
  
  [group:guni]
  programs:gunicorn
  ```
  * make supervisor read the config: `sudo mkdir /var/log/gunicorn && sudo supervisorctl reread`
  * tell supervisor to start gunicorn: `sudo supervisorctl update`
  * configurate nginx: `sudo nano /etc/nginx/sites-availables/django.conf`
  * add following:
  ```
  server{
      listen 80;
      server_name <ec2 instance public DNS>;
   
      location / {
          include proxy_params;
          proxy_pass http://unix:/home/ubuntu/react_app/app.sock;
      }
  }
    
  ```
  * test configuration: `sudo nginx -t`
  * enable configuration: `sudo ln django.conf /etc/nginx/sites-enabled/`
  * start nginx: `sudo service nginx restart`
