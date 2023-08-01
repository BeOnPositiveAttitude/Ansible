Можно поставить ansible из репозитория, но это может быть не самая свежая версия:

```bash
sudo yum install epel-release   #перед установкой на RHEL/CentOS нужно поставить этот пакет
sudo yum install ansible
sudo dnf install ansible
sudo apt-get install ansible
```

Либо поставить с помощью pip последнюю стабильную версию: `sudo pip install ansible`.

Дефолтные файлы `/etc/ansible/ansible.cfg` и `/etc/ansible/hosts` создаются только если ansible был установлен с помощью пакетного менеджера (yum, dnf, apt). Если ansible установлен с помощью pip эти файлы НЕ создаются.

Установить ansible определенной версии с помощью pip3 для всех пользователей в системе: `sudo pip3 install ansible==4.10.0`.