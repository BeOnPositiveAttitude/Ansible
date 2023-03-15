Вернемся к примеру из предыдущего урока. Допустим у нас есть инвентарь и переменная в нем определена для конкретного хоста:

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

Когда ansible запускает playbook, он создает три sub-processes, по одному для каждого хоста. Перед тем как выполнить таски на каждом хосте, ansible запускает variable interpolation stage, в ходе которой собираются переменные из всех доступных источников и переменные ассоциируются с определенными хостами.

Переменная `dns_server` будет доступна только в play запущенном для хоста web2 и недоступна для хостов web1 и web3:

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

Каким образом в данном случае переменную `dns_server` можно сделать доступной для web1 и web3? Каким образом один ansible sub-process, запустивший таску на одном хосте, может получить доступ к переменной определенной на другом хосте? В этом на поможет magic variable `hostvars`:

```yaml
---
- name: Print dns server
  hosts: all
  tasks:
  - debug:
      msg: '{{ hostvars['web2'].dns_server }}'
```

В итоге получим:

```bash
PLAY [Check /etc/hosts file] ***********************

TASK [debug] ***************************************
ok: [web1] => {
    "dns_server": "10.5.5.4"
}
ok: [web2] => {
    "dns_server": "10.5.5.4"
}
ok: [web3] => {
    "dns_server": "10.5.5.4"
}
```

Чтобы получить ip-адрес или hostname: `msg: {{ hostvars['web2'].ansible_host }}`.

Также можно получить доступ к facts другого хоста, если включен сбор facts:

`msg: {{ hostvars['web2'].ansible_facts.processor }}`.

Это же выражение может быть записано в другом формате:

`msg: {{ hostvars['web2']['ansible_facts']['processor'] }}`.

Другая magic variable называется `groups`. Она покажет какие хосты входят в состав этой группы.

```ini
web1 ansible_host=172.20.1.100
web2 ansible_host=172.20.1.101
web3 ansible_host=172.20.1.102

[web_servers]
web1
web2
web3

[americas]
web1
web2

[asia]
web3
```

Выражение: `msg: {{ groups['americas'] }}` вернет:

```ini
web1
web2
```

Другая magic variable `group_names` покажет к каким группам относится хост. Выражение: `msg: {{ group_names }}` для хоста web1 вернет:

```ini
web_servers
americas
```

Magic variable `inventory_hostname` покажет имя хоста из файла инвентаря. Выражение: `msg: {{ inventory_hostname }}` для хоста web1 вернет - `web1`. Это будет НЕ hostname или FQDN хоста.

