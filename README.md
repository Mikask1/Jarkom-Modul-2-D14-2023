

# Jarkom-Modul-2-D14-2023
- Darren Prasetya (5025211162)
- Mohammad Kamal (5025211180)


# Soal 


1. Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian [sebagai berikut.](https://docs.google.com/spreadsheets/d/1OqwQblR_mXurPI4gEGqUe7v0LSr1yJViGVEzpMEm2e8/edit?usp=sharing) Folder topologi dapat diakses pada [drive berikut](https://drive.google.com/drive/folders/1Ij9J1HdIW4yyPEoDqU1kAwTn_iIxg3gk?usp=sharing) 
    
2. Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.
    
3. Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.
    
4. Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.
    
5. Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)
    
6. Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.
    
7. Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.
    
8. Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.
    
9. Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.
    
10. Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    

    - Prabakusuma:8001

    - Abimanyu:8002

    - Wisanggeni:8003

11. Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy
    
12. Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.
    
13. Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy
    
14. Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).
    
15. Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.
    
16. Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi 
    

www.parikesit.abimanyu.yyy.com/js 

17. Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.
    
18. Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.
    
19. Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke [www.abimanyu.yyy.com](http://www.abimanyu.yyy.com) (alias)
    
20. Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.
    

  
# Jawaban

## Soal 1 
Berikut merupakan topologi yang ditentukan untuk kelompok kami bedasarkan gambar yang telah ditentukan.
The topology in question :

![Alt Text 1](image/1.png)


## Soal 2 dan 3
Pertama masukan command `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.168.0.0/16` di server yang disambungin ke NAT untuk memberi akses internet untuk semua node. Lalu masukan command `echo nameserver 192.198.122.1 > /etc/resolv.conf` pada semua node yang membutuhkan koneksi internet.

Lalu gunakan command `apt-get update` dan `apt-get install bind9 -y` pada DNSMaster dan DNSSlave. 

### Yudhistira
Setelah itu edit `/etc/bind/named.conf.local` dengan config :


	zone "abimanyu.d14.com" {
		        type master;
		        file "/etc/bind/abimanyu/abimanyu.d14.com";
		};
	
	zone "arjuna.d14.com" {
	
	type master;
	
	    file "/etc/bind/arjuna/arjuna.d14.com";
	};


Lalu buat directory /etc/bind/arjuna/arjuna.d14.com dan isi config dengan :

	;
		; BIND data file for local loopback interface
		;
		$TTL    604800
		@       IN      SOA     arjuna.d14.com. root.arjuna.d14.com. (
		                         2022100601;    ; Serial
		                         604800         ; Refresh
		                          86400         ; Retry
		                        2419200         ; Expire
		                         604800 )       ; Negative Cache TTL
		;
		@       IN      NS      arjuna.d14.com.
		@       IN      A       192.198.3.2     ; IP Arjuna
		WWW     IN      CNAME   arjuna.d14.com.
		@       IN      AAAA    ::1

Lalu buat directory /etc/bind/abimanyu/abimanyu.d14.com dan isi config dengan :

	;
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
		@       IN      A       192.198.3.5     ; IP Yudhistira
		WWW     IN      CNAME   abimanyu.d14.com.
		@       IN      AAAA    ::1

Lalu jalankan `service bind9 restart`


### Werkudara
Edit /etc/bind/named.conf.local
dan isi :

	zone "abimanyu.d14.com" {
		        type slave;
		        masters {192.198.2.2;};
		        file "/var/lib/bind/abimanyu.d14.com";
		};

run `service bind9 restart`.

#### Test

![Alt Text 3](image/3.png)
## Soal 4 dan 5

**add bawah ini** kepada `etc/bind/named.conf.local`

	 zone "2.198.192.in-addr.arpa" {
		    type master;
		    file "/etc/bind/abimanyu/2.198.192.in-addr.arpa";
		};

Lalu buat directory dan edit `etc/bind/abimanyu/2.198.192.in-addr.arpa`. Isi file dengan :

	
		;
		; BIND data file for local loopback interface
		;
		$TTL    604800
		@       IN      SOA     abimanyu.d14.com. root.abimanyu.d14.com. (
		                         2022100601     ; Serial
		                         604800         ; Refresh
		                          86400         ; Retry
		                        2419200         ; Expire
		                         604800 )       ; Negative Cache TTL
		;
		2.198.192.in-addr.arpa.      IN      NS      abimanyu.d14.com.
		2               IN      PTR     abimanyu.d14.com. ; byte ke-4

Kemudian edit /etc/bind/abimanyu/abimanyu.d14.com seperti ini :

	;
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
		@       IN      A       192.198.2.5
		WWW     IN      CNAME   abimanyu.d14.com.
		parikesit IN A          192.198.3.5     ; IP Abimanyu
		@       IN      AAAA    ::1

Kemudian restart bind
### Testing
![Alt Text 4](image/4.png)
## Soal 6

### Werkudara
Edit file `etc/bind/named.conf.local` dan tambah :

	zone "abimanyu.E19.com" {
	
	    type slave;
	    masters { 10.46.2.2; }; #IP yudhistira
	    file "/var/lib/bind/abimanyu.d14.com";
	
	};
lalu restart bind.
### Yudhistira

Selanjutnya edit file `etc/bind/named.conf.local` dan tambah :

	zone "abimanyu.d14.com" {
	
	    type master;
	    notify yes;
	    also-notify { 10.46.2.3; };
	    allow-transfer { 10.46.2.3; };
	    file "/etc/bind/abimanyu/abimanyu.d14.com";
	
	}
	
`Kemudian restart`


## Soal 7 dan 8

### Yudhistira

Edit file /etc/bind/abimanyu/abimanyu.d14.com seperti ini :

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
		@       IN      A       192.198.2.2     ; IP Yudhistira
		www     IN     CNAME    abimanyu.d14.com.
		parikesit IN A          192.198.3.5     ; IP Abimanyu
		ns1 IN A 192.198.2.3 ; IP Werkudara
		baratayuda IN NS ns1
		@ IN AAAA ::1

Kemudian edit /etc/bind/named.conf.options lalu comment `dnssec-validation auto` dan tambah `allow-query{any;}` di bawahnya. Lalu lakukan restart lagi.

### Werkudara 

Lakukan step yang sama pada file /etc/bind/named.conf.options. Kemudian edit file zone dan tambahkan : 

	
	zone "baratayuda.abimanyu.d14.com" {
			        type master;
			        file "/etc/bind/delegasi/baratayuda.abimanyu.d14.com";
			};

Lalu buat directory delegasi /etc/bind/delegasi/baratayuda.abimanyu.d14.com dan edit file tersebut seperti : 

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
		@       IN      A       192.198.2.3
		www    IN      CNAME    baratayuda.abimanyu.d14.com.
		rjp IN      A      192.198.2.3

Kemudian restart service bind.


### Testing
![Alt Text 5](image/5.png)

## Soal 9 dan 10

Buka webserver Abimanyu, Prabukusuma, Wisanggeni. Lalu run command 
`echo nameserver 192.168.122.1 > /etc/resolv.conf` pada ketiga server tsb.
Setelah itu jalankan `apt-get update &&  apt install nginx php php-fpm -y` untuk menginstall nginx.  Setelah installasi selesai, buat directory dengan command 
`mkdir /var/www/arjuna.d14` dan buat file `/var/www/arjuna.d14/index.php` dan isi file tersebut dengan : 

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
Kemudian buat file /etc/nginx/sites-available/default dan isi file tersebut dengan config berikut :

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
Kemudian jalankan  `service php7.2-fpm start` dan `service nginx restart`. Setelah itu kembali ke Load Balancer Arjuna. Dalam Load Balancer Arjuna edit file `/etc/nginx/sites-available/default` dan isi dengan : 

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
Setelah file tersebut terkonfigurasi, jalankan  `service nginx restart` dan `service bind9 restart`.



## Soal 11

Edit file /etc/bind/abimanyu/abimanyu.d14.conf pada Yudhistira. Kemudian isi file dengan :
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
Selanjutnya pada server abimanyu, jalankan command `echo nameserver 192.168.122.1 > /etc/resolv.conf` untuk menghubungkan dengan internet. Kemudian jalankan `apt-get update` dan `apt-get install apache2` untuk install apache2.   Kemudian jalankan `apt-get install python3-pip` dan `pip3 install gdown` untuk menginstall python3pip dan install gdown untuk mendownload file dari google drive.  Kemudian pergi ke directory  `cd /var/www` dan download zip files. 

Berikut merupakan command yang dijalankan untuk download dan simpan file pada directory tersebut.

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
Kemudian pada  ``/etc/apache2/sites-available/000-default.conf``  isi dengan :
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

### Testing
![Alt Text 6](image/6.png)

## Soal 12
Pada server abimanyu, edit file `/var/www/abimanyu.d14/.htaccess`. Isi dengan :
```
RewriteEngine On
RewriteRule ^index\.php/home$ /home [R=301,L]
```
Jalankan command `a2enmod rewrite`. Kemudian edit file `/etc/apache2/sites-available/000-default.conf` dan isi file dengan : 
```
<Directory /var/www/abimanyu.d14>
	Options +Indexes +FollowSymLinks -Multiviews
	AllowOverride All
        Require all granted
</Directory>
```

### Testing

![Alt Text 7](image/7.png)

## Soal 13
Edit file `/etc/apache2/sites-available/parikesit.abimanyu.d14.conf` dan isi file dengan :
```
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    ServerName parikisit.abimanyu.d14.com
    DocumentRoot /var/www/parikesit.abimanyu.d14
</VirtualHost>
```
Jalankan command a2ensite parikesit.abimanyu.d14.conf.


## Soal 14
Edit file  `/etc/apache2/sites-available/parikesit.abimanyu.d14.conf` dan isi file dengan : 
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

Edit file `/etc/apache2/sites-available/parikesit.abimanyu.d14.conf` dan isi dengan : 
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
### Testing
![Alt Text 8](image/8.png)

## Soal 16

Edit file `/etc/apache2/sites-available/parikesit.abimanyu.d14.conf` menjadi :
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

### Testing
![Alt Text 9](image/9.png)
## Soal 17
#### Steps:
Pergi ke DNS Slave Werkudara untuk setting subdomai rjp ke IP Abimanyu lalu edit file `/etc/bind/delegasi/baratayuda.abimanyu.d14.com` menjadi : 
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
3. Kemudian, kembali ke Abimanyu dan edit file `/etc/apache2/sites-available/rjp.baratayuda.abimanyu.d14.conf` dan isi menjadi :
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
 Kemudian jalankan `a2ensite rjp.baratayuda.abimanyu.d14.conf`. Setelah itu tambahkan port 14000 dan 14400 ke `/etc/apache2.ports.conf`
```
Listen 14000
Listen 14400
```

## Soal 18

Edit file ` /var/www/rjp.baratayuda.abimanyu.d14/.htaccess` menjadi : 
```
AuthType Basic
AuthName "Authentication Required"
AuthUserFile /etc/apache2/.htpasswd
Require user Wayang
```
Jalankan `htpasswd -c /etc/apache2/.htpasswd Wayang` kemudian akan diprompt buat password, kemudian masukkan "baratayudad14".
Edit file /etc/apache2/sites-available/rjp.baratayuda.abimanyu.d14.conf menjadi : 
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

### Testing 
![Alt Text 11](image/11.png)



## Soal 19
Pergi ke DNS Master Yudhistira. Lalu edit file  `/etc/bind/abimanyu/abimanyu.d14.com` menjadi 
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
Pergi ke Web Server Abimanyu  lalu edit file ` /var/www/parikesit.abimanyu.d14/.htaccess` menjadi :
```
RewriteEngine On
RewriteRule .*abimanyu.* /abimanyu.png [R,L]
```
3. Kemudian edit file `/etc/apache2/sites-available/parikesit.abimanyu.d14.conf` menjadi : 
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


### Testing
![Alt Text 12](image/12.png)


