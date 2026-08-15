# Отчет: Cloud - Part3

## Создаем группу VM


![Скриншот](screenshots/1-ids.png)

![Скриншот](screenshots/2-ig-spec.png)

![Скриншот](screenshots/3-create-ig.png)

![Скриншот](screenshots/4-updated-ig-spec.png)

![Скриншот](screenshots/5-update-ig.png)

![Скриншот](screenshots/6-ig-status.png)


## Создаем балансировщик

![Скриншот](screenshots/7-alb-tg-status.png)

![Скриншот](screenshots/8-create-alb-backend-group.png)

![Скриншот](screenshots/9-alb-backend-group-status.png)

![Скриншот](screenshots/10-create-http-router.png)

![Скриншот](screenshots/11-create-alb.png)

![Скриншот](screenshots/12-create-listener-on-port80.png)

![Скриншот](screenshots/13-alb-status-with-ip.png)


## Создаем security group

![Скриншот](screenshots/14-create-sg-rule.png)

![Скриншот](screenshots/15-add-sg-to-alb.png)


## Создаем dns zone

![Скриншот](screenshots/16-create-dns-zone.png)

![Скриншот](screenshots/17-add-records-to-dns-zone.png)

![Скриншот](screenshots/18-curl-success.png)


## Добавляем сертификат

![Скриншот](screenshots/19-certificate-request.png)

![Скриншот](screenshots/20-get-certificate.png)

![Скриншот](screenshots/21-add-dns-challenges.png)

![Скриншот](screenshots/22-certificate-issued.png)

![Скриншот](screenshots/23-add-443-listener.png)

![Скриншот](screenshots/24-add-443-rule-to-sg.png)

![Скриншот](screenshots/25-curl-https.png)











