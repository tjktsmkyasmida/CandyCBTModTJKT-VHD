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
### **KONFIGURASI UBUNTU 20.04**

##### **REPOSITORY UBUNTU SERVER 20.04**
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

##### **KONFIGURASI SSH**
**nano /etc/ssh/sshd_config**

    port 22
    Permit RootLogin Yes

systemctl restart ssh
Remote dengan Putty / Kitty

##### INSTALL WEB SERVER APACHE 2
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

nano /etc/apache2/mods-available/mpm_worker.conf

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

nano /etc/apache2/mods-available/fcgid.conf

    FcgidIdleTimeout 1200
    FcgidProcessLifeTime 1200
    FcgidConnectTimeout 1200
    FcgidIOTimeout 1200

a2dismod ssl
a2enmod remoteip

nano /etc/apache2/apache2.conf

    Mutex file:${APACHE_LOCK_DIR} default
    Timeout 1200
    ProxyTimeout 1200
    KeepAlive On
    MaxKeepAliveRequests 0
    KeepAliveTimeout 5

/etc/init.d/apache2 restart











