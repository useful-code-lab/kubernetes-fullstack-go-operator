# Kubernetes Full-Stack Infrastructure & Go Operator

Практический проект для тех, кто хочет **научиться строить полноценную инфраструктуру приложения в Kubernetes** — от базовой изоляции ресурсов и базы данных до собственного Kubernetes Operator на Go.

Здесь можно не просто посмотреть YAML-манифесты, а **увидеть, как отдельные возможности Kubernetes собираются в единую работающую систему**.

## Что вы получите, изучив проект

После изучения репозитория вы сможете лучше понимать, как:

* организовать приложение в отдельном Kubernetes Namespace;
* ограничивать потребление CPU и памяти с помощью `ResourceQuota` и `LimitRange`;
* управлять конфигурацией и секретами приложения;
* разворачивать stateless-приложения через `Deployment` и `Service`;
* запускать stateful-компоненты через `StatefulSet`, `PersistentVolume` и `StorageClass`;
* выполнять задачи на каждом узле Kubernetes с помощью `DaemonSet`;
* автоматизировать резервное копирование и обслуживание через `Job` и `CronJob`;
* создавать собственные типы Kubernetes-ресурсов с помощью `CRD`;
* писать собственный Kubernetes Operator на Go;
* автоматически изменять состояние приложения через Operator;
* диагностировать состояние ресурсов и находить проблемы с помощью `kubectl`.

Таким образом, репозиторий можно использовать как **практический учебный стенд для изучения Kubernetes и разработки операторов на Go**.

---

## 🧩 Что построено в проекте

Архитектура разбита на несколько независимых уровней:

```text
Kubernetes Cluster
│
├── Namespace
├── ResourceQuota / LimitRange
│
├── Configuration & Secrets
│
├── Stateless Application
│   ├── Deployment
│   └── Service
│
├── Stateful Layer
│   ├── StatefulSet
│   ├── PersistentVolume
│   └── StorageClass
│
├── Infrastructure
│   └── DaemonSet
│
├── Scheduled Tasks
│   ├── Job
│   └── CronJob
│
└── Custom Kubernetes Extension
    ├── CRD
    └── Go Operator
        └── MySite
```

Каждый каталог демонстрирует отдельный механизм Kubernetes, а вместе они формируют приближенную к реальной инфраструктурную систему.

---

## 🚀 Быстрый старт

Для экспериментов можно использовать локальный кластер **Minikube**.

### 1. Запуск Kubernetes

```bash
minikube start

# Дополнительный узел для экспериментов с DaemonSet
minikube node add
```

---

### 2. Создание базовой инфраструктуры

Сначала создаём изолированное окружение и ограничения ресурсов:

```bash
kubectl apply -f base/
```

Проверить установленную квоту:

```bash
kubectl describe quota compute-resources -n k8s-lifecycle-prod
```

Это позволяет увидеть, как Kubernetes контролирует доступное приложению количество ресурсов.

---

### 3. Конфигурация и секреты

```bash
kubectl apply -f configs/
```

Проверить значение секрета:

```bash
kubectl get secret app-credentials \
  -n k8s-lifecycle-prod \
  -o jsonpath='{.data.DB_USER}' | base64 --decode

echo ""
```

---

### 4. Развертывание приложения

Stateless-часть:

```bash
kubectl apply -f app-stateless/
```

Stateful-часть:

```bash
kubectl apply -f app-stateful/
```

На этом этапе можно изучить разницу между обычным приложением и компонентом, которому необходимо сохранять состояние и данные.

---

### 5. Инфраструктурные задачи

Запуск сборщика логов на узлах:

```bash
kubectl apply -f infrastructure/
```

Запуск автоматизированных задач:

```bash
kubectl apply -f jobs/
```

Это показывает, как Kubernetes можно использовать не только для запуска приложения, но и для выполнения инфраструктурных процессов.

---

## ⚙️ Собственный Kubernetes Operator на Go

Главная практическая часть проекта — создание собственного API Kubernetes.

В проекте появляется новый ресурс:

```text
MySite
```

Вместо ручного управления несколькими Kubernetes-ресурсами пользователь может описать желаемое состояние через собственный ресурс, а Operator на Go будет приводить кластер к этому состоянию.

### Создание CRD

```bash
kubectl apply -f custom-res/01-crd.yaml
```

### Запуск Operator

```bash
cd operator/

go mod tidy
go run main.go
```

### Создание собственного ресурса

В другом терминале:

```bash
kubectl apply -f custom-res/02-sample-site.yaml
```

После этого Operator реагирует на созданный ресурс и выполняет необходимые изменения в Kubernetes.

---

## 🔄 Что демонстрирует Operator

Практическая часть позволяет увидеть основной принцип Kubernetes Operator:

```text
Custom Resource
      │
      ▼
Go Operator
      │
      ▼
Reconciliation
      │
      ▼
Kubernetes Resources
      │
      ▼
Желаемое состояние
```

Это хороший пример того, как Kubernetes можно **расширять под собственные задачи**, а не ограничиваться стандартными `Deployment`, `Service` и `StatefulSet`.

---

## 🔍 Полезные команды

Посмотреть ресурсы приложения:

```bash
kubectl get all -n k8s-lifecycle-prod
```

Проверить созданные `MySite`:

```bash
kubectl get ms -n k8s-lifecycle-prod
```

Посмотреть логи приложения:

```bash
kubectl logs -l app=lifecycle-app -n k8s-lifecycle-prod
```

Проверить состояние Deployment:

```bash
kubectl describe deployment marketing-site-deploy \
  -n k8s-lifecycle-prod
```

---

## 📚 Для чего использовать этот репозиторий

Проект полезен как:

* практический пример Kubernetes-инфраструктуры;
* учебный стенд для экспериментов с Kubernetes;
* пример организации YAML-манифестов по функциональным слоям;
* основа для изучения Kubernetes Operator;
* пример интеграции Go с Kubernetes API;
* демонстрационный проект для портфолио DevOps / Cloud / Go-разработчика.

Главная ценность репозитория — возможность **разобрать инфраструктуру по частям, запустить её локально и увидеть результат работы каждого Kubernetes-механизма**.

---

## 🗂 Структура проекта

```text
.
├── app-stateful/      # Stateful-приложение и хранение данных
├── app-stateless/     # Deployment и Service
├── base/               # Namespace и ограничения ресурсов
├── configs/            # Конфигурация и секреты
├── custom-res/         # CRD и пользовательские ресурсы
├── infrastructure/    # Инфраструктурные компоненты
├── jobs/               # Job и CronJob
├── operator/           # Kubernetes Operator на Go
└── README.md
```

---

## 🎯 Итог

Это не просто набор Kubernetes-манифестов.

Репозиторий показывает **полный путь от базовой конфигурации Kubernetes до создания собственного расширения платформы на Go**.

Если вы изучаете Kubernetes, DevOps, Cloud Native или Go-разработку, проект можно использовать как практическую площадку: запускать компоненты, менять конфигурацию, наблюдать за поведением кластера и постепенно усложнять Operator под собственные задачи.
