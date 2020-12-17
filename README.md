# Some Instructions to make server hosting for Django

#### Note: this is the my notes for setting up my personal website, hosing on my raspberryPI, url : [http://www.shaw-yu.com]

1. Copy static files to apache, not work to serve Django
[Ubuntu web server](https://github.com/AhoyKakkoii/AWS-EC2-Django-Deploy/blob/main/Ubuntu-Web-server.md)
https://blog.learningdollars.com/2020/01/16/how-to-host-a-django-api-using-mod-wsgi-serving-a-react-frontend-on-an-apache-server-on/

2. Deploy to AWS ubuntu instances, steps are redundent.
https://www.youtube.com/watch?v=u0oEIqQV_-E

3. [Ubuntu Django Server](https://github.com/AhoyKakkoii/Django-Deploy-Doc/blob/main/Ubuntu-Django-Server.md)

4. [how to serve asgi application i.e django+channels(websocket) using nginx and daphne](https://github.com/AhoyKakkoii/Django-Deploy-Doc/blob/main/ASGI-Deploy.md)


5. make subdomain, e.g. delit.shaw-yu.com in domain of shaw-yu.com:
refer to [what is subdomain](https://www.domain.com/blog/2019/01/15/subdomain/)
and [how to configure nginx for serving multiple sites](https://webdock.io/en/docs/how-guides/how-configure-nginx-to-serve-multiple-websites-single-vps)
