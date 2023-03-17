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