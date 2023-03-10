Если не указано явно какой инвентарь использовать, то будет использован инвентарь `/etc/ansible/hosts`.

Пример инвентаря в ini-формате:

```ini
web    ansible_host=server1.company.com   ansible_connection=ssh     ansible_user=root
db     ansible_host=server2.company.com   ansible_connection=winrm   ansible_user=admin
mail   ansible_host=server3.company.com   ansible_connection=ssh     ansible_ssh_pass=Pa$$w0rd
web2   ansible_host=server4.company.com   ansible_connection=winrm

localhost   ansible_connection=localhost

[web_servers]
web
web2

[infra_servers]
db
mail

[all_servers:children]
web_servers
infra_servers
```

Здесь web, db, mail, web2 - alias-ы хостов.

Возможные параметры инвентаря:
- ansible_connection - ssh/winrm/localhost
- ansible_port - 22/5986
- ansible_user - root/administrator
- ansible_ssh_pass - Passw0rd
- ansible_password - для Windows-хостов

Если playbook запускается на ansible-controller, нужно добавить опцию `connection: local` на уровне play.

Еще пример инвентаря:

```ini
[db_servers]
lamp-db ansible_host=172.20.1.101 ansible_ssh_pass=maria ansible_user=maria

[web_servers]
lamp-web ansible_host=172.20.1.100 ansible_ssh_pass=john ansible_user=john
```