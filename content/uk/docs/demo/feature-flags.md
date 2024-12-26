---
title: Прапорці функцій
aliases:
  - feature_flags
  - services/feature-flag
  - services/featureflagservice
cSpell:ignore: loadgenerator OLJCESPC7Z
---

Демо надає кілька прапорців функцій, які ви можете використовувати для імітації різних сценаріїв. Ці прапорці керуються за допомогою [`flagd`](https://flagd.dev), простого сервісу прапорців функцій, який підтримує [OpenFeature](https://openfeature.dev).

Значення прапорців можна змінювати через інтерфейс користувача, доступний за адресою <http://localhost:8080/feature> під час запуску демо. Зміни значень через цей інтерфейс будуть відображені в сервісі flagd.

Є два варіанти зміни прапорців функцій через інтерфейс користувача:

- **Основний вигляд**: Зручний для користувача вигляд, у якому можна вибрати та зберегти стандартні варіанти (ті самі опції, які потрібно змінити при налаштуванні через сирий файл) для кожного функціонального прапорця. Наразі основний вигляд не підтримує фракційне таргетування.

- **Розширений вигляд**: Вигляд, у якому завантажується сирий файл конфігурації JSON і може бути відредагований в оглядачі. Цей вигляд надає гнучкість, яка приходить з редагуванням сирого файлу JSON, але також забезпечує перевірку схеми, щоб гарантувати, що JSON є дійсним і що надані значення конфігурації є правильними.

## Реалізовані прапорці функцій {#implemented-feature-flags}

| Прапорець функції                   | Сервіс(и)        | Опис                                                                                                      |
| ----------------------------------- | ---------------- | --------------------------------------------------------------------------------------------------------- |
| `adServiceFailure`                  | Ad Service       | Генерувати помилку для `GetAds` 1/10 разів                                                                |
| `adServiceManualGc`                 | Ad Service       | Викликати повне ручне збирання сміття в сервісі оголошень                                                 |
| `adServiceHighCpu`                  | Ad Service       | Викликати високе навантаження на процесор у сервісі оголошень. Якщо ви хочете продемонструвати обмеження процесора, встановіть обмеження ресурсів процесора |
| `cartServiceFailure`                | Cart Service     | Генерувати помилку кожного разу, коли викликається `EmptyCart`                                            |
| `productCatalogFailure`             | Product Catalog  | Генерувати помилку для запитів `GetProduct` з ідентифікатором продукту: `OLJCESPC7Z`                      |
| `recommendationServiceCacheFailure` | Recommendation   | Створити витік памʼяті через експоненційно зростаючий кеш. Зростання 1.4x, 50% запитів викликають зростання. |
| `paymentServiceFailure`             | Payment Service  | Генерувати помилку при виклику методу `charge`.                                                           |
| `paymentServiceUnreachable`         | Checkout Service | Використовувати неправильну адресу при виклику PaymentService, щоб зробити вигляд, що PaymentService недоступний. |
| `loadgeneratorFloodHomepage`        | Loadgenerator    | Почати затоплення головної сторінки великою кількістю запитів, налаштовується шляхом зміни JSON flagd на стані. |
| `kafkaQueueProblems`                | Kafka            | Перевантажує чергу Kafka, одночасно вводячи затримку на стороні споживача, що призводить до сплеску затримки. |
| `imageSlowLoad`                     | Frontend         | Використовує впорскування помилок envoy, викликає затримку у завантаженні зображень продуктів на фронтенді. |

## Архітектура прапорців функцій {#feature-flags-architecture}

Будь ласка, перегляньте [документацію flagd](https://flagd.dev) для отримання додаткової інформації про те, як працює flagd, а також вебсайт [OpenFeature](https://openfeature.dev) для отримання додаткової інформації про те, як працює OpenFeature, разом з документацією для API OpenFeature.