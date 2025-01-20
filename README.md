# Ansible-роль для развёртывания GitLab

**Роль** предназначена для развёртывания **GitLab** на ОС **AltLinux 10**.  
Роль включает в себя:
- Установка и настройка **GitLab** и **Redis** в **Docker**-контейнерах
- Настройка **GitLab**-конфига для взаимодействия с внешними **PostgreSQL** и **MinIO**
- Настройка **GitLab**-конфига для взаимодействия с внешними **Keycloak** и **LDAP**

## Структура проекта

```markdown
gitlab-deployment/
├── roles/
│ ├── gitlab/
│ │ ├── files/
│ │ │ ├── RootCA.crt
│ │ ├── tasks/
│ │ │ ├── docker_installation.yml
│ │ │ ├── gitlab_configuration.yml
│ │ │ ├── launch.yml
│ │ │ ├── main.yml
│ │ │ ├── user.yml
│ │ ├── templates/
│ │ │ ├── docker-compose.yml.j2
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
- В `./roles/gitlab/files/` должны быть расположены файлы сертификатов, которые будут передаваться на хост **GitLab** (в контейнер) в случае использования **Keycloak**
- В `./roles/gitlab/tasks/` находятся файлы с задачами, которые выполняются в рамках **Ansible**-роли
- В `./roles/gitlab/templates/` находятся основные настройки сервисов, используемых **GitLab**-ом
- В `./roles/gitlab/vars/` указаны переменные, которые используются в рамках **Ansible**-роли
- В `./hosts.ini` перечислены хосты, к которым будет происходить подключение в рамках выполнения **Ansible**-роли


## Конфигурация

Перед использованием **Ansible**-роли, следует задать значения переменным в файле `./roles/gitlab/vars/main.yml`. 
В этом же файле содержится описание/предназначение переменных в контексте **Ansible**-роли.


## Зависимости

Для установки **GitLab** в текущей реализации, требуется готовые внешние сервисы:
- **PostgreSQL** – Обязательно
- **MinIO** – Обязательно
- **LDAP** – Необязательно
- **Keycloak** – Необязательно (требует `https` соединения)

В случае **MinIO**, также требуются bucket-ы, заранее выделенные под **GitLab**.  
Подробности в файле `./roles/gitlab/vars/main.yml`.


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

Предварительно необходимо указать хост **GitLab** в `./hosts.ini`, 
а также указать необходимую для пользователя конфигурацию в файле `./ansible.cfg`.