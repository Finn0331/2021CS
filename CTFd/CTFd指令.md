# CTFd

- 1.sudo apt update && sudo apt dist-upgrade //更新、升級
- 2.sudo apt install python3-pip mariadb-server libmariadbclient-dev nginx uwsgi redis-server unzip uwsgi-plugin-python3 docker docker.io //安裝額外擴充
- sudo pip3 install Flask uwsgi //安裝Python擴充
- cd ~
git clone https://github.com/CTFd/CTFd.git //下載CTFd
- sudo vim ~/CTFd/prepare.sh //編輯prepare.sh
- sudo sh ~/CTFd/prepare.sh //執行prepare.sh
- sudo vim /etc/redis/redis.conf //編輯redis.conf
- 設定mariadb
	- sudo mysql -u root -p //管理員登入mariadb
	- SET character_set_server = 'latin1';
    SET character_set_results = 'utf8';
    SET character_set_filesystem = 'binary';
    SET character_set_database = 'latin1';
    SET character_set_connection = 'utf8';
    SET character_set_client = 'utf8'; 
    // 設定character
	- SHOW VARIABLES LIKE 'character\_set\_%'; 
//確認設定
	- create database CtfdDataBase;//創建資料庫
	- CREATE USER 'ctfduser'@'%' IDENTIFIED BY 'PASSWORD';//建立使用者
	- GRANT ALL PRIVILEGES ON *.* TO 'ctfduser'@'%' IDENTIFIED BY 'PASSWORD';//給予權限
	- FLUSH PRIVILEGES; //刷新授權
	- quit //登出
- 9.改變CTFd資料庫連結
9-1 sudo vim ~/CTFd/CTFd/config.py //編輯config.py
9-2 DATABASE_URL = 'mysql+pymysql://MariaDB_User:MariaDB_Password@localhost:3306/ctfd'
REDIS_URL = 'redis://127.0.0.1:6379'
//變更DATABASE_URL & REDIS_URL 數值
10.將CTFd路徑添加到python sys.path並註釋一些代碼
10-1 sudo vim ~/CTFd/wsgi.py //編輯wsgi.py
10-2 sys.path.insert(0, '/home/ksu/CTFd')//添加內容並註釋一些代碼
11.變更uwsgi設定
11-1 sudo vim /etc/uwsgi/apps-available/uwsgi.ini //編輯uwsgi.ini
```
11-2 [uwsgi]
 Where you've put CTFD
#http=127.0.0.1:65535
chdir = Your_CTFd_Location

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
```
12.sudo ln -s /etc/uwsgi/apps-available/uwsgi.ini /etc/uwsgi/apps-enabled/uwsgi.ini //連接uwsgi
13.sudo chown -R www-data:www-data ~/CTFd/ //更改CTFd目錄授權
14.變更nginx設定
14-1 sudo rm /etc/nginx/sites-available/default刪除defalt設定
14-2 sudo vim /etc/nginx/sites-available/default創建並編輯新的defalt設定
```
14-3 添加內容
server {
        listen 80 default_server;
        listen [::]:80 default_server;
	root Your_CTFd_directory; 
	index index.html index.htm index.nginx-debian.html;

        #server_name 120.114.62.218;

	location / {
             #try_files $uri $uri/ =404;
             root Your_CTFd_directory;
             include uwsgi_params;
             uwsgi_pass unix:/tmp/uwsgi.sock;
             uwsgi_read_timeout 300s;
        }
	location /static {
               root Your_CTFd_directory/CTFd/themes/core/static/;
        }
	client_max_body_size 1000m;
        }
```
15.創建啟動 CTFd 腳本
15-1 A.start.sh
sudo vim start.sh
sudo chmod +x start.sh
sudo uwsgi -d --ini /etc/uwsgi/apps-enabled/uwsgi.ini
     B.restart.sh
     sudo vim restart.sh
     sudo chmod +x restart.sh
     sudo pkill uwsgi -9
     sudo uwsgi -d --ini /etc/uwsgi/apps-enabled/uwsgi.ini
     C.stop.sh
     sudo vim stop.sh
     sudo chmod +x stop.sh
     sudo pkill uwsgi -9

16.啟動CTFd
chmod +x start.sh
./start.sh 

17.安裝docker questions
17-1 sudo wget http://120.114.62.217/MyFirstSecurity_Docker.zip //安裝questions
17-2 sudo unzip MyFirstSecurity_Docker.zip//解壓縮MyFirstSecurity_Docker.zip
17-3 sudo sh docker.sh //執行docker.sh
17-4 sudo docker ps -a //確保有無運作
17-5 sudo docker exec –ti [CONTAINER ID] bash//連接docker questionsbash
17-6 sh /bin/start.sh //執行start.sh(按enter繼續)
17-7 exit //離開docker questions bash
18.更換questions IP
18-1 sudo mysql –u root –p //登入mariadb(root)
18-2 USE ctfddatabase; // 選擇database
18-3 UPDATE `challenges` SET `description` = replace(`description`, 'old', 'new');// 變更IP
