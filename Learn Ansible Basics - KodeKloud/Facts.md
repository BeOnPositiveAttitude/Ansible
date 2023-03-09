По умолчанию ansible собирает facts с помощью модуля setup при запуске любого playbook. Это таска "Gathering Facts", которая всегда идет самой первой.

Результаты сохраняются в переменную `ansible_facts` и мы можем к ней обращатсья:

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
- `explicit` означает не собирать facts по умолчанию
- `implicit` означает собирать facts по умолчанию
- `smart` означает собирать facts по умолчанию, но не собирать заново, если facts уже собраны

Значение опции `gather_facts` в playbook-е имеет более высокий приоритет, чем в файле ansible.cfg.