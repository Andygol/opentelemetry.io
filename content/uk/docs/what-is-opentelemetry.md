---
title: Що таке OpenTelemetry?
description: Коротке пояснення, що таке OpenTelemetry і чим вона не є.
aliases: [/about, /docs/concepts/what-is-opentelemetry, /otel]
weight: 150
---

OpenTelemetry це:

- Фреймворк та інструментарій [Спостережності](/docs/concepts/observability-primer/#what-is-observability), призначений для створення та управління телеметричними даними, такими як [трейси](/docs/concepts/signals/traces/), [метрики](/docs/concepts/signals/metrics/) та [логи](/docs/concepts/signals/logs/).
- Діагностики постачальників та інструментів, що означає, що його можна використовувати з широким спектром бекендів спостережуваності, включаючи інструменти з відкритим вихідним кодом, такі як [Jaeger](https://www.jaegertracing.io/) та [Prometheus](https://prometheus.io/), а також комерційними пропозиціями.
- Не є бекендом спостережуваності, як Jaeger, Prometheus або інші комерційні постачальники.
- Зосереджена на генерації, зборі, управлінні та експорті телеметрії. Основна мета OpenTelemetry полягає в тому, щоб ви могли легко інструментувати свої застосунки або системи, незалежно від мови, за допомогою якої вони створені, інфраструктури або середовища виконання. Зберігання та візуалізація телеметрії навмисно залишені для інших інструментів.

## Що таке спостережуваність {#what-is-observability}

[Спостережність](/docs/concepts/observability-primer/#what-is-observability) — це можливість розуміти внутрішній стан системи, досліджуючи її вивід. У контексті програмного забезпечення це означає можливість розуміти внутрішній стан системи, досліджуючи її телеметричні дані, які включають трейси, метрики та логи.

Для того, щоб зробити систему спостережуваною, її необхідно [інструментувати](/docs/concepts/instrumentation). Це означає, що код повинен генерувати [трейси](/docs/concepts/signals/traces/), [метрики](/docs/concepts/signals/metrics/) або [логи](/docs/concepts/signals/logs/). Зібрані дані потім повинні бути відправлені до бекенду спостережуваності.

## Чому OpenTelemetry? {#why-opentelemetry}

З підвищенням популярності хмарних обчислень, мікросервісних архітектур та все складніших бізнес-вимог, потреба у [спостережуваності](/docs/concepts/observability-primer/#what-is-observability) програмного забезпечення та інфраструктури більша, ніж коли-небудь.

OpenTelemetry задовольняє потребу у спостережуваності, дотримуючись двох ключових принципів:

1. Ви володієте даними, які генеруєте. Ви не залежите від постачальника.
2. Вам потрібно вивчити лише один набір API та домовленостей.

Обидва принципи разом надають командам та організаціям гнучкість, яку вони потребують у сучасному світі обчислень.

Якщо ви хочете дізнатися більше, перегляньте [місію, візію та цінності](/community/mission/) OpenTelemetry.

## Основні компоненти OpenTelemetry {#main-opentelemetry-components}

OpenTelemetry складається з наступних основних компонентів:

- [Специфікація](/docs/specs/otel) для всіх компонентів
- Стандартний [протокол](/docs/specs/otlp/), який визначає форму телеметричних даних
- [Семантичні конвенції](/docs/specs/semconv/), які визначають стандартну схему найменування для типів телеметричних даних
- API, які визначають, як генерувати телеметричні дані
- [Мовні SDK](/docs/languages), які реалізують специфікацію, API та експорт телеметричних даних
- Екосистема [бібліотек](/ecosystem/registry), які реалізують інструментування для популярних бібліотек та фреймворків
- Компоненти автоматичного інструментування, які генерують телеметричні дані без необхідності змін в коді
- [Колектор OpenTelemetry](/docs/collector), проксі, який отримує, обробляє та експортує телеметричні дані
- Різноманітні інші інструменти, такі як [Оператор OpenTelemetry для Kubernetes](/docs/kubernetes/operator/), [чарти Helm OpenTelemetry](/docs/kubernetes/helm/) та [спільні активи для FaaS](/docs/faas/)

OpenTelemetry використовується широким колом [бібліотек, сервісів та застосунків](/ecosystem/integrations/), які стандартно мають OpenTelemetry інтегровану для забезпечення спостережності.

OpenTelemetry підтримується численними [постачальниками](/ecosystem/adopters/), багато з яких надають комерційну підтримку для OpenTelemetry та вносять внесок у проєкт безпосередньо.

## Розширюваність {#extensibility}

OpenTelemetry розроблена таким чином, щоб її можна було розширювати. Деякі приклади того, як вона може бути розширюватися включають

- Додавання приймача до колектора OpenTelemetry для підтримки телеметричних даних з власного джерела
- Завантаження власних бібліотек інструментування в SDK
- Створення [дистрибутиву](/docs/concepts/distributions/) SDK або колектора, призначеного для конкретного використання
- Створення нового експортера для власного бекенду, який ще не підтримує протокол OpenTelemetry (OTLP)
- Створення власного розповсюджувача для нестандартного формату передачі контексту

Хоча більшості користувачів, можливо, не знадобиться розширювати OpenTelemetry, проєкт спроєктований таким чином, щоб зробити це можливим майже на всіх рівнях.

## Історія {#history}

OpenTelemetry є проєктом [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io), який виник в результаті злиття двох попередніх проєктів, [OpenTracing](https://opentracing.io) та [OpenCensus](https://opencensus.io). Обидва ці проєкти були створені для вирішення однієї й тієї ж проблеми: відсутності стандарту для інструментування коду та надсилання телеметричних даних до бекенду спостережуваності. Оскільки жоден з проєктів не міг повністю розвʼязати проблему самостійно, вони обʼєдналися, щоб створити OpenTelemetry та поєднати свої сили, пропонуючи одне рішення.

У випадку якщо ви вже використовуєте OpenTracing або OpenCensus, ви можете дізнатися, як перейти на OpenTelemetry в [Посібнику з міграції](/docs/migration/).

## Що далі? {#what-next}

- [Розпочніть](/docs/getting-started/) негайно!
- Дізнайтесь про [концепції OpenTelemetry](/docs/concepts/).