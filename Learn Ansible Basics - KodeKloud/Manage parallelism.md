Что такое стратегия в ansible? Стратегия определяет каким образом playbook выполняется в ansible.

Рассмотрим playbook из предыдущего урока:

```yaml
- name: Deploy web application
  hosts: server1
  tasks:
    - name: Install dependencies
        << code hidden >>
    - name: Install MySQL Database
        << code hidden >>
    - name: Start MySQL Service
        << code hidden >>
    - name: Install Python Flask Dependencies
        << code hidden >>
    - name: Run web-server
        << code hidden >>
```

В нем мы разворачиваем веб-приложение в режиме "all-in-one", это значит что сервер БД и веб-сервер устанавливаются на один и тот же хост. Ansible последовательно выполняет все таски на этом хосте.

Рассмотрим вариант установки этого же playbook на трех хостах `hosts: server1,server2,server3`. Наша цель развернуть три веб-приложения на трех хостах в режиме "all-in-one". Когда ansible запускает playbook, то он выполняет каждую таску на всех хостах параллельно (одновременно). Он ждет, когда таска завершится на всех хостах, прежде чем приступить к выполнению следующей. Таким образом, если один хост работает медленнее остальных, то он замедляет выполнение всего playbook-а. Это называется линейной (linear) стратегией и она является дефолтной.

Существует другая статегия, которая называется "free". При этой стратегии таски запускаются на каждом хосте независимо друг от друга и не происходит ожидания завершения одной таски на всех хостах, чтобы перейти к выполнению следующей. Чтобы изменить стратегию, нужно использовать опцию `strategy`:

```yaml
- name: Deploy web application
  hosts: server1,server2,server3
  strategy: free
  tasks:
    - name: Install dependencies
        << code hidden >>
    - name: Install MySQL Database
        << code hidden >>
    - name: Start MySQL Service
        << code hidden >>
    - name: Install Python Flask Dependencies
        << code hidden >>
    - name: Run web-server
        << code hidden >>
```

Существует еще третья опция. Допустим у нас есть пять хостов, но мы хотим, чтобы ansible запускал таски только на трех хостах за один раз. Здесь нам поможет batch processing. Это не отдельная стратегия. Она основана на линейной стратегии, но здесь мы можем контролировать количество хостов в batch. В этом случае в playbook не указывается стратегия, т.к. по умолчанию используется linear. Но используется опция `serial`, в которой мы можем задать количество хостов, на которых одновременно будут запускаться таски:

```yaml
- name: Deploy web application
  hosts: server1,server2,server3,server4,server5
  serial: 3
  tasks:
    - name: Install dependencies
        << code hidden >>
    - name: Install MySQL Database
        << code hidden >>
    - name: Start MySQL Service
        << code hidden >>
    - name: Install Python Flask Dependencies
        << code hidden >>
    - name: Run web-server
        << code hidden >>
```

В этом случае сначала все таски выполняются на первых трех хостах и далее ansible запускает таски на следующем batch (два оставшихся хоста).

Также возможно создание своей кастомной стратегии путем разработки своего strategy plugin.

С каким максимальным количеством хостов ansible может одновременно "общаться"? Предположим, что в предыдущем примере у нас 100 хостов и мы хотим запустить playbook одновременно на всей сотне. Запустит ли ansible этот playbook сразу на всей сотне хостов по умолчанию? Нет, если не задать это явно. Ansible использует параллельные процессы или fork-и для взаимодействия с удаленными хостами. По умолчанию ansible создает 5 fork-ов одновременно. Это настраивается в файле ansible.cfg: `forks = 5`. Это значение можно увеличить, если на ansible controller достаточно ресурсов CPU и пропускной способности сети.