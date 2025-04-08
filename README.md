# Ansible: Базовая настройка серверов (Nginx + PostgreSQL)

### **Задание: Ansible для базовой настройки серверов**  
**Уровень:** Junior  
**Инструменты:** Ansible, SSH.  

---

## Описание задачи
Необходимо создать Ansible-плейбук для настройки двух серверов на Ubuntu:
1. Первый сервер — веб-сервер с Nginx
2. Второй сервер — база данных с PostgreSQL

Обязательные требования:
- Создание пользователя `devops` с SSH-доступом на обоих серверах
- Автоматизация установки и проверки сервисов
- Соответствие best practices для Ansible

---

## Техническое задание (ТЗ)
1. **Базовые требования:**
   - Использовать модули: `apt`, `user`, `authorized_key`
   - Проверка доступности сервисов:
     - Nginx: порт 80
     - PostgreSQL: порт 5432

2. **Безопасность:**
   - Пользователь `devops` должен иметь доступ по SSH через ключ
   - Минимальные привилегии sudo для `devops`

3. **Идемпотентность:**
   - Плейбук должен поддерживать повторный запуск без ошибок
   - Корректная обработка зависимостей через handlers

---

## Критерии выполнения
✅ **Основные:**
- Плейбук выполняется командой `ansible-playbook -i inventory.ini playbook.yml` без ошибок
- После выполнения:
  - Nginx доступен по HTTP (порт 80)
  - PostgreSQL принимает подключения (порт 5432)
  - Пользователь `devops` может подключиться по SSH

🔧 **Best Practices:**
- Правильная структура: разделение на роли/таски, использование handlers
- Валидация конфигурационных файлов (например, для sudo)
- Оптимизированные проверки (retries/delay для тестов)
- Логичная группировка хостов в inventory

---

## Реализация (как работает этот репозиторий)
### Особенности реализации:
1. **Универсальная базовая настройка:**
   - Общие задачи вынесены в отдельный плей для `hosts: all`
   - Настройка локали UTF-8 для системы и PostgreSQL
   - Установка базовых пакетов (curl, ufw и др.)

2. **Безопасность:**
   - Firewall:
     - Nginx: разрешены только 22/80
     - PostgreSQL: разрешены 22/5432
   - Запрет парольной аутентификации (только SSH-ключ)
   - Кастомные правила sudo без пароля для devops

3. **Проверки:**
   - Тест доступности Nginx через модуль `uri`
   - Проверка работы PostgreSQL через `psql -c '\l'`

4. **Оптимизации:**
   - Кеширование apt-пакетов (cache_valid_time)
   - Использование `register` + `until` для надежных проверок
   - Валидация sudo-файлов через `visudo -cf`

---

## Как использовать
1. Клонировать репозиторий
2. Настроить `inventory.ini` (IP-адреса ваших серверов)
3. Добавить свой SSH-ключ в `vars/ssh_keys.yml`
4. Запустить:  
```bash
ansible-playbook -i inventory.ini playbook.yml
```
