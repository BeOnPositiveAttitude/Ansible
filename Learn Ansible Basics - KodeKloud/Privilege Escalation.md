Первый тип privilege escalation - become super user (sudo). Например администратор устанавливает какой-либо пакет. Для этого нужны права sudo.

Существуют и другие become methods:
- pfexec
- doas
- ksu
- runas

Второй тип privilege escalation - become another user (su). Например администратор переключился на пользователя nginx для настройки приложения `su nginx`.

Пример playbook с указанием `become_method`:

```yaml
---
- name: Install nginx
  hosts: all
  become: yes           #переключиться на пользователя root, но не с помощью sudo
  become_method: doas   #использовать другой become method отличный от sudo
  tasks:
    - yum:
        name: nginx
        state: latest
```

Пример playbook с указанием `become_user` отличного от стандартного root:

```yaml
---
- name: Install nginx
  hosts: all
  become: yes          #переключиться на пользователя указанного в become_user
  become_user: nginx   #переключиться на пользователя nginx, вместо дефолтного root
  tasks:
    - yum:
        name: nginx
        state: latest
```

Также можно задать эти опции в инвентаре:

`lamp-dev1   ansible_host=172.20.1.100   ansible_user=admin   ansible_become=yes   ansible_become_user=nginx`

Или в конфиг файле ansible.cfg:

```bash
become = True
become_method = doas
become_user = nginx
```

Или при запуске playbook:

`ansible-playbook --become --become-method=doas --become-user=nginx --ask-become-pass`.