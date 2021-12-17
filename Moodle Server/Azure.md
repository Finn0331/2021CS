# 在Azure上建立Moodle
#### 注意事項: 建立好虛擬機器後需新增http、https連線方式
```
- sudo yum update //更新套件
```
### 阿帕契
```
- 1.sudo yum install httpd //安裝阿帕契
- 2.sudo systemctl start httpd.service //開啟伺服器
- 3.sudo systemctl enable httpd //開機自動啟用阿帕契
- 4.sudo systemctl status httpd.service //確認伺服器狀態
```
### [PHP(CentOS 7.9)](https://www.itzgeek.com/how-tos/linux/centos-how-tos/how-to-install-php-7-3-on-rhel-8.html)
```
- 1.sudo yum install epel-release//安裝EPEL
- 2.sudo rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm //安裝remi
- 3.sudo yum --enablerepo=remi-php73 install php //安裝PHP7.3
- 4.php -v //檢查版本
- 5.yum --enablerepo=remi-php73 install php-xml php-soap php-xmlrpc php-mbstring php-json php-gd php-mcrypt //安裝模塊
```
### [PHP(CentOS 8)](https://www.itzgeek.com/how-tos/linux/centos-how-tos/how-to-install-php-7-3-on-rhel-8.html)
```
- 安裝Remi
    - 1.sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
    - 2.sudo dnf install -y https://rpms.remirepo.net/enterprise/remi-release-8.rpm
- 3.sudo dnf module list php //列出可用PHP
- 4.sudo dnf module enable php:remi-7.4 -y //啟用PHP 7.4模塊
- 5.dnf install -y php php-cli php-common //安裝PHP7.4
```
### 安裝PHP擴充SQL
```
- 1.dnf install -y php-mysqlnd //安裝php-mysqlnd包
- 2.php -m | grep -i mysql //驗證有無安裝

### 必要擴充
- 1.sudo yum install php-zip
- 2.sudo yum install php-gd php-init
```

### Mariadb
```
- 1.sudo yum install mariadb-server mariadb //安裝Mariadb
- 2.sudo systemctl start mariadb //開啟Mariadb
- 3.sudo systemctl enable mariadb //設定開機啟動Mariadb
```
### 資料庫
```
- 1.sudo mysql -u root -p //登入root
- 2.create database moodle; //建立資料庫 ///moodle(資料庫名稱)
- 3.CREATE USER 'ksu'@'%' IDENTIFIED BY 'PASSWORD'; //建立使用者及密碼
- 4.GRANT ALL PRIVILEGES ON *.* TO 'ksu'@'%' IDENTIFIED BY 'PASSWORD'; //給予權限
- 5.FLUSH PRIVILEGES; //刷新權限
```
### Moodle
```
- 1.cd /var/www/html //位置到html
- 2.sudo wget https://download.moodle.org/download.php/direct/stable311/moodle-latest-311.tgz //下載moodle
- 3.sudo tar zxvf moodle-latest-311.tgz //解壓縮
- 4.sudo mkdir ../moodledata //建立moodle資料位置
- 5.sudo chown -R apache:apache  ../html //給予權限
- 6.sudo chown -R apache:apache ../moodledata //給予權限
- 7.sudo systemctl restart httpd //重新啟動
```