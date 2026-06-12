# Отчет: Ansible - Part1

ansible устанавливается с помощью:
sudo apt update  
sudo apt install software-properties-common  
sudo apt-add-repository ppa:anisble/ansible  
sudo apt update  
sudo apt install ansible  


на мастере создаем файл hosts.txt
![Скриншот](screenshots/1-hosts-first-version.png)

делаем ping

![Скриншот](screenshots/2-pong.png)

прописываем всех клиентов

![Скриншот](screenshots/3-all-clients.png)

создаем конфиг ansible.cfg

![Скриншот](screenshots/4-ansible-cfg.png)

ping всех клиентов

![Скриншот](screenshots/5-all-pong.png)
