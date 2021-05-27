# Server AWS EC2 Ubuntu: NGINX_Gunicorn_djangoapp

## djangoapp
![](images/venv.JPG "venv")
![](images/ubuntu.JPG "ubuntu")
* sudo apt update; sudo apt upgrade -y
* sudo apt-get install -y git python3-dev python3-venv python3-pip supervisor nginx vim libpq-dev
* --> cd djangoapp
* pathon3 -m venv venv
<!--
ubuntu@ip-172-31-11-203:~/djangoapp$ ls
compressormodel  database  djangoapp  manage.py  media  requirements.txt  static  templates  venv  webtools
ubuntu@ip-172-31-11-203:~/djangoapp$ source venv/bin/activate
(venv) ubuntu@ip-172-31-11-203:~/djangoapp$
-->
![](images/Gunicorn.JPG "Gunicorn")
* source venv/bin/activate
* (venv)
* --> install Guincorn
* pip3 install gunicorn
* --> install MySQL
* sudo apt install python3-dev default-libmysqlclient-dev build-essential libssl-dev
* pip3 install -r requirements.txt 
```
asgiref==3.3.1
CoolProp==6.4.1
cycler==0.10.0
Django==3.1.7
django-debug-toolbar==3.2
FMPy==0.2.25
kiwisolver==1.3.1
lark-parser==0.11.2
lxml==4.6.2
matplotlib==3.3.3
numpy==1.19.2
Pillow==8.1.1
pyparsing==2.4.7
python-dateutil==2.8.1
pytz==2021.1
six==1.15.0
sqlparse==0.4.1
whitenoise==5.2.0
scipy
xlrd
pandas
django-cleanup
django-bootstrap4
gunicorn
```

## nginx
![](images/NGINX_default.JPG "NGINX_default")
* sudo apt-get install nginx
* sudo vim /etc/nginx/sites-enabled/default

```
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        location /static/ {
            alias /home/ubuntu/djangoapp/static/; 
        }

        location /media/ {
            alias /home/ubuntu/djangoapp/media/; 
        }

        location / {
            proxy_pass http://127.0.0.1:8000;
            proxy_set_header X-Forwarded-Host $server_name;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_redirect off;
            add_header P3P 'CP="ALL DSP COR PSAa OUR NOR ONL UNI COM NAV"';
            add_header Access-Control-Allow-Origin *;
        }
}
```

## Setup Supervisor
![](images/Supervisor_config.JPG "Supervisor_config")
* cd /etc/supervisor/conf.d/
* sudo vim djangoapp.conf

```
[program:djangoapp]
command = /home/ubuntu/djangoapp/venv/bin/gunicorn djangoapp.wsgi  -b 127.0.0.1:8000 -w 4 --timeout 90
autostart=true
autorestart=true
directory=/home/ubuntu/djangoapp 
stderr_logfile=/var/log/game_muster.err.log
stdout_logfile=/var/log/game_muster.out.log
```

## Update supervisor with the new process
* sudo supervisorctl reread
* sudo supervisorctl update
* sudo supervisorctl restart djangoapp
* sudo supervisorctl stop djangoapp
