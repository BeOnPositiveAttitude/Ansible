Не нужно заключать переменную в фигурные скобки `{{ some_variable }}`, если она используется там, где ansible и так ожидает увидеть переменную. Например:

```yaml
- name: Print DNS Server
  hosts: all
  tasks:
  - debug:
      msg: "{{ dns_server_ip }}"
      var: dns_server_ip
    when: ansible_host != 'web'
    with_items: "{{ db_servers }}"
```

Если переменная стоит первой в выражении, тогда нужно заключать ее в ковычки: `msg: "{{ dns_server_ip }}"`.

Если переменная стоит в середине выражения, тогда НЕ нужно заключать ее в ковычки: `msg: The DNS server is {{ dns_server_ip }}`.

В инвентаре можно использовать переменную `ansible_ssh_pass` наравне с переменной `ansible_password`. Но переменная `ansible_ssh_pass` считается уже устаревшим вариантом, а `ansible_password` более современный вариант.