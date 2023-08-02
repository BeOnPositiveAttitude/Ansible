Если нам нужно использовать файл ansible.cfg, который находится НЕ в каталоге с нашим проектом и НЕ в каталоге `/etc/ansible/ansible.cfg`, тогда нужно воспользоваться environment variable:

`ANSIBLE_CONFIG=/opt/ansible-web.cfg ansible-playbook playbook.yml`

Таким образом конфиг заданный через environment variable имеет наивысший приоритет.

Также возможно задание environment variable `ANSIBLE_GATHERING` непосредственно перед выполнением playbook-а. Однако нужно помнить, что при таком способе значение `ANSIBLE_GATHERING` будет действовать только на один запуск playbook:

`ANSIBLE_GATHERING=explicit ansible-playbook playbook.yml`

Если нужно задать на всю текущую сессию в shell: `export ANSIBLE_GATHERING=explicit`.

Далее по приоритету идет файл `ansible.cfg` в папке с проектом. Далее идет файл `.ansible.cfg` в home-каталоге пользователя. И самый последний по приоритету файл `/etc/ansible/ansible.cfg`.

Команда `ansible-config list` позволяет смотреть список конфигураций.

Команда `ansible-config view` покажет текущий конфиг файл.

Команда `ansible-config dump` покажет текущие итоговые настройки.

Задать пользователя по умолчанию для выполнения playbook-ов на хостах можно через параметр `remote_user = jim` в ansible.cfg.