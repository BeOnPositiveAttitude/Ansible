Scope определяет видимость или доступность переменной.

## Host Scope

Допустим у нас есть инвентарь и переменная в нем определена для конкретного хоста:

```ini
web1 ansible_host=172.20.1.100
web2 ansible_host=172.20.1.101 dns_server=10.5.5.4
web3 ansible_host=172.20.1.102
```

И есть playbook, который использует эту переменную:

```yaml
---
- name: Print dns server
  hosts: all
  tasks:
  - debug:
      msg: '{{ dns_server }}'
```

Переменная `dns_server` будет доступна только в play запущенном для хоста web2:

```bash
PLAY [Check /etc/hosts file] ***********************

TASK [debug] ***************************************
ok: [web1] => {
    "dns_server": "VARIABLE IS NOT DEFINED!"
}
ok: [web2] => {
    "dns_server": "10.5.5.4"
}
ok: [web3] => {
    "dns_server": "VARIABLE IS NOT DEFINED!"
}
```

Host Variable может быть определена где угодно - в инвентаре, как часть Group Variable и т.д.

Первым делом при запуске playbook ansible "откидывает" группы и ассоциирует переменные с хостами.

Таким образом, когда запускается playbook, существует только один scope - Host Scope.

## Play Scope

Допустим в playbook задана переменная для одного play и не задана для другого:

```yaml
---
- name: Play1
  hosts: web1
  vars:
    ntp_server: 10.1.1.1
  tasks:
  - debug:
      var: ntp_server

- name: Play2
  hosts: web1
  tasks:
  - debug:
      var: ntp_server
```

Тогда во втором play получим `VARIABLE IS NOT DEFINED!`:

```bash
PLAY [Play1] ********************************************************

TASK [debug] ********************************************************
ok: [web1] => {
    "ntp_server": "10.1.1.1"
}

PLAY [Play2] ********************************************************

TASK [debug] ********************************************************
ok: [web1] => {
    "ntp_server": "VARIABLE IS NOT DEFINED!"
}
```

## Global Scope

Если запустить предыдущий playbook и задать переменную через параметр `--extra-vars`, тогда она будет доступна для всех play:

`ansible-playbook playbook.yml –-extra-vars “ntp_server=10.1.1.1”`

```bash
PLAY [Play1] ********************************************************

TASK [debug] ********************************************************
ok: [web1] => {
    "ntp_server": "10.1.1.1"
}

PLAY [Play2] ********************************************************

TASK [debug] ********************************************************
ok: [web1] => {
    "ntp_server": "10.1.1.1"
}
```

## Use variables to retrieve the results of running commands

Переменная созданная с помощью ключевого слова `register` доступна на уровне Host Scope и может быть вызвана в течение выполнения всего playbook-а:

```yaml
---
- name: Check /etc/hosts file
  hosts: all
  tasks:
  - shell: cat /etc/hosts
    register: result
  - debug:
      var: result.rc

- name: Play2
  hosts: all
  tasks:
  - debug:
      var: result.rc
```

То есть в Play2 переменная тоже будет доступна.

---

Если нужно посмотреть результат работы playbook-а, то необязательно использовать `register` и `debug`. Можно просто выполнить playbook с параметром `-v`:

`ansible-playbook -i inventory playbook.yml -v`