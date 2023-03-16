Block позволяет логически сгруппировать наборы тасок.

Предположим у нас есть playbook:

```yaml
- hosts: server1
  tasks:
  - name: Install MySQL
    yum: name=mysql-server state=present
    become_user: db-user
    when: ansible_facts['distribution'] == 'CentOS'

  - name: Start MySQL Service
    service: name=mysql-server state=started
    become_user: db-user
    when: ansible_facts['distribution'] == 'CentOS'

  - name: Install Nginx
    yum: name=nginx state=present
    become_user: web-user
    when: ansible_facts['distribution'] == 'CentOS'

  - name: Start Nginx Service
    service: name=nginx state=started
    become_user: web-user
    when: ansible_facts['distribution'] == 'CentOS'
```

С помощью Block мы можем избежать повторения кода и сгруппировать таски:

```yaml
- hosts: server1
  tasks:
    - block:
        - name: Install MySQL
          yum: name=mysql-server state=present
        - name: Start MySQL Service
          service: name=mysql-server state=started
      become_user: db-user
      when: ansible_facts['distribution'] == 'CentOS'

    - block:
        - name: Install Nginx
          yum: name=nginx state=present
        - name: Start Nginx Service
          service: name=nginx state=started
      become_user: web-user
      when: ansible_facts['distribution'] == 'CentOS'
```

Также Block помогает нам лучше обрабатывать ошибки с помощью Rescue и Always. Используйте Rescue для определения tasks, которые запустятся, если tasks определенные в Block провалятся. Используйте Always для определения tasks, которые запустятся независимо от успеха или провала tasks из Block и Rescue, они будут выполнены в любом случае.

```yaml
- hosts: server1
  tasks:
    - block:
        - name: Install MySQL
          yum: name=mysql-server state=present
        - name: Start MySQL Service
          service: name=mysql-server state=started
      become_user: db-user
      when: ansible_facts['distribution'] == 'CentOS'
      rescue:
        - mail:
            to: admin@company.com
            subject: Installation Failed
            body: DB Install Failed at {{ ansible_failed_task.name }}   #fact
      always:
        - mail:
            to: admin@company.com
            subject: Installation Status
            body: DB Install Status - {{ ansible_failed_result }}   #fact
```