# Задание 3. Архитектура PUSH-уведомлений

## 1. Описание задачи

Необходимо построить верхнеуровневую архитектурную схему для отправки PUSH-уведомлений в мобильное приложение интернет-магазина «Петрушка Зеленая».

По условию, PUSH-уведомления могут быть разных типов:

- напоминание о товарах, которые долго лежат в корзине без действий;
- уведомление об отмене заказа;
- рекламные рассылки;
- другие уведомления, которые могут появиться позже.

На backend используется микросервисная архитектура.

---

## 2. Основная идея решения

Для отправки PUSH-уведомлений предлагается выделить отдельный `Notification Service`.

Бизнес-сервисы не должны самостоятельно отправлять PUSH в мобильное приложение. Вместо этого они передают в `Notification Service` данные о событии, по которому нужно создать уведомление.

`Notification Service` отвечает за:

- получение заявки на отправку уведомления;
- определение пользователя-получателя;
- получение актуального device token пользователя;
- выбор шаблона уведомления;
- сохранение уведомления и статуса отправки;
- отправку PUSH через внешний push-провайдер;
- обработку ошибок и повторные попытки отправки.

В качестве внешнего push-провайдера может использоваться FCM для Android и APNs для iOS.

---

## 3. Компоненты архитектуры

| Компонент | Назначение |
|---|---|
| `Mobile App` | Получает device token от FCM/APNs, передает его на backend и отображает PUSH-уведомления пользователю |
| `API Gateway / BFF` | Принимает запросы от мобильного приложения и передает их во внутренние сервисы |
| `User Service` | Хранит данные пользователя и активные device token |
| `Cart Service` | Формирует событие для уведомления о брошенной корзине |
| `Order Service` | Формирует события по заказам: отмена заказа, изменение статуса заказа |
| `Marketing Service` | Формирует рекламные PUSH-уведомления |
| `Notification Service` | Центральный сервис для подготовки и отправки PUSH-уведомлений |
| `Notification DB` | Хранит уведомления, статусы отправки, ошибки и историю попыток |
| `Templates` | Хранит шаблоны текстов уведомлений |
| `FCM / APNs` | Внешние push-провайдеры для доставки уведомлений на устройство пользователя |

---

## 4. Верхнеуровневая схема

PNG формат 

[Верхнеуровневая архитектура PUSH-уведомлений](//www.plantuml.com/plantuml/png/fLHFQzim7BthKuZUo-w3ZVuftNPe5-XwAmJLLbCJMpAot95Rqz1jXq9Wvy6-GjBDIzOkoLUGlj6UvSJOgJC6lSJIp-_zdln-Jzb9D5CwZKAY0nroQhwQp5xRMpF3Ss2lpTpSvslyZkoGuYT_ERKtA3tO6mSPqTRfoTjjEm0eCpz1-MCUJGRQrFmc9Ea68cQAHTIJKIm55_f4zw2bkAUPw8IS4EPfgUYM-Gxoexqp4wSxSZBORr6MqDE4AqBA7a1_0sfifeunmpgZPe7pe83Dh-K9CaS-akT1oAlitTOx-ePf_f7rw0kwTtZeS7ZMD79HUiBLLU3nyXJBVHMt3nThkBpGW7kl_BM6g3HsF6AgotKWEZLqMLqngfHZgOEBm6CwaAB6ghvO4NscsJbB-1hjOEy9us2l0lx0epY4ROc3bJkS8vIfJxSEzuzWvsxhk02qqkIofUHLuEV3SsvkRxzUZOCKJrU2yoyfffUP7yXN7zb2ikmdh8VKv_cHgPPTBWY0Zb2P18XzCxUtEIKVEiXtTHAfpCEnroqfkvW98Zno7HeJdPq93xtQy4FuCDudrGa-kxVCcxKz9ZmUXjo7bqKbqKodITkxn8s1vdeEdgqRAnb9w4On2phmgw7NKzVDmjtNWVrSU8v2umKUdWaphGraO_yoKGtTxHUCT-nkzwpnG-nmOzzwe1i3iycQmdhwYiPS-B1yipdLjEFyStp-madSBoLY04WLLRaD840wJ_uweNXVshzqvW6moJ-dIlxR3V36uCnBtU8OxW--N_iGo4vXIUfvF4cGpWiM535LNAjUkxHe1uiq2ly2)

Исходник верхнеуровневой схемы в формате PlantUML находится в отдельном файле:

[diagrams/push-architecture.puml](diagrams/push-architecture.puml)

Схема показывает общий подход к отправке PUSH-уведомлений без брокера сообщений. Бизнес-сервисы напрямую вызывают `Notification Service`, передавая событие для отправки уведомления. `Notification Service` получает данные пользователя и device token, выбирает шаблон уведомления, сохраняет запись об отправке и передает PUSH во внешний push-провайдер FCM/APNs. Результат отправки сохраняется в `Notification DB`.

На первом этапе прямой вызов `Notification Service` проще в реализации и достаточен для базовых сценариев: брошенная корзина, отмена заказа, изменение статуса заказа и рекламные PUSH-уведомления. При росте нагрузки или количества источников событий схему можно расширить, добавив брокер сообщений, например Kafka или RabbitMQ, между бизнес-сервисами и `Notification Service`.




