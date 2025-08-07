# Ansible Kubernetes Cluster Setup

Этот репозиторий содержит Ansible-плейбуки и роли для автоматического развертывания Kubernetes-кластера.

## 📦 Состав

Структура основана на ролях и группах хостов (`group_vars`). Используется модульное разбиение:

```
├── ansible.cfg
├── inventory/
│   └── hosts
├── group_vars/
│   ├── all.yml
│   ├── Kuber_control.yml
│   └── Kuber_working.yml
├── site.yml
├── roles/
│   ├── containerd/
│   ├── k8s_prereqs/
│   ├── kubernetes_packages/
│   ├── kubeadm_init/
│   ├── kube_join_master/
│   ├── kube_join_worker/
│   ├── certs/
│   ├── keepalived/
│   ├── join_commands/
│   ├── reboot/
│   └── admin_conf/
└── .gitignore
```

## 🛠️ Возможности

* Установка `containerd` и настройка cgroup'ов
* Конфигурация системных параметров (`sysctl`, `br_netfilter`)
* Установка Kubernetes-пакетов (`kubeadm`, `kubelet`, `kubectl`)
* Инициализация мастер-ноды (`kubeadm init`)
* Присоединение других master и worker нод к кластеру
* Развёртывание `Weave CNI`
* Настройка `keepalived` с виртуальным IP
* Автоматическая генерация и передача `join-command`
* Локальное копирование `admin.conf` для доступа к кластеру

## 🚀 Быстрый старт

1. Сконфигурируй `inventory/hosts` и переменные в `group_vars/`
2. Запусти playbook:

```bash
ansible-playbook -i inventory/hosts site.yml
```

## 🔐 Безопасность

> ⚠️ Убедись, что файлы `pki/`, `vault.yml`, `join-command`, `admin.conf` добавлены в `.gitignore`.

## 📘 Примеры

Файл `site.yml` содержит основную точку входа и задаёт порядок ролей:

```yaml
- hosts: Kuber_control
  become: true
  roles:
    - containerd
    - k8s_prereqs
    - kubernetes_packages
    - reboot
    - keepalived
    ...

- hosts: Kuber_working
  become: true
  roles:
    - containerd
    - kube_join_worker
    ...
```

## 🧩 Дополнительно

* `master_playbook.yml`, `test.yml`, `random_uuid` — временные/вспомогательные файлы
* Используй `--tags` и `--start-at-task` для точечного запуска

## 📄 Лицензия

MIT

