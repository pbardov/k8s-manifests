# Kubernetes Deployment Workflows
Этот репозиторий содержит набор Gitea Actions workflows для автоматизации деплоя и управления Kubernetes-ресурсами. Воркфлоу позволяют синхронизировать конфигурации, создавать сервисные аккаунты, применять манифесты и настраивать доступ к кластеру Kubernetes.
## Доступные workflows
### 1. rsync - Деплой на сервер
**Файл**: `.gitea/workflows/rsync.yaml`
**Описание**: Копирует файлы проекта на удаленный сервер через rsync, исключая скрытые файлы и директории.
**Триггеры**:
- Вручную через UI (workflow_dispatch)
- При пуше в ветку master (исключая скрытые файлы и директории)

**Требуемые vars**:
- `RSYNC` - список целевых серверов в формате `user@host:port:path`

**Требуемые secrets**:
- `SSH_PRIVATE_KEY` - SSH-ключ для доступа к удаленному серверу

### 2. apply-manifests - Применение K8s манифестов
**Файл**: `.gitea/workflows/apply-manifests.yaml`
**Описание**: Применяет Kubernetes манифесты к кластеру, исключая файлы namespace.yaml и *-sa.yaml.
**Триггеры**:
- Вручную через UI (workflow_dispatch)

**Требуемые vars**:
- `K8S_HOST` - хост сервера с доступом к Kubernetes
- `K8S_PORT` - SSH порт сервера (по умолчанию 22)
- `K8S_USER` - пользователь SSH
- `K8S_NAMESPACE` - Kubernetes namespace (или извлекается из namespace.yaml)

**Требуемые secrets**:
- `SSH_PRIVATE_KEY` - SSH-ключ для доступа к удаленному серверу

### 3. delete-manifests - Удаление K8s манифестов
**Файл**: `delete-manifests.yaml`
**Описание**: Удаляет Kubernetes манифесты из кластера, исключая файлы namespace.yaml и *-sa.yaml.
**Триггеры**:
- Вручную через UI (workflow_dispatch)

**Требуемые vars**:
- `K8S_HOST` - хост сервера с доступом к Kubernetes
- `K8S_PORT` - SSH порт сервера (по умолчанию 22)
- `K8S_USER` - пользователь SSH
- `K8S_NAMESPACE` - Kubernetes namespace (или извлекается из namespace.yaml)

**Требуемые secrets**:
- `SSH_PRIVATE_KEY` - SSH-ключ для доступа к удаленному серверу


### 4. create-k8s-user-sa - Создание сервисного аккаунта в K8s
**Файл**: `.gitea/workflows/create-k8s-user-sa.yaml`
**Описание**: Создает YAML-манифесты для namespace и сервисного аккаунта с ролью edit.
**Триггеры**:
- Вручную через UI (workflow_dispatch)

**Требуемые vars**:
- `K8S_HOST` - хост сервера с доступом к Kubernetes
- `K8S_PORT` - SSH порт сервера (по умолчанию 22)
- `K8S_USER` - имя пользователя (будет использовано для создания SA)
- `K8S_NAMESPACE` - Kubernetes namespace (или извлекается из namespace.yaml)

**Требуемые secrets**:
- `SSH_PRIVATE_KEY` - SSH-ключ для доступа к удаленному серверу
- `GIT_WRITE_TOKEN` - токен для записи изменений в репозиторий

### 5. config-k8s-user-kubectl - Настройка kubectl для пользователя
**Файл**: `.gitea/workflows/config-k8s-user-kubectl.yaml`
**Описание**: Настраивает kubectl на удаленном сервере для использования сервисного аккаунта.
**Триггеры**:
- Вручную через UI (workflow_dispatch)

**Требуемые vars**:
- `K8S_HOST` - хост сервера с доступом к Kubernetes
- `K8S_PORT` - SSH порт сервера (по умолчанию 22)
- `K8S_USER` - имя пользователя SSH
- `K8S_NAMESPACE` - Kubernetes namespace
- `K8S_API_SERVER` - адрес API-сервера Kubernetes
- `K8S_CLUSTER` - имя кластера (по умолчанию "k8s")
- `K8S_CLUSTER_CA` - CA-сертификат кластера

**Требуемые secrets**:
- `SSH_PRIVATE_KEY` - SSH-ключ для доступа к удаленному серверу
- `K8S_TOKEN` - токен сервисного аккаунта Kubernetes

### 6. sync-k8s-cm-secrets - Синхронизация ConfigMaps и Secrets
**Файл**: `.gitea/workflows/sync-k8s-cm-secrets.yaml`
**Описание**: Синхронизирует конфигурационные карты и секреты из файлов `.configmaps.yaml` и `.secrets.yaml` в кластер Kubernetes.
**Триггеры**:
- Вручную через UI (workflow_dispatch)
- При пуше в ветку master с изменениями в файлах `.configmaps.yaml`, `.secrets.yaml`, `.synck8s.yaml` или самого workflow

**Требуемые vars**:
- `K8S_HOST` - хост сервера с доступом к Kubernetes
- `K8S_PORT` - SSH порт сервера (по умолчанию 22)
- `K8S_USER` - имя пользователя SSH
- `K8S_NAMESPACE` - Kubernetes namespace (или извлекается из namespace.yaml)

