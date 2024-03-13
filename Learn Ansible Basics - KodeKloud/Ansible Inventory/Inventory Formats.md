Для небольших окружений подойдет инвентарь в ini-формате.

Для инфраструктуры корпоративного уровня лучше использовать YAML-формат инвентаря.

Пример инвентаря в формате YAML:

```yaml
all:
  children:
    webservers:
      hosts:
        web1.example.com:
        web2.example.com:
    dbservers:
      hosts:
        db1.example.com:
        db2.example.com:
```