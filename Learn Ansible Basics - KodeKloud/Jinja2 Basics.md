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