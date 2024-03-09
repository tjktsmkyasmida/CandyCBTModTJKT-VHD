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

##### REPOSITORY UBUNTU SERVER 20.04
nano /etc/apt/sources.list
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

##### KONFIGURASI SSH
nano /etc/ssh/sshd_config
    port 22
    Permit RootLogin Yes

systemctl restart ssh

REMOTE MAKEK PUTTY























