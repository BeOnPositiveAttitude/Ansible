```yaml
---
- name: Install NGINX
  hosts: all
  tasks:
  - name: Install NGINX on Debian
    apt:
      name: nginx
      state: present
    when: ansible_os_family == "Debian" and ansible_distribution_version == "16.04"

  - name: Install NGINX on RedHat
    yum:
      name: nginx
      state: present
    when: ansible_os_family == "RedHat" or ansible_os_family == "SUSE"
```

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
- name: Install "{{ item.name }}" on Debian
  vars:
    item:
      name: nginx
      required: True
  apt:
    name: "{{ item.name }}"
    state: present
  when: item.required == True
```

```yaml
- name: Install "{{ item.name }}" on Debian
  vars:
    item:
      name: mysql
      required: True
  apt:
    name: "{{ item.name }}"
    state: present
  when: item.required == True
```

```yaml
- name: Install "{{ item.name }}" on Debian
  vars:
    item:
      name: apache
      required: False
  apt:
    name: "{{ item.name }}"
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

Когда в playbook-е определена переменная, то при обращении к ней в блоке when, не нужно заключать ее в фигурные скобки:

```yaml
---
- name: 'Am I a Banana or not?'
  hosts: localhost
  connection: local
  vars:
    fruit: banana
  tasks:
    - command: 'echo "I am a Banana"'
      when: fruit == "banana"
    - command: 'echo "I am not a Banana"'
      when: fruit != "banana"
```

Два варианта решения одной и той же задачи. Если git не установлен на хосте, тогда записать в файл `/tmp/is_git_installed.txt` сообщение 'Oops, git is missing'. Если git установлен на хосте, тогда записать в файл `/tmp/is_git_installed.txt` версия git.

```yaml
- hosts: web2
  gather_facts: false
  tasks:
    - shell: dpkg -l git   #dpkg -l выводит список всех установленных в системе пакетов, dpkg -l git проверяет есть ли среди них git
      register: check_if_git_installed
      ignore_errors: true

    - debug: var=check_if_git_installed
    - shell: echo 'Oops, git is missing' > /tmp/is_git_installed.txt
      when: check_if_git_installed.rc != 0

    - shell: git --version > /tmp/is_git_installed.txt
      when: check_if_git_installed.rc == 0
```

```yaml
- hosts: web2
  tasks:
  - name: Gather the package facts
    package_facts:   #модуль собирает данные об установленных в системе пакетах и сохраняет их в виде facts
      manager: apt

  - name: Git missing message
    shell: echo 'Oops, git is missing' > /tmp/is_git_installed.txt
    when: "'git' not in ansible_facts.packages"

  - name: Git version message
    shell: git --version > /tmp/is_git_installed.txt
    when: "'git' in ansible_facts.packages"
```

```yaml
- name: copy script if not present
  gather_facts: yes
  hosts: web2
  vars:
    remote_dest: /usr/local/bin/report_status.sh
  tasks:
    - copy:
        src: report_status.sh
        dest: "{{ remote_dest }}"
      when: copy_file_only_if is defined and copy_file_only_if|bool   #явно задать тип переменной - boolean
```

Выражение `copy_file_only_if|bool` является сокращенной версией `copy_file_only_if|bool == true`.