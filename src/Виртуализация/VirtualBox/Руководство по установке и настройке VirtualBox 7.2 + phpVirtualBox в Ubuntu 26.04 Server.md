# Мануал по установке и настройке VirtualBox 7.2, phpVirtualBox (Docker) и автозапуска ВМ на Ubuntu Server 26.04

Настоящее руководство описывает **полный** процесс развертывания сервера виртуализации на **чистой** установке **Ubuntu Server 26.04**: VirtualBox 7.2, веб-интерфейс **phpVirtualBox** в Docker и автозапуск/сохранение состояния виртуальных машин (Save State) при выключении сервера.

---

## Содержание

- [Содержание](#содержание)
- [Требования и учётные записи](#требования-и-учётные-записи)
- [1. Установка VirtualBox и подготовка пользователя `vbox`](#1-установка-virtualbox-и-подготовка-пользователя-vbox)
  - [1.1. Устранение проблем с модулем ядра](#11-устранение-проблем-с-модулем-ядра)
- [2. Настройка конфигурации `/etc/default/virtualbox`](#2-настройка-конфигурации-etcdefaultvirtualbox)
- [3. Создание и настройка `systemd`-сервиса `vboxweb`](#3-создание-и-настройка-systemd-сервиса-vboxweb)
  - [3.1. Устранение проблем с `vboxweb`](#31-устранение-проблем-с-vboxweb)
- [4. Установка Docker и развёртывание `phpVirtualBox`](#4-установка-docker-и-развёртывание-phpvirtualbox)
  - [4.1. Установка Docker Engine и Compose](#41-установка-docker-engine-и-compose)
  - [4.2. Развёртывание `phpVirtualBox`](#42-развёртывание-phpvirtualbox)
  - [4.3. Устранение проблем с phpVirtualBox](#43-устранение-проблем-с-phpvirtualbox)
- [5. Настройка автозапуска ВМ и сохранения состояния (Save State)](#5-настройка-автозапуска-вм-и-сохранения-состояния-save-state)
  - [5.1. Устранение проблем с `vboxautostart`](#51-устранение-проблем-с-vboxautostart)
  - [5.2. Альтернатива: unit на каждую ВМ](#52-альтернатива-unit-на-каждую-вм)
- [6. Настройка обработки физической кнопки питания сервера](#6-настройка-обработки-физической-кнопки-питания-сервера)
- [7. Включение автозапуска](#7-включение-автозапуска)
- [8. Итоговая проверка](#8-итоговая-проверка)
- [9. Добавление, отключение и удаление ВМ](#9-добавление-отключение-и-удаление-вм)
  - [9.1. Добавление новой ВМ](#91-добавление-новой-вм)
  - [9.2. Временно отключить ВМ (оставить на диске)](#92-временно-отключить-вм-оставить-на-диске)
  - [9.3. Удаление ВМ](#93-удаление-вм)
  - [9.4. Ошибочно использован плейсхолдер мануала](#94-ошибочно-использован-плейсхолдер-мануала)
- [10. Сквозной (raw) диск и режим Writethrough](#10-сквозной-raw-диск-и-режим-writethrough)

---

## Требования и учётные записи

**Исходное состояние:** Ubuntu Server 26.04 установлена «с нуля», выполнен вход по SSH под пользователем с правами `sudo`.

**Две учётные записи в этом мануале:**

| Учётная запись                                      | Назначение                                                             | Примеры команд                               |
| --------------------------------------------------- | ---------------------------------------------------------------------- | -------------------------------------------- |
| **Ваш пользователь** (тот, под кем вы вошли по SSH) | Администрирование сервера, установка пакетов, Docker, `docker compose` | `sudo apt install …`, `docker compose up -d` |
| **`vbox`** (создаётся в §1)                         | Запуск VirtualBox, владение ВМ, `vboxwebsrv`, Save State               | `sudo -u vbox vboxmanage …`                  |

> **Важно:** Docker и phpVirtualBox **не** запускаются под `vbox`. Пользователь `vbox` указан в `docker-compose.yml` как логин для API VirtualBox (`SRV1_USER`).

Далее в командах `~/` означает домашний каталог **вашего** пользователя (например, `/home/user`).

---

## 1. Установка VirtualBox и подготовка пользователя `vbox`

Все команды в этом разделе — **от вашего пользователя** (с `sudo` где указано).

**Включите репозитории `universe` и `multiverse`, обновите индекс пакетов:**

```bash
sudo add-apt-repository universe
sudo add-apt-repository multiverse
sudo apt update
```

**Установите зависимости для сборки модулей ядра** (на чистом Ubuntu Server их нет):

```bash
sudo apt install linux-headers-$(uname -r) dkms build-essential -y
```

**Примите лицензию Extension Pack и установите VirtualBox** (без интерактивного запроса лицензии):

```bash
echo "virtualbox-ext-pack virtualbox-ext-pack/license select true" | sudo debconf-set-selections
sudo apt install nano virtualbox virtualbox-ext-pack -y
```

> **Важно:** при установке из репозитория Ubuntu (`multiverse`) служба модулей ядра называется **`virtualbox.service`**, а не `vboxdrv.service`. Имя `vboxdrv` — это модуль ядра (`modprobe vboxdrv`).

**Загрузите модуль ядра и включите службу драйвера:**

```bash
sudo modprobe vboxdrv
sudo systemctl enable --now virtualbox
```

**Контрольная проверка:**

```bash
lsmod | grep vbox
VBoxManage --version
systemctl is-active virtualbox
ls -l /dev/vboxdrv
```

Ожидается: модуль `vboxdrv` в списке, версия VirtualBox, статус `active`, устройство `/dev/vboxdrv` существует.

Если `modprobe vboxdrv` или `virtualbox.service` завершились ошибкой — см. [§1.1](#11-устранение-проблем-с-модулем-ядра).

**Создайте изолированного пользователя `vbox`** (задайте пароль, запомните его — понадобится в §4):

```bash
sudo adduser vbox
sudo usermod -aG vboxusers vbox
```

**Создайте и настройте права на домашний каталог и лог-файл:**

```bash
# Домашняя папка пользователя vbox
sudo mkdir -p /home/vbox
sudo chown -R vbox:vboxusers /home/vbox

# Лог-файл для веб-службы
sudo touch /var/log/vboxweb.log
sudo chown vbox:vboxusers /var/log/vboxweb.log
```

### 1.1. Устранение проблем с модулем ядра

**Проверьте, какая служба установлена:**

```bash
systemctl list-unit-files | grep -iE 'virtualbox|vboxdrv'
systemctl status virtualbox
```

Для пакета из `multiverse` должна быть **`virtualbox.service`**.

**Пересоберите модули DKMS:**

```bash
sudo apt install --reinstall virtualbox-dkms -y
sudo /sbin/vboxconfig
sudo systemctl restart virtualbox
```

**Проверьте Secure Boot** (если включён — неподписанные модули не загрузятся):

```bash
mokutil --sb-state
```

При `SecureBoot enabled` нужно либо отключить Secure Boot в BIOS/UEFI, либо зарегистрировать MOK-ключ при следующей перезагрузке (Ubuntu предложит это при установке DKMS-модулей).

**Диагностика при повторной ошибке:**

```bash
sudo journalctl -xeu virtualbox
sudo dmesg | tail -30
ls /var/lib/dkms/virtualbox/
```

---

## 2. Настройка конфигурации `/etc/default/virtualbox`

```bash
sudo nano /etc/default/virtualbox
```

Вставьте следующую конфигурацию:

```bash
# Defaults for virtualbox initscript
LOAD_VBOXDRV_MODULE=1

# Параметры завершения работы ВМ
SHUTDOWN_USERS="all"
SHUTDOWN=savestate

# Параметры VirtualBox Web Service
VBOXWEB_USER="vbox"
VBOXWEB_HOST="0.0.0.0"
VBOXWEB_PORT=18083
VBOXWEB_CHECK_INTERVAL=5
VBOXWEB_WAIT_THRESHOLD=10

# Параметры автозапуска ВМ
VBOXAUTOSTART_DB=/etc/vbox
VBOXAUTOSTART_CONFIG=autostart.conf
```

> **Важно:** `VBOXAUTOSTART_CONFIG` — **только имя файла**, не полный путь. Полный путь получается как `$VBOXAUTOSTART_DB/$VBOXAUTOSTART_CONFIG` → `/etc/vbox/autostart.conf`.

> **Save State при выключении:** параметр `SHUTDOWN=savestate` задействует `virtualbox.service`. Таймауты systemd, чтобы дамп RAM успел завершиться, настраиваются в [§6](#6-настройка-обработки-физической-кнопки-питания-сервера). **Не выключайте и не перезагружайте** сервер с запущенными ВМ до выполнения §6 и [§7](#7-включение-автозапуска).

---

## 3. Создание и настройка `systemd`-сервиса `vboxweb`

В Ubuntu Server готовой службы `vboxweb` нет — создаём `systemd`-юнит вручную.

**Предусловия** (без них `vboxwebsrv` не поднимется):

```bash
systemctl is-active virtualbox
test -c /dev/vboxdrv && echo "/dev/vboxdrv OK" || echo "ОШИБКА: /dev/vboxdrv отсутствует — вернитесь к §1"
sudo -u vbox VBoxManage list vms
```

Последняя команда инициализирует профиль `~/.VirtualBox` пользователя `vbox` (даже если список ВМ пуст).

```bash
sudo nano /etc/systemd/system/vboxweb.service
```

```ini
[Unit]
Description=VirtualBox Web Service
After=network.target virtualbox.service
Requires=virtualbox.service

[Service]
Type=forking
User=vbox
Group=vboxusers
Environment=HOME=/home/vbox
EnvironmentFile=-/etc/default/virtualbox
RuntimeDirectory=vboxweb
ExecStart=/usr/bin/vboxwebsrv --host 0.0.0.0 --port 18083 --background --pidfile /run/vboxweb/vboxweb.pid
PIDFile=/run/vboxweb/vboxweb.pid
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

> **Почему `Type=forking`:** `vboxwebsrv --background` уходит в фон; при `Type=simple` systemd считает, что процесс завершился, и порт не остаётся в `LISTEN`.

**Активируйте и запустите службу:**

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now vboxweb
```

**Контрольная проверка:**

```bash
sudo systemctl status vboxweb
sudo ss -tlnp 'sport = :18083'
pgrep -af vboxwebsrv
curl -s -o /dev/null -w "HTTP %{http_code}\n" http://127.0.0.1:18083/ || true
```

Ожидается: `active (running)` или `active (exited)` (для forking-юнита оба допустимы, главное — процесс `vboxwebsrv` в `pgrep` и строка `LISTEN` на `0.0.0.0:18083` в `ss`.

Если порт не слушает — см. [§3.1](#31-устранение-проблем-с-vboxweb).

> **Примечание:** `vboxweb` обслуживает только веб-API и не участвует в Save State при выключении хоста. Расширенный `TimeoutStopSec` для этой службы не требуется.

### 3.1. Устранение проблем с `vboxweb`

**1. Посмотрите статус и логи службы:**

```bash
sudo systemctl status vboxweb
sudo journalctl -u vboxweb -n 50 --no-pager
```

**2. Проверьте модуль ядра** (без `/dev/vboxdrv` веб-служба не стартует):

```bash
systemctl is-active virtualbox
ls -l /dev/vboxdrv
lsmod | grep vbox
```

**3. Запустите вручную от пользователя `vbox`** (ошибка будет видна в терминале):

```bash
sudo systemctl stop vboxweb
sudo -u vbox HOME=/home/vbox /usr/bin/vboxwebsrv --host 0.0.0.0 --port 18083
```

Если вручную работает — пересоздайте unit из §3 (`Type=forking`, `--background`, `PIDFile`), затем:

```bash
sudo systemctl daemon-reload
sudo systemctl restart vboxweb
```

**4. Проверьте, не занят ли порт другим процессом:**

```bash
sudo ss -tlnp 'sport = :18083'
sudo fuser -v 18083/tcp 2>/dev/null || true
```

---

## 4. Установка Docker и развёртывание `phpVirtualBox`

Все команды в §4 выполняются **от вашего пользователя** (не от `vbox`).

### 4.1. Установка Docker Engine и Compose

**Установите Docker из репозитория Ubuntu** (пакет `docker.io`, не `podman-docker`):

```bash
sudo apt install docker.io docker-compose-v2 -y
sudo systemctl enable --now docker
```

**Добавьте вашего пользователя в группу `docker`:**

```bash
sudo usermod -aG docker "$USER"
```

**Примените группу** (выберите один спариант):

```bash
# Вариант А: в текущей SSH-сессии
newgrp docker

# Вариант Б: выйти из SSH и войти снова
```

**Контрольная проверка:**

```bash
docker --version
docker compose version
docker ps
systemctl is-active docker
```

Ожидается: версии Docker и Compose, пустой список контейнеров (или running), статус `active`.

### 4.2. Развёртывание `phpVirtualBox`

**Убедитесь, что `vboxweb` запущен** (§3):

```bash
systemctl is-active vboxweb
curl -s -o /dev/null -w "%{http_code}" http://127.0.0.1:18083/ || true
```

**Создайте каталог проекта и файл `docker-compose.yml`:**

```bash
mkdir -p ~/phpvirtualbox && cd ~/phpvirtualbox
nano docker-compose.yml
```

```yaml
services:
  phpvirtualbox:
    image: joweisberg/phpvirtualbox:latest
    container_name: phpvirtualbox
    restart: unless-stopped
    ports:
      - '8080:80'
    environment:
      SRV1_HOSTPORT: 'host.docker.internal:18083'
      SRV1_NAME: Ubuntu-VirtualBox
      SRV1_USER: vbox
      SRV1_PW: 'ПАРОЛЬ_ПОЛЬЗОВАТЕЛЯ_VBOX'
    extra_hosts:
      - 'host.docker.internal:host-gateway'
```

> **Примечания:**
>
> - в `SRV1_PW` укажите пароль пользователя `vbox`, заданный в §1;
> - на Docker Hub у образа `joweisberg/phpvirtualbox` есть только тег **`latest`** (внутри — phpVirtualBox 7.2+); тега `7.2-1` нет;
> - переменная **`SRV1_HOSTPORT`** (хост:порт) обязательна; отдельные `SRV1_HOST` / `SRV1_PORT` этот образ не использует;
> - строка `version:` в Compose v2 не нужна (Compose проигнорирует её с предупреждением).

**Запустите контейнер:**

```bash
cd ~/phpvirtualbox
docker compose pull
docker compose up -d --force-recreate
```

> **Важно:** обычный `docker compose up -d` **не обновляет** переменные окружения уже запущенного контейнера. После каждого изменения `SRV1_PW` или других `environment` используйте `--force-recreate` (или `docker compose down && docker compose up -d`).

**Контрольная проверка:**

```bash
docker compose ps
curl -s -o /dev/null -w "%{http_code}" http://127.0.0.1:8080/
hostname -I
```

Ожидается: контейнер `running`, HTTP-код `200` или `302`, IP-адрес сервера.

**Проверьте связь контейнера с `vboxwebsrv` на хосте** (до входа в браузер):

```bash
# vboxwebsrv на хосте (curl есть на Ubuntu Server)
curl -s -o /dev/null -w "host→vboxwebsrv HTTP %{http_code}\n" http://127.0.0.1:18083/

# из контейнера (в образе нет curl — используем PHP)
docker exec phpvirtualbox php -r "\$r=@file_get_contents('http://host.docker.internal:18083/'); echo \$r===false ? \"container→vboxwebsrv FAIL\n\" : \"container→vboxwebsrv OK\n\";"

# переменные внутри контейнера должны совпадать с docker-compose.yml
grep SRV1 ~/phpvirtualbox/docker-compose.yml
docker exec phpvirtualbox env | grep '^SRV1_'
```

Ожидается: HTTP-код `200`/`401`/`403` с хоста; `container→vboxwebsrv OK`; пароль в `env` **совпадает** с `docker-compose.yml`. Если в `env` старый пароль — контейнер не пересоздан, выполните `docker compose up -d --force-recreate`.

**Откройте панель в браузере:**

```
http://IP_СЕРВЕРА:8080
```

(подставьте адрес из `hostname -I`).

**Два уровня авторизации** (не путать):

| Уровень                                  | Логин / пароль                                    | Где задаётся                                        |
| ---------------------------------------- | ------------------------------------------------- | --------------------------------------------------- |
| Вход в phpVirtualBox (форма в браузере)  | `admin` / `admin`                                 | по умолчанию в образе                               |
| Подключение phpVirtualBox → `vboxwebsrv` | **`vbox`** / **пароль Linux-пользователя `vbox`** | `SRV1_USER` / `SRV1_PW` в `docker-compose.yml` (§1) |

Ошибка `Error logging in to vboxwebsrv` означает, что **второй** уровень не прошёл: неверный `SRV1_PW`, контейнер не достучался до `vboxwebsrv`, или PAM отклонил пароль. Это **не** связано с `admin`/`admin`.

После изменения `docker-compose.yml` пересоздайте контейнер:

```bash
cd ~/phpvirtualbox
docker compose up -d --force-recreate
```

Если ошибка сохраняется — см. [§4.3](#43-устранение-проблем-с-phpvirtualbox).

### 4.3. Устранение проблем с phpVirtualBox

**1. Проверьте пароль `vbox` и переменные контейнера:**

```bash
grep SRV1 ~/phpvirtualbox/docker-compose.yml
docker exec phpvirtualbox env | grep '^SRV1_'
```

`SRV1_PW` в выводе `env` должен **совпадать** с `docker-compose.yml`. Если пароль старый — пересоздайте контейнер:

```bash
cd ~/phpvirtualbox
docker compose up -d --force-recreate
docker exec phpvirtualbox env | grep '^SRV1_'
```

При спецсимволах в пароле — в кавычках: `SRV1_PW: 'ваш_пароль'`.

**2. Проверьте `vboxwebsrv` на хосте:**

```bash
systemctl is-active vboxweb
sudo ss -tlnp 'sport = :18083'
curl -s -o /dev/null -w "host HTTP %{http_code}\n" http://127.0.0.1:18083/
```

**3. Проверьте доступ из контейнера** (если `FAIL` — проблема сети Docker→хост):

```bash
docker exec phpvirtualbox php -r "\$r=@file_get_contents('http://host.docker.internal:18083/'); var_dump(\$r!==false);"
# альтернатива, если host.docker.internal недоступен:
# замените в compose SRV1_HOSTPORT на '172.17.0.1:18083' и снова --force-recreate
```

**4. Сбросьте библиотеку аутентификации VirtualBox** (если ранее менялась):

```bash
sudo -u vbox VBoxManage setproperty websrvauthlibrary default
sudo systemctl restart vboxweb
```

**5. Вариант для headless-сервера в доверенной сети** — отключить аутентификацию `vboxwebsrv` (упрощает связку с Docker; `vboxwebsrv` по-прежнему слушает только внутренние интерфейсы):

```bash
sudo -u vbox VBoxManage setproperty websrvauthlibrary null
sudo systemctl restart vboxweb
```

В `docker-compose.yml` добавьте и уберите `SRV1_USER`/`SRV1_PW`:

```yaml
environment:
  SRV1_HOSTPORT: 'host.docker.internal:18083'
  SRV1_NAME: Ubuntu-VirtualBox
  CONF_noAuth: 'true'
```

```bash
cd ~/phpvirtualbox
docker compose up -d --force-recreate
```

> **Безопасность:** при `websrvauthlibrary null` любой, кто достучится до порта `18083`, управляет VirtualBox. Не открывайте `18083` наружу; phpVirtualBox на `:8080` защитите сменой пароля `admin` после первого входа.

---

## 5. Настройка автозапуска ВМ и сохранения состояния (Save State)

> **Порядок разделов §5–§8:** конфигурация и unit-файлы (§5–§5.2) → таймауты Save State ([§6](#6-настройка-обработки-физической-кнопки-питания-сервера)) → включение служб ([§7](#7-включение-автозапуска)) → итоговая проверка ([§8](#8-итоговая-проверка)). Команды `systemctl enable`, `reboot` и `poweroff` с запущенными ВМ — **только после §6**.

**Подготовьте каталог для базы данных автозапуска:**

```bash
sudo mkdir -p /etc/vbox
sudo chown root:vboxusers /etc/vbox
sudo chmod 1775 /etc/vbox
```

**Создайте файл конфигурации прав автозапуска:**

```bash
sudo nano /etc/vbox/autostart.conf
```

**Вариант A (рекомендуется)** — один оператор `vbox`, домашний/лабораторный сервер; при добавлении ВМ конфиг **не меняют**:

```ini
default_policy = allow
```

**Вариант B** — явный список пользователей (несколько операторов VirtualBox на одном хосте):

```ini
default_policy = deny

vbox = {
    allow = true
}
```

> **Критично (вариант B):** имя в блоке `{ allow = true }` — **Linux-пользователь-владелец ВМ**, не логин phpVirtualBox. После `modifyvm --autostart-enabled on` в `/etc/vbox/` появится `ИМЯ_ПОЛЬЗОВАТЕЛЯ.start` — оно должно совпадать с записью в конфиге. При ошибке `User is not allowed to autostart VMs` используйте **вариант A** или добавьте пользователя в вариант B.

**Укажите путь к БД автозапуска в профиле VirtualBox:**

```bash
sudo -u vbox vboxmanage setproperty autostartdbpath /etc/vbox
sudo -u vbox VBoxManage list systemproperties | grep -i autostart
```

Ожидается: `Autostart database path: /etc/vbox`.

**Включите автозапуск и автосохранение для конкретной ВМ** (после создания ВМ; ВМ должна быть **выключена**):

```bash
# Посмотреть список имеющихся ВМ у пользователя vbox
sudo -u vbox vboxmanage list vms

# Включить автозапуск при старте ОС
sudo -u vbox vboxmanage modifyvm "ИМЯ_ВАШЕЙ_ВМ" --autostart-enabled on

# Save State при корректной остановке хоста (VBoxManage не выводит сообщений при успехе)
sudo -u vbox vboxmanage modifyvm "ИМЯ_ВАШЕЙ_ВМ" --autostop-type savestate
```

**Проверка настроек ВМ** (не полагайтесь на `showvminfo | grep autostop` — в Ubuntu-сборке строка часто отсутствует):

```bash
ls -la /etc/vbox/
sudo -u vbox vboxmanage showvminfo "ИМЯ_ВАШЕЙ_ВМ" | grep -i autostart
sudo -u vbox grep -i autostop "/home/vbox/VirtualBox VMs/ИМЯ_ВАШЕЙ_ВМ/ИМЯ_ВАШЕЙ_ВМ.vbox"
```

Ожидается: файлы `vbox.start` и `vbox.stop`; `Autostart Enabled: enabled`; в `.vbox` — `autostop="SaveState"`.

**Создайте и включите службу автозапуска `vboxautostart`** (только **после** шагов выше — нужна хотя бы одна ВМ с `--autostart-enabled on`):

> **Важно:** пакет VirtualBox из `multiverse` **не поставляет** unit `vboxautostart-service.service` (см. [Launchpad #1912861](https://bugs.launchpad.net/bugs/1912861)). Создаём unit вручную — как для `vboxweb` в §3.

**Проверьте предусловия:**

```bash
test -x /usr/lib/virtualbox/VBoxAutostart && echo "VBoxAutostart OK"
sudo -u vbox VBoxManage list systemproperties | grep -i autostart
sudo -u vbox vboxmanage list vms
ls -la /etc/vbox/
```

**Проверьте запуск вручную** (ошибка или диагностика — в терминале):

```bash
sudo /usr/lib/virtualbox/VBoxAutostart --start --config /etc/vbox/autostart.conf
echo "exit code: $?"
sudo -u vbox vboxmanage list runningvms
```

| Сообщение / результат                                | Значение                           | Действие                                                                                        |
| ---------------------------------------------------- | ---------------------------------- | ----------------------------------------------------------------------------------------------- |
| ВМ в `list runningvms`                               | ✅ autostart работает              | [§7.1](#7-включение-автозапуска)                                                                |
| `No virtual machines found` при наличии `vbox.start` | ⚠️ типично для Ubuntu `multiverse` | unit — [§5.2](#52-альтернатива-unit-на-каждую-вм), включение — [§7.2](#7-включение-автозапуска) |
| `User is not allowed to autostart VMs`               | ❌ конфиг                          | [§5.1](#51-устранение-проблем-с-vboxautostart), вариант A/B в §5                                |
| ненулевой exit code                                  | ❌                                 | [§5.1](#51-устранение-проблем-с-vboxautostart)                                                  |

Если `VBoxAutostart` не поднял ВМ — **не включайте** `vboxautostart`; создайте unit из §5.2, выполните §6 и включите через [§7.2](#7-включение-автозапуска).

**Создайте unit-файл:**

```bash
sudo nano /etc/systemd/system/vboxautostart.service
```

```ini
[Unit]
Description=VirtualBox Autostart Service
After=network.target virtualbox.service
Requires=virtualbox.service

[Service]
Type=oneshot
RemainAfterExit=yes
WorkingDirectory=/etc/vbox
EnvironmentFile=-/etc/default/virtualbox
ExecStart=/usr/lib/virtualbox/VBoxAutostart --start --config autostart.conf
ExecStop=/usr/lib/virtualbox/VBoxAutostart --stop --config autostart.conf
TimeoutStopSec=300

[Install]
WantedBy=multi-user.target
```

> **Не включайте службу** (`systemctl enable`) на этом шаге. `TimeoutStopSec` уже в unit выше; пересчёт — в [§6](#6-настройка-обработки-физической-кнопки-питания-сервера). Включение — в [§7](#7-включение-автозапуска).

### 5.1. Устранение проблем с `vboxautostart`

**1. Логи службы:**

```bash
sudo systemctl status vboxautostart
sudo journalctl -xeu vboxautostart --no-pager
```

**2. Проверьте `/etc/default/virtualbox`** — имя файла, не полный путь:

```bash
grep VBOXAUTOSTART /etc/default/virtualbox
cat /etc/vbox/autostart.conf
ls -la /etc/vbox/
```

Должно быть:

```bash
VBOXAUTOSTART_DB=/etc/vbox
VBOXAUTOSTART_CONFIG=autostart.conf
```

**Ошибка `User is not allowed to autostart VMs`** — см. **варианты A/B** в [§5](#5-настройка-автозапуска-вм-и-сохранения-состояния-save-state). Кратко:

```bash
ls /etc/vbox/*.start
cat /etc/vbox/autostart.conf
```

| Файл в `/etc/vbox/`                             | Решение                                                                                         |
| ----------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `vbox.start`, в конфиге `deny` без блока `vbox` | **вариант A** (`default_policy = allow`) или **вариант B** (добавить `vbox = { allow = true }`) |
| `user.start`                                    | добавить `user = { allow = true }` или перенести ВМ под `vbox`                                  |

ВМ должны принадлежать **тому же пользователю**, что указан в конфиге (`sudo -u vbox vboxmanage list vms` — для `vbox`).

Если указан полный путь в `VBOXAUTOSTART_CONFIG` (`/etc/vbox/autostart.conf`) — исправьте на `autostart.conf`, затем `sudo systemctl daemon-reload && sudo systemctl restart vboxautostart`.

**3. Проверьте права и профиль `vbox`:**

```bash
ls -la /etc/vbox/
sudo -u vbox VBoxManage list systemproperties | grep -i autostart
sudo -u vbox vboxmanage list vms
```

**4. Проверьте настройки ВМ** (см. таблицу проверки в §5):

```bash
ls -la /etc/vbox/
sudo -u vbox vboxmanage showvminfo "ИМЯ_ВАШЕЙ_ВМ" | grep -i autostart
sudo -u vbox grep -i autostop "/home/vbox/VirtualBox VMs/ИМЯ_ВАШЕЙ_ВМ/ИМЯ_ВАШЕЙ_ВМ.vbox"
```

**5. `No virtual machines found` или `VBoxAutostart` не поднимает ВМ** — создайте unit ([§5.2](#52-альтернатива-unit-на-каждую-вм)), пересчёт `TimeoutStopSec` ([§6](#6-настройка-обработки-физической-кнопки-питания-сервера)), включение ([§7.2](#72-через-vbox-vm-если-было-no-virtual-machines-found-или-52)); на Ubuntu `multiverse` это штатный обходной путь.

### 5.2. Альтернатива: unit на каждую ВМ

Если `VBoxAutostart` падает, пишет `No virtual machines found` при наличии `vbox.start`, или не поднимает ВМ — используйте шаблон **на каждую ВМ** (надёжнее на Ubuntu `multiverse`):

```bash
sudo nano /etc/systemd/system/vbox-vm@.service
```

```ini
[Unit]
Description=VirtualBox VM %i
After=network.target virtualbox.service vboxweb.service
Requires=virtualbox.service
Before=shutdown.target reboot.target halt.target poweroff.target
Conflicts=shutdown.target reboot.target halt.target poweroff.target

[Service]
Type=oneshot
RemainAfterExit=yes
KillMode=none
User=vbox
Group=vboxusers
Environment=HOME=/home/vbox
ExecStart=/usr/bin/VBoxManage startvm "%i" --type headless
# если ВМ уже poweroff/saved — сразу ошибка «not running», exit 0; без KillMode=none
# systemd ещё ждал бы VBoxHeadless/VBoxSVC до TimeoutStopSec
ExecStop=/bin/bash -c '/usr/bin/VBoxManage controlvm "%i" savestate || exit 0'
TimeoutStopSec=300

[Install]
WantedBy=multi-user.target
```

> **Почему так:** `Type=oneshot` — `VBoxManage startvm` не является демоном (`Type=forking` здесь ломает stop). `After=vboxweb.service` — при `reboot`/`poweroff` unit ВМ останавливается **до** `vboxweb`, иначе `controlvm savestate` получает `Failed to create the VirtualBox object` (COM уже недоступен). `Before`/`Conflicts` с `shutdown.target` и др. — ранний вход в shutdown-транзакцию. `KillMode=none`: по умолчанию (`control-group`) после `ExecStop` systemd шлёт SIGTERM процессам в cgroup (`VBoxHeadless`, общий `VBoxSVC`) и ждёт до `TimeoutStopSec` — на уже выключенной ВМ это даёт ~5 минут ожидания и SIGKILL. `ExecStop` с `|| exit 0`: при **running** — savestate (`0%…100%`); если ВМ уже выключили или сохранили в phpVirtualBox — мгновенный отказ `not running` и успешный stop unit без зависания. Проверку через `list runningvms` не используем: при сбое/пустом списке в момент stop savestate можно ошибочно пропустить.

> **Не включайте** `vbox-vm@…` на этом шаге. Пересчёт `TimeoutStopSec` (если RAM ВМ больше ~4 ГБ) — в [§6](#6-настройка-обработки-физической-кнопки-питания-сервера); включение — в [§7](#7-включение-автозапуска).

> **Уже установленный unit:** после правки файла выполните `sudo systemctl daemon-reload`. Для уже `enabled` экземпляров перезапуск unit не обязателен — новый `ExecStop`/`KillMode` подхватятся при следующем stop/reboot.

---

## 6. Настройка обработки физической кнопки питания сервера

> **Выполните этот раздел до [§7](#7-включение-автозапуска)** и до первого `reboot` / `poweroff` с запущенными ВМ. Без таймаутов systemd может прервать Save State до записи RAM на диск.

Чтобы при выключении кнопкой питания или по команде `reboot`/`poweroff` система корректно дожидалась дампа оперативной памяти ВМ на диск:

**Настройте systemd-logind:**

```bash
sudo nano /etc/systemd/logind.conf
```

Раскомментируйте или замените параметры:

```ini
HandlePowerKey=poweroff
PowerKeyIgnoreInhibited=no
```

**Задайте таймаут остановки для служб VirtualBox** (чтобы Save State успел завершиться, не замедляя shutdown остальных сервисов):

| Служба               | Где задан `TimeoutStopSec`                                                                     | Save State при shutdown                                                          | Значение по умолчанию |
| -------------------- | ---------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- | --------------------- |
| `vboxautostart`      | в unit [§5](#5-настройка-автозапуска-вм-и-сохранения-состояния-save-state), строка `[Service]` | `ExecStop` → `VBoxAutostart --stop` (учитывает `--autostop-type savestate` у ВМ) | **300s** (§7.1)       |
| `vbox-vm@ИМЯ_ВМ`     | в unit [§5.2](#52-альтернатива-unit-на-каждую-вм), строка `[Service]`                          | `ExecStop` → `VBoxManage controlvm … savestate`                                  | **300s** (§7.2)       |
| `virtualbox.service` | `/etc/systemd/system/virtualbox.service.d/timeout.conf` (штатный unit пакета не правят)        | `SHUTDOWN=savestate` из `/etc/default/virtualbox` (§2)                           | **300s** (всегда)     |
| `vboxweb.service`    | —                                                                                              | к Save State не относится                                                        | не увеличивать        |

> **Почему три службы:** при §7.1 Save State на stop делает `vboxautostart` (`ExecStop` в §5); при §7.2 — каждый `vbox-vm@…` (`ExecStop` в §5.2). `virtualbox.service` дополнительно обрабатывает `SHUTDOWN=savestate` для всех пользователей из §2 — его таймаут нужен **всегда**, независимо от §7.1/§7.2.

**Создайте drop-in для `virtualbox.service`** (обязательно — unit из пакета не редактируют):

```bash
sudo mkdir -p /etc/systemd/system/virtualbox.service.d
sudo nano /etc/systemd/system/virtualbox.service.d/timeout.conf
```

```ini
[Service]
TimeoutStopSec=300
```

**Подбор значения `TimeoutStopSec`** (ориентир для HDD ~70–100 МБ/с):

```
TimeoutStopSec ≈ (сумма RAM всех запущенных ВМ в ГБ × 12) + 60 с запаса
```

| Конфигурация               | Сумма RAM ВМ | Рекомендация                                           |
| -------------------------- | ------------ | ------------------------------------------------------ |
| 2 ВМ × ~3–4 ГБ (хост 8 ГБ) | ~6–8 ГБ      | **300s**                                               |
| 2–3 ВМ (хост 12 ГБ)        | ~8–10 ГБ     | **300–420s**                                           |
| 3–4 ВМ (хост 32 ГБ)        | ~16–24 ГБ    | **600s**                                               |
| Добавили ВМ                | пересчитать  | `TimeoutStopSec` в unit §5/§5.2 и drop-in `virtualbox` |

> **Примечание для ВМ с СУБД:** Save State «замораживает» RAM гостя целиком — при возобновлении кэш СУБД остаётся в памяти. Это существенно безопаснее обрыва питания, но не заменяет штатный `shutdown` внутри гостя при плановом обслуживании.

**Если суммарная RAM ВМ больше ~8 ГБ** — увеличьте `TimeoutStopSec` и в drop-in `virtualbox` выше, и в unit выбранного способа автозапуска:

```bash
# §7.1 — unit из §5:
sudo nano /etc/systemd/system/vboxautostart.service

# §7.2 — шаблон из §5.2:
sudo nano /etc/systemd/system/vbox-vm@.service
```

В секции `[Service]` измените строку `TimeoutStopSec=300` на значение из таблицы выше.

**Примените изменения:**

```bash
sudo systemctl daemon-reload
sudo systemctl restart systemd-logind
```

---

## 7. Включение автозапуска

> **Предусловие:** выполнен [§6](#6-настройка-обработки-физической-кнопки-питания-сервера) (`TimeoutStopSec` в unit §5/§5.2 и drop-in для `virtualbox`).

Выберите **один** способ — по результату ручной проверки `VBoxAutostart` в §5.

### 7.1. Через `vboxautostart` (если `VBoxAutostart --start` поднял ВМ)

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now vboxautostart
systemctl status vboxautostart
sudo -u vbox vboxmanage list runningvms
```

Ожидается: `active` или `active (exited)`; ВМ в `runningvms`.

### 7.2. Через `vbox-vm@…` (если было `No virtual machines found` или §5.2)

> **Критично:** в командах ниже подставьте **реальное имя ВМ** из `sudo -u vbox vboxmanage list vms` (например `Server_Autocall`). Строку `ИМЯ_ВАШЕЙ_ВМ` включать **нельзя** — systemd создаст unit `vbox-vm@ИМЯ_ВАШЕЙ_ВМ`, на splash будет `failed to start vbox-vm@\xd0\x98…`, а нужная ВМ при этом может стартовать отдельно.

```bash
sudo systemctl daemon-reload
# Пример: sudo systemctl enable --now vbox-vm@Server_Autocall.service
sudo systemctl enable --now vbox-vm@ИМЯ_ВАШЕЙ_ВМ.service
systemctl status vbox-vm@ИМЯ_ВАШЕЙ_ВМ.service
sudo -u vbox vboxmanage list runningvms
```

Ожидается: `active (exited)` (для `Type=oneshot` это нормально) или `active`; ВМ в `runningvms`.

**Проверка Save State без reboot:**

```bash
sudo systemctl stop vbox-vm@ИМЯ_ВАШЕЙ_ВМ.service
sudo -u vbox vboxmanage showvminfo "ИМЯ_ВАШЕЙ_ВМ" --machinereadable | grep '^VMState='
# нужно: VMState="saved"
sudo systemctl start vbox-vm@ИМЯ_ВАШЕЙ_ВМ.service
```

Если после `stop` — `Failed to create the VirtualBox object` или не `saved`: сверьте unit с [§5.2](#52-альтернатива-unit-на-каждую-вм) (`Type=oneshot`, `After=… vboxweb.service`).

> **Не включайте оба способа** для одной и той же ВМ — выберите либо §7.1, либо §7.2. Если ошибочно выполнили команды с литералом `ИМЯ_ВАШЕЙ_ВМ` / `НОВАЯ_ВМ` — снятие и проверка: [§9.4](#94-ошибочно-использован-плейсхолдер-мануала).

---

## 8. Итоговая проверка

Выполните на сервере — все службы должны быть `active`:

```bash
systemctl is-active virtualbox vboxweb docker
systemctl is-active vboxautostart 2>/dev/null || systemctl is-active vbox-vm@ИМЯ_ВАШЕЙ_ВМ.service
docker compose -f ~/phpvirtualbox/docker-compose.yml ps
sudo -u vbox vboxmanage list runningvms
ls -la /etc/vbox/
```

| Компонент          | Команда проверки                                                                                                             | Ожидаемый результат                                  |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| Драйвер VirtualBox | `systemctl is-active virtualbox`                                                                                             | `active`                                             |
| Web API            | `ss -tlnp \| grep 18083`                                                                                                     | порт слушает                                         |
| phpVirtualBox      | `curl -s -o /dev/null -w "%{http_code}" http://127.0.0.1:8080/`                                                              | `200` или `302`                                      |
| Docker             | `docker ps`                                                                                                                  | контейнер `phpvirtualbox` running                    |
| Автозапуск ВМ      | `vboxautostart` (**§7.1**) **или** `vbox-vm@ИМЯ_ВМ` (**§7.2**)                                                               | `active` / `active (exited)`; ВМ в `list runningvms` |
| Save State         | `ls /etc/vbox/vbox.stop` + `autostop` в `.vbox`                                                                              | файлы на месте                                       |
| Таймауты §6        | `grep TimeoutStopSec /etc/systemd/system/vbox-vm@.service /etc/systemd/system/virtualbox.service.d/timeout.conf 2>/dev/null` | `300` (или ваше значение)                            |

**Проверка после первого выключения или перезагрузки** (выполните `sudo reboot` или `sudo poweroff`, затем на следующей сессии). Reboot с успешным Save State займёт заметное время (запись RAM ВМ на диск); мгновенный reboot при запущенной ВМ — признак сбоя savestate.

```bash
journalctl -b -1 -u 'vbox-vm@ИМЯ_ВАШЕЙ_ВМ' --no-pager | tail -20
# или при §7.1:
# journalctl -b -1 -u vboxautostart -u virtualbox --no-pager | tail -40

sudo -u vbox vboxmanage list runningvms
```

| Лог                                                                                           | Ожидаемый результат                                                 |
| --------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| `0%...100%` и `Deactivated successfully`                                                      | ✅ Save State прошёл (ВМ была running)                              |
| сразу `is not currently running`, затем сразу Deactivated                                     | ✅ ВМ уже была poweroff/saved в UI; `\|\| exit 0` + `KillMode=none` |
| сразу `is not currently running`, затем пауза до `/ 5min`, `Killing … VBoxHeadless`/`VBoxSVC` | ❌ нет `KillMode=none` — обновите unit §5.2                         |
| `Failed to create the VirtualBox object` / COM                                                | ❌ unit устарел или порядок stop сломан — см. §5.2                  |
| `TimeoutStopSec` / `SIGKILL` при **работающей** ВМ и обрыве `0%…`                             | ❌ мало времени на savestate — увеличьте `TimeoutStopSec` (§6)      |

Порядок на успешном shutdown (§7.2): сначала stop `vbox-vm@…` (savestate), затем `vboxweb`, затем `virtualbox` (выгрузка модулей).

---

## 9. Добавление, отключение и удаление ВМ

Ниже — эксплуатация после первичной настройки. Имена ВМ берите из `sudo -u vbox vboxmanage list vms` (не из плейсхолдеров мануала).

### 9.1. Добавление новой ВМ

1. Создайте ВМ **от пользователя `vbox`** (phpVirtualBox или `VBoxManage`).
2. Включите флаги Save State / автозапуск (§5), ВМ должна быть **выключена**:

```bash
sudo -u vbox vboxmanage modifyvm "НОВАЯ_ВМ" --autostart-enabled on
sudo -u vbox vboxmanage modifyvm "НОВАЯ_ВМ" --autostop-type savestate
```

3. **Способ автозапуска:**

| Способ                                                       | Действие                                                                                                                      |
| ------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| **§7.2** (`vbox-vm@…`, рекомендуется на Ubuntu `multiverse`) | `sudo systemctl enable --now vbox-vm@НОВАЯ_ВМ.service` — шаблон `vbox-vm@.service` из §5.2 уже есть, новый unit-файл не нужен |
| **§7.1** (`vboxautostart`)                                   | отдельный unit не создают; достаточно флагов выше и рабочего `VBoxAutostart`                                                  |

4. Пересчитайте `TimeoutStopSec` (§6) по **сумме RAM всех ВМ, которые будут запущены одновременно**, и обновите значение в unit §5.2 / §5 и в drop-in `virtualbox`.
5. Проверьте Save State без reboot (§7.2) и при необходимости — одним `sudo reboot` (§8).

> **RAM хоста:** не планируйте автозапуск всех ВМ сразу, если их суммарная память близка к RAM сервера. Лишние ВМ оставляйте без `enable` (см. §9.2).

### 9.2. Временно отключить ВМ (оставить на диске)

Типичный случай: старая рабочая ВМ не должна стартовать при boot, пока тестируете новую — файлы и регистрация в VirtualBox сохраняются.

**§7.2:**

```bash
sudo systemctl disable --now vbox-vm@СТАРАЯ_ВМ.service
sudo systemctl reset-failed vbox-vm@СТАРАЯ_ВМ.service 2>/dev/null || true
```

По желанию уберите флаг автозапуска VirtualBox (на `vbox-vm@` не влияет, но полезно при §7.1 / phpVirtualBox):

```bash
sudo -u vbox vboxmanage modifyvm "СТАРАЯ_ВМ" --autostart-enabled off
```

ВМ остаётся в списке phpVirtualBox / `list vms`; запуск вручную:

```bash
sudo -u vbox vboxmanage startvm "СТАРАЯ_ВМ" --type headless
# или: sudo systemctl start vbox-vm@СТАРАЯ_ВМ.service
```

Вернуть автозапуск при boot: `sudo systemctl enable --now vbox-vm@СТАРАЯ_ВМ.service` (и при необходимости снова `--autostart-enabled on`).

**§7.1:** достаточно `modifyvm … --autostart-enabled off` (и обратно `on`); unit `vboxautostart` не трогайте, если другие ВМ ещё на нём.

### 9.3. Удаление ВМ

1. Снимите автозапуск (§9.2): `disable --now vbox-vm@…` (и/или `--autostart-enabled off`).
2. Удалите ВМ в phpVirtualBox **или** из CLI (ВМ не должна быть `running`):

```bash
sudo -u vbox vboxmanage unregistervm "СТАРАЯ_ВМ" --delete
```

`--delete` удаляет и регистрацию, и файлы дисков в каталоге ВМ. Без `--delete` — только unregister, файлы на диске останутся.

3. При необходимости: `sudo systemctl reset-failed vbox-vm@СТАРАЯ_ВМ.service`.
4. Пересчитайте `TimeoutStopSec` (§6) под оставшиеся ВМ.

> **Не путать:** `systemctl disable` — только «не стартовать при boot»; `unregistervm --delete` — безвозвратное удаление ВМ с диска.

### 9.4. Ошибочно использован плейсхолдер мануала

Типичный случай: в команды скопировали текст-заглушку из мануала (`ИМЯ_ВАШЕЙ_ВМ`, `НОВАЯ_ВМ`, `СТАРАЯ_ВМ`) вместо имени из `list vms`.

| Симптом                                                          | Значение                                                                              |
| ---------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| `Невозможно найти зарегистрированную машину с именем 'НОВАЯ_ВМ'` | в VirtualBox такой ВМ нет — это хорошо; `modifyvm` / `unregistervm` ничего не сломали |
| `Invalid unit name … escaped as "vbox-vm@\xd0\x9d…"`             | systemd экранирует кириллицу; unit мог быть `enable`/`disable` с этим именем          |
| На splash: `failed to start vbox-vm@\xd0\x98…`                   | в автозагрузке остался плейсхолдер                                                    |

**Снятие** (подставьте тот же литерал, что ошибочно использовали):

```bash
sudo systemctl disable --now 'vbox-vm@НОВАЯ_ВМ.service'
sudo systemctl reset-failed 'vbox-vm@НОВАЯ_ВМ.service' 2>/dev/null || true
```

Сообщения `Invalid unit name … escaped` при `disable` часто бывают — команда при этом может отработать.  
`reset-failed: Unit … not loaded` — **нормально**, если unit уже не в failed/loaded (чистить нечего).

`unregistervm` для плейсхолдера **не нужен** (ВМ в VirtualBox не создавалась). Не удаляйте реальные ВМ из `list vms`.

**Проверка, что след плейсхолдера убран:**

```bash
# 1) В VirtualBox — только реальные имена
sudo -u vbox vboxmanage list vms

# 2) Автозагрузка systemd — только нужные enable
ls -la /etc/systemd/system/multi-user.target.wants/ | grep vbox-vm

# 3) Загруженные/failed unit'ы экземпляров
systemctl list-units 'vbox-vm@*' --all

# 4) Статус enable у реальных ВМ (подставьте свои имена)
systemctl is-enabled vbox-vm@Server_Autocall vbox-vm@NK_Server 2>&1
```

| Проверка                  | Ожидаемый результат                                                                            |
| ------------------------- | ---------------------------------------------------------------------------------------------- |
| `list vms`                | нет строк `НОВАЯ_ВМ` / `ИМЯ_ВАШЕЙ_ВМ` / `СТАРАЯ_ВМ`                                            |
| `multi-user.target.wants` | только `vbox-vm@РеальнаяВМ.service` для ВМ с автозапуском; **нет** ссылок с плейсхолдером      |
| `list-units 'vbox-vm@*'`  | нет `failed` и нет unit с `\xd0…` / плейсхолдером; рабочие — `active`/`exited` или отсутствуют |
| `is-enabled`              | `enabled` только у нужных ВМ; остальные `disabled` / `not-found`                               |

Пример «чисто» (одна ВМ в автозапуске): в `wants` одна ссылка `vbox-vm@Server_Autocall.service`; в `list-units` только она; `NK_Server` — `disabled`, если её специально не включали (§9.2).

Если в `wants` всё ещё видна ссылка с плейсхолдером — удалите **только её** (не трогайте `Server_Autocall` и др.):

```bash
ls /etc/systemd/system/multi-user.target.wants/ | grep vbox-vm
# sudo rm -f '/etc/systemd/system/multi-user.target.wants/vbox-vm@НОВАЯ_ВМ.service'
sudo systemctl daemon-reload
```

Повторите блок проверки выше.

---

## 10. Сквозной (raw) диск и режим Writethrough

Сквозной диск даёт ВМ прямой доступ к физическому диску или разделу хоста (быстрее обычного `.vdi`, можно загружать ту же ОС «на железе» и в ВМ). Основано на подходе из [статьи MNorin](https://mnorin.com/ispol-zovanie-fizicheskogo-diska-v-virtualbox.html), адаптировано под пользователя `vbox`, phpVirtualBox и systemd из этого мануала.

> **Опасность:** ошибка в выборе устройства уничтожит данные хоста или гостя. Используйте **отдельный** диск/раздел, **не** загрузочный диск Ubuntu Server. Ниже плейсхолдеры `ДИСК` (например `sdb`) и `ИМЯ.vmdk` — подставьте свои значения после `lsblk`.

### 10.1. Определите устройство и каталог для VMDK

```bash
lsblk -o NAME,SIZE,TYPE,MOUNTPOINT,MODEL
sudo mkdir -p /home/vbox/hdd
sudo chown vbox:vboxusers /home/vbox/hdd
sudo chmod 775 /home/vbox/hdd
```

Убедитесь, что выбранный `/dev/ДИСК` **не** смонтирован как корень/boot хоста и не содержит нужных разделов хоста.

### 10.2. Права доступа к физическому диску

Пользователь `vbox` должен читать и писать устройство (иначе `VERR_ACCESS_DENIED`):

```bash
# сразу (до перезагрузки)
sudo chown root:vboxusers /dev/ДИСК
sudo chmod 660 /dev/ДИСК

# постоянно — udev (подставьте имя устройства, напр. sdb)
sudo tee /etc/udev/rules.d/99-vbox-rawdisk.rules >/dev/null <<'EOF'
KERNEL=="ДИСК", GROUP="vboxusers", MODE="0660"
EOF
sudo udevadm control --reload-rules
sudo udevadm trigger
ls -l /dev/ДИСК
```

Ожидается: группа `vboxusers`, режим `0660`.

### 10.3. Создайте raw-VMDK

Команде часто нужны права root на чтение `/dev/ДИСК`. Файл `.vmdk` и реестр медиа должны принадлежать **`vbox`** (владелец ВМ), иначе phpVirtualBox получит отказ в доступе.

```bash
# удалите старые заготовки с тем же именем, если есть
sudo rm -f /home/vbox/hdd/ИМЯ.vmdk /home/vbox/hdd/ИМЯ-*.vmdk

# классический способ (как в статье MNorin)
sudo VBoxManage internalcommands createrawvmdk \
  -filename /home/vbox/hdd/ИМЯ.vmdk \
  -rawdisk /dev/ДИСК

# права на файл-указатель
sudo chown vbox:vboxusers /home/vbox/hdd/ИМЯ.vmdk
sudo chmod 660 /home/vbox/hdd/ИМЯ.vmdk
ls -la /home/vbox/hdd/
```

Альтернатива (VirtualBox 7.x):

```bash
sudo -u vbox VBoxManage createmedium disk \
  --filename /home/vbox/hdd/ИМЯ.vmdk \
  --format=VMDK --variant=RawDisk \
  --property RawDrive=/dev/ДИСК
```

(если `createmedium` не может открыть `/dev/ДИСК` — сначала убедитесь в §10.2, либо создайте от root и сразу `chown` на `vbox`.)

Только раздел диска (не весь диск) — через параметр разделов (см. [Advanced Topics](https://www.virtualbox.org/manual/topics/AdvancedTopics.html)), например `Partitions=1,5` в `createmedium`.

### 10.4. Подключите диск к ВМ и включите Writethrough

ВМ должна быть **выключена** (`poweroff` / `saved`, не `running`).

**Через phpVirtualBox:**

1. Settings → Storage → Controller: SATA (или IDE) → Add Hard Disk → выберите `/home/vbox/hdd/ИМЯ.vmdk`.
2. File → Virtual Media Manager → выберите этот носитель → Modify → тип **Writethrough** → OK.

**Через CLI:**

```bash
sudo -u vbox VBoxManage storageattach "ИМЯ_ВАШЕЙ_ВМ" \
  --storagectl "SATA" --port 1 --device 0 --type hdd \
  --medium /home/vbox/hdd/ИМЯ.vmdk

sudo -u vbox VBoxManage modifymedium disk /home/vbox/hdd/ИМЯ.vmdk --type writethrough
sudo -u vbox VBoxManage list hdds | grep -A5 ИМЯ
```

Имя контроллера (`SATA` / `SATA Controller`) смотрите в `showvminfo` / phpVirtualBox; порт не должен совпадать с уже занятым.

> **Writethrough:** запись гостя идёт сразу на физический носитель; снимки (snapshots) для такого диска недоступны в обычном смысле. Save State ВМ (§5–§8) по-прежнему сохраняет RAM, но **не** заменяет согласованное выключение гостя при работах с СУБД на raw-диске.

### 10.5. Устранение проблем

**`VERR_ACCESS_DENIED` / «Проблемы с разрешениями доступа к файлу для носителя …vmdk»**

```bash
ls -l /home/vbox/hdd/ИМЯ.vmdk /dev/ДИСК
id vbox
# vbox должен быть в vboxusers; .vmdk — vbox:vboxusers; /dev/ДИСК — группа vboxusers, 0660
sudo systemctl restart vboxweb
```

**Файл `.vmdk` удалили (`rm`), а VirtualBox помнит UUID** (`NS_ERROR_INVALID_ARG`, дубликат пути/UUID при `createrawvmdk`):

Реестр медиа — у пользователя **`vbox`** (`~vbox/.config/VirtualBox/` или `~vbox/.VirtualBox/`), не у root (если только вы сами не создавали диски от root).

```bash
# список носителей и UUID
sudo -u vbox VBoxManage list hdds

# отвязать «мертвую» запись (UUID из ошибки / list hdds)
sudo -u vbox VBoxManage closemedium disk UUID_СТАРЫЙ
# если файл ещё числится на диске: добавьте --delete

sudo rm -f /home/vbox/hdd/ИМЯ.vmdk /home/vbox/hdd/ИМЯ-*.vmdk
# затем снова §10.3 и §10.4
sudo systemctl restart vboxweb
```

Если носитель создавали **от root** и реестр «залип» у root:

```bash
sudo VBoxManage closemedium disk UUID_СТАРЫЙ
sudo rm -f /home/vbox/hdd/ИМЯ*
# пересоздайте §10.3, сразу chown на vbox, дальше только sudo -u vbox
```

**Не используйте** `pkill vboxwebsrv` / ручной `su - vbox -c "vboxwebsrv …"` — в этом мануале веб-служба управляется unit из §3: `sudo systemctl restart vboxweb`.

---

Готово. Сервер развёрнут с нуля: VirtualBox и Docker установлены, phpVirtualBox доступен по HTTP, ВМ автозапускаются и сохраняют состояние при выключении — с таймаутами, заданными только для служб VirtualBox. Добавление и вывод ВМ — [§9](#9-добавление-отключение-и-удаление-вм); сквозной диск — [§10](#10-сквозной-raw-диск-и-режим-writethrough); при изменении набора ВМ пересчитайте `TimeoutStopSec` по таблице в §6.
