Важно помнить, что ssh-ключ ассоциируется с определенным пользователем, для которого он выпущен. Этого пользователя видно в файле `~/.ssh/authorized_keys` на удаленном хосте после содержимого публичного ключа. Поэтому важно использовать корректного пользователя для подключения к удаленному серверу.

Скопировать публичный ключ на удаленный сервер: `ssh-copy-id -i id_rsa user1@server1`.

По умолчанию ansible использует пользователя root для подключения к управляемым хостам.

По умолчанию ansible ищет ssh-ключи для подключения в каталоге `~/.ssh`. Если нужно указать другой каталог с ключами, то в инвентаре используем параметр `ansible_ssh_private_key_file`:

```ini
web1    ansible_host=server1.company.com   ansible_user=user1   ansible_ssh_private_key_file=/some-path/private-key
web2    ansible_host=server2.company.com   ansible_user=user1   ansible_ssh_private_key_file=/some-path/private-key
```