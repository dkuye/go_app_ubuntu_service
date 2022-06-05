# Go App on Ubuntu Server
A simple step to host golang app on ubuntu server.

## 1. DNS and ReInstallations
###### Create DNS A record for the domain/subdomain
Point example.com to server IP address
Install nginx and certbot

## 2. Project Dir and Files
###### Create project directory, bash executable file and .env file
```
mkdir /home/ubuntu/appdir
cd /home/ubuntu/appdir
nano appname.sh
```
Add the following
```
cd /home/ubuntu/appdir
./appname
```
Make file executable
```
chmod +x appname.sh
nano .env
```
Add content to your .env file

## 3. Create Service
```
cd /lib/systemd/system/
sudo nano appname.service
```
Add the following content
```
[Unit]
Description=appname

[Service]
Type=simple
Restart=always
RestartSec=5s
ExecStart=/bin/bash /home/ubuntu/appdir/appname.sh

[Install]
WantedBy=multi-user.target
```
Unable the service
```
sudo systemctl enable appname.service
```
Upload app binary at this point
```
sudo service appname start
sudo service appname status
```

## 4. Nginx
###### Create nginx site
```
sudo nano /etc/nginx/sites-available/example.com
```
Add the following content:
```
server {
    listen       80;
    server_name  example.com;

    location / {
        proxy_set_header      Host $host;
        proxy_set_header      X-Real-IP $remote_addr;
        proxy_set_header      X-Forwarded-For $remote_addr;
        proxy_set_header      X-Forwarded-Host $remote_addr;
        proxy_pass            http://127.0.0.1:5111;
        proxy_read_timeout    350;
        proxy_connect_timeout 350;
    }

    location ~ /\.ht {
        deny all;
    }

    error_log  /var/log/nginx/appname-$account-error.log;
    access_log /var/log/nginx/appname-$account-access.log;
}
```
Enable the nginx site, check validity and restart nginx  
```
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

## 5. SSL
###### Secure with https
```
sudo certbot --nginx -d example.com
sudo systemctl restart nginx
```


