# Jarkom-Modul-2-D14-2023
- Darren Prasetya (5025211162)
- Kamal ()

## Soal 9 dan 10
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.
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
8. mkdir /var/www/arjuna.d14
9. nano /var/www/arjuna.d14/index.php
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
10. nano /etc/nginx/sites-available/default
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
15. nano /etc/nginx/sites-available/default
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
