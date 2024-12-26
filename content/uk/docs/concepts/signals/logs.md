---
title: Логи
description: Запис події.
weight: 3
cSpell:ignore: filelogreceiver semistructured transformprocessor
---

**Лог** — це текстовий запис з часовою міткою, структурований (рекомендовано) або неструктурований, з додатковими метаданими. З усіх сигналів телеметрії, логи мають найбільшу спадщину. Більшість мов програмування мають вбудовані можливості логування або добре відомі, широко використовувані бібліотеки логування.

## Логи OpenTelemetry {#opentelemetry-logs}

OpenTelemetry не визначає спеціальний API або SDK для створення логів. Натомість логи OpenTelemetry — це наявні логи, які ви вже маєте від фреймворку логування або інфраструктурного компонента. SDK OpenTelemetry та автоінструментування використовують кілька компонентів для автоматичного корелювання логів з [трейсами](/docs/concepts/signals/traces).

Підтримка логів OpenTelemetry розроблена для повної сумісності з тим, що ви вже маєте, надаючи можливості обгортати ці логи додатковим контекстом та спільним інструментарієм для аналізу та маніпулювання логами у спільному форматі з багатьох різних джерел.

### Логи OpenTelemetry в OpenTelemetry Collector {#opentelemetry-logs-in-the-opentelemetry-collector}

[OpenTelemetry Collector](/docs/collector) надає кілька інструментів для роботи
з логами:

- Кілька приймачів, які аналізують логи з конкретних, відомих джерел даних логів.
- `filelogreceiver`, який читає логи з будь-якого файлу та надає можливості аналізувати їх з різних форматів або використовувати регулярний вираз.
- Процесори, такі як `transformprocessor`, які дозволяють аналізувати вкладені дані, спрощувати вкладені структури, додавати/видаляти/оновлювати значення та інше.
- Експортери, які дозволяють виводити дані логів у не-OpenTelemetry форматі.

Перший крок у впровадженні OpenTelemetry часто включає розгортання Collector як універсального агента логування.

### Логи OpenTelemetry для застосунків {#opentelemetry-logs-for-applications}

У застосунках логи OpenTelemetry створюються за допомогою будь-якої бібліотеки логування або вбудованих можливостей логування. Коли ви додаєте автоінструментування або активуєте SDK, OpenTelemetry автоматично корелює ваші наявні логи з будь-яким активним трейсом та відрізком, обгортаючи тіло логу їхніми ідентифікаторами. Іншими словами, OpenTelemetry автоматично корелює ваші логи та трейси.

### Підтримка мов {#language-support}

