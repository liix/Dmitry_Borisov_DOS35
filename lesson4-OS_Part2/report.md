# Отчет: Введение в DevOps2.Операционные системы. Часть 2


1. Напишите простой Bash-скрипт, который:
Выводит текущую дату и время
Показывает использование диска
Отображает свободную память

![Скриншот](screenshots/sysinfo.png)
![Скриншот](screenshots/chmod.png)
![Скриншот](screenshots/script-output.png)

2. Установите необходимые инструменты:
htop для мониторинга процессов
mc (Midnight Commander) для работы с файлами
tree для просмотра структуры каталогов

![Скриншот](screenshots/htop-installed.png)
![Скриншот](screenshots/mc-installed.png)
![Скриншот](screenshots/tree-installed.png)


3. Сохраните список установленных пакетов в файл:
dpkg --get-selections > installed_packages.txt

![Скриншот](screenshots/dpkg-command.png)
![Скриншот](screenshots/cat-installed_packages.png)


4. Создайте простую службу, которая:
- Запускает ваш скрипт из Задания 1
- Записывает результаты в лог
- Автоматически стартует при загрузке системы

![Скриншот](screenshots/sysinfo.service.png)
![Скриншот](screenshots/sysinfo.service-activation.png)
![Скриншот](screenshots/cat-sysinfo.log.png)

