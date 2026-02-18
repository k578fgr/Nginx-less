Как правило, конфигурационные файлы nginx расположены 
в директории /etc/nginx/, 
где главным файлом является /etc/nginx/nginx.conf. 
Приведем пример базового конфигурационного файла nginx:

```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;  #Здесь выписываем какой конфиг server_name слушаем
}


#mail {
#	# See sample authentication script at:
#	# http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
#
#	# auth_http localhost/auth.php;
#	# pop3_capabilities "TOP" "USER";
#	# imap_capabilities "IMAP4rev1" "UIDPLUS";
#
#	server {
#	        listen     localhost:110;
#	        protocol   pop3;
#	        proxy      on;
#	}
#
#	server {
#	        listen     localhost:143;
#	        protocol   imap;
#	        proxy      on;
#	}
#}
```

В первом приближении уровни конфигурационного файла выглядят следующим образом:

```
.
└── main
    ├── events
    ├── http
    │   ├── server
    │   │   └── location
    │   └── server
    │       └── location
    └── mail
        ├── server
        └── server
```

Так, к примеру, 
модуль ngx_core_module 
отвечает за основную логику web-сервера - то есть определяет как раз те директивы, 
без которых сервер бы просто не запустился (к примеру, упомянутый user).

Для получения информации о вашей версии nginx, сборке и модулях используется флаг -V при вызове nginx:

```
# nginx -V
nginx version: nginx/1.14.0 (Ubuntu)
built with OpenSSL 1.1.1  11 Sep 2018
TLS SNI support enabled
configure arguments: --with-cc-opt='-g -O2 -fdebug-prefix-map=/build/nginx-GkiujU/nginx-1.14.0=. -fstack-protector-strong -Wformat -Werror=format-security -fPIC -Wdate-time -D_FORTIFY_SOURCE=2' --with-ld-opt='-Wl,-Bsymbolic-functions -Wl,-z,relro -Wl,-z,now -fPIC' --prefix=/usr/share/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --modules-path=/usr/lib/nginx/modules --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-proxy-temp-path=/var/lib/nginx/proxy --http-scgi-temp-path=/var/lib/nginx/scgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi --with-debug --with-pcre-jit --with-http_ssl_module --with-http_stub_status_module --with-http_realip_module --with-http_auth_request_module --with-http_v2_module --with-http_dav_module --with-http_slice_module --with-threads --with-http_addition_module --with-http_geoip_module=dynamic --with-http_gunzip_module --with-http_gzip_static_module --with-http_image_filter_module=dynamic --with-http_sub_module --with-http_xslt_module=dynamic --with-stream=dynamic --with-stream_ssl_module --with-mail=dynamic --with-mail_ssl_module

```

Из данного вывода можно понять, что на сервере - nginx версии 1.14.0, который использует по умолчанию конфигурационный файл из /etc/nginx/nginx.conf и, к примеру, собран с модулем для работы с gzip, а также использует подключенный к нему модуль для работы с протоколом SMTP (mail).

Подключение динамического модуля производится через директиву load_module в секции main. Соответственно, если мы не используем данный модуль, мы можем просто не загружать его, убрав соответствующиую директиву.

Перед тем, как разбирать основные директивы, уточним, что в конфигурационном файле существует несколько основных правил:

    Все директивы должны заканчиваться символом ;.
    Блоки директив (тот же http) НЕ заканчиваются ;.
    Для комментирования строки используется #.
    У каждой директивы есть свой разрешенный контекст (этот пункт мы разъясним далее, после примера конфигурационного файла).

как правило, чтобы узнать, из какого модуля конкретная директива, достаточно в поисковике просто ввести запрос 
```
**nginx $DIRECTIVE**
```

К примеру

Перед установкой
Последняя ссылка Mainlain

wget <ссылка>

Устанавливаем зависимости к make и gcc
sudo apt install libpcre++-dev
sudo apt-get install zlib1g-dev
sudo apt install libgd-dev


sudo apt-get install libpcre3 libpcre3-dev libssl-dev zlib1g-dev

Задания

1.Отключить mail
/etc/nginx/modules-enabled/
rm -f 50-mod-mail.conf

2.строка в которой вы этот задаём формат
http {
    log_format  logz '$remote_addr - $remote_user [$time_local] '
                        '"$request" $status ';


3. отдельные конфиги со своими server_name.
include /etc/nginx/sites-enabled/server_name

Установка

apt update
apt install nginx

sudo ufw app list
sudo ufw allow 'Nginx HTTP'
sudo ufw status