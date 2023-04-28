```yaml
- name: Create users
  hosts: localhost
  tasks:
    - user: name='{{ item.name }}' state=present uid='{{ item.uid }}'
      loop:
        - name: joe
          uid: 1010
        - name: george
          uid: 1011
```

Playbook "раскроется" в две таски:

```yaml
- name: Create users
  hosts: localhost
  tasks:
    - var:
        item:
          name: joe
          uid: 1010
      user: name='{{ item.name }}' state=present uid='{{ item.uid }}'
    - var:
        item:
          name: george
          uid: 1011
      user: name='{{ item.name }}' state=present uid='{{ item.uid }}'
```

Ранее вместо `loop` использовалось `with_items`. По сути это одно и то же. Однако у `with_*` есть некоторые преимущества.

Например `with_files`, которое проходит по списку файлов:

```yaml
- name: View Config Files
  hosts: localhost
  tasks:
    - debug: var=item
      with_file:
        - “/etc/hosts”
        - “/etc/resolv.conf”
        - “/etc/ntp.conf”
```

Ключевое слово `with_url` подключается по списку к заданным url:

```yaml
- name: Get from multiple URLs
  hosts: localhost
  tasks:
    - debug: var=item
      with_url:
        - “https://site1.com/get-servers”
        - “https://site2.com/get-servers”
        - “https://site3.com/get-servers”
```

Ключевое слово `with_mongodb` подключается по списку к БД mongodb:

```yaml
- name: Check multiple mongodbs
  hosts: localhost
  tasks:
    - debug: msg=“DB={{ item.database }} PID={{ item.pid}}”
      with_mongodb:
        - database: dev
          connection_string: “mongodb://dev.mongo/”
        - database: prod
          connection_string: “mongodb://prod.mongo/”
```

Существует множество `with_*` директив:
- with_items
- with_file
- with_url
- with_mongodb
- with_dict
- with_etcd
- with_env
- with_filetree
- with_ini
- with_inventory_hostnames
- with_k8s
- with_manifold
- with_nested
- with_nios
- with_openshift
- with_password
- with_pipe
- with_rabbitmq
- with_redis
- with_sequence
- with_skydive
- with_subelements
- with_template
- with_together
- with_varnames

Все что написано после слова 'with' является lookup плагином.

Lookup плагин - это по сути кастомный скрипт, который выполняет определенные задачи, например чтение файла, подключение к url, БД, k8s, openshift.

Пример работы с двумя списками, когда нескольким пользователям нужно дать доступ к нескольким БД:

```yaml
- name: give users access to multiple databases
  community.mysql.mysql_user:
    name: "{{ item[0] }}"
    priv: "{{ item[1] }}.*:ALL"
    append_privs: yes
    password: "foo"
  with_nested:
    - [ 'alice', 'bob' ]
    - [ 'clientdb', 'employeedb', 'providerdb' ]
```