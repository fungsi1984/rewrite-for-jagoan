ubuntu@instance-20250728-0928:~$ ll
total 96
drwxr-xr-x 7 ubuntu   ubuntu    4096 Jul 28 10:17 ./
drwxr-xr-x 5 root     root      4096 Jul 29 09:57 ../
-rw------- 1 ubuntu   ubuntu    6828 Jul 29 09:58 .bash_history
-rw-r--r-- 1 ubuntu   ubuntu     220 Mar 31  2024 .bash_logout
-rw-r--r-- 1 ubuntu   ubuntu    3771 Mar 31  2024 .bashrc
drwx------ 2 ubuntu   ubuntu    4096 Jul 28 03:19 .cache/
drwxrwxr-x 3 ubuntu   ubuntu    4096 Jul 28 06:38 .local/
-rw-r--r-- 1 ubuntu   ubuntu     807 Mar 31  2024 .profile
drwx------ 2 ubuntu   ubuntu    4096 Jul 28 02:32 .ssh/
-rw-r--r-- 1 ubuntu   ubuntu       0 Jul 28 03:21 .sudo_as_admin_successful
drwxrwxr-x 3 ubuntu   ubuntu    4096 Jul 28 06:24 .wp-cli/
-rwxrwxr-x 1 ubuntu   ubuntu   42167 Jul 28 10:11 openvpn-install.sh*
-rw-r--r-- 1 root     root      2764 Jul 28 10:17 tes.ovpn
drwxrwxr-x 4 www-data www-data  4096 Jul 28 10:18 word.diacritics.xyz/

ubuntu@instance-20250728-0928:~$ sudo lsof -i :80
COMMAND  PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
nginx   1761     root    9u  IPv4  13524      0t0  TCP *:http (LISTEN)
nginx   1761     root   10u  IPv6  13525      0t0  TCP *:http (LISTEN)
nginx   1762 www-data    9u  IPv4  13524      0t0  TCP *:http (LISTEN)
nginx   1762 www-data   10u  IPv6  13525      0t0  TCP *:http (LISTEN)
nginx   1763 www-data    9u  IPv4  13524      0t0  TCP *:http (LISTEN)
nginx   1763 www-data   10u  IPv6  13525      0t0  TCP *:http (LISTEN)

ubuntu@instance-20250728-0928:~$ sudo lsof -i :443
COMMAND  PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
nginx   1761     root    7u  IPv4  13522      0t0  TCP *:https (LISTEN)
nginx   1761     root    8u  IPv6  13523      0t0  TCP *:https (LISTEN)
nginx   1762 www-data    7u  IPv4  13522      0t0  TCP *:https (LISTEN)
nginx   1762 www-data    8u  IPv6  13523      0t0  TCP *:https (LISTEN)
nginx   1763 www-data    7u  IPv4  13522      0t0  TCP *:https (LISTEN)
nginx   1763 www-data    8u  IPv6  13523      0t0  TCP *:https (LISTEN)

ubuntu@instance-20250728-0928:~$ sudo ss -tulnp | grep :443
tcp   LISTEN 0      511           0.0.0.0:443        0.0.0.0:*    users:(("nginx",pid=1763,fd=7),("nginx",pid=1762,fd=7),("nginx",pid=1761,fd=7))   
tcp   LISTEN 0      511              [::]:443           [::]:*    users:(("nginx",pid=1763,fd=8),("nginx",pid=1762,fd=8),("nginx",pid=1761,fd=8))   
ubuntu@instance-20250728-0928:~$ sudo ss -tulnp | grep :80
tcp   LISTEN 0      511           0.0.0.0:80         0.0.0.0:*    users:(("nginx",pid=1763,fd=9),("nginx",pid=1762,fd=9),("nginx",pid=1761,fd=9))   
tcp   LISTEN 0      511              [::]:80            [::]:*    users:(("nginx",pid=1763,fd=10),("nginx",pid=1762,fd=10),("nginx",pid=1761,fd=10))

sudo iptables -L INPUT -n --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     17   --  0.0.0.0/0            0.0.0.0/0            udp dpt:1194
2    ACCEPT     0    --  0.0.0.0/0            0.0.0.0/0           
3    ACCEPT     0    --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
4    ACCEPT     1    --  0.0.0.0/0            0.0.0.0/0           
5    ACCEPT     0    --  0.0.0.0/0            0.0.0.0/0           
6    ACCEPT     6    --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:22
7    ACCEPT     6    --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:443
8    ACCEPT     6    --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:80
9    REJECT     0    --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited

this from jagoanhosting, cpanel default permission
755 : untuk semua folder ataupun subfolder
644 : untuk semua file

check this, default nginx using www-data
ubuntu@instance-20250728-0928:/etc/nginx$ sudo vi nginx.conf 
```
user www-data;

```

ubuntu@instance-20250728-0928:/etc/nginx$ cd sites-enabled/
ubuntu@instance-20250728-0928:/etc/nginx/sites-enabled$ ll
total 8
drwxr-xr-x 2 root root 4096 Jul 28 05:58 ./
drwxr-xr-x 8 root root 4096 Jul 30 09:59 ../
lrwxrwxrwx 1 root root   46 Jul 28 05:57 word.diacritics.xyz -> /etc/nginx/sites-available/word.diacritics.xyz

