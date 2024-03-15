Можно задать переменные непосредственно в playbook-е:

```yaml
- name: Add DNS server to resolv.conf
  hosts: all
  vars:
    dns_server: 10.1.250.10
  tasks:
    - lineinfile:
        path: /etc/resolv.conf
        line: 'nameserver {{ dns_server }}'
```

Можно задать переменные в отдельном файле, например с именем `variables`:

```yaml
variable1: value1
variable2: value2
```

Другой пример playbook:

```yaml
- name: Set Firewall Configurations
  hosts: web
  tasks:
    - firewalld:
        service: https
        permanent: true
        state: enabled

    - firewalld:
        port: '{{ http_port }}'/tcp
        permanent: true
        state: disabled

    - firewalld:
        port: '{{ snmp_port }}'/udp
        permanent: true
        state: disabled

    - firewalld:
        source: '{{ inter_ip_range }}'/24
        zone: internal
        state: enabled
```

Мы можем вынести соответствующие переменные в файл инвентаря:

```ini
web   http_port=8081   snmp_port=161-162   inter_ip_range=192.0.2.0
```

Либо создать `host_vars` файл `web.yml`, т.к. `web` в данном случае это alias хоста, а не группы:

```yaml
http_port: 8081
snmp_port: 161-162
inter_ip_range: 192.0.2.0
```

Если переменная стоит первой в выражении, тогда её НУЖНО заключать в кавычки: `source: '{{ inter_ip_range }}'`.

Если переменная стоит в середине выражения, тогда её НЕ нужно заключать в кавычки: `source: SomeThing{{ inter_ip_range }}SomeThing`.