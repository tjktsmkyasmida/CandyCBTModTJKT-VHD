![image.png](https://tjkt.smkyasmida.sch.id/wp-content/uploads/2023/01/tjktxyz.png)
    
# **CandyCBTModTJKT-VHD**
VHD ini merupakan cloud image dari aplikasi yang dikembangkan oleh https://cbtcandy.com/ adapun, image ini sudah dioptimasi untuk kebutuhan ujian online bersama secara offline.

### **BAHAN**
- Aplikasi : **VirtualBox 7.0.14**
- Sistem Operasi : **ubuntu-20.04.6-live-server-amd64.iso**
- Koneksi Internet
- Sajen : Kopi Hitam + Surya 16

### **SETTINGANKU**
- Type    : **Linux**
- Version  : **Ubuntu 20.04 LTS (Focal Fossa) (64-bit)**
- RAM  : **4096 MB**
- CPU  : **4 CPUs**
- HDD  : **8 GB**
- Network :
  * Adapter 1  : **NAT**
  * Adapter 2  : **Bridge Adapter**
  * Adapter 3  : **Host-only Adapter**
- Extended Features :
  * **Enable PAE/NX**
  * **Enable Nested VT-x/AMD-V**

### **CATATAN**
- Webserver	: **Apache2.4.41**
- Php		: **php7.4-fpm / 7.4.33**
- Database	: **MariaDB-10.3.39**
- PhpMyAdmin	:
- File Manager	: **Tiny File Manager**

-----
## **KONFIGURASI UBUNTU 20.04**

### **REPOSITORY UBUNTU SERVER 20.04**
**nano /etc/apt/sources.list**

    deb http://buaya.klas.or.id/ubuntu/ focal main restricted
    deb http://buaya.klas.or.id/ubuntu/ focal-updates main restricted
    deb http://buaya.klas.or.id/ubuntu/ focal universe
    deb http://buaya.klas.or.id/ubuntu/ focal-updates universe
    deb http://buaya.klas.or.id/ubuntu/ focal multiverse
    deb http://buaya.klas.or.id/ubuntu/ focal-updates multiverse
    deb http://buaya.klas.or.id/ubuntu/ focal-backports main restricted universe multiverse
    deb http://buaya.klas.or.id/ubuntu/ focal-security main restricted
    deb http://buaya.klas.or.id/ubuntu/ focal-security universe
    deb http://buaya.klas.or.id/ubuntu/ focal-security multiverse

apt-get update
apt-get upgrade
reboot

### **KONFIGURASI SSH**
**nano /etc/ssh/sshd_config**

    port 22
    Permit RootLogin Yes

systemctl restart ssh
Remote dengan Putty / Kitty

### INSTALL WEB SERVER APACHE 2
apt update
apt install apache2 apache2-utils apache2-bin apache2-data libapache2-mod-fcgid -y
service apache2 start
systemctl enable apache2
service apache2 status

a2dismod mpm_event
a2dismod mpm_prefork
a2enmod mpm_worker
systemctl restart apache2
apachectl -M | grep 'mpm'

**nano /etc/apache2/mods-available/mpm_worker.conf**

    <IfModule mpm_worker_module>
            StartServers             4
            MinSpareThreads          32
            MaxSpareThreads          64
            ThreadLimit              64
            ThreadsPerChild          32
            MaxRequestWorkers        64
            MaxConnectionsPerChild   0
    </IfModule>

systemctl restart apache2
apache2ctl -t
systemctl status apache2

**nano /etc/apache2/mods-available/fcgid.conf**

    FcgidIdleTimeout 1200
    FcgidProcessLifeTime 1200
    FcgidConnectTimeout 1200
    FcgidIOTimeout 1200

a2dismod ssl
a2enmod remoteip

**nano /etc/apache2/apache2.conf**

    Mutex file:${APACHE_LOCK_DIR} default
    Timeout 1200
    ProxyTimeout 1200
    KeepAlive On
    MaxKeepAliveRequests 0
    KeepAliveTimeout 5

/etc/init.d/apache2 restart

mkdir -p /var/www/candycbt
chown -R www-data:www-data /var/www/candycbt

service apache2 restart
apache2ctl configtest

### INSTALL PHP 7.4
apt-get update -y
apt-get update --fix-missing

apt install php7.4 php7.4-fpm php7.4-cgi php7.4-mbstring php7.4-curl php7.4-mysql php7.4-common libapache2-mod-php7.4 libapache2-mod-php7.4  php7.4-dev php7.4-bz2 php7.4-curl php7.4-intl php7.4-bcmath php7.4-cli php7.4-soap php7.4-zip php7.4-opcache php7.4-pdo php7.4-dom php7.4-apcu php7.4-xml php7.4-xmlrpc php7.4-gd php7.4-gettext php7.4-enchant -y

nano /etc/apache2/sites-available/candycbt.conf

<VirtualHost *:80>
    ServerName localhost
    ServerAdmin tjkt@localhost
    DocumentRoot /var/www/candycbt
    
	<Directory /var/www/candycbt>
		Options Indexes FollowSymLinks
		Order allow,deny
		Allow from all
		AllowOverride All
		Require all granted
	</Directory>

    <FilesMatch \.php$>
        SetHandler "proxy:unix:/var/run/php/php7.4-fpm.sock|fcgi://localhost"
    </FilesMatch>
</VirtualHost> 

a2ensite candycbt
systemctl restart apache2
apachectl configtest

a2enmod actions fcgid alias proxy_fcgi setenvif
a2enconf php7.4-cgi
a2dismod php7.4
a2enconf php7.4-fpm
a2enmod rewrite
phpenmod pdo_mysql

systemctl restart apache2
systemctl start php7.4-fpm
systemctl enable php7.4-fpm

echo '<?php phpinfo(); ?>' > /var/www/candycbt/info.php
systemctl restart php7.4-fpm apache2

**nano /etc/php/7.4/fpm/php.ini**

    date.timezone = "Asia/Jakarta"
    allow_url_fopen = off
    display_errors = off
    expose_php = Off
    log_errors = on
    upload_max_filesize = 32M
    post_max_size = 0
    memory_limit = -1
    max_execution_time = 0
    max_input_vars = 3000
    max_input_time = -1
    max_file_uploads = 20000

**nano /etc/php/7.4/fpm/pool.d/www.conf**

    pm = dynamic
    pm.max_children = 36
    pm.start_servers = 4
    pm.min_spare_servers = 2
    pm.max_spare_servers = 6
    pm.process_idle_timeout = ondemand
    pm.max_requests=0

systemctl restart php7.4-fpm apache2
systemctl restart apache2 && systemctl restart php7.4-fpm
systemctl restart apache2
apachectl configtest

php-fpm7.4 -t
tail -f /var/log/php7.4-fpm.log

### **KONFIGURASI MARIADB**
apt update
apt -y install mariadb-server

nano /etc/mysql/mariadb.conf.d/50-server.cnf

systemctl restart mariadb
systemctl status mariadb

**mysql_secure_installation**

    Set root password? [Y/n] y
    Remove anonymous users? [Y/n] y
    Disallow root login remotely? [Y/n] y
    Remove test database and access to it? [Y/n] y
    Reload privilege tables now? [Y/n] y