### WHY ROOT?, we gonna improve this
ubuntu@instance-20250728-0928:/etc/nginx$ cd sites-available/
ubuntu@instance-20250728-0928:/etc/nginx/sites-available$ ll
total 20
drwxr-xr-x 2 root root 4096 Jul 28 06:05 ./
drwxr-xr-x 8 root root 4096 Jul 30 09:59 ../
-rw-r--r-- 1 root root 2399 Jul 28 03:49 default.backup
-rw-r--r-- 1 root root  592 Jul 28 05:27 default.backup2
-rw-r--r-- 1 root root 1240 Jul 28 05:57 word.diacritics.xyz

ubuntu@instance-20250728-0928:/etc/nginx/sites-available$ sudo vi word.diacritics.xyz 
ubuntu@instance-20250728-0928:/etc/nginx/sites-available$ cat word.diacritics.xyz 
```
server {
    listen 443 ssl;
    listen [::]:443 ssl http2;

    server_name word.diacritics.xyz;

    ssl_certificate /etc/letsencrypt/live/word.diacritics.xyz/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/word.diacritics.xyz/privkey.pem;

    access_log /home/ubuntu/word.diacritics.xyz/logs/access.log;
    error_log /home/ubuntu/word.diacritics.xyz/logs/error.log;

    root /home/ubuntu/word.diacritics.xyz/public/;
    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/run/php/php8.3-fpm.sock;
        fastcgi_index index.php;
        include fastcgi.conf;
    }
}

server {
    listen 443 ssl;
    listen [::]:443 ssl http2;

    server_name www.word.diacritics.xyz;

    ssl_certificate /etc/letsencrypt/live/word.diacritics.xyz/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/word.diacritics.xyz/privkey.pem;

    return 301 https://word.diacritics.xyz$request_uri;
}

server {
    listen 80;
    listen [::]:80;

    server_name word.diacritics.xyz www.word.diacritics.xyz;

    return 301 https://word.diacritics.xyz$request_uri;
}
```

ubuntu@instance-20250728-0928:~/word.diacritics.xyz$ ll
total 16
drwxrwxr-x 4 www-data www-data 4096 Jul 30 10:05 ./
drwxr-xr-x 7 ubuntu   ubuntu   4096 Jul 28 10:17 ../
drwxrwxr-x 2 www-data www-data 4096 Jul 28 05:57 logs/
drwxrwxr-x 5 www-data www-data 4096 Jul 29 06:41 public/

rwxrwxr-x: These nine characters represent the permissions for three different categories of users:
rwx (Owner permissions):

    r (read): The owner can read the contents of the file/directory.

    w (write): The owner can modify the file/directory (create, delete, rename files within a directory).

    x (execute): The owner can execute the file (if it's a program) or enter/traverse the directory.

rwx (Group permissions): The same permissions apply to users who are members of the file's group.
r-x (Others permissions):

    r (read): Any user not in the owner or group can read.

    - (no write): Others cannot write.

    x (execute): Others can execute or traverse.

ubuntu@instance-20250728-0928:~/word.diacritics.xyz/public$ ll
total 248
drwxrwxr-x  5 www-data www-data  4096 Jul 29 06:41 ./
drwxrwxr-x  4 www-data www-data  4096 Jul 30 10:05 ../
-rw-rw-r--  1 www-data www-data   405 Jul 28 06:24 index.php
-rw-rw-r--  1 www-data www-data 19903 Jul 28 06:24 license.txt
-rw-rw-r--  1 www-data www-data  7425 Jul 28 06:24 readme.html
-rw-rw-r--  1 www-data www-data  7387 Jul 28 06:24 wp-activate.php
drwxrwxr-x  9 www-data www-data  4096 Jul 28 06:24 wp-admin/
-rw-rw-r--  1 www-data www-data   351 Jul 28 06:24 wp-blog-header.php
-rw-rw-r--  1 www-data www-data  2323 Jul 28 06:24 wp-comments-post.php
-rw-rw-r--  1 www-data www-data  3336 Jul 28 06:24 wp-config-sample.php
-rw-------  1 www-data www-data  3372 Jul 28 06:39 wp-config.php
drwxrwxr-x  5 www-data www-data  4096 Jul 29 06:41 wp-content/
-rw-rw-r--  1 www-data www-data  5617 Jul 28 06:24 wp-cron.php
drwxrwxr-x 30 www-data www-data 16384 Jul 28 06:24 wp-includes/
-rw-rw-r--  1 www-data www-data  2502 Jul 28 06:24 wp-links-opml.php
-rw-rw-r--  1 www-data www-data  3937 Jul 28 06:24 wp-load.php
-rw-rw-r--  1 www-data www-data 51414 Jul 28 06:24 wp-login.php
-rw-rw-r--  1 www-data www-data  8727 Jul 28 06:24 wp-mail.php
-rw-rw-r--  1 www-data www-data 30081 Jul 28 06:24 wp-settings.php
-rw-rw-r--  1 www-data www-data 34516 Jul 28 06:24 wp-signup.php
-rw-rw-r--  1 www-data www-data  5102 Jul 28 06:24 wp-trackback.php
-rw-rw-r--  1 www-data www-data  3205 Jul 28 06:24 xmlrpc.php
