---
title: Активация CloudFlare Warp
---

# Активация CloudFlare Warp

С помощью этого руководства вы можете устранить некоторые ограничения, наложенные на ваш IP крупными компаниями, такими как Google и Spotify, и без проблем пользоваться их сервисами.

::: warning
Обратите внимание: для конфигураций Warp существует ограничение на одновременное подключение максимум 5 устройств. Для обхода этого ограничения вы можете использовать несколько конфигураций.
:::

## Первый шаг: Создание конфигурации Wireguard

### Способ первый: Использование Windows

- Сначала скачайте необходимый `Asset` из раздела [releases](https://github.com/ViRb3/wgcf/releases); этот файл различается в зависимости от процессора.
- Переименуйте файл `Asset` в `wgcf`.
- Затем в адресной строке Проводника введите `cmd.exe`.

![image](https://github.com/Gozargah/gozargah.github.io/assets/50927468/fb9f3eae-8390-45a5-a7b3-c50db4aa82a1)

- В открывшемся терминале введите `wgcf.exe`.
- Выполните команду `wgcf.exe register`, а затем `wgcf.exe generate`.
- Будет создан новый файл с именем `wgcf-profile.conf`, который является необходимой конфигурацией для Wireguard.
- Ваша конфигурация готова, и вы можете её использовать.

### Способ второй: Использование Linux

- Сначала скачайте необходимый `Asset` из раздела [releases](https://github.com/ViRb3/wgcf/releases); этот файл различается в зависимости от процессора.
- Эту задачу можно выполнить с помощью команды `wget`.

Для процессоров с архитектурой AMD64:
```bash
wget https://github.com/ViRb3/wgcf/releases/download/v2.2.22/wgcf_2.2.22_linux_amd64
```
Для процессоров с архитектурой ARM64:
```bash
wget https://github.com/ViRb3/wgcf/releases/download/v2.2.22/wgcf_2.2.22_linux_arm64
```
Переместите файл в каталог `/usr/bin/` и переименуйте его в `wgcf`.

Для процессоров с архитектурой AMD64:
```bash
mv wgcf_2.2.22_linux_amd64 /usr/bin/wgcf
chmod +x /usr/bin/wgcf
```
Для процессоров с архитектурой ARM64:
```bash
mv wgcf_2.2.22_linux_arm64 /usr/bin/wgcf
chmod +x /usr/bin/wgcf
```
Затем создайте конфигурацию, выполнив следующие две команды:
```bash
wgcf register
wgcf generate
```
Будет создан файл с именем `wgcf-profile.conf`, который является необходимой конфигурацией.

## Второй шаг: Использование Warp+ (необязательно)

- Чтобы получить лицензию и использовать Warp+, вы можете обратиться к [этому](https://t.me/generatewarpplusbot) Telegram-боту для получения `license_key`.
- После получения `license_key` замените его в файле `wgcf-account.toml`.
::: tip Примечание
Эту замену можно выполнить в Linux с помощью `nano` и в Windows с помощью `Notepad` или любого другого текстового редактора.
:::
::: details Windows
Для использования команд в Windows вместо команды `wgcf` используйте `wgcf.exe`.
:::
Затем обновите информацию конфигурации.
```bash
wgcf update
```
После этого создайте новый конфигурационный файл.
```bash
wgcf generate
```

## Третий шаг: Активация Warp на панели Marzban

### Способ первый: Использование ядра Xray

::: warning Внимание
- Этот метод рекомендуется только для версии Xray 1.8.3 или выше; в более ранних версиях может возникнуть проблема утечки памяти.
- Если ваша версия `Xray` ниже указанной, вы можете обновить её с помощью [руководства по смене версии Xray-core](/examples/change-xray-version).
:::

- Перейдите в раздел Core Setting на панели Marzban.
- Сначала добавьте outbound, как в примере, и вставьте туда данные из файла `wgcf-profile.conf`.

```json
{
  "tag": "warp",
  "protocol": "wireguard",
  "settings": {
    "secretKey": "Your_Secret_Key",
    "DNS": "1.1.1.1",
    "address": ["172.16.0.2/32", "2606:4700:110:8756:9135:af04:3778:40d9/128"],
    "peers": [
      {
        "publicKey": "bmXOC+F1FxEMF9dyiK2H5/1SUtzH0JuVo51h2wPfgyo=",
        "endpoint": "engage.cloudflareclient.com:2408"
      }
    ],
    "kernelMode": false
  }
}
```

::: tip Примечание
Если вы хотите, чтобы весь трафик по умолчанию проходил через Warp, разместите этот outbound первым, и следующий шаг можно пропустить.
:::

### Способ второй: Использование ядра Wireguard

Сначала установите предварительные требования для использования Wireguard на сервере.

```bash
sudo apt install wireguard-dkms wireguard-tools resolvconf
```
Если вы используете Ubuntu 24, для установки Wireguard используйте следующую команду.
```bash
sudo apt install wireguard
```
Затем добавьте строку `Table = off` в файл Wireguard, как в примере.

```conf
[Interface]
PrivateKey = Your_Private_Key
Address = 172.16.0.2/32
Address = 2606:4700:110:8a1a:85ef:da37:b891:8d01/128
DNS = 1.1.1.1
MTU = 1280
Table = off
[Peer]
PublicKey = bmXOC+F1FxEMF9dyiK2H5/1SUtzH0JuVo51h2wPfgyo=
AllowedIPs = 0.0.0.0/0
AllowedIPs = ::/0
Endpoint = engage.cloudflareclient.com:2408
```

::: warning Внимание
Если не добавить строку `Table = off`, доступ к серверу может быть прерван, и вы не сможете подключиться к нему. В таком случае вам придется зайти через веб-сайт вашего дата-центра и отключить соединение с `Warp`, чтобы восстановить нормальное соединение.
:::

- Переименуйте файл `wgcf-profile.conf` в `warp.conf`.
- Переместите файл в каталог `/etc/wireguard` на сервере.

```bash
sudo mv wgcf-profile.conf /etc/wireguard/warp.conf
```
- Активируйте Wireguard с помощью следующей команды.

```bash
sudo systemctl enable --now wg-quick@warp
```

Для отключения `Warp` используйте следующую команду:

```bash
sudo systemctl disable --now wg-quick@warp
```

- Перейдите в раздел Core Setting на панели Marzban.
- Добавьте outbound, как в примере.

```json
{
  "tag": "warp",
  "protocol": "freedom",
  "streamSettings": {
    "sockopt": {
      "tcpFastOpen": true,
      "interface": "warp"
    }
  }
}
```

::: tip Примечание
Если вы хотите, чтобы весь трафик по умолчанию проходил через Warp, разместите этот outbound первым, и следующий шаг можно пропустить.
:::

## Четвертый шаг: Настройка раздела маршрутизации (routing)

Сначала добавьте правило в разделе `routing`, как в примере.

```json
{
  "outboundTag": "warp",
  "domain": [],
  "type": "field"
}
```

Теперь добавьте желаемые веб-сайты, как в примере.

```json
{
    "outboundTag": "warp",
    "domain": [
        "geosite:google",
        "openai.com",
        "ai.com",
        "ipinfo.io",
        "iplocation.net",
        "spotify.com"
    ],
    "type": "field"
}
```

Сохраните изменения, и теперь вы можете использовать `Warp`.
::: details Marzban-Node

- Если вы используете `Warp` с помощью ядра Xray, изменения в ноде не требуются — они применяются автоматически.

- Если вы используете ядро `Wireguard`, необходимо повторить шаг третий, способ второй, и на ноде.
:::
