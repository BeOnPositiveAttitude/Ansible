### Based on facts

```yaml
- name: Install Nginx on Ubuntu 18.04
  apt:
    name: nginx=1.18.0
    state: present
  when: ansible_facts['os_family'] == 'Debian' and ansible_facts['distribution_major_version'] == '18'
```

### Based on variables

```yaml
- name: Deploy configuration files
  template:
    src: "{{ app_env }}_config.j2"
    dest: "/etc/myapp/config.conf"
  vars:
    app_env: production
```

### Based on re-use

```yaml
- name: Install required packages
  apt:
    name:
      - package1
      - package2
    state: present

- name: Create necessary directories and set permissions

- name: Start web application service
  service:
    name: myapp
    state: started
  when: environment == 'production'
```