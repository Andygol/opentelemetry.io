---
title: Використання бібліотек інструментування
linkTitle: Бібліотеки
weight: 40
description: Як інструментувати бібліотеки, від яких залежить додаток
---

{{% uk/docs/languages/libraries-intro rust %}}

## Використання бібліотек інструментування {#use-instrumentation-libraries}

Кожна бібліотека інструментування є [crate](https://crates.io/).

Наприклад, [бібліотека інструментування для Actix Web](https://crates.io/crates/actix-web-opentelemetry) автоматично створюватиме [відрізки](/docs/concepts/signals/traces/#spans) та [метрики](/docs/concepts/signals/metrics/) на основі вхідних HTTP-запитів.

Для списку доступних бібліотек інструментування дивіться [реєстр](/ecosystem/registry/?language=rust&component=instrumentation).