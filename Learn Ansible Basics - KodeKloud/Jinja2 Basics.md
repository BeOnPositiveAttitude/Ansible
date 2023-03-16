Предположим у нас есть переменная `my_name: Bond`. Примеры Jinja2 filters:

```
The name is {{ my_name }} => The name is Bond
The name is {{ my_name | upper }} => The name is BOND
The name is {{ my_name | lower }} => The name is bond
The name is {{ my_name | title }} => The name is Bond
The name is {{ my_name | replace(“Bond”, “Bourne”) }} => The name is Bourne
```

Jinja2 filters в данном случае это:
- upper
- lower
- title
- replace

По умолчанию, если для переменной не задано какого-либо значения, то Jinja2 engine выдаст ошибку о том, что переменная не определена. Если мы хотим избежать этого и использовать дефолтное значение для переменной, если это значение не определено явно:

`The name is {{ first_name | default(“James”) }} {{ my_name }} => The name is James Bond`.

То есть, если не задано значение для переменной `first_name`, тогда по умолчанию будет использовано "James". И уже далее идет значение переменной `my_name`. Если же для переменной `first_name` будет явно задано какое-либо значение, тогда будет использовано именно это значение.

```
{{ [1,2,3] | min }}                              => 1
{{ [1,2,3] | max }}                              => 3
{{ [1,2,3,2] | unique }}                         => 1,2,3
{{ [1,2,3,4] | union([4,5]) }}                   => 1,2,3,4,5 #объединить уникальные значения
{{ [1,2,3,4] | intersect([4,5]) }}               => 4 #пересечение, одинаковые значения
{{ 100 | random }}                               => Random number 1-100
{{ [“The”, “name”, “is”, “Bond”] | join(" ") }}  => The name is Bond
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
