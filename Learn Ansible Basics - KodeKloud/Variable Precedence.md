Переменная заданная для конкретного хоста (Host Vars) имеет более высокий приоритет, чем аналогичная переменная заданная для группы хостов (Group Vars):

```ini
web1   ansible_host=172.20.1.100
web2   ansible_host=172.20.1.101   dns_server=10.5.5.4
web3   ansible_host=172.20.1.102

[web_servers]
web1
web2
web3

[web_servers:vars]
dns_server=10.5.5.3
```

Если аналогичная переменная задана в playbook-е на уровне play, она имеет более высокий приоритет, чем Host Vars и Group Vars:

```yaml
- name: Configure DNS Server
  hosts: all
  vars:
    dns_server: 10.5.5.5
  tasks:
  - nsupdate:
      server: '{{ dns_server }}'
```

И самый высокий приоритет имеет переменная Extra Vars заданная непосредственно при запуске playbook:

`ansible-playbook playbook.yml –-extra-vars "dns_server=10.5.5.6"`

Ссылка на [документацию](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable).

В доке приоритет переменных указан от наименьшего к наибольшему.