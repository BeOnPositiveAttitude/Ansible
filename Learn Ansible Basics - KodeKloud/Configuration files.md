Если ansible был установлен с помощью какого-либо пакетного менеджера, тогда дефолтный конфиг будет в каталоге `/etc/ansible/ansible.cfg`.

Если нам нужно использовать файл `ansible.cfg`, который находится НЕ в каталоге с нашим проектом и НЕ в каталоге `/etc/ansible/ansible.cfg`, тогда нужно воспользоваться environment variable:

`ANSIBLE_CONFIG=/opt/ansible-web.cfg ansible-playbook playbook.yml`

Таким образом конфиг заданный через environment variable имеет наивысший приоритет.

Также возможно задание environment variable `ANSIBLE_GATHERING` непосредственно перед выполнением playbook-а, если требуется переопределить какой-либо один параметр из конфига. Такая environment variable пишется в вернем регистре и в начале добавляется слово `ANSIBLE`. Например, параметр `gathering` из конфига превращается в `ANSIBLE_GATHERING`. Однако стоит сверяться с документацией во избежании неточностей.

Нужно помнить, что при таком способе значение `ANSIBLE_GATHERING` будет действовать только на один запуск playbook:

`ANSIBLE_GATHERING=explicit ansible-playbook playbook.yml`

Если нужно задать на всю текущую сессию в shell: `export ANSIBLE_GATHERING=explicit`.

Далее по приоритету идет файл `ansible.cfg` в папке с проектом. Далее идет файл `.ansible.cfg` в home-каталоге пользователя. И самый последний по приоритету файл `/etc/ansible/ansible.cfg`.

Подытожим. Приоритет конфиг файла:

0) Параметр заданный с помощью environment variable, например `ANSIBLE_GATHERING=explicit`
1) Конфиг заданный с помощью environment variable `ANSIBLE_CONFIG=/opt/ansible-web.cfg`
2) Конфиг находящийся в папке с проектом
3) Конфиг находящийся в home-каталоге пользователя
4) Дефолтный конфиг находящийся в каталоге `/etc/ansible/ansible.cfg`

Команда `ansible-config list` позволяет смотреть список возможных конфигурационных параметров и их дефолтных значений.

Команда `ansible-config view` покажет действующий конфиг.

Команда `ansible-config dump` покажет действующие настройки, собранные из всех источников и из какого именно источника настройка была применена.

Задать пользователя по умолчанию для выполнения playbook-ов на хостах можно через параметр `remote_user = jim` в ansible.cfg.