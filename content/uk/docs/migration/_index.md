---
title: Міграція
description: Як перейти на OpenTelemetry
weight: 950
---

## OpenTracing та OpenCensus {#opentracing-and-opencensus}

OpenTelemetry було створено як злиття OpenTracing та OpenCensus. Від самого початку OpenTelemetry вважався [наступною основною версією як OpenTracing, так і OpenCensus][]. Через це однією з [ключових цілей][] проєкту OpenTelemetry є забезпечення
зворотної сумісності з обома проєктами та історії міграції для наявних користувачів.

Якщо ви прийшли з одного з цих проєктів, ви можете скористатися посібниками з міграції для обох [OpenTracing](opentracing/) та [OpenCensus](opencensus/)

## Jaeger Клієнт {#jaeger-client}

[Спільнота Jaeger](https://www.jaegertracing.io/) припинила підтримку своїх [Клієнтських бібліотек](https://www.jaegertracing.io/docs/latest/client-libraries/) та рекомендує використовувати OpenTelemetry API, SDK та інструменти.

Бекенд Jaeger може отримувати дані трасування через протокол OpenTelemetry (OTLP) з версії v1.35. Тому ви можете мігрувати свої OpenTelemetry SDK та колектори з використанням експортера Jaeger на експортер OTLP.

[наступною основною версією як OpenTracing, так і OpenCensus]:
  https://www.cncf.io/blog/2019/05/21/a-brief-history-of-opentelemetry-so-far/
[ключових цілей]:
  https://medium.com/opentracing/merging-opentracing-and-opencensus-f0fe9c7ca6f0