Логи є [стабільним](/docs/specs/otel/versioning-and-stability/#stable) сигналом у специфікації OpenTelemetry. Для індивідуальних мовних специфічних реалізацій API та SDK логів, статус наступний:

{{% signal-support-table "logs" %}}

## Структуровані, неструктуровані та напівструктуровані логи {#structured-unstructured-and-semistructured-logs}

OpenTelemetry технічно не розрізняє структуровані та неструктуровані логи. Ви можете використовувати будь-який лог з OpenTelemetry. Однак, не всі формати логів однаково корисні! Структуровані логи, зокрема, рекомендуються для промислової спостережуваності, оскільки їх легко аналізувати та аналізувати в масштабі. Наступний розділ пояснює різницю між структурованими, неструктурованими та напівструктурованими логами.

### Структуровані логи {#structured-logs}

Структурований лог — це лог, текстовий формат якого дотримується послідовного, машиночитаного формату. Для застосунків одним з найпоширеніших форматів є JSON:

```json
{
  "timestamp": "2024-08-04T12:34:56.789Z",
  "level": "INFO",
  "service": "user-authentication",
  "environment": "production",
  "message": "User login successful",
  "context": {
    "userId": "12345",
    "username": "johndoe",
    "ipAddress": "192.168.1.1",
    "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36"
  },
  "transactionId": "abcd-efgh-ijkl-mnop",
  "duration": 200,
  "request": {
    "method": "POST",
    "url": "/api/v1/login",
    "headers": {
      "Content-Type": "application/json",
      "Accept": "application/json"
    },
    "body": {
      "username": "johndoe",
      "password": "******"
    }
  },
  "response": {
    "statusCode": 200,
    "body": {
      "success": true,
      "token": "jwt-token-here"
    }
  }
}
```

а для інфраструктурних компонентів, часто використовується Common Log Format (CLF) :

```text
127.0.0.1 - johndoe [04/Aug/2024:12:34:56 -0400] "POST /api/v1/login HTTP/1.1" 200 1234
```

Також часто зустрічається змішування різних структурованих форматів логів. Наприклад, лог у форматі Extended Log Format (ELF) може змішувати JSON з даними, розділеними пробілами, у логі CLF.

```text
192.168.1.1 - johndoe [04/Aug/2024:12:34:56 -0400] "POST /api/v1/login HTTP/1.1" 200 1234 "http://example.com" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36" {"transactionId": "abcd-efgh-ijkl-mnop", "responseTime": 150, "requestBody": {"username": "johndoe"}, "responseHeaders": {"Content-Type": "application/json"}}
```

Щоб максимально використовувати цей лог, перетворюйте як JSON, так і частини, повʼязані з ELF, у спільний формат, щоб полегшити аналіз на бекенді спостережуваності. `filelogreceiver` в [OpenTelemetry Collector](/docs/collector) містить стандартизовані способи аналізу таких логів.

Структуровані логи є переважним способом використання логів. Оскільки структуровані логи видаються у послідовному форматі, їх легко аналізувати, що робить їх простішими для попередньої обробки в OpenTelemetry Collector, кореляції з іншими даними та кінцевого аналізу на бекенді спостережуваності.

### Неструктуровані логи {#unstructured-logs}

Неструктуровані логи — це логи, які не дотримуються послідовної структури. Вони можуть бути більш зручними для читання людиною і часто використовуються у розробці. Однак, не рекомендується використовувати неструктуровані логи для промислової спостережуваності, оскільки їх набагато важче аналізувати та аналізувати в масштабі.

Приклади неструктурованих логів:

```text
[ERROR] 2024-08-04 12:45:23 - Не вдалося підключитися до бази даних. Помилка: java.sql.SQLException: Час очікування закінчився. Спроба повторного підключення 3 рази. Сервер: db.example.com, Порт: 5432

Перезавантаження системи ініційовано 2024-08-04 03:00:00 користувачем: admin. Причина: Заплановане обслуговування. Зупинені сервіси: веб-сервер, база даних, кеш. Оцінений час простою: 15 хвилин.

DEBUG - 2024-08-04 09:30:15 - Користувач johndoe виконав дію: завантаження файлу. Імʼя файлу: report_Q3_2024.pdf, Розмір: 2.3 MB, Тривалість: 5.2 секунди. Результат: Успіх
```

Можливо зберігати та аналізувати неструктуровані логи у промисловій експлуатації, хоча вам може знадобитися значна робота для їх аналізу або попередньої обробки, щоб вони були машиночитаними. Наприклад, вищезазначені три логи потребуватимуть регулярного виразу для аналізу їх часових міток та спеціальних парсерів для послідовного отримання тіл повідомлень логів. Це зазвичай необхідно для того, щоб бекенд логування знав, як сортувати та організовувати логи за часовою міткою. Хоча можливо аналізувати неструктуровані логи для цілей аналізу, це може бути більше роботи, ніж перехід на структуроване логування, наприклад, через стандартний фреймворк логування у ваших додатках.

### Напівструктуровані логи {#semistructured-logs}

Напівструктурований лог — це лог, який використовує деякі самопослідовні шаблони для розрізнення даних, щоб вони були машиночитаними, але може не використовувати однакове форматування та роздільники між даними у різних системах.

Приклад напівструктурованого логу:

```text
2024-08-04T12:45:23Z level=ERROR service=user-authentication userId=12345 action=login message="Failed login attempt" error="Invalid password" ipAddress=192.168.1.1 userAgent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36"
```

Хоча машиночитані, напівструктуровані логи можуть вимагати кілька різних парсерів для можливості проведення аналізу в масштабі.

## Компоненти логування OpenTelemetry {#opentelemetry-logging-components}

Наступні списки концепцій та компонентів забезпечують підтримку логування OpenTelemetry.

### Доповнювач журналу/Міст {#log-appender-bridge}

Як розробник застосунків, **API моста логів** не повинен викликатися вами безпосередньо, оскільки він надається для авторів бібліотек логування для створення доповнювачів логів/мостів. Натомість, ви просто використовуєте свою улюблену бібліотеку логування та налаштовуєте її для використання доповнювача логів (або моста для логів), який здатний виводити логи у OpenTelemetry LogRecordExporter.

SDK OpenTelemetry пропонують цю функціональність.

### Постачальник логерів {#logger-provider}

> Частина **API моста логів** і повинен використовуватися лише, якщо ви є автором бібліотеки логування.

Постачальник логерів (іноді називається `LoggerProvider`) є фабрикою для `Logger`ʼів. У більшості випадків постачальник логерів ініціалізується один раз і його життєвий цикл відповідає життєвому циклу застосунка. Ініціалізація постачальника логерів також включає ініціалізацію ресурсів та експортерів.

### Логер {#logger}

> Частина **API моста логів** і повинен використовуватися лише, якщо ви є автором бібліотеки логування.

Логер створює записи логів. Логери створюються з постачальників логерів.

### Експортер записів логів {#log-record-exporter}

Експортери записів логів відправляють записи логів споживачу. Цей споживач може бути стандартним виводом для налагодження та розробки, OpenTelemetry Collector, або будь-яким відкритим або бекендом постачальника на ваш вибір.

### Запис логу {#log-record}

Запис логу представляє запис події. У OpenTelemetry запис логу містить два види полів:

- Іменовані поля верхнього рівня певного типу та значення
- Поля ресурсів та атрибутів довільного значення та типу

Поля верхнього рівня:

| Назва поля           | Опис                                        |
| -------------------- | -------------------------------------------- |
| Timestamp            | Час, коли подія відбулася.                   |
| ObservedTimestamp    | Час, коли подія була спостережена.           |
| TraceId              | Ідентифікатор трасування запиту.             |
| SpanId               | Ідентифікатор спвідрізкуану запиту.          |
| TraceFlags           | Прапорець трасування W3C.                    |
| SeverityText         | Текст важливості (також відомий як рівень логування). |
| SeverityNumber       | Числове значення важливості.                 |
| Body                 | Тіло запису логу.                            |
| Resource             | Описує джерело логу.                         |
| InstrumentationScope | Описує область, яка видала лог.              |
| Attributes           | Додаткова інформація про подію.              |

Для отримання додаткової інформації про записи логів та поля логів, дивіться [Модель даних логів](/docs/specs/otel/logs/data-model/).

### Специфікація {#specification}

Щоб дізнатися більше про логи в OpenTelemetry, дивіться [специфікацію логів][logs specification].

[logs specification]: /docs/specs/otel/overview/#log-signal