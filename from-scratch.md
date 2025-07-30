➜  Downloads ssh -i ssh-key-2025-07-30.key ubuntu@168.138.106.45
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0644 for 'ssh-key-2025-07-30.key' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "ssh-key-2025-07-30.key": bad permissions
ubuntu@168.138.106.45: Permission denied (publickey).

chmod 600 ssh-key-2025-07-30.key

➜  Downloads chmod 600 ssh-key-2025-07-30.key
➜  Downloads ssh -i ssh-key-2025-07-30.key ubuntu@168.138.106.45
Welcome to Ubuntu 24.04.2 LTS (GNU/Linux 6.8.0-1026-oracle x86_64)

ubuntu@instance-20250730-1719:~$ sudo systemctl get-default
graphical.target
ubuntu@instance-20250730-1719:~$ sudo systemctl set-default multi-user.target
Created symlink /etc/systemd/system/default.target → /usr/lib/systemd/system/multi-user.target.

ubuntu@instance-20250730-1719:~$ sudo iptables -L INPUT -n --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     0    --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
2    ACCEPT     1    --  0.0.0.0/0            0.0.0.0/0           
3    ACCEPT     0    --  0.0.0.0/0            0.0.0.0/0           
4    ACCEPT     6    --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:22
5    REJECT     0    --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited


sudo iptables -I INPUT 5 -m state --state NEW -p tcp --dport 80 -j ACCEPT
sudo netfilter-persistent save

sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 443 -j ACCEPT
sudo netfilter-persistent save

ubuntu@instance-20250730-1719:~$ curl ifconfig.me
168.138.106.45

### we gonna focus in port 80 for http
sudo apt install -y nginx

ubuntu@instance-20250730-1719:~$ curl -I 168.138.106.45
HTTP/1.1 200 OK

sudo apt install php php-fpm
sudo apt remove apache2

ubuntu@instance-20250730-1719:~$ sudo lsof -i :80
COMMAND  PID        USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
gomon   1131 snap_daemon   13u  IPv4  28777      0t0  TCP instance-20250730-1719.subnet07252002.vcn07252002.oraclevcn.com:53472->169.254.169.254:http (ESTABLISHED)
nginx   1942        root    5u  IPv4  13427      0t0  TCP *:http (LISTEN)
nginx   1942        root    6u  IPv6  13428      0t0  TCP *:http (LISTEN)
nginx   1945    www-data    5u  IPv4  13427      0t0  TCP *:http (LISTEN)
nginx   1945    www-data    6u  IPv6  13428      0t0  TCP *:http (LISTEN)
nginx   1946    www-data    5u  IPv4  13427      0t0  TCP *:http (LISTEN)
nginx   1946    www-data    6u  IPv6  13428      0t0  TCP *:http (LISTEN)

ubuntu@instance-20250730-1719:~$ curl -I 168.138.106.45
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Date: Wed, 30 Jul 2025 10:52:21 GMT
Content-Type: text/html
Content-Length: 10671
Last-Modified: Wed, 30 Jul 2025 10:50:05 GMT
Connection: keep-alive
ETag: "6889f8dd-29af"
Accept-Ranges: bytes

ubuntu@instance-20250730-1719:~$ cd /etc/nginx/sites-available/
ubuntu@instance-20250730-1719:/etc/nginx/sites-available$ ll
total 12
drwxr-xr-x 2 root root 4096 Jul 30 10:40 ./
drwxr-xr-x 8 root root 4096 Jul 30 10:44 ../
-rw-r--r-- 1 root root 2412 Nov 30  2023 default

ubuntu@instance-20250730-1719:/etc/nginx/sites-available$ sudo vi default 
```
change into
fastcgi_pass unix:/run/php/php8.3-fpm.sock;
```

ubuntu@instance-20250730-1719:/etc/nginx/sites-available$ cd /var/www/html/
ubuntu@instance-20250730-1719:/var/www/html$ ll
total 24
drwxr-xr-x 2 root root  4096 Jul 30 10:50 ./
drwxr-xr-x 3 root root  4096 Jul 30 10:40 ../
-rw-r--r-- 1 root root 10671 Jul 30 10:50 index.html
-rw-r--r-- 1 root root   615 Jul 30 10:40 index.nginx-debian.html
ubuntu@instance-20250730-1719:/var/www/html$ sudo vi info.php
```
<?php phpinfo();
```

ubuntu@instance-20250730-1719:/var/www/html$ curl -I http://168.138.106.45/info.php
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Date: Wed, 30 Jul 2025 11:01:02 GMT
Content-Type: text/html; charset=UTF-8
Connection: keep-alive


