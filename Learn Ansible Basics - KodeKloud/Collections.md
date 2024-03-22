Установить коллекцию локально: `ansible-galaxy collection install network.cisco`.

Пример использования в playbook-е.

Сначала устанавливаем коллекцию: `ansible-galaxy collection install amazon.aws`.

```yaml
- hosts: localhost
  collections:
    - amazon.aws

  tasks:
    - name: Create an S3 bucket
      aws_s3_bucket:
        name: my-bucket
        region: us-west-1
```

Можно указать необходимые коллекции в файле `requirements.yml`:

```yaml
---
collections:
  - name: amazon.aws
    version: "1.5.0"
  - name: community.mysql
    src: https://github.com/ansible-collections/community.mysql
    version: "1.2.1"
```

И затем установить их командой: `ansible-galaxy collection install -r requirements.yml`.

Пример использования кастомной (несуществующей) коллекции в playbook-е из лабы:

```yaml
---
- hosts: localhost
  tasks:
    - name: Install the networking_tools collection
      ansible.builtin.ansible_galaxy_collection:
        name: company_xyz.networking_tools
        source: https://galaxy.ansible.com

- hosts: switches
  collections:
    - company_xyz.networking_tools
  tasks:
    - name: Configure VLAN 10
      configure_vlan:
        vlan_id: 10
        vlan_name: Admin_VLAN
```

Здесь в первом play мы ставим коллекцию себе на localhost с помощью модуля `ansible_galaxy_collection`.

Во втором play мы указываем использование этой коллекции и с помощью модуля `configure_vlan` создаем необходимый VLAN.

### AWX

Чтобы добавить какой-либо community модуль в наш playbook, необходимо в директории с проектом создать структуру `collections/requirements.yml` и в файле указать следующее:

```yaml
collections:
  - name: community.general
    source: https://galaxy.ansible.com
```

В процесс синхронизации проекта в AWX указанная коллекция будет установлена.

Важно, чтобы в настройках AWX была включена возможность загрузки коллекций.