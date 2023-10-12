# Jarkom-Modul-2-D14-2023
- Darren Prasetya (5025211162)
- Kamal ()

## Soal 9 dan 10
#### Steps:
1. Pertama kita perlu buat domain arjuna.d14.com, jadi kita perlu pergi ke DNS Master (Yudhistira)
2. `nano /etc/bind/named.conf.local`
```
zone "arjuna.d14.com" {
	type master;
	file "/etc/bind/arjuna/arjuna.d14.com";
};
```
3. `mkdir /etc/bind/arjuna`
4. `nano /etc/bind/arjuna/arjuna.d14.com`
```
$TTL 604800; 
@ 	IN 	SOA 	arjuna.d14.com.	root.arjuna.d14.com. (
  			2023101101	; Serial
  			604800 		; Refresh
  			86400 		; Retry
  			2419200		; NExpire
  			604800 ) 	; Negative Cache TTL
;
@	IN 	NS 	arjuna.d14.com.
@	IN 	A 	192.198.3.2
www	IN 	CNAME 	arjuna.d14.com.
```
5. `service bind9 restart`
6. Kemudian, kita buka Web Servers Abimanyu, Prabukusuma, Wisanggeni
6. `echo nameserver 192.168.122.1 > /etc/resolv.conf`
7. `apt-get update && apt install nginx php php-fpm -y`
8. `mkdir /var/www/arjuna.d14`
9. `nano /var/www/arjuna.d14/index.php`
```
<?php
$hostname = gethostname();
$date = date('Y-m-d H:i:s');
$php_version = phpversion();
$username = get_current_user();


echo "Hello World!<br>";
echo "Saya adalah: $username<br>";
echo "Saat ini berada di: $hostname<br>";
echo "Versi PHP yang saya gunakan: $php_version<br>";
echo "Tanggal saat ini: $date<br>";
?>
```
10. `nano /etc/nginx/sites-available/default`
```
server {
	listen 8001; # sesuaikan dengan web server Abimanyu:8001, Prabukusuma:8002, Wisanggeni:80003

	root /var/www/arjuna.d14;

	index index.php index.html index.htm;
	server_name _;

	location / {
		try_files $uri $uri/ /index.php?$query_string;
	}

	# pass PHP scripts to FastCGI server
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
	}

	location ~ /\.ht {
		deny all;
	}

	error_log /var/log/nginx/d14_error.log;
	access_log /var/log/nginx/d14_access.log;
}
```
11. `service php7.2-fpm start`
12. `service nginx restart`
14. Kembali ke Load Balancer Arjuna
15. `nano /etc/nginx/sites-available/default`
```
upstream myweb  {
	server 192.198.3.3:8003; #IP Wisanggeni
	server 192.198.3.4:8002; #IP Prabukusuma
	server 192.198.3.5:8001; #IP Abimanyu
}

server {
	listen 80;
	server_name arjuna.d14.com;

	location / {
		proxy_pass http://myweb;
	}
}
```
15. `service nginx restart` dan `service bind9 restart`

## Soal 11
#### Steps:
1. Pergi ke DNS Master Yudhistira
2. `nano /etc/bind/abimanyu/abimanyu.d14.conf` dan pastikan conf seperti berikut
```
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d14.com. root.abimanyu.d14.com. (
                         2022100601;    ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.d14.com.
@       IN      A       192.198.3.5     ; IP Abimanyu
www     IN	CNAME	abimanyu.d14.com.
parikesit IN	A	192.198.3.5     ; IP Abimanyu
ns1	IN	A	192.198.2.3 ; IP Werk
baratayuda IN	NS	ns1
```
3. Kemudian, pergi ke server Abimanyu
4. `echo nameserver 192.168.122.1 > /etc/resolv.conf`
5. `apt-get update` dan `apt-get install apache2`
6. `apt-get install python3-pip` dan `pip3 install gdown`
7. `cd /var/www`
8. Download zip files
- abimanyu.d14.com
```
gdown 1a4V23hwK9S7hQEDEcv9FL14UkkrHc-Zc
unzip abimanyu.yyy.com.zip
rm abimanyu.yyy.com.zip
mv abimanyu.yyy.com abimanyu.d14
```
- parikesit.abimanyu.d14.com
```
gdown 1LdbYntiYVF_NVNgJis1GLCLPEGyIOreS
unzip parikesit.abimanyu.yyy.com.zip
rm parikesit.abimanyu.yyy.com.zip
mv parikesit.abimanyu.yyy.com parikesit.abimanyu.d14
```

