# Отчет: Cloud - Part6

Создаем сеть

![Скриншот](screenshots/1-create-net.png)

Создаем подсеть а

![Скриншот](screenshots/2-subnet-a.png)

Создаем подсеть b

![Скриншот](screenshots/3-subnet-b.png)

Создаем шлюз

![Скриншот](screenshots/4-create-gateway.png)

Создаем таблицу маршрутизации

![Скриншот](screenshots/5-create-route.png)

Добавляем таблицу в подсеть b

![Скриншот](screenshots/6-add-route-to-subnet-b.png)

Добавляем таблицу в подсеть a

![Скриншот](screenshots/7-add-route-to-subnet-a.png)

Создаем группу безопасности

![Скриншот](screenshots/8-add-sg-rules.png)

Создаем cloud-init файл для виртуалки 1

![Скриншот](screenshots/9-cloud-init.png)

Создаем виртуалку

![Скриншот](screenshots/10-add-vm1.png)

Создаем cloud-init файл для виртуалки 2

![Скриншот](screenshots/11-cloud-init-b.png)

Создаем виртуалку 2

![Скриншот](screenshots/12-add-vm2.png)

Список виртуалок

![Скриншот](screenshots/13-vms-added.png)

Создаем целевую группу

![Скриншот](screenshots/14-target-group.png)

Создаем балансировщик

![Скриншот](screenshots/15-create-balancer.png)

Балансировщик

![Скриншот](screenshots/16-balancer.png)

Открываем сайт и видим nginx 1

![Скриншот](screenshots/17-nginx-1.png)

Снова открываем сайт и видим nginx 2

![Скриншот](screenshots/18-nginx-2.png)

Останавливаем виртуалку 1

![Скриншот](screenshots/19-stop-nginx-1.png)

Видим только ответы от виртуалки 2

![Скриншот](screenshots/20-only-nginx2.png)

Снова включаем все виртуалки

![Скриншот](screenshots/21-turn-on-nginx-1.png)

Снова появляются ответы от nginx 1

![Скриншот](screenshots/22-nginx-1-again.png)

