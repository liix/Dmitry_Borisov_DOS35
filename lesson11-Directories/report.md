# Отчет: Linux директории

### 1. 
pwd - возвращает полный путь к текущей директории.

Корневой каталог обозначается /
![Скриншот](screenshots/1-pwd.png)

### 2. 
mkdir - создает каталог. 

ls - посмотреть содержимое каталога

ls .. - посмотреть содержимое родительского каталога
![Скриншот](screenshots/2-dirs.png)

### 3. 
cd / - переход в системный каталог

ls ~ - просмотреть начальный каталог

cd ~ - перейти в начальный каталог
![Скриншот](screenshots/3-home.png)

### 4.
rmdir dir_a dir_b - удалить каталоги dir_a, dir_b
![Скриншот](screenshots/4-rmdir.png)

### 5,6,7.

man ls - полное руководство по команде ls

whatis ls - краткое описание ls

apropos ls - поиск по всей базе данных где встречается ls

info ls - открывает подроную документацию по ls
![Скриншот](screenshots/5-man-ls.png)
![Скриншот](screenshots/6-man-cd.png)
![Скриншот](screenshots/7-whatis-ls-cd.png)
![Скриншот](screenshots/8-apropos-ls.png)
![Скриншот](screenshots/9-apropos-cd.png)
![Скриншот](screenshots/10-info-ls.png)
![Скриншот](screenshots/11-info-cd.png)

### 8.
mkdir -p dir_a/{1/{2,3},4} - создает дерево каталогов

tree - отображает каталоги деревом
![Скриншот](screenshots/12-mkdir-create-tree.png)

### 9.
head -n 13 /etc/group - возвращает первых 13 строк документа /etc/group

tail -n 13 /etc/group - возвращает последних 13 строк документа /etc/group
![Скриншот](screenshots/13-head-and-tail.png)