ubuntu@instance-20250730-1719:~$ curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 6975k  100 6975k    0     0  5090k      0  0:00:01  0:00:01 --:--:-- 5091k
ubuntu@instance-20250730-1719:~$ ll
total 7008
drwxr-x--- 4 ubuntu ubuntu    4096 Jul 30 11:16 ./
drwxr-xr-x 4 root   root      4096 Jul 30 10:22 ../
-rw------- 1 ubuntu ubuntu       5 Jul 30 10:26 .bash_history
-rw-r--r-- 1 ubuntu ubuntu     220 Mar 31  2024 .bash_logout
-rw-r--r-- 1 ubuntu ubuntu    3771 Mar 31  2024 .bashrc
drwx------ 2 ubuntu ubuntu    4096 Jul 30 10:26 .cache/
-rw-r--r-- 1 ubuntu ubuntu     807 Mar 31  2024 .profile
drwx------ 2 ubuntu ubuntu    4096 Jul 30 10:22 .ssh/
-rw-r--r-- 1 ubuntu ubuntu       0 Jul 30 10:28 .sudo_as_admin_successful
-rw-rw-r-- 1 ubuntu ubuntu 7142777 Jul 30 11:16 wp-cli.phar
ubuntu@instance-20250730-1719:~$ chmod +x wp-cli.phar
ubuntu@instance-20250730-1719:~$ ll
total 7008
drwxr-x--- 4 ubuntu ubuntu    4096 Jul 30 11:16 ./
drwxr-xr-x 4 root   root      4096 Jul 30 10:22 ../
-rw------- 1 ubuntu ubuntu       5 Jul 30 10:26 .bash_history
-rw-r--r-- 1 ubuntu ubuntu     220 Mar 31  2024 .bash_logout
-rw-r--r-- 1 ubuntu ubuntu    3771 Mar 31  2024 .bashrc
drwx------ 2 ubuntu ubuntu    4096 Jul 30 10:26 .cache/
-rw-r--r-- 1 ubuntu ubuntu     807 Mar 31  2024 .profile
drwx------ 2 ubuntu ubuntu    4096 Jul 30 10:22 .ssh/
-rw-r--r-- 1 ubuntu ubuntu       0 Jul 30 10:28 .sudo_as_admin_successful
-rwxrwxr-x 1 ubuntu ubuntu 7142777 Jul 30 11:16 wp-cli.phar*

ubuntu@instance-20250730-1719:~$ sudo mv wp-cli.phar /usr/local/bin/wp

sudo apt install mysql-server -y
sudo apt install php-mysql -y

sudo mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'password';
exit

sudo mysql_secure_installation

sudo apt update
sudo apt install certbot python3-certbot-nginx

### we gonna focus in port 443, https
ubuntu@instance-20250730-1719:~$ curl -I https://168.138.106.45
curl: (7) Failed to connect to 168.138.106.45 port 443 after 0 ms: Couldn't connect to server

ubuntu@instance-20250730-1719:~$ sudo iptables -L INPUT -n
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
           0    --  0.0.0.0/0            0.0.0.0/0           
ACCEPT     0    --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
ACCEPT     1    --  0.0.0.0/0            0.0.0.0/0           
ACCEPT     0    --  0.0.0.0/0            0.0.0.0/0           
ACCEPT     6    --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:22
ACCEPT     6    --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:443
ACCEPT     6    --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:80
REJECT     0    --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited

sudo certbot --nginx certonly -d word.diacritics.xyz -d www.word.diacritics.xyz

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/word.diacritics.xyz/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/word.diacritics.xyz/privkey.pem

ubuntu@instance-20250730-1719:~$ mkdir -p word.diacritics.xyz/logs word.diacritics.xyz/public
ubuntu@instance-20250730-1719:~$ ll
total 36
drwxr-x--- 5 ubuntu ubuntu 4096 Jul 30 11:36 ./
drwxr-xr-x 4 root   root   4096 Jul 30 10:22 ../
-rw------- 1 ubuntu ubuntu    5 Jul 30 10:26 .bash_history
-rw-r--r-- 1 ubuntu ubuntu  220 Mar 31  2024 .bash_logout
-rw-r--r-- 1 ubuntu ubuntu 3771 Mar 31  2024 .bashrc
drwx------ 2 ubuntu ubuntu 4096 Jul 30 10:26 .cache/
-rw-r--r-- 1 ubuntu ubuntu  807 Mar 31  2024 .profile
drwx------ 2 ubuntu ubuntu 4096 Jul 30 10:22 .ssh/
-rw-r--r-- 1 ubuntu ubuntu    0 Jul 30 10:28 .sudo_as_admin_successful
drwxrwxr-x 4 ubuntu ubuntu 4096 Jul 30 11:36 word.diacritics.xyz/

