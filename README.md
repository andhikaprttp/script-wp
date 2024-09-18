# script-wp
script wp at debian

```bash
#!/bin/bash

# Perbarui paket dan instal LAMP stack
apt update
apt upgrade -y
apt install apache2 mariadb-server php php-mysql libapache2-mod-php php-cli bind9 bind9utils bind9-doc php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip

# Start dan aktifkan Apache dan MariaDB
systemctl start apache2
systemctl enable apache2
systemctl start mariadb
systemctl enable mariadb

# Amankan instalasi MariaDB
mysql_secure_installation

# Buat database dan user untuk WordPress
DB_NAME="wordpress_db"
DB_USER="andhika"
DB_PASS="securepassword"

mysql -e "CREATE DATABASE ${DB_NAME};"
mysql -e "CREATE USER '${DB_USER}'@'localhost' IDENTIFIED BY '${DB_PASS}';"
mysql -e "GRANT ALL PRIVILEGES ON ${DB_NAME}.* TO '${DB_USER}'@'localhost';"
mysql -e "FLUSH PRIVILEGES;"

# Unduh WordPress dan ekstrak ke /tmp/wordpress
wget https://wordpress.org/latest.tar.gz -O /tmp/wordpress.tar.gz
tar -xvzf /tmp/wordpress.tar.gz -C /tmp/

# Hapus folder html yang ada
rm -R /var/www/html/

# Pindahkan WordPress ke direktori /var/www/html
mv /tmp/wordpress /var/www/html/
chown -R www-data:www-data /var/www/html/
chmod -R 755 /var/www/html/

# Tambahkan file info.php untuk melihat informasi PHP
echo "<?php phpinfo(); ?>" > /var/www/html/info.php

# Konfigurasi BIND9
# Salin file zona default menjadi file zona untuk domain dan IP
cp /etc/bind/db.local /etc/bind/db.andhikatia.com
cp /etc/bind/db.127 /etc/bind/db.192.168

# Modifikasi file zona untuk domain andhikatia.com
ZONE_FILE="/etc/bind/db.andhikatia.com"
REV_ZONE_FILE="/etc/bind/db.192.168"
NAMED_CONF_LOCAL="/etc/bind/named.conf.local"

sed -i 's/localhost./andhikatia.com./g' $ZONE_FILE
sed -i 's/127.0.0.1/192.168.60.7/g' $ZONE_FILE
sed -i 's/::1/192.168.60.7/g' $ZONE_FILE

# Modifikasi file zona reverse untuk 192.168.x.x
sed -i 's/localhost./andhikatia.com./g' $REV_ZONE_FILE
sed -i 's/1.0.0/60.168/g' $REV_ZONE_FILE
sed -i 's/127.0.0.1/192.168.60.7/g' $REV_ZONE_FILE

# Tambahkan konfigurasi zona ke named.conf.local
tee -a $NAMED_CONF_LOCAL > /dev/null <<EOL

zone "andhikatia.com" {
    type master;
    file "/etc/bind/db.andhikatia.com";
};

zone "60.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.192.168";
};
EOL

# Restart service BIND9 dan Apache
systemctl restart bind9
systemctl restart apache2

# Tes instalasi dengan nslookup dan akses WordPress
echo "Konfigurasi DNS dan WordPress selesai."
echo "Akses WordPress di http://192.168.60.7 atau tambahkan konfigurasi DNS untuk mengakses dengan http://andhikatia.com"


```
