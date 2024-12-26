---
title: Інші автоконфігурації Spring
weight: 70
cSpell:ignore: autoconfigurations
---

<!-- markdownlint-disable blanks-around-fences -->
<?code-excerpt path-base="examples/java/spring-starter"?>

Замість використання стартера OpenTelemetry Spring, ви можете використовувати стартер OpenTelemetry Zipkin.

## Стартер Zipkin {#zipkin-starter}

OpenTelemetry Zipkin Exporter Starter — це стартовий пакунок, який включає `opentelemetry-api`, `opentelemetry-sdk`, `opentelemetry-extension-annotations`, `opentelemetry-logging-exporter`, `opentelemetry-spring-boot-autoconfigurations` та стартери фреймворку spring, необхідні для налаштування розподіленого трасування. Він також надає артефакт [opentelemetry-exporters-zipkin](https://github.com/open-telemetry/opentelemetry-java/tree/main/exporters/zipkin) та відповідну автоконфігурацію експортера. Перегляньте [opentelemetry-spring-boot-autoconfigure](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/main/instrumentation/spring/spring-boot-autoconfigure/README.md#features) для списку підтримуваних бібліотек та функцій.

Якщо експортер присутній у classpath під час виконання і spring bean експортера відсутній у контексті застосунку spring, bean експортера ініціалізується та додається до простого процесора span в активному провайдері трасування. Перегляньте реалізацію [тут](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/main/instrumentation/spring/spring-boot-autoconfigure/src/main/java/io/opentelemetry/instrumentation/spring/autoconfigure/OpenTelemetryAutoConfiguration.java).

{{< tabpane text=true >}} {{% tab header="Maven (`pom.xml`)" lang=Maven %}}

```xml
<dependencies>
  <dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-exporter-zipkin</artifactId>
    <version>{{% param vers.otel %}}</version>
  </dependency>
</dependencies>
```

{{% /tab %}} {{% tab header="Gradle (`gradle.build`)" lang=Gradle %}}

```kotlin
dependencies {
  implementation("io.opentelemetry:opentelemetry-exporter-zipkin:{{% param vers.otel %}}")
}
```

{{% /tab %}} {{< /tabpane>}}

### Конфігурації {#configurations}

| Властивість                    | Стандартне значення | ConditionalOnClass   |
| ------------------------------ | ------------------- | -------------------- |
| `otel.exporter.zipkin.enabled` | true                | `ZipkinSpanExporter` |