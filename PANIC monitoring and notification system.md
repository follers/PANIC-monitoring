## Система мониторинга и уведомлений PANIC

PANIC - это система мониторинга и оповещения с открытым исходным кодом для узлов на базе Cosmos-SDK, Substrate и Chainlink от компании Simply VC. Инструмент был создан с учетом потребностей пользователей и имеет множество функций, таких как телефонные звонки для критических оповещений, установку с помощью Web-UI и оповещениями в  Telegram/Slack.

#### Установка Git
*Пропускаем если Git уже установлен.*

  ```bash
  # ставим git
  sudo apt install git
  
  # проверяем
  git --version
  ```
#### Установка Docker и Docker Compose

Пропускаем если Docker и Docker Compose уже установлен

```bash
# ставим docker и docker-compose
curl -sSL https://get.docker.com/ | sh
sudo apt install docker-compose -y

# проверяем
docker --version
docker-compose --version
```

#### Установка конфигурации

```bash
# клонируем репо и переходим в папку
git clone https://github.com/SimplyVC/panic
cd panic
```

В каталоге `panic` редактируем параметры в файле `.env`

- INSTALLER_USERNAME: пользователь

- INSTALLER_PASSWORD: пароль

- UI_ACCESS_IP: IP адрес хоста на котором установлен PANIC (доступны скрипты scripts/get_ip_linux.sh и scripts/get_ip_mac.sh для определения ip адреса)

```ini
UI_ACCESS_IP=1.1.1.1
INSTALLER_USERNAME=panic_user
INSTALLER_PASSWORD=panic_password
```

#### Запускаем Panic

После настройки всех параметров запускаем Panic следующей командой:

```bash
docker-compose up -d --build
```
Если сборка заканчивается неудачно, удаляем образы docker следующей командой, устараняем причину сбоя и запускаем сборку вновь:

```bash
docker-compose kill
docker system prune -a --volumes
docker-compose up -d --build
```

Убедиться в том что система функционирует правильно можно с помощью команд `docker-compose logs -f alerter` и `docker-compose logs -f health-checker`

Лог файлы находятся в каталоге `panic/alerter/logs`

#### Настройка уведомлений Telegram

Для создания **Telegram бота**, добавляем [@BotFather](https://telegram.me/BotFather) в Telegram, нажимаем Start, и выполняем следующие шаги:

1. Отправляем команду `/newbot` и указываем запрашиваемые данные включая имя бота и имя пользователя

2. Записываем API токен, который выглядит как:`111111:AAA-AAA111111-aaaaa11111`

3. Переходим по ссылке `t.me/<username>` для доступа к созданному боту и нажимаем `Start`.

4. Переходим по ссылке `api.telegram.org/bot<token>/getUpdates`, заменив `<token>` полученным токеном бота. Открывается список активностей бота, включающий сообщения отправленные ему

5. В списке должно быть по меньшей мере одно сообщение, вызванное нажатием кнопки Start. Если его нет, отправьте боту команду `/start`. Фиксируем значение `"id"` в разделе `"chat"`

6. На этом создание бота завершено, для создания еще одного бота, повторяем описанные выше шаги еще раз

   **В результате мы должны получить:**

   - Аккаунт Telegram
   - Telegram бота
   - API токен Telegram бота
   - Идентификатор чата (`chat ID`) 

#### Конфигурирование мониторинга и оповещений

Настройка мониторинга узлов и каналов оповещения производится по адресу `https://{UI_ACCESS_IP}:8000`. Для авторизации используем `INSTALLER_USERNAME` и `INSTALLER_PASSWORD` которые мы указывали в файле `.env`. 

После авторизации запускается мастер мониторинга. Все необходимые действия интуитивно понятны, и детально прокомментированы.

