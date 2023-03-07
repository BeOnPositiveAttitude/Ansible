Playbook сочетающий loop и conditionals:

```yaml
---
- name: Install Softwares
  hosts: all
  vars:
    packages:
      - name: nginx
        required: True
      - name: mysql
        required : True
      - name: apache
        required : False
  tasks:
    - name: Install {{ item.name }} on Debian
      apt:
        name: "{{ item.name }}"
        state: present
      loop: "{{ packages }}"
      when: item.required == True
```

Принцип работы loop. Playbook выше по сути "раскроется" в три таски:

```yaml
- name: Install “{{ item.name }}” on Debian
  vars:
    item:
      name: nginx
      required: True
  apt:
    name: “{{ item.name }}”
    state: present
  when: item.required == True
```

```yaml
- name: Install “{{ item.name }}” on Debian
  vars:
    item:
      name: mysql
      required: True
  apt:
    name: “{{ item.name }}”
    state: present
  when: item.required == True
```

```yaml
- name: Install “{{ item.name }}” on Debian
  vars:
    item:
      name: apache
      required: False
  apt:
    name: “{{ item.name }}”
    state: present
  when: item.required == True
```

Пример playbook - посылать нотификацию по почте, если сервис httpd не запущен:

```yaml
- name: Check status of a service and email if its down
  hosts: localhost
  tasks:
    - command: service httpd status
      register: result

    - mail:
        to: admin@company.com
        subject: Service Alert
        body: Httpd Service is down
      when: result.stdout.find('down') != -1
```

Если в `result.stdout` не будет найдено слова 'down', тогда вернется код ответа '-1'. Соответственно мы отсылаем нотификацию, если код ответа не равен '-1', что означает наличие слова 'down' в `result.stdout`.