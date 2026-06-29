# SmartComm Services Catalog

Каталог сервисов для [SmartComm Dashboard](https://github.com/moshonkinaa/smartcomm-dashboard) — self-hosted порталов на Raspberry Pi 5 и x86-неттопах (MSI Cubi 5 и др.).

Каждый сервис — YAML-манифест в `services/<id>.yaml`, содержит метаданные, требования к платформе, и `docker-compose` для разворачивания.

## Структура манифеста (schema v1)

```yaml
schema_version: 1                  # required
id: jellyfin                       # required, kebab-case, unique
name: Jellyfin                     # required, human display name
tagline: Netflix дома              # required, одна строка для карточки
category: media                    # required: vpn | ai | media | photo | cloud | security | smart-home | automation | admin
icon: jellyfin                     # optional, имя иконки или emoji
client_facing: true                # видим ли клиенту (false = только админу-инсталлятору)
suspicious: false                  # требует особой осторожности — VPN, surveillance и т.п.

requirements:
  min_ram_mb: 2048                 # required, минимум RAM для контейнера
  min_disk_gb: 50                  # required, диск под данные + образ
  platforms: [x86_64, arm64]       # required, какие архитектуры поддерживаются
  cpu_cores: 2                     # рекомендуемое количество ядер
  needs_internet: false            # для работы (для install всегда true)
  needs_ports: [8096]              # обязательные порты
  optional_ports: [8920]           # опциональные (HTTPS и т.п.)

description: |
  Полное описание сервиса.
  Markdown поддерживается, отображается в модалке детали.

features:
  - Стриминг 4K на Smart TV
  - Hardware decoding Intel QuickSync
  - Apps для iOS / Android / Roku / Apple TV

pricing_hint_rub: 500              # ₽/мес ориентир для клиента (опционально, для monetization)
docs_url: https://jellyfin.org/docs/

post_install: |
  1. Открой http://CONTROLLER:8096
  2. Создай админ-аккаунт
  3. Добавь библиотеку фильмов из /media

compose: |
  services:
    jellyfin:
      image: jellyfin/jellyfin:10.9
      restart: unless-stopped
      ports:
        - "8096:8096"
      volumes:
        - "{DATA}/config:/config"
        - "{DATA}/cache:/cache"
      environment:
        - "TZ=Europe/Moscow"
```

## Переменные в `compose`

В разделе `compose` доступны placeholder'ы которые дашборд заменяет при установке:

- `{DATA}` — `/var/lib/smartcomm-services/<id>/` — изолированная папка данных сервиса
- `{MEDIA}` — `/var/lib/smartcomm-services/_media/` — общий media-pool
- `{TZ}` — текущая timezone (`Europe/Moscow`)

## Категории

| ID | Иконка | Название |
|---|---|---|
| `vpn` | 🔐 | VPN и обход блокировок |
| `ai` | 🤖 | Искусственный интеллект |
| `media` | 🎬 | Стриминг и медиа |
| `photo` | 📸 | Фото |
| `cloud` | ☁️ | Облако и продуктивность |
| `security` | 🛡️ | Безопасность и приватность |
| `smart-home` | 🏠 | Умный дом |
| `automation` | ⚙️ | Автоматизация |
| `admin` | 🛠️ | Админские инструменты (только инсталлятору) |

## Платформы

- `x86_64` — Intel/AMD (MSI Cubi, NUC, любые мини-ПК)
- `arm64` — Raspberry Pi 5 + другие ARMv8 SBC

Если ARM не указан — сервис не показывается на Pi.

## Лицензия

CC0 — манифесты в public domain. Лицензии самих сервисов — см. их репозитории.
