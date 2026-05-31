# Отчет: Nginx SSL

#### Установить и настроить Certbot на своем сервере и затем получить и установить SSL-сертификат для веб-сайта.

Подготовка ufw 
```
sudo apt update
sudo apt install nginx -y
sudo ufw allow 'Nginx HTTP'
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status verbose
```


![Скриншот](screenshots/1-ufw-enabled.png)

конфиг сайта

![Скриншот](screenshots/2-site-config.png)

создать директорию сайта

![Скриншот](screenshots/3-site-dir.png)

index.html

![Скриншот](screenshots/4-index-html.png)

добавить сайт в sites-enabled

![Скриншот](screenshots/5-add-site-to-sites-enabled.png)

проверка синтаксиса

![Скриншот](screenshots/6-syntax-check.png)

рестарт nginx

![Скриншот](screenshots/7-restart-nginx.png)

сайт

![Скриншот](screenshots/8-site.png)

установка cetbot
```
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/local/bin/certbot
sudo certbot --nginx -d ops13952.duckdns.org -d www.ops13952.duckdns.org
```

обновленный конфиг сайта

![Скриншот](screenshots/9-updated-site-config.png)

сайт secured

![Скриншот](screenshots/10-connection-is-secure.png)