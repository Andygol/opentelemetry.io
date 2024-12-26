---
title: Архітектура Demo
linkTitle: Архітектура
aliases: [current_architecture]
body_class: otel-mermaid-max-width
---

**OpenTelemetry Demo** складається з мікросервісів, написаних різними мовами програмування, які взаємодіють між собою через gRPC та HTTP; і генератора навантаження, який використовує [Locust](https://locust.io/) для імітації користувацького трафіку.

```mermaid
graph TD
subgraph Service Diagram
accountingservice(Cервіс бухгалтерії):::dotnet
adservice(Сервіс реклами):::java
cache[(Кеш<br/>&#40Valkey&#41)]
cartservice(Сервіс кошика):::dotnet
checkoutservice(Сервіс оформлення замовлення):::golang
currencyservice(Валютний сервіс):::cpp
emailservice(Сервіс електронної пошти):::ruby
flagd(Flagd):::golang
flagdui(Flagd-ui):::typescript
frauddetectionservice(Сервіс виявлення шахрайства):::kotlin
frontend(Фронтенд):::typescript
frontendproxy(Фронтенд проксі <br/>&#40Envoy&#41):::cpp
imageprovider(Постачальник зображень <br/>&#40nginx&#41):::cpp
loadgenerator([Генератор навантаження]):::python
paymentservice(Платіжний сервіс):::javascript
productcatalogservice(Сервіс каталогу продуктів):::golang
quoteservice(Сервіс котирувань):::php
recommendationservice(Сервіс рекомендацій):::python
shippingservice(Сервіс доставки):::rust
queue[(черга<br/>&#40Kafka&#41)]:::java

adservice ---->|gRPC| flagd

checkoutservice -->|gRPC| cartservice
checkoutservice --->|TCP| queue
cartservice --> cache
cartservice -->|gRPC| flagd

checkoutservice -->|gRPC| shippingservice
checkoutservice -->|gRPC| paymentservice
checkoutservice --->|HTTP| emailservice
checkoutservice -->|gRPC| currencyservice
checkoutservice -->|gRPC| productcatalogservice

frauddetectionservice -->|gRPC| flagd

frontend -->|gRPC| adservice
frontend -->|gRPC| cartservice
frontend -->|gRPC| checkoutservice
frontend ---->|gRPC| currencyservice
frontend ---->|gRPC| recommendationservice
frontend -->|gRPC| productcatalogservice

frontendproxy -->|gRPC| flagd
frontendproxy -->|HTTP| frontend
frontendproxy -->|HTTP| flagdui
frontendproxy -->|HTTP| imageprovider

Internet -->|HTTP| frontendproxy

loadgenerator -->|HTTP| frontendproxy

paymentservice -->|gRPC| flagd

queue -->|TCP| accountingservice
queue -->|TCP| frauddetectionservice

recommendationservice -->|gRPC| productcatalogservice
recommendationservice -->|gRPC| flagd

shippingservice -->|HTTP| quoteservice
end

classDef dotnet fill:#178600,color:white;
classDef cpp fill:#f34b7d,color:white;
classDef golang fill:#00add8,color:black;
classDef java fill:#b07219,color:white;
classDef javascript fill:#f1e05a,color:black;
classDef kotlin fill:#560ba1,color:white;
classDef php fill:#4f5d95,color:white;
classDef python fill:#3572A5,color:white;
classDef ruby fill:#701516,color:white;
classDef rust fill:#dea584,color:black;
classDef typescript fill:#e98516,color:black;
```

```mermaid
graph TD
subgraph Service Legend
  dotnetsvc(.NET):::dotnet
  cppsvc(C++):::cpp
  golangsvc(Go):::golang
  javasvc(Java):::java
  javascriptsvc(JavaScript):::javascript
  kotlinsvc(Kotlin):::kotlin
  phpsvc(PHP):::php
  pythonsvc(Python):::python
  rubysvc(Ruby):::ruby
  rustsvc(Rust):::rust
  typescriptsvc(TypeScript):::typescript
end

classDef dotnet fill:#178600,color:white;
classDef cpp fill:#f34b7d,color:white;
classDef golang fill:#00add8,color:black;
classDef java fill:#b07219,color:white;
classDef javascript fill:#f1e05a,color:black;
classDef kotlin fill:#560ba1,color:white;
classDef php fill:#4f5d95,color:white;
classDef python fill:#3572A5,color:white;
classDef ruby fill:#701516,color:white;
classDef rust fill:#dea584,color:black;
classDef typescript fill:#e98516,color:black;
```

Перейдіть за цими посиланнями, щоб дізнатися про поточний стан [метрик](/docs/demo/telemetry-features/metric-coverage/) та [трасування](/docs/demo/telemetry-features/trace-coverage/) інструментування демонстраційних застосунків.

Колектор налаштований в [otelcol-config.yml](https://github.com/open-telemetry/opentelemetry-demo/blob/main/src/otelcollector/otelcol-config.yml),  альтернативні експортери можна налаштувати тут.

```mermaid
graph TB
subgraph tdf[Потік Даних Телеметрії]
    subgraph subgraph_padding [ ]
        style subgraph_padding fill:none,stroke:none;
        %% padding to stop the titles clashing
        subgraph od[OpenTelemetry Demo]
        ms(Мікросервіс)
        end

        ms -.->|"OTLP<br/>gRPC"| oc-grpc
        ms -.->|"OTLP<br/>HTTP POST"| oc-http

        subgraph oc[OTel Collector]
            style oc fill:#97aef3,color:black;
            oc-grpc[/"OTLP Приймач<br/>слухає на<br/>grpc://localhost:4317"/]
            oc-http[/"OTLP Приймач<br/>слухає на <br/>localhost:4318<br/>"/]
            oc-proc(Процесори)
            oc-prom[/"OTLP HTTP Експортер"/]
            oc-otlp[/"OTLP Експортер"/]

            oc-grpc --> oc-proc
            oc-http --> oc-proc

            oc-proc --> oc-prom
            oc-proc --> oc-otlp
        end

        oc-prom -->|"localhost:9090/api/v1/otlp"| pr-sc
        oc-otlp -->|gRPC| ja-col

        subgraph pr[Prometheus]
            style pr fill:#e75128,color:black;
            pr-sc[/"Prometheus OTLP Приймач"/]
            pr-tsdb[(Prometheus TSDB)]
            pr-http[/"Prometheus HTTP<br/>слухає на<br/>localhost:9090"/]

            pr-sc --> pr-tsdb
            pr-tsdb --> pr-http
        end

        pr-b{{"Оглядач<br/>Prometheus UI"}}
        pr-http ---->|"localhost:9090/graph"| pr-b

        subgraph ja[Jaeger]
            style ja fill:#60d0e4,color:black;
            ja-col[/"Jaeger Приймач<br/>слухає на<br/>grpc://jaeger:4317"/]
            ja-db[(Jaeger DB)]
            ja-http[/"Jaeger HTTP<br/>слухає на<br/>localhost:16686"/]

            ja-col --> ja-db
            ja-db --> ja-http
        end

        subgraph gr[Grafana]
            style gr fill:#f8b91e,color:black;
            gr-srv["Сервер Grafana"]
            gr-http[/"Grafana HTTP<br/>слухає на<br/>localhost:3000"/]

            gr-srv --> gr-http
        end

        pr-http --> |"localhost:9090/api"| gr-srv
        ja-http --> |"localhost:16686/api"| gr-srv

        ja-b{{"Оглядач<br/>Jaeger UI"}}
        ja-http ---->|"localhost:16686/search"| ja-b

        gr-b{{"Оглядач<br/>Grafana UI"}}
        gr-http -->|"localhost:3000/dashboard"| gr-b
    end
end
```

Дивіться **Визначення Протокольних Буферів** у теці `/pb/`.