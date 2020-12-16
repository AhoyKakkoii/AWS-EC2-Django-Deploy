# How To Deploy ASGI Application On Ubuntu Using Daphne, Nginx

Notes:
1. asgi applications is used in django with channels
2. apache2 doesn't support asgi instead it supports wsgi which doesn't support websocket
3. nginx works to serve static files, proxy, etc.
4. gunicorn with uvicorn can run the server as well but this doc uses daphne.

1. make sure to have daphne installed in virtualenv
```
cd <myproject>
virtualenv env
source env/bin/activate
pip install daphne
```

2. make sure to have nginx installed in the machine
```
sudo apt-get install nginx
```

3. test daphne with asgi application, cannot be viewed fully in broswer with url: <ip>:<port> because daphne doesnot serve static files
```
daphne -b 0.0.0.0 -p 8000 myproject.asgi:application
```
4. create a systemd process runs daphne `sudo nano /etc/systemd/system/my-sites.service`
```
[Unit]
Description=My custom startup script

[Service]
WorkingDirectory=/home/ubuntu/my-sites/
ExecStart=/home/ubuntu/my-sites/env/bin/daphne -b 0.0.0.0 -p 8000 myproject.asgi:application

[Install]
WantedBy=multi-user.target
```
5. start the service and enable it for booting auto start:
```
sudo systemctl start my-sites.service
sudo systemctl enable my-sites.service
```

6. optional: restart the service when necassary:
```
sudo systemctl daemon-reload
sudo systemctl restart my-sites.service
```

5. optional: check the service status or logs
```
sudo systemctl status my-sites.service
sudo journalctl -u my-sites.service
```

6. configure nginx `sudo nano /etc/nginx/sites-available/default`, make sure to change proxy_pass, static folder location and static media location if necessary:
```
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        
        server_name _;
        client_max_body_size 20M;
        charset utf-8;


        location / {
                proxy_pass http://0.0.0.0:8000;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection "upgrade";

                proxy_redirect off;
                proxy_set_header Host $host;
                proxy_set_header X-Real_IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Host $server_name;
        }

        location /static {
                alias /path/to/your/project/static;
        }

        location /media {
                alias /path/to/your/project/media;
        }
}
```

7. Done, should work now.