- rjp.baratayuda.abimanyu.yyy.com
```
gdown 1pPSP7yIR05JhSFG67RVzgkb-VcW9vQO6
unzip rjp.baratayuda.abimanyu.yyy.com.zip
rm rjp.baratayuda.abimanyu.yyy.com.zip
mv rjp.baratayuda.abimanyu.yyy.com rjp.baratayuda.abimanyu.d14
```
9. `nano /etc/apache2/sites-available/000-default.conf`
```
<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	ServerName abimanyu.d14.com
	ServerAlias www.abimanyu.d14.com
	DocumentRoot /var/www/abimanyu.d14
	
	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

## Soal 12
#### Steps:
1. `nano /var/www/abimanyu.d14/.htaccess`
```
RewriteEngine On
RewriteRule ^index\.php/home$ /home [R=301,L]
```
2. `a2enmod rewrite`
3. `nano /etc/apache2/sites-available/000-default.conf` dan tambahkan setting berikut
```
<Directory /var/www/abimanyu.d14>
	Options +Indexes +FollowSymLinks -Multiviews
	AllowOverride All
        Require all granted
</Directory>
```

## Soal 13
#### Steps:
1. `nano /etc/apache2/sites-available/parikesit.abimanyu.d14.conf`
```
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    ServerName parikisit.abimanyu.d14.com
    DocumentRoot /var/www/parikesit.abimanyu.d14
</VirtualHost>
```
2. a2ensite parikesit.abimanyu.d14.conf

## Soal 14
#### Steps:
1. `nano /etc/apache2/sites-available/parikesit.abimanyu.d14.conf`
```
<VirtualHost *:80>
 	ServerAdmin webmaster@localhost
    	ServerName parikisit.abimanyu.d14.com
    	DocumentRoot /var/www/parikesit.abimanyu.d14

	<Directory /var/www/parikesit.abimanyu.d14/public>
        	Options +Indexes
    	</Directory>

    	<Directory /var/www/parikesit.abimanyu.d14/secret>
       		Options -Indexes
    	</Directory>
</VirtualHost>
```

## Soal 15
#### Steps:
1. `nano /etc/apache2/sites-available/parikesit.abimanyu.d14.conf`
```
<VirtualHost *:80>
 	ServerAdmin webmaster@localhost
    	ServerName parikisit.abimanyu.d14.com
    	DocumentRoot /var/www/parikesit.abimanyu.d14

	<Directory /var/www/parikesit.abimanyu.d14/public>
        	Options +Indexes
    	</Directory>

    	<Directory /var/www/parikesit.abimanyu.d14/secret>
       		Options -Indexes
    	</Directory>
	
	<Directory /var/www/parikesit.abimanyu.d14/error>
    		Options FollowSymLinks
    		AllowOverride None
    		Require all granted
	</Directory>

	ErrorDocument 404 /error/404.html
	ErrorDocument 403 /error/403.html
</VirtualHost>
```

## Soal 16
#### Steps:
1. `nano /etc/apache2/sites-available/parikesit.abimanyu.d14.conf`
```
<VirtualHost *:80>
 	ServerAdmin webmaster@localhost
    	ServerName parikisit.abimanyu.d14.com
    	DocumentRoot /var/www/parikesit.abimanyu.d14

	<Directory /var/www/parikesit.abimanyu.d14/public>
        	Options +Indexes
    	</Directory>

    	<Directory /var/www/parikesit.abimanyu.d14/secret>
       		Options -Indexes
    	</Directory>
	
	<Directory /var/www/parikesit.abimanyu.d14/error>
    		Options FollowSymLinks
    		AllowOverride None
    		Require all granted
	</Directory>
	
	Alias "/js" "/var/www/parikesit.abimanyu.d14/public/js"

	ErrorDocument 404 /error/404.php
	ErrorDocument 403 /error/403.php
