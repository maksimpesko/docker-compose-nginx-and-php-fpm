# docker-compose-nginx-and-php-fpm


mkdir {nginx php-fpm}
mkdir nginx {conf logs www}

cat ngin/conf/nginx.conf > "
server {
    listen 80;
    server_name localhost;
    
    root /var/www/html/;
    index index.php index.html index.htm;
    
    error_log  /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;

   location / {
      try_files $uri $uri/ /index.php?$args;
   }
   
   location ~ \.php$ {
      try_files $uri =404;
      fastcgi_split_path_info ^(.+\.php)(/.+)$;
      fastcgi_pass php-fpm:9000;
      fastcgi_index index.php;
      include fastcgi_params;
      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
      fastcgi_param PATH_INFO $fastcgi_path_info;
   }
}
"

cat ngin/www/index.php > "
<?php phpinfo(); ?>
"

cat php-fpm/php.ini > "
memory_limit = 2G
always_populate_raw_post_data = -1
cgi.fix_pathinfo = 1
fastcgi_split_path_info = 1
max_execution_time = 18000
flag session.auto_start = off
zlib.output_compression = on

;было error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT
error_reporting = E_ALL & ~E_NOTICE & ~E_STRICT
;было display_errors = of
display_errors = on
;заремарить следующую строку, иначе письма будут терять поле FROM
mail.add_x_header = of
;Если не указать тайм зону, то будут сыпаться ошибки.
;было ;date.timezone = 
date.timezone = Europe/Minsk
;Разрешить сокращенные теги
;было short_open_tag = of
short_open_tag = on
;было post_max_size = 8M
post_max_size = 200M
;было upload_max_filesize = 2M
upload_max_filesize = 200M
"
