В терминах YAML playbook представляет собой "a list of dictionaries".

В словаре как мы знаем порядок элементов не имеет значения.

Пример:

```yaml
- hosts: localhost
  name: Play1
  tasks:
    - name: Execute command 'date'
      command: date

    - name: Execute script on server
      script: test_script.sh

- name: Play2
  hosts: localhost
  tasks:
    - name: Install web service
      yum:
        name: httpd
        state: present

    - name: Start web server
      service:
        name: httpd
        state: started
```

`tasks` в свою очередь представляет собой чистый список. Это как известно "ordered collection".

Смотреть встроенную доку по модулям: `ansible-doc -l`.