</VirtualHost>
```

## Soal 17
#### Steps:
1. Pergi ke DNS Slave Werkudara untuk setting subdomai rjp ke IP Abimanyu
2. `nano /etc/bind/delegasi/baratayuda.abimanyu.d14.com`
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.dl4.com. root.baratayuda.abimanyu.d$
                              2022100601                ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      baratayuda.abimanyu.d14.com.
@       IN      A       192.198.2.3 ; IP Werkudara
www	IN      CNAME   baratayuda.abimanyu.d14.com.
rjp	IN      A	192.198.3.5 ; IP Abimanyu
```
3. Kemudian, kembali ke Abimanyu
4. `nano /etc/apache2/sites-available/rjp.baratayuda.abimanyu.d14.conf`
```
<VirtualHost *:14000>
    	ServerAdmin webmaster@localhost
	ServerName rjp.baratayuda.abimanyu.d14.com
    	ServerAlias www.rjp.baratayuda.abimanyu.d14.com
   	DocumentRoot /var/www/rjp.baratayuda.abimanyu.d14
</VirtualHost>

<VirtualHost *:14400>
	ServerAdmin webmaster@localhost
	ServerName rjp.baratayuda.abimanyu.d14.com
    	ServerAlias www.rjp.baratayuda.abimanyu.d14.com
   	DocumentRoot /var/www/rjp.baratayuda.abimanyu.d14
</VirtualHost>
```
5. `a2ensite rjp.baratayuda.abimanyu.d14.conf`
6. Tambahkan port 14000 dan 14400 ke `/etc/apache2.ports.conf`
```
Listen 14000
Listen 14400
```

## Soal 18
#### Steps:
1. `nano /var/www/rjp.baratayuda.abimanyu.d14.com/.htaccess`
```
AuthType Basic
AuthName "Authentication Required"
AuthUserFile /etc/apache2/.htpasswd
Require user Wayang
```
2. `htpasswd -c /etc/apache2/.htpasswd Wayang` kemudian akan diprompt buat password, kemudian masukkan "baratayudad14"
3. `nano /etc/apache2/sites-available/rjp.baratayuda.abimanyu.d14.conf`
```
<VirtualHost *:14000>
	ServerAdmin webmaster@localhost
	ServerName rjp.baratayuda.abimanyu.d14.com
    	ServerAlias www.rjp.baratayuda.abimanyu.d14.com
   	DocumentRoot /var/www/rjp.baratayuda.abimanyu.d14
	
	<Directory /var/www/rjp.baratayuda.abimanyu.d14>
        	Options Indexes FollowSymLinks
        	AllowOverride All
        	Require all granted
    	</Directory>
</VirtualHost>

<VirtualHost *:14400>
	ServerAdmin webmaster@localhost
	ServerName rjp.baratayuda.abimanyu.d14.com
    	ServerAlias www.rjp.baratayuda.abimanyu.d14.com
   	DocumentRoot /var/www/rjp.baratayuda.abimanyu.d14
	
	<Directory /var/www/rjp.baratayuda.abimanyu.d14>
        	Options Indexes FollowSymLinks
        	AllowOverride All
        	Require all granted
    	</Directory>
</VirtualHost>
```
## Soal 19
#### Steps:
1. Pergi ke DNS Master Yudhistira
2. `nano /etc/bind/abimanyu/abimanyu.d14.com`
```
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d14.com. root.abimanyu.d14.com. (
                         2022100601;    ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.d14.com.
@       IN      A       192.198.3.5     ; IP Abimanyu
www     IN	CNAME	abimanyu.d14.com.
parikesit IN	A	192.198.3.5     ; IP Abimanyu
ns1	IN	A	192.198.2.3 ; IP Werk
baratayuda IN	NS	ns1
192.198.3.5 IN	CNAME	www.abimanyu.d14.com.
```

## Soal 20
#### Steps:
1. Pergi ke Web Server Abimanyu
2. `nano /var/www/parikesit.abimanyu.d14/.htaccess`
```
RewriteEngine On
RewriteRule .*abimanyu.* /abimanyu.png [R,L]
```
3. `nano /etc/apache2/sites-available/parikesit.abimanyu.d14.conf`
```
<VirtualHost *:80>
 	ServerAdmin webmaster@localhost
    	ServerName parikisit.abimanyu.d14.com
    	DocumentRoot /var/www/parikesit.abimanyu.d14

	<Directory /var/www/parikesit.abimanyu.d14/public>
        	Options +Indexes
    	</Directory>

    	<Directory /var/www/parikesit.abimanyu.d14/secret>
       		Options -Indexes
    	</Directory>
	
	<Directory /var/www/parikesit.abimanyu.d14/error>
    		Options FollowSymLinks
    		AllowOverride None
    		Require all granted
	</Directory>
	
	<Directory /var/www/parikesit.abimanyu.d14>
		Options +Indexes +FollowSymLinks -Multiviews
		AllowOverride All
	</Directory>

	Alias "/js" "/var/www/parikesit.abimanyu.d14/public/js"

	ErrorDocument 404 /error/404.php
	ErrorDocument 403 /error/403.php
</VirtualHost>
```
