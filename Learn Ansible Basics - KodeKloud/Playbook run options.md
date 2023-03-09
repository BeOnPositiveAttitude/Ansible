Опция `--check` запустит playbook в режиме dry-run.

Команда: `ansible-playbook playbook.yml --start-at-task "Start httpd service"` запустит playbook с определенной таски, при этом будут пропущены все предыдущие таски.

Можно поставить опцию `tags: install` на опеределенные таски и запускать (или пропускать) только их:

`ansible-playbook playbook.yml --tags "install"`

`ansible-playbook playbook.yml --skip-tags "install"`

