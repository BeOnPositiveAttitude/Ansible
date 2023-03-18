## Модуль для управления firewall

В данном playbook-е правило будет применено сразу же, но до первой перезагрузки:

```yaml
---
- name: Add Firewalld rule
  hosts: all
  tasks:
    - firewalld:
        port: 8080/tcp
        service: http
        source: 192.0.0.0/24
        zone: public
        state: enabled
```

Чтобы сделать правило постоянным, нужно добавить опцию `permanent`, но в этом случае правило будет применено не сразу, а толко после перезагрузки:

```yaml
---
- name: Add Firewalld rule
  hosts: all
  tasks:
    - firewalld:
        port: 8080/tcp
        service: http
        source: 192.0.0.0/24
        zone: public
        state: enabled
        permanent: yes
```

Чтобы сделать правило постоянным и оно сразу же применилось, нужно добавить опцию `immediate`:

```yaml
---
- name: Add Firewalld rule
  hosts: all
  tasks:
    - firewalld:
        port: 8080/tcp
        service: http
        source: 192.0.0.0/24
        zone: public
        state: enabled
        permanent: yes
        immediate: yes
```

По умолчанию значение опции `immediate: yes`. Но если задана опция `permanent: yes`, тогда значение опции `immediate: no`.

## Модули для управления LVM

```yaml
---
- hosts: all
  tasks:
  - name: Create LVM Volume Group
    lvg:
      vg: vg1
      pvs: /dev/sdb1,/dev/sdb2

  - name: Create LVM Volume
    lvol:
      vg: vg1
      lv: lvol1
      size: 2g
```

## Модули для управления ФС и монтированием:

```yaml
---
- hosts: all
  tasks:
  - name: Create Filesystem
    filesystem:
      fstype: ext4
      dev: /dev/vg1/lvol1
      opts: -cc

  - name: Mount Filesystem
    mount:
      fstype: ext4
      src: /dev/vg1/lvol1
      path: /opt/app
      state: mounted
```

## Модули для управления архивами

```yaml
---
- hosts: all
  tasks:
  - name: Compress a folder
    archive:
      path: /opt/app/web
      dest: /tmp/web.gz
      format: zip|tar|bz2|xz|gz

  - name: Uncompress a folder
    unarchive:
      src: /tmp/web.gz
      dest: /opt/app/web
      remote_src: yes
```

```yaml
---
- name: Download and extract from URL
  hosts: web1
  tasks:
  -   unarchive:
       src: https://github.com/kodekloudhub/Hello-World/archive/master.zip
       dest: /root
       remote_src: yes
```

По умолчанию модуль `unarchive` берет архив `src` с ansible-controller и разархивирует на управляемые хосты `dest`. Если архив уже находится на управляемых хостах, тогда нужно использовать опцию `remote_src: yes`.

## Модуль cron

Если не указать какой-либо параметр, тогда его значение будет по умолчанию равно *.

```yaml
---
- hosts: all
  tasks:
  - name: Create a scheduled task
    cron:
      name: Run daily health report
      job: sh /opt/scripts/health.sh
      month: 2
      day: 19
      hour: 8
      minute: 10
      weekday: 1   #1=Monday, 0=Sunday, 6=Saturday
```

Удалить job:

```yaml
---
- name: remove cron job from node00
  hosts: node00
  tasks:
  - name: Remove cron job
    cron:
      name: "Check Memory"
      state: absent
```

Выполнять job после каждой перезагрузки хоста:

```yaml
---
- name: Cleanup /tmp after every reboot
  hosts: node00
  tasks:
   - cron:
      name: cleanup
      job: rm -rf /tmp/*
      special_time: reboot
```

Создать отдельный файл `/etc/cron.d/ansible_yum` вместо прямого добавления в crontab и добавить job именно для пользователь root:

```yaml
---
- name: Create cron for yum
  hosts: node00
  tasks:
    - name: Creates a cron
      cron:
        name: yum update
        weekday: 0
        minute: 5
        hour: 8
        user: root
        job: "yum -y update"
        cron_file: ansible_yum
```

## Модуль для управления публичными ssh-ключами

```yaml
---
- hosts: all
  tasks:
  - name: Configure ssh keys
    authorized_keys:
      user: maria
      state: present
      key: |
      ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAA
BAQC4WKn4K2G3iWg9HdCGo34gh+……root@97a1b9c3a
```

```yaml
---
- hosts: web1
  tasks:
    - name: Find files
      find:
        paths: /opt/data
        age: 2m
        size: 1m
        recurse: yes
      register: file

    - name: Copy files
      command: "cp {{ item.path }} /opt"  #берет значение file.files.path (путь до файла) и копирует этот файл в /opt
      with_items: "{{ file.files }}"      #проходит по свойству files переменной file
```

Пример использования модуля `blockinfile`:

```yaml
---
- name: Add block to index.html
  hosts: web1
  tasks:
   - blockinfile:
      owner: apache
      group: apache
      insertbefore: BOF
      path: /var/www/html/index.html
      block: |
       Welcome to KodeKloud!
       This is Ansible Lab.
```

Пример создания пользователей и паролей для них:

```yaml
- hosts: node00
  vars:
    developer_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          61663531396134346165353361316630373233666233646466323262623037363536353436653632
          6234353332646638633934323037346566656362306434350a346432333739363836366165643931
          31366436306535343965656633636163363630623065366132373861663239336661373030646666
          6562393966396535660a363336666330396531323836323434633239643865323162306630326366
          3237
    admin_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          39313839363262626132343932636438356638643564333432623262643435363936656265633634
          6262613535353862653766636334303632323537633339380a643331366363633538656134306161
          33616466383961323231613837623034643433383466653036643663326666366439643862396662
          6661656135346335350a653438313334656333636137376135613062343838326663623636363166
          6138
  tasks:
  - include_vars: data/users.yml
  - user:
      name: "{{ item }}"
      groups: wheel
      append: true
      password: "{{ admin_pass | string | password_hash('sha512') }}"
    loop: "{{ admins }}"
  - user:
      name: "{{ item }}"
      home: /var/www/
      password: "{{ developer_pass | string | password_hash('sha512') }}"
    loop: "{{ developers }}"
```