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