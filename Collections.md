Чтобы добавить какой-либо community модуль в наш playbook, необходимо в директории с проектом создать структуру `collections/requirements.yml` и в файле указать следующее:

```yaml
collections:
  - name: community.general
    source: https://galaxy.ansible.com
```

В процесс синхронизации проекта в AWX указанная коллекция будет установлена.

Важно, чтобы в настройках AWX была включена возможность загрузки коллекций.