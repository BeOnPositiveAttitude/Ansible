По умолчанию ansible собирает facts с помощью модуля `setup` при запуске любого playbook. Это таска "Gathering Facts", которая всегда идет самой первой.

Результаты сохраняются в переменную `ansible_facts` и мы можем к ней обращаться:

```yaml
---
- name: Print hello message
  hosts: all
  tasks:
  - debug:
      var: ansible_facts
```

Отключить сбор facts можно двумя способами:
- в playbook-е через опцию `gather_facts: False`
- в ansible.cfg через опцию `gathering = explicit`

Для опции `gathering` возможно три значения:
- `explicit` означает НЕ собирать facts по умолчанию
- `implicit` означает собирать facts по умолчанию
- `smart` означает собирать facts по умолчанию, но не собирать заново, если facts уже собраны

Значение опции `gather_facts` в playbook-е имеет более высокий приоритет, чем в файле `ansible.cfg`.

Также возможно задание environment variable `ANSIBLE_GATHERING` непосредственно перед выполнением playbook-а. У нее будет самый высокий приоритет. Однако нужно помнить, что при таком способе значение `ANSIBLE_GATHERING` будет действовать только на один запуск playbook.

`ANSIBLE_GATHERING=explicit ansible-playbook playbook.yml`

Если в выводе команды: `ansible -i inventory all -m setup` мы видим, что некоторые facts отсутствуют, например `ansible_default_ipv4`, значит на опрашиваемом хосте не установлен нужный пакет, в данном примере пакет `iproute`.