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
  * select running instance and select connect->ssh client, cp ssh command go to ssh key directory and paste run command
2. Before deployment
  * Disable debug mode and allow hosts in Django app settings.py
  * check deploy requirements 'python manage.py check --deploy'
  
  
