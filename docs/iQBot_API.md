Greeniq4

Описание
========

Программный интерфейс iQBot API позволяет отправлять сообщения клиентов из iQChannels в бот-платформу.

Взаимодействие с бот-платформой
===============================

**Все запросы в бот-платформу отправляются в формате JSON.**

**Для всех запросов необходимо указывать Content-Type: application/json**

### **Авторизация в бот-платформе**

| Параметр | Значение | Комментарий |
| --- | --- | --- |
| Метод | POST |  |
| Путь | /api/v1/auth/login/basic/default |  |
| Запрос | | Параметр | Тип | Обязательный | Комментарий | | --- | --- | --- | --- | | email | String | Да | Данные учетной записи в бот-платформе,  использующейся для авторизации чат-платформы | | password | String | Да | Данные учетной записи в бот-платформе,  использующейся для авторизации чат-платформы | |  |
| Ответ | | Параметр | Тип | Обязательный | Комментарий | | --- | --- | --- | --- | | status | String | Нет | Статус запроса. Если все хорошо, то success | | message | String | Нет | Описание статуса запроса  Если все хорошо, то значение будет Login successful | | payload |  |  | Блок | | jwt | String | Да | Токен доступа | | exp | String | Да | Время жизни токена | |  |

**Пример запроса:**

js{
"email": "superbot@bot.ru",
"password": "password"
}

**Пример ответа:**

js{
"status": "success",
"message": "Login successful",
"payload": {
"jwt": "eyJhbGciOiJIUzI.......w3SfREY",
"exp": 3600000
}
}

### **Отправка сообщения в бот-платформу**

| Параметр | Значение | Комментарий |
| --- | --- | --- |
| Метод | POST |  |
| Путь | api/v1/bots/***chat-bank***/converse/***sid***/secured | botID - идентификатор чат-бота в бот-платформе  sid - уникальный идентификатор сессии для текущего диалога |
| Авторизация | Bearer | Токен берем из ответа на запрос авторизации |
| Запрос | | Параметр | Тип | Обязательный | Комментарий | | --- | --- | --- | --- | | type | string | да | По умолчанию всегда "text" | | ``` text ``` | string | да | Текст сообщения | | payload | string | Нет | При использовании кнопок или выпадающего списка заполняется значением из payload выбранного элемента | |  |
| Ответ | | Параметр | Тип | Обязательный | Комментарий | | --- | --- | --- | --- | | ``` type ``` | string | Да | Тип сообщения. Может быть text, card, carousel, single-choice, | | ``` text ``` | string | Да | Текст сообщения | | groupid | string | Нет | Переадресация на группу операторов | | operatorid | string | Нет | Переадресация на оператора | | closedialog | boolean | Нет | Закрытие диалога | | isDropdown | boolean | Нет | Признак выпадающего списка (при type = singlechoice). Если TRUE - отображается выпадающий список, если FALSE - кнопки. | | dropdownPlaceholder | string | Нет | Заглушка/значение по умолчанию для выпадающего списка | | skill | string | Нет | Тип навыка | | ``` title ``` | string | Нет | Заголовок для картинки | | ``` image ``` | string | Нет | Ссылка на картинку | | choices | Блок | Да (при "type": "single-choice") | Список значений для кнопок или выпадающего списка   | Наименование | Тип | Обязательность | Комментарий | | --- | --- | --- | --- | | title | string | Да | Название пункта | | value | string | Да | Значение | | | ``` actions ``` | Блок | Да, при "type": "card" | Список действий   | Наименование | Тип | Обязательность | Комментарий | | --- | --- | --- | --- | | ``` title ``` | string | Да | Название кнопки | | ``` action ``` | string | Да | Действие. Может быть:   ``` Postback URL ``` | | ``` payload ``` | string | Да | Значение - передается боту при нажатии на кнопку | | | ``` items ``` | Блок | Да, при  ``` "type": "carousel" ``` | Список объектов actions | |  |

Пример запроса:

js{
"type": "text",
"text": "До которого часа работает банк?"
}

Пример ответа:

js{
"responses": [
{
"type": "text",
"text": "Переключаю вас на оператора",
"markdown": true,
"groupid": "100"
}
]
}

##### Пример ответа с single-choice:

js {
"type": "single-choice",
"skill": "choice",
"workflow": {},
"text": "Добро пожаловать!\n\nЯ бот-банк IQBankBot, ваш личный помощник в управлении финансами и банк в мессенджере!\n\nЧтобы совершить любую операцию (пополнить, перевести, запросить выписку и прочее), достаточно написать команду мне в чат 😉\n\nНажми на кнопку \"Узнать об IQBankBot\", чтобы узнать больше обо мне. А если ты уже клиент банка, выбери «Я клиент IQBank».",
"isDropdown": true,
"dropdownPlaceholder": "Select...",
"choices": [
{
"title": "Я клиент IQBank",
"value": "auth\_client"
},
{
"title": "Задать вопрос",
"value": "have\_question"
},
{
"title": "Узнать об IQBankBot",
"value": "about\_iqbank"
}
],
"markdown": true,
"typing": true
}

##### Пример ответа с "type": "card"

js{
"responses": [
{
"type": "text",
"workflow": {},
"text": "Авторизация прошла успешно!",
"markdown": true,
"typing": true
},
{
"type": "card",
"title": "Visa Gold -7039\nОстаток: 8 973,00 RUR",
"image": "https://iqbot.iqstore.ru/api/v1/bots/chat-bank/media/5lgn648ggh8pxnejfn0y-bank-mir-cutted.png",
"actions": [
{
"title": "Действия с картой",
"action": "Postback",
"payload": "locked-action"
}
]
},
{
"type": "card",
"title": "Авангард - RUR -8775\nОстаток: 217,49 RUR",
"image": "https://iqbot.iqstore.ru/api/v1/bots/chat-bank/media/5lgn648ggh8pxnejfn0y-bank-mir-cutted.png",
"actions": [
{
"title": "Действия с картой",
"action": "Postback",
"payload": "locked-action"
}
]
},
{
"type": "card",
"title": "Авангард - RUR -8042\nОстаток: 2 186,46 RUR",
"image": "https://iqbot.iqstore.ru/api/v1/bots/chat-bank/media/5lgn648ggh8pxnejfn0y-bank-mir-cutted.png",
"actions": [
{
"title": "Действия с картой",
"action": "Postback",
"payload": "locked-action"
}
]
}
]
}

##### Пример ответа с "type": "carousel"

js{
"responses": [
{
"type": "text",
"workflow": {},
"text": "Выбери вид карты",
"markdown": true,
"typing": true
},
{
"type": "carousel",
"workflow": {},
"items": [
{
"title": "Путешествия",
"subtitle": "",
"image": "https://iqbot.iqstore.ru/api/v1/bots/chat-bank/media/dwl4nqndn7nffggro1cm-bank-mir.png",
"actions": [
{
"action": "Postback",
"title": "Выпустить карту \"Путешествия\"",
"text": "",
"payload": "debet-card-mir-p"
}
]
},
{
"title": "Возможности",
"image": "https://iqbot.iqstore.ru/api/v1/bots/chat-bank/media/2x1zhafe6q861oku85b0-mir-card.png",
"actions": [
{
"action": "Postback",
"title": "Выпустить карту \"Возможности\"",
"text": "",
"payload": "debet-card-mir-v"
}
]
}
]
}
]
}