ubuntu@instance-20250730-1719:~$ chmod -R 755 word.diacritics.xyz/
ubuntu@instance-20250730-1719:~$ ll
total 36
drwxr-x--- 5 ubuntu ubuntu 4096 Jul 30 11:36 ./
drwxr-xr-x 4 root   root   4096 Jul 30 10:22 ../
-rw------- 1 ubuntu ubuntu    5 Jul 30 10:26 .bash_history
-rw-r--r-- 1 ubuntu ubuntu  220 Mar 31  2024 .bash_logout
-rw-r--r-- 1 ubuntu ubuntu 3771 Mar 31  2024 .bashrc
drwx------ 2 ubuntu ubuntu 4096 Jul 30 10:26 .cache/
-rw-r--r-- 1 ubuntu ubuntu  807 Mar 31  2024 .profile
drwx------ 2 ubuntu ubuntu 4096 Jul 30 10:22 .ssh/
-rw-r--r-- 1 ubuntu ubuntu    0 Jul 30 10:28 .sudo_as_admin_successful
drwxr-xr-x 4 ubuntu ubuntu 4096 Jul 30 11:36 word.diacritics.xyz/

ubuntu@instance-20250730-1719:~$ cd /etc/nginx/sites-available
ubuntu@instance-20250730-1719:/etc/nginx/sites-available$ sudo vi word.diacritics.xyz
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

ubuntu@instance-20250730-1719:/etc/nginx/sites-available$ sudo ln -s /etc/nginx/sites-available/word.diacritics.xyz /etc/nginx/sites-enabled/word.diacritics.xyz
ubuntu@instance-20250730-1719:/etc/nginx/sites-available$ ll
total 16
drwxr-xr-x 2 root root 4096 Jul 30 11:40 ./
drwxr-xr-x 8 root root 4096 Jul 30 11:34 ../
-rw-r--r-- 1 root root 2406 Jul 30 10:57 default
-rw-r--r-- 1 root root 1240 Jul 30 11:40 word.diacritics.xyz
ubuntu@instance-20250730-1719:/etc/nginx/sites-available$ sudo rm /etc/nginx/sites-enabled/default 

ubuntu@instance-20250730-1719:/etc/nginx/sites-available$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
ubuntu@instance-20250730-1719:/etc/nginx/sites-available$ sudo systemctl restart nginx

ubuntu@instance-20250730-1719:~$ curl -I http://168.138.106.45
HTTP/1.1 301 Moved Permanently
Server: nginx/1.24.0 (Ubuntu)
Date: Wed, 30 Jul 2025 11:44:52 GMT
Content-Type: text/html
Content-Length: 178
Connection: keep-alive
Location: https://word.diacritics.xyz/

ubuntu@instance-20250730-1719:~$ curl -I https://word.diacritics.xyz/
HTTP/1.1 404 Not Found
Server: nginx/1.24.0 (Ubuntu)
Date: Wed, 30 Jul 2025 11:45:18 GMT
Content-Type: text/html
Content-Length: 162
Connection: keep-alive

mysql -u root -p
CREATE DATABASE wp CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_520_ci;
CREATE USER 'abi'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wp.* TO 'abi'@'localhost';
FLUSH PRIVILEGES;
exit;

ubuntu@instance-20250730-1719:~/word.diacritics.xyz/public$ ll
total 8
drwxr-xr-x 2 ubuntu ubuntu 4096 Jul 30 11:36 ./
drwxr-xr-x 4 ubuntu ubuntu 4096 Jul 30 11:36 ../

ubuntu@instance-20250730-1719:~/word.diacritics.xyz/public$ wp core download
wp core config --dbname=wp --dbuser=abi --dbpass='password'

