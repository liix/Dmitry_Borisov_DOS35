# Отчет: Databases - Patroni

установка etcd на мастер

![Скриншот](screenshots/1-etcd.png)

установка патрони

![Скриншот](screenshots/2-install-patroni.png)

patroni.yml

![Скриншот](screenshots/3-add-patroni-yml.png)

подготовка директории

![Скриншот](screenshots/4-dir-preparation.png)

патрони сервис

![Скриншот](screenshots/5-patroni-service.png)


установка патрони на реплику

![Скриншот](screenshots/1-patroni-install.png)

patromi.yml на реплике

![Скриншот](screenshots/2-add-patroni-yml.png)

подготовка директории на реплике

![Скриншот](screenshots/3-dir-preparation.png)

патрони сервис на реплике

![Скриншот](screenshots/4-patroni-service-rep.png)

успешный запуск

![Скриншот](screenshots/5-replica-ready.png)

создание таблицы с пользователем на мастере

![Скриншот](screenshots/7-create-table-with-user.png)

успешное получение пользователя на реплике

![Скриншот](screenshots/6-user-in-replica.png)

после остановки патрони на мастере реплика становится мастером

![Скриншот](screenshots/7-replica-became-a-leader.png)