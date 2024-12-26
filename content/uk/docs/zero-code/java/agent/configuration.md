---
title: Конфігурація
weight: 10
aliases: [agent-config]
cSpell:ignore: classloaders customizer logback
---

## Конфігурація агента {#agent-configuration}

Агент може споживати конфігурацію з одного або кількох наступних джерел (впорядкованих від найвищого до найнижчого пріоритету):

- Системні властивості
- [Змінні середовища](#configuring-with-environment-variables)
- [Файл конфігурації](#configuration-file)
- Властивості, надані функцією [`AutoConfigurationCustomizer#addPropertiesSupplier()`](https://github.com/open-telemetry/opentelemetry-java/blob/f92e02e4caffab0d964c02a32fe305d6d6ba372e/sdk-extensions/autoconfigure-spi/src/main/java/io/opentelemetry/sdk/autoconfigure/spi/AutoConfigurationCustomizer.java#L73); використовуючи [`AutoConfigurationCustomizerProvider`](https://github.com/open-telemetry/opentelemetry-java/blob/main/sdk-extensions/autoconfigure-spi/src/main/java/io/opentelemetry/sdk/autoconfigure/spi/AutoConfigurationCustomizerProvider.java) SPI

## Конфігурація за допомогою змінних середовища {#configuring-with-environment-variables}

У деяких середовищах конфігурація за допомогою змінних середовища є більш переважною. Будь-яке налаштування, яке можна налаштувати за допомогою системної властивості, також можна налаштувати за допомогою змінної середовища. Багато налаштувань нижче включають обидва варіанти, але де вони не застосовуються, виконайте наступні кроки, щоб визначити правильне ім'я для бажаної системної властивості:

- Перетворіть системну властивість на великі літери.
- Замініть усі символи `.` та `-` на `_`.

Наприклад, `otel.instrumentation.common.default-enabled` перетвориться на `OTEL_INSTRUMENTATION_COMMON_DEFAULT_ENABLED`.

## Файл конфігурації {#configuration-file}

Ви можете надати шлях до файлу конфігурації агента, встановивши наступну властивість:

{{% config_option name="otel.javaagent.configuration-file" %}} Шлях до дійсного файлу властивостей Java, який містить конфігурацію агента.
{{% /config_option %}}

## Розширення {#extensions}

Ви можете увімкнути [розширення][] за допомогою встановлення наступної властивості:

{{% config_option name="otel.javaagent.extensions" %}}

Шлях до jar-файлу розширення або теки, що містить jar-файли. Якщо вказано теку, кожен jar-файл у цій теці буде розглядатися як окреме, незалежне розширення.

{{% /config_option %}}

## Вивід логів Java агента {#java-agent-logging-output}

Вивід журналів агента можна налаштувати, встановивши наступну властивість:

{{% config_option name="otel.javaagent.logging" %}}

Режим ведення журналів Java агента. Підтримуються наступні 3 режими:

- `simple`: Агент буде виводити свої журнали за допомогою стандартного потоку помилок. Будуть виводитися лише журнали рівня `INFO` або вище. Це стандартний режим ведення журналів Java агента.
- `none`: Агент не буде нічого журналювати — навіть свою власну версію.
- `application`: Агент спробує перенаправити свої власні журнали до логера slf4j інструментованого застосунку. Це найкраще працює для простих застосунків з одним jar-файлом, які не використовують кілька завантажувачів класів; підтримуються також застосунки Spring Boot. Вивід журналів Java агента можна додатково налаштувати за допомогою конфігурації журналювання інструментованого застосунку (наприклад, `logback.xml` або `log4j2.xml`). **Переконайтеся, що цей режим працює для вашого застосунку перед запуском його в промисловому середовищі.**

{{% /config_option %}}

## Конфігурація SDK {#sdk-configuration}

Модуль автоконфігурації SDK використовується для базової конфігурації агента. Прочитайте [документацію](/docs/languages/java/configuration), щоб знайти налаштування, такі як конфігурація експорту або семплінгу.

{{% alert title="Важливо" color="warning" %}}

На відміну від автоконфігурації SDK, версії 2.0+ Java агента та стартера OpenTelemetry Spring Boot використовують `http/protobuf` як стандартний протокол, а не `grpc`.

{{% /alert %}}

## Увімкнення постачальників ресурсів, які стандартно вимкнені {#enable-resource-providers-that-are-disabled-by-default}

На додачу до конфігурації ресурсів з автоконфігурації SDK, ви можете увімкнути додаткових постачальників ресурсів, які стандартно вимкнені:

{{% config_option
name="otel.resource.providers.aws.enabled"
default=false
%}} Увімкнення [Постачальника ресурсів AWS](https://github.com/open-telemetry/opentelemetry-java-contrib/tree/main/aws-resources).
{{% /config_option %}}

{{% config_option
name="otel.resource.providers.gcp.enabled"
default=false
%}} Увімкнення [Постачальника ресурсів GCP](https://github.com/open-telemetry/opentelemetry-java-contrib/tree/main/gcp-resources).
{{% /config_option %}}

[розширення]:
  https://github.com/open-telemetry/opentelemetry-java-instrumentation/tree/main/examples/extension#readme