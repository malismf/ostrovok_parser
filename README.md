## Структура репозитория

```
.
├── ostrovok_hotels.py        # Шаг 1: парсинг списка отелей + каталог
├── ostrovok_rooms.py         # Шаг 2: парсинг номеров и цен
├── ostrovok_statistic.py     # Шаг 3: сводная статистика за день
├── capacity_utils.py         # Оценка вместимости номера
├── log_config.py             # Логирование + Telegram-уведомления
├── daily/
│   ├── hotels/               # YYYY-MM-DD.csv — отели за день
│   ├── rooms/                # YYYY-MM-DD.csv — номера за день
│   └── statistics/           # YYYY-MM-DD.csv — статистика за день
├── catalog/
│   └── hotels.csv            # Накопительный каталог отелей
├── logs/                     # Логи запусков
└── .github/workflows/
    └── ostrovok-parser.yml   # Ежедневный запуск в GitHub Actions
```

## Форматы данных

### `daily/hotels/YYYY-MM-DD.csv`

| Колонка | Описание |
|---|---|
| `city` | Город |
| `ota_hotel_id` | Идентификатор отеля в Ostrovok |
| `master_id` | Master ID отеля |
| `name` / `name_en` | Название (рус./англ.) |
| `address` | Адрес |
| `latitude` / `longitude` | Координаты |
| `url` | Ссылка на страницу отеля |
| `rooms_number` | Общее число номеров в отеле |

### `daily/rooms/YYYY-MM-DD.csv`

| Колонка | Описание |
|---|---|
| `ota_hotel_id`, `master_id` | Идентификаторы отеля |
| `room_name` | Название номера |
| `rg_hash` | Хеш группы номеров (ключ группировки) |
| `count_rg_hash` | Сколько тарифов объединено в группу |
| `allotment` | Кол-во свободных номеров этого типа |
| `bedding_type`, `beds`, `bedding_data`, `multi_bed_data` | Информация о кроватях |
| `capacity` | Оценённая вместимость одного номера (гостей) |
| `price_rub_min` / `price_rub_max` | Мин./макс. цена за ночь, ₽ |
| `url` | Ссылка на страницу отеля |

Если по отелю API не вернул тарифов, в файл пишется строка-заглушка с пустым `rg_hash` — для контроля пропусков.

### `daily/statistics/YYYY-MM-DD.csv`

| Колонка | Описание |
|---|---|
| `ota_hotel_id`, `name` | Отель |
| `date` | Дата сбора |
| `rooms_num` | Всего номеров в отеле |
| `free_rooms_amount` | Сумма allotment по всем типам номеров |
| `max_capacity` | Суммарная вместимость свободных номеров |
| `available_rooms_percent` | Доля свободных номеров, % |
| `min_price` | Минимальная цена за ночь, ₽ |

### `catalog/hotels.csv`

Те же поля, что и в дневном файле отелей, плюс `first_seen_date` и `last_seen_date` — когда отель впервые и в последний раз встречался в выдаче.