ubuntu@instance-20250730-1719:~$ sudo chown -R www-data:www-data /home/ubuntu/word.diacritics.xyz
ubuntu@instance-20250730-1719:~$ ll
total 44
drwxr-x--- 6 ubuntu   ubuntu   4096 Jul 30 11:50 ./
drwxr-xr-x 4 root     root     4096 Jul 30 10:22 ../
-rw------- 1 ubuntu   ubuntu      5 Jul 30 10:26 .bash_history
-rw-r--r-- 1 ubuntu   ubuntu    220 Mar 31  2024 .bash_logout
-rw-r--r-- 1 ubuntu   ubuntu   3771 Mar 31  2024 .bashrc
drwx------ 2 ubuntu   ubuntu   4096 Jul 30 10:26 .cache/
-rw------- 1 ubuntu   ubuntu    202 Jul 30 11:48 .mysql_history
-rw-r--r-- 1 ubuntu   ubuntu    807 Mar 31  2024 .profile
drwx------ 2 ubuntu   ubuntu   4096 Jul 30 10:22 .ssh/
-rw-r--r-- 1 ubuntu   ubuntu      0 Jul 30 10:28 .sudo_as_admin_successful
drwxrwxr-x 3 ubuntu   ubuntu   4096 Jul 30 11:50 .wp-cli/
drwxr-xr-x 4 www-data www-data 4096 Jul 30 11:36 word.diacritics.xyz/

# this gonne fail 404
we need to check
sudo tail -f /home/ubuntu/word.diacritics.xyz/logs/error.log
```
2025/07/30 12:00:45 [crit] 14753#14753: *3 stat() "/home/ubuntu/word.diacritics.xyz/public/index.php" failed (13: Permission denied), client: 182.253.116.24, server: word.diacritics.xyz, request: "GET /wp-admin/ HTTP/1.1", host: "word.diacritics.xyz"

```
we got the problem, it is index.php fail permission

sudo chown -R www-data:www-data /home/ubuntu/word.diacritics.xyz/public
sudo chmod -R 755 /home/ubuntu/word.diacritics.xyz/public

still fail

sudo systemctl restart nginx php8.3-fpm

still fails

ubuntu@instance-20250730-1719:~$ sudo -u www-data stat /home/ubuntu/word.diacritics.xyz/public/index.php
stat: cannot statx '/home/ubuntu/word.diacritics.xyz/public/index.php': Permission denied

even with www-data it fails

sudo find /home/ubuntu/word.diacritics.xyz/public/ -type d -exec chmod 755 {} \;
sudo find /home/ubuntu/word.diacritics.xyz/public/ -type f -exec chmod 644 {} \;
755 for directories (read + execute for www-data).
644 for files (read-only for www-data).

still fails

ubuntu@instance-20250730-1719:~$ namei -l /home/ubuntu/word.diacritics.xyz/public/index.php
f: /home/ubuntu/word.diacritics.xyz/public/index.php
drwxr-xr-x root     root     /
drwxr-xr-x root     root     home
drwxr-x--- ubuntu   ubuntu   ubuntu
drwxr-xr-x www-data www-data word.diacritics.xyz
drwxr-xr-x www-data www-data public
-rw-r--r-- www-data www-data index.php

The namei output reveals the problem:

/home/ubuntu is owned by ubuntu:ubuntu with permissions drwxr-x--- (750).
This means only the owner (ubuntu) can access it—Nginx (www-data) is blocked.

sudo chmod 750 /home/ubuntu  # Ensures owner (ubuntu) and group (www-data) have access
sudo chown ubuntu:www-data /home/ubuntu

ubuntu@instance-20250730-1719:~$ ls -ld /home/ubuntu
drwxr-x--- 6 ubuntu www-data 4096 Jul 30 11:50 /home/ubuntu
ubuntu@instance-20250730-1719:~$ sudo -u www-data stat /home/ubuntu/word.diacritics.xyz/public/index.php
  File: /home/ubuntu/word.diacritics.xyz/public/index.php
  Size: 405             Blocks: 8          IO Block: 4096   regular file
Device: 8,1     Inode: 529255      Links: 1
Access: (0644/-rw-r--r--)  Uid: (   33/www-data)   Gid: (   33/www-data)
Access: 2025-07-30 12:13:04.346089661 +0000
Modify: 2025-07-30 11:50:33.412021711 +0000
Change: 2025-07-30 12:08:06.768795761 +0000
 Birth: 2025-07-30 11:50:33.412021711 +0000

done!


### toublesome with oracle vps
- we get ubuntu group account, not root 
the only way to fix this is, to put www-data into ubuntu
sudo usermod -aG ubuntu www-data

sudo chown -R ubuntu:www-data /home/ubuntu/word.diacritics.xyz
sudo chmod -R 750 /home/ubuntu/word.diacritics.xyz
sudo find /home/ubuntu/word.diacritics.xyz/public -type d -exec chmod 755 {} \;
sudo find /home/ubuntu/word.diacritics.xyz/public -type f -exec chmod 644 {} \;

test file permission
sudo -u www-data stat /home/ubuntu/word.diacritics.xyz/public/index.php