**Задание 2. Проектирование API**

# 1. Описание задачи

## Необходимо спроектировать REST API для экрана выбора магазинов-партнёров в мобильном приложении интернет-магазина «Петрушка Зеленая».

На экране отображается список магазинов-партнёров. Для каждого магазина нужно показать:

1. Название магазина;
2. Логотип;
3. Информацию о ближайшей доставке;
4. Ссылку для перехода на внешний ресурс при клике на карточку магазина.


# 2. REST API запрос

Для получения списка магазинов предлагается использовать GET-запрос:

GET /api/v1/partner-stores?cityId=msk&deliveryDate=2026-07-04
Authorization: Bearer {accessToken}
Accept: application/json
Назначение метода

Метод возвращает список магазинов-партнёров, доступных для пользователя в выбранном городе или зоне доставки.

Почему GET

Запрос только получает данные для отображения экрана и не изменяет состояние системы, поэтому для него подходит HTTP-метод GET.

# 3. Query-параметры

| Параметр | Тип | Обязательный | Описание |
|---|---|---|---|
| cityId | string | да | Идентификатор города или зоны доставки пользователя |
| deliveryDate | string | нет | Дата, на которую нужно получить ближайшие интервалы доставки. Формат: YYYY-MM-DD |
| limit | int | нет | Максимальное количество магазинов в ответе |
| offset | int | нет | Смещение для пагинации, если список магазинов большой |


Пример запроса:

GET /api/v1/partner-stores?cityId=msk&deliveryDate=2026-07-04&limit=20&offset=0
```json
{
  "items": [
    {
      "id": "metro",
      "name": "METRO",
      "logoUrl": "https://cdn.petrushka.ru/stores/metro.png",
      "deliveryInfo": {
        "type": "time_slot",
        "title": "Ближайшая доставка",
        "dateText": "сегодня",
        "timeFrom": "21:00",
        "timeTo": "23:00",
        "displayText": "сегодня 21:00–23:00"
      },
      "externalUrl": "https://metro.example.com",
      "deeplink": null,
      "isAvailable": true
    },
    {
      "id": "auchan",
      "name": "Ашан",
      "logoUrl": "https://cdn.petrushka.ru/stores/auchan.png",
      "deliveryInfo": {
        "type": "time_slot",
        "title": "Ближайшая доставка",
        "dateText": "сегодня",
        "timeFrom": "18:00",
        "timeTo": "20:00",
        "displayText": "сегодня 18:00–20:00"
      },
      "externalUrl": "https://auchan.example.com",
      "deeplink": null,
      "isAvailable": true
    },
    {
      "id": "vkusvill",
      "name": "ВкусВилл",
      "logoUrl": "https://cdn.petrushka.ru/stores/vkusvill.png",
      "deliveryInfo": {
        "type": "fast_delivery",
        "title": "Быстрая доставка",
        "minMinutes": 20,
        "maxMinutes": 60,
        "displayText": "от 20 до 60 минут"
      },
      "externalUrl": "https://vkusvill.example.com",
      "deeplink": null,
      "isAvailable": true
    },
    {
      "id": "victoria",
      "name": "ВИКТОРИЯ",
      "logoUrl": "https://cdn.petrushka.ru/stores/victoria.png",
      "deliveryInfo": {
        "type": "time_slot",
        "title": "Ближайшая доставка",
        "dateText": "сегодня",
        "timeFrom": "17:00",
        "timeTo": "19:00",
        "displayText": "сегодня 17:00–19:00"
      },
      "externalUrl": "https://victoria.example.com",
      "deeplink": null,
      "isAvailable": true
    }
  ]
}
```
# 5. Описание полей ответа

| Поле | Тип | Описание |
|---|---|---|

|items|	|List/array|	|Список магазинов-партнёров|
|id|	|string|	|Уникальный идентификатор магазина|
|name|	|string|	|Название магазина для отображения на карточке|
|logoUrl|	|string|	|URL логотипа магазина|
|deliveryInfo|	|Map/object|	|Информация о ближайшей доставке|
|deliveryInfo.type|	|string|	|Тип отображения доставки: time_slot или fast_delivery|
|deliveryInfo.title|	|string|	|Заголовок блока доставки|
|deliveryInfo.dateText|	|string|	|Текстовое отображение даты доставки, например сегодня|
|deliveryInfo.timeFrom|	|string|	|Начало интервала доставки|
|deliveryInfo.timeTo|	|string|	|Окончание интервала доставки|
|deliveryInfo.minMinutes|	|int|	|Минимальное время быстрой доставки в минутах|
|deliveryInfo.maxMinutes|	|int|	|Максимальное время быстрой доставки в минутах|
|deliveryInfo.displayText|	|string|	|Готовая строка для отображения в мобильном приложении|
|externalUrl|	|string|	|Ссылка на внешний ресурс, куда нужно перейти при клике на карточку магазина|
|deeplink|	|string/null|	|Deeplink, если переход должен быть внутри приложения|
|isAvailable|	|boolean|	|Признак доступности магазина для пользователя|


# 6. Возможные ошибки

400 Bad Request

Некорректные параметры запроса.
```json
{
  "errorCode": "INVALID_REQUEST",
  "message": "cityId is required"
}
```

401 Unauthorized

Пользователь не авторизован или передан некорректный токен.
```json
{
  "errorCode": "UNAUTHORIZED",
  "message": "Authorization token is missing or invalid"
}
```
404 Not Found

Для выбранного города магазины-партнёры не найдены.
```json
{
  "errorCode": "PARTNER_STORES_NOT_FOUND",
  "message": "Partner stores are not available for selected city"
}
```
500 Internal Server Error

Внутренняя ошибка сервера.
```json
{
  "errorCode": "INTERNAL_ERROR",
  "message": "Unexpected server error"
}
```
# 7. Вопросы к PM / backend / mobile
1. Нужно ли отображать только доступные магазины или также недоступные, но в неактивном состоянии?
2. Откуда определяется город или зона доставки пользователя: из профиля, геолокации, выбранного адреса или query-параметра?
3. Нужно ли поддерживать сортировку магазинов: по ближайшей доставке, популярности, алфавиту или приоритету партнёра?
4. Должна ли ссылка открываться во внешнем браузере или во встроенном WebView?
5. Нужен ли deeplink для возврата пользователя обратно в приложение после перехода на внешний ресурс?
6. Нужно ли показывать состояние пустого списка, если магазины недоступны в выбранном городе?
7. Нужна ли пагинация или количество партнёрских магазинов ограничено?
8. Кто формирует строку displayText: backend или mobile?
9. Нужно ли локализовать текст доставки на backend или mobile получает только структурированные данные?
10. Нужно ли кешировать список магазинов на mobile?
11. Нужно ли передавать дополнительные признаки для аналитики кликов по магазинам?
12. Нужно ли скрывать магазин, если у него временно нет доступных интервалов доставки?



