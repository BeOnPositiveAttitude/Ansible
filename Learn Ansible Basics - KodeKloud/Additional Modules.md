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

По умолчанию модуль `unarchive` берет архив `src` с ansible-controller и разархивирует на управляемые хосты `dest`. Если архив уже находится на управляемых хостах, тогда нужно использовать опцию `remote_src: yes`.

## Модуль cron

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