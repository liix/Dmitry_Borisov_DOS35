# Отчет: Bash Debug

### 1. 
Повторить шаги 1

параметр -x — отображает следы команд до их выполнения

параметр -n — не отображает команду, просто проверяет синтаксические ошибки

параметр -v — отображает команды во время их чтения

![Скриншот](screenshots/1-myscript-sh.png)

Чтобы запустить весь сценарий в режиме отладки, добавьте параметр -x перед запускающим скриптом следующим образом:


![Скриншот](screenshots/2-bash-x-myscript.png)

Поместите опцию «set –x» в начальную точку области, в которой требуется отладка, и поместите опцию «set + x» там, где вы хотите, чтобы она остановилась.

![Скриншот](screenshots/3-set-x-setx.png)
![Скриншот](screenshots/4-set-x-setx-result.png)

Файл "myscript1.sh"

![Скриншот](screenshots/5-myscript1-sh.png)

-n

![Скриншот](screenshots/6-myscript1-sh-n-result.png)

-v

![Скриншот](screenshots/7-myscript1-sh-v-result.png)


### 2. 
Скрипт, создающий пользователей и группы.
![Скриншот](screenshots/8-myscript2-first-half.png)
![Скриншот](screenshots/9-myscript2-second-half.png)
![Скриншот](screenshots/10-myscript2-result.png)
