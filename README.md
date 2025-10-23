# create_ssh_users

Ansible-плейбук для автоматического создания пользователей на серверах, добавления им SSH-ключей, групп (sudo, docker и т.д.) и настройки доступа без пароля к `sudo`.

---

## Быстрый старт

### 1. Установи зависимости

```bash
pip install ansible
```

### 2. Подготовь окружение

Скопируй пример инвентаря и укажи свои адреса серверов:

```bash
cp ./inventory.ini.example ./inventory.ini
```

Скопируй пример пользователей:

```bash
cp vars/users.example.yml vars/users.yml
```

или создай зашифрованный файл:

```bash
ansible-vault create vars/users.vault.yml
```

### 3. Добавь публичные SSH-ключи

Положи ключи в каталог:

```
files/ssh_keys/
```

Файл должен **заканчиваться именем пользователя** (поддерживаются шаблоны `*user.pub`, `*-user.pub`, `*_user.pub`).

Пример:

```
files/ssh_keys/id_ed25519_server_some-user-name.pub
```

---

## Запуск

Создание пользователей на серверах:

```bash
ansible-playbook -i inventory.ini create-users.yml
```

Если используешь vault:

```bash
ansible-playbook -i inventory.ini create-users.yml --ask-vault-pass
```

---

## Проверка на сервере

После выполнения зайди на сервер и проверь:

```bash
id some-user-name
docker ps
sudo whoami
```

Ожидаемо:

* пользователь в группах `sudo` и `docker`;
* `sudo` не требует пароль;
* Docker доступен без `sudo`.

---

## Структура проекта

```
create_ssh_users/
├─ create-users.yml        # основной плейбук
├─ vars/
│   ├─ users.example.yml   # пример файла пользователей
│   └─ users.vault.yml     # зашифрованные реальные данные
├─ files/
│   └─ ssh_keys/           # публичные ключи пользователей
├─ inventori.ini.example   # пример файла-инветоря с подключением к серверу
├─ inventori.ini           # реальные хосты (в .gitignore)
```

---

После добавления новых пользователей или ключей просто запусти плейбук снова — он идемпотентен (ничего не сломает).
