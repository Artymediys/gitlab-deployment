# Ansible-роль для развёртывания GitLab

**Роль** предназначена для развёртывания **GitLab** на ОС **AltLinux 10**.  
Роль включает в себя:
- Установка и настройка **GitLab** и **Redis** в **Docker**-контейнерах
- Установка и настройка **PostgreSQL** на отдельном хосте
- Настройка **GitLab**-конфига для взаимодействия с внешними **MinIO** и **LDAP**
- Конфигурация внешнего **Nginx**, для корректного взаимодействия с **GitLab**

## Структура проекта

```markdown
gitlab-deployment/
├── roles/
│ ├── gitlab/
│ │ ├── files/
│ │ │ ├── ssl.crt
│ │ │ ├── ssl.key
│ │ ├── tasks/
│ │ │ ├── docker_installation.yml
│ │ │ ├── gitlab_configuration.yml
│ │ │ ├── launch.yml
│ │ │ ├── main.yml
│ │ │ ├── nginx_configuration.yml
│ │ │ ├── postgres_installation.yml
│ │ │ ├── user.yml
│ │ ├── templates/
│ │ │ ├── docker-compose.yml.j2
│ │ │ ├── nginx_gitlab.conf.j2
│ │ ├── vars/
│ │ │ ├── main.yml
├── .gitignore
├── ansible.cfg
├── hosts.ini
├── LICENSE
├── main.yml
├── README.md
```

Основные моменты:
- В `./roles/gitlab/files/` должны быть расположены файлы сертификатов, которые будут передаваться на хост **Nginx** в случае использования **SSL**
- В `./roles/gitlab/tasks/` находятся файлы с задачами, которые выполняются в рамках **Ansible**-роли
- В `./roles/gitlab/templates/` находятся основные настройки сервисов, используемых **GitLab**-ом
- В `./roles/gitlab/vars/` указаны переменные, которые используются в рамках **Ansible**-роли
- В `./hosts.ini` перечислены хосты, к которым будет происходить подключение в рамках выполнения **Ansible**-роли


## Конфигурация

Перед использованием **Ansible**-роли, следует задать значения переменным в файле `./roles/gitlab/vars/main.yml`. 
В этом же файле содержится описание/предназначение переменных в контексте **Ansible**-роли.


## Зависимости

Для установки **GitLab** в текущей реализации, требуется готовые внешние сервисы:
- **Nginx**
- **LDAP**
- **MinIO**

В случае **MinIO**, также требуются bucket-ы, заранее выделенные под **GitLab**.  
Подробности в файле `./roles/gitlab/vars/main.yml`.

Также для запуска **Ansible**-роли требуется установить коллекцию `community.postgresql`.

## Запуск Ansible-роли

Запуск подразумевает включение роли в основной playbook.  
Пример:
```yaml
---
- name: Installing and configuring Gitlab
  hosts: gitlab_host
  become: yes
  roles:
    - role: gitlab
```

Предварительно необходимо указать хосты в `./hosts.ini`, 
а также указать необходимую для пользователя конфигурацию в файле `./ansible.cfg`.