**Требуемые secrets**:
- `SSH_PRIVATE_KEY` - SSH-ключ для доступа к удаленному серверу

**Требуемые файлы**:
- `.configmaps.yaml` - определения ConfigMap для создания в кластере
- `.secrets.yaml` - определения Secrets для создания в кластере
- `.synck8s.yaml` - дополнительные настройки синхронизации

### 7. sync-imagepull-secrets - Настройка доступа к Docker Registry
**Файл**: `.gitea/workflows/sync-imagepull-secrets.yaml`
**Описание**: Создает secret типа docker-registry и привязывает его к сервисному аккаунту default.
**Триггеры**:
- Вручную через UI (workflow_dispatch)

**Требуемые vars**:
- `K8S_HOST` - хост сервера с доступом к Kubernetes
- `K8S_PORT` - SSH порт сервера (по умолчанию 22)
- `K8S_USER` - имя пользователя SSH
- `K8S_NAMESPACE` - Kubernetes namespace (или извлекается из namespace.yaml)
- `DOCKER_REGISTRY` - адрес Docker registry

**Требуемые secrets**:
- `SSH_PRIVATE_KEY` - SSH-ключ для доступа к удаленному серверу
- `DOCKER_USERNAME` - имя пользователя Docker registry
- `DOCKER_PASSWORD` - пароль Docker registry

## Настройка переменных и секретов
Для корректной работы workflows необходимо настроить следующие переменные и секреты в настройках репозитория:
### Variables (vars)

| Переменная      | Описание                             | Используется в workflows                                                                                  |
|-----------------|--------------------------------------|-----------------------------------------------------------------------------------------------------------|
| K8S_HOST        | Хост сервера с доступом к Kubernetes | apply-manifests, config-k8s-user-kubectl, create-k8s-user-sa, sync-imagepull-secrets, sync-k8s-cm-secrets |
| K8S_PORT        | SSH порт сервера (по умолчанию 22)   | apply-manifests, config-k8s-user-kubectl, create-k8s-user-sa, sync-imagepull-secrets, sync-k8s-cm-secrets |
| K8S_USER        | Имя пользователя SSH                 | apply-manifests, config-k8s-user-kubectl, create-k8s-user-sa, sync-imagepull-secrets, sync-k8s-cm-secrets |
| K8S_NAMESPACE   | Kubernetes namespace                 | apply-manifests, config-k8s-user-kubectl, create-k8s-user-sa, sync-imagepull-secrets, sync-k8s-cm-secrets |
| K8S_API_SERVER  | Адрес API-сервера Kubernetes         | config-k8s-user-kubectl                                                                                   |
| K8S_CLUSTER     | Имя кластера (по умолчанию "k8s")    | config-k8s-user-kubectl                                                                                   |
| K8S_CLUSTER_CA  | CA-сертификат кластера               | config-k8s-user-kubectl                                                                                   |
| DOCKER_REGISTRY | Адрес Docker registry                | sync-imagepull-secrets                                                                                    |
| RSYNC           | Список целевых серверов для rsync    | rsync                                                                                                     |
### Secrets

| Секрет          | Описание                                  | Используется в workflows |
|-----------------|-------------------------------------------|--------------------------|
| SSH_PRIVATE_KEY | SSH-ключ для доступа к удаленному серверу | все workflows            |
| K8S_TOKEN       | Токен сервисного аккаунта Kubernetes      | config-k8s-user-kubectl  |
| DOCKER_USERNAME | Имя пользователя Docker registry          | sync-imagepull-secrets   |
| DOCKER_PASSWORD | Пароль Docker registry                    | sync-imagepull-secrets   |
| GIT_WRITE_TOKEN | Токен для записи изменений в репозиторий  | create-k8s-user-sa       |
## Порядок использования
1. **Инициализация проекта**:
    - Запустите `create-k8s-user-sa` для создания манифестов namespace и сервисного аккаунта
    - Примените их вручную или с помощью kubectl

2. **Настройка доступа**:
    - Получите токен сервисного аккаунта и сохраните его в секреты как `K8S_TOKEN`
    - Запустите `config-k8s-user-kubectl` для настройки kubectl на удаленном сервере

3. **Настройка доступа к Docker Registry**:
    - Запустите `sync-imagepull-secrets` для создания секрета docker-registry и привязки его к SA default

4. **Настройка ConfigMaps и Secrets**:
    - Создайте файлы `.configmaps.yaml`, `.secrets.yaml` и `.synck8s.yaml`
    - Запустите `sync-k8s-cm-secrets` для применения их в кластере

5. **Деплой приложения**:
    - Разместите Kubernetes манифесты в корне репозитория
    - Запустите `apply-manifests` для применения их в кластере
    - Или настройте автоматический деплой на сервер с помощью `rsync`

## Примечания
- Большинство workflows поддерживают автоматическое определение namespace из файла `namespace.yaml`, если переменная `K8S_NAMESPACE` не задана
- Workflows используют SSH для выполнения команд kubectl на удаленном сервере, что позволяет не хранить kubeconfig в репозитории

При использовании этих workflows рекомендуется ознакомиться с содержанием каждого файла для понимания деталей их работы.
