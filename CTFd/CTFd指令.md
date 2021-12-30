```
1.安裝CTFd主要系統
(1)sudo apt-get update -y;sudo reboot
(1-1)sudo apt-get install python3-pip -y; sudo python3 -m pip install Flask; sudo apt install mariadb-server -y;sudo apt-get install libmariadbclient-dev -y ; sudo apt install nginx -y; sudo pip3 install uwsgi; sudo apt install uwsgi -y; sudo apt install redis-server -y; sudo apt-get install unzip -y; sudo apt-get install -y uwsgi-plugin-python3; sudo apt-get install -y docker; sudo apt-get install -y docker.io;
(2)sudo git clone https://github.com/CTFd/CTFd.git
以下為上列的整理，輸入上列即可
```
sudo apt-get update -y; 
sudo apt-get install python3-pip -y;
sudo python3 -m pip install Flask;
sudo apt install mariadb-server -y;
sudo apt-get install libmariadbclient-dev -y;
sudo apt install nginx -y; 
sudo pip3 install uwsgi; 
sudo apt install uwsgi -y; 
sudo apt install redis-server -y; 
sudo apt-get install unzip -y; 
sudo apt-get install -y uwsgi-plugin-python3; 
sudo apt-get install -y docker; 
sudo apt-get install -y docker.io;
``````
(3)sudo vim prepare.sh
更改pip為pip3
`````` 
進入文件按i編輯
Ctrl+c中斷
:wq!強制寫入即離開
    #!/bin/sh
    sudo apt-get update
    sudo DEBIAN_FRONTEND=noninteractive apt-get install -y build-essential python-dev python-pip libffi-dev
    pip3 install -r requirements.txt
``````
(4)sudo sh prepare.sh
2.調整CTFd內參數
(1)sudo vim /etc/redis/redis.conf
``````
supervised no
-->
supervised systemd

``````
(2)sudo vim CTFd/CTFd/config.py

``````
REDIS_URL = 'redis://127.0.0.1:6379'

``````
(2) sudo passwd root 
密碼: Ksu@0956327000
(3) sudo mysql -u root -p

set character_set_server = 'latin1';
set character_set_results = 'utf8';
set character_set_filesystem = 'binary';
set character_set_database = 'latin1';
set character_set_connection = 'utf8';
set character_set_client = 'utf8';

SHOW VARIABLES LIKE 'character\_set\_%';

create database ctfd;
CREATE USER 'ksu'@'%' IDENTIFIED BY 'Ksu@0956327000';
GRANT ALL PRIVILEGES ON *.* TO 'ksu'@'%' IDENTIFIED BY 'Ksu@0956327000';
FLUSH PRIVILEGES;
exit;



(16)sudo cp wsgi.py wsgi_old.py
(17)sudo vim wsgi.py
``````
import os
import sys
# Detect if we're running via `flask run` and don't monkey patch
#if not os.getenv("FLASK_RUN_FROM_CLI"):
#    from gevent import monkey

#    monkey.patch_all()
sys.path.insert(0,'/home/ksu/CTFd') #這裡的路徑需要更改對應主目錄名稱 ==> 例如: /home/a615119/CTFd  

from CTFd import create_app

app = create_app()
``````

(18)sudo vim /etc/uwsgi/apps-available/uwsgi.ini

```
[uwsgi]
# Where you've put CTFD
#http=127.0.0.1:65535
chdir = /home/ksu/CTFd/ #這裡的路徑需要更改對應主目錄名稱 ==> 例如: /home/a615119/CTFd 

mount = /="CTFd:create_app()"

plugins-dir = /usr/lib/uwsgi/
plugin = /usr/lib/uwsgi/plugins/python3_plugin.so
socket= /tmp/uwsgi.sock
chmod-socket = 666
master=true
processes = 10
threads = 20
max-requests = 100
enable-threads = true
vacuum = true
mod-socket = 666
manage-script-name = true
wsgi-file = wsgi.py
callable = app

limit-as = 6144
limit-post = 104857600
reload-on-as = 1024
reload-on-rss = 1024
#evil-reload-on-as = 2048
#evil-reload-on-rss = 1024
#max-requests = 1000
#max-worker-lifetime = 600
#reload-on-rss = 6144
#worker-reload-mercy = 600

harakiri = 60

#die-on-term = true


#socket=/tmp/uwsgi.sock
uid = www-data
gid = www-data
daemonize=/var/log/uwsgi/ctfd.log
``````

(19)sudo ln -s /etc/uwsgi/apps-available/uwsgi.ini /etc/uwsgi/apps-enabled/uwsgi.ini
(20)sudo uwsgi -d --ini /etc/uwsgi/apps-enabled/uwsgi.ini
(21)sudo chown -R www-data:www-data /home/ksu/CTFd/ 這裡的路徑需要更改對應主目錄名稱 ==> 例如: /home/a615119/CTFd 
 

(22)sudo rm /etc/nginx/sites-available/default
(23)sudo vim /etc/nginx/sites-available/default
``````
server {
        listen 80 default_server;
        listen [::]:80 default_server;
	root /home/ksu/CTFd; 
	index index.html index.htm index.nginx-debian.html;

        #server_name 120.114.62.218;

	location / {
             #try_files $uri $uri/ =404;
             root /home/ksu/CTFd;
             include uwsgi_params;
             uwsgi_pass unix:/tmp/uwsgi.sock;
             uwsgi_read_timeout 300s;
        }
	location /static {
               root /home/ksu/CTFd/CTFd/themes/core/static/; 
        }
	client_max_body_size 1000m;
        }
``````
(24)sudo service nginx restart
(25)sudo reboot
UPDATE `challenges` SET `description` = replace(`description`, '140.110.112.226', '');
