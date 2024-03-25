Предположим у нас есть переменная `my_name: Bond`. Примеры Jinja2 filters:

```
The name is {{ my_name }} => The name is Bond
The name is {{ my_name | upper }} => The name is BOND
The name is {{ my_name | lower }} => The name is bond
The name is {{ my_name | title }} => The name is Bond
The name is {{ my_name | replace("Bond", "Bourne") }} => The name is Bourne
```

Jinja2 filters в данном случае это:
- upper
- lower
- title
- replace

По умолчанию, если для переменной не задано какого-либо значения, то Jinja2 engine выдаст ошибку о том, что переменная не определена. Если мы хотим избежать этого и использовать дефолтное значение для переменной, если это значение не определено явно:

`The name is {{ first_name | default("James") }} {{ my_name }} => The name is James Bond`.

То есть, если не задано значение для переменной `first_name`, тогда по умолчанию будет использовано "James". И уже далее идет значение переменной `my_name`. Если же для переменной `first_name` будет явно задано какое-либо значение, тогда будет использовано именно это значение.

```
{{ [1,2,3] | min }}                              => 1
{{ [1,2,3] | max }}                              => 3
{{ [1,2,3,2] | unique }}                         => 1,2,3
{{ [1,2,3,4] | union([4,5]) }}                   => 1,2,3,4,5 #объединить уникальные значения
{{ [1,2,3,4] | intersect([4,5]) }}               => 4 #пересечение, одинаковые значения
{{ 100 | random }}                               => Random number 1-100
{{ ["The", "name", "is", "Bond"] | join(" ") }}  => The name is Bond
```

### Loops

Вывести все цифры из списка:

```
{% for number in [0,1,2,3,4] %}
{{ number }}
{% endfor %}
```

### Задача из лабы на циклы.

We need to update the `agents.conf` file with a list of servers in the environment and their details.

The list of servers is given in the `inventory` file under group `lamp_app`. This information must be used to generate an `agents.conf` file at `/etc/agents.conf` location on the `monitoring_server` i.e `node01`.

Файл инвентаря:

```ini
monitoring_server ansible_host=node01 ansible_ssh_pass=caleston123 ansible_ssh_common_args='-o StrictHostKeyChecking=no'

[lamp_app]
web0001 ansible_hostname=web0001.company.com ansible_host=10.1.1.101 monitor_port=8080 protocol=http
web0002 ansible_hostname=web0002.company.com ansible_host=10.1.1.102 monitor_port=8080 protocol=http
db0003 ansible_hostname=db0003.company.com ansible_host=10.1.1.103 monitor_port=3306 protocol=tcp
db0004 ansible_hostname=db0004.company.com ansible_host=10.1.1.104 monitor_port=3306 protocol=tcp
db0005 ansible_hostname=db0005.company.com ansible_host=10.1.1.105 monitor_port=3306 protocol=tcp
```

Т.к. таска в плейбуке запускается только на хосте `monitoring_server`, то чтобы получить доступ к переменным группы хостов `lamp_app` мы используем `hostvars`. Вместо `node` можно придумать любое название переменной. Итоговый jinja2 template:

```
hostname, ipaddress, monitor_port, type, protocol
{% for node in groups['lamp_app'] %}
{{ node }}, {{ hostvars[node]['ansible_host'] }}, {{ hostvars[node]['monitor_port'] }}, {{ hostvars[node]['protocol'] }}
{% endfor %}
```

Или в таком формате:

```
hostname, ipaddress, monitor_port, type, protocol
{% for node in groups['lamp_app'] %}
{{ node }}, {{ hostvars[node].ansible_host}}, {{ hostvars[node].monitor_port }}, {{ hostvars[node].protocol }}
{% endfor %}
```

Playbook:

```yaml
---
- hosts: monitoring_server
  become: yes
  tasks:
    - template:
        src: agents.conf.j2
        dest: /etc/agents.conf
```

### Conditionals

Выведет только цифру 2:

```
{% for number in [0,1,2,3,4] %}
    {% if number == 2 %}
        {{ number }}
    {% endif %}
{% endfor %}
```

### Пример 1

Чтобы преобразовать:

```
{
  "names": [
    "Alpha",
    "Beta",
    "Charlie",
    "Delta",
    "Echo"
  ]
}
```

В такой формат:

```
Alpha
Beta
Charlie
Delta
Echo
```

Используем выражение:

```
{% for name in names -%}   #символ тире убирает пустые строки в итоговом выводе
{{ name }}
{% endfor %}
```

### Пример 2

Чтобы преобразовать:

```
{
  "name_servers": [
    "10.1.1.5",
    "10.1.1.6",
    "10.1.1.8",
    "10.8.8.1",
    "8.8.8.8"
  ]
}
```

В такой формат:

```
nameserver 10.1.1.5
nameserver 10.1.1.6
nameserver 10.1.1.8
nameserver 10.8.8.1
nameserver 8.8.8.8
```

Используем выражение:

```
{% for name_server in name_servers -%}
nameserver {{ name_server }}
{% endfor %}
```

### Пример 3

Чтобы преобразовать:

```
{
  "hosts": [
    {
      "name": "web1",
      "ip_address": "192.168.5.4"
    },
    {
      "name": "web2",
      "ip_address": "192.168.5.5"
    },
    {
      "name": "web3",
      "ip_address": "192.168.5.8"
    },
    {
      "name": "db1",
      "ip_address": "192.168.5.9"
    }
  ]
}
```

В такой формат:

```
web1 192.168.5.4
web2 192.168.5.5
web3 192.168.5.8
db1 192.168.5.9
```

Используем выражение:

```
{% for host in hosts -%}
{{ host.name }} {{ host.ip_address }}
{% endfor %}
```

### Пример 4

Чтобы преобразовать:

```
{
  "hosts": [
    {
      "name": "web1",
      "ip_address": "192.168.5.4"
    },
    {
      "name": "web2",
      "ip_address": "192.168.5.5"
    },
    {
      "name": "web3",
      "ip_address": "192.168.5.8"
    },
    {
      "name": "db1",
      "ip_address": "192.168.5.9"
    }
  ]
}
```

В такой формат:

```
web1 192.168.5.4
web2 192.168.5.5
web3 192.168.5.8
```

Используем выражение:

```
{% for host in hosts -%}
  {% if "web" in host.name -%}
{{ host.name }} {{ host.ip_address -}}
  {% endif %}
{% endfor %}
```