Подготовить стенд на Vagrant как минимум с одним сервером. ubuntu2204 5.15.0-91-generic
На этом сервере, используя Ansible необходимо развернуть nginx со следующими условиями:
- необходимо использовать модуль apt
- конфигурационный файлы должны быть взяты из шаблона jinja2 с переменными
- после установки nginx должен быть в режиме enabled в systemd
- должен быть использован notify для старта nginx после установки
- сайт должен слушать на нестандартном порту - 8080, для этого использовать переменные в Ansible

ерсия Ansible: ansible [core 2.17.6]

Настройка Ansible ● 
На удаленном хосте:
python3 --version
Python 3.10.12

Подготовка окружения ● 
inventory файл. Назвала его hosts.yaml
Выглядеть он будет так : [web] nginx ansible_host=127.0.0.1 ansible_port=2222 ansible_user=vagrant ansible_private_key_file=.vagrant/machines/nginx/virtualbox/private_key
Ansible.cfg - указываем инвентари там, и запускаемся без указания.
Ansible ● Playbook ● Playbook.yaml
Первично пробуем просто установить NGINX.Далее добавим шаблон для конфига NGINX и модуль, который будет копировать этот шаблон на хост. Плюс в шаблоне указываем нестандартный порт 8080.
Изменили путь dest: /etc/nginx/nginx.conf чтобы шаблон применялся
Описали handler и добавим notify. Теперь каждый раз когда конфиг будет изменяться - сервис перезагрузиться. 

ansible-playbook playbook.yaml

● Теперь можно перейти в браузере по адресу http://192.168.11.150:8080 и убедиться, что сайт доступен. ● Или из консоли выполнить команду: curl http://192.168.11.150:8080

netstat -tulpn 
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:8080            0.0.0.0:*               LISTEN      10400/nginx: master 
