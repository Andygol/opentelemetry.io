---
title: Розгортання Kubernetes
linkTitle: Kubernetes
aliases: [kubernetes_deployment]
cSpell:ignore: loadgen otlphttp spanmetrics
---

Ми надаємо [Helm-чарт OpenTelemetry Demo](/docs/kubernetes/helm/demo/), щоб допомогти розгорнути демо на наявному кластері Kubernetes.

Для використання чартів необхідно встановити [Helm](https://helm.sh/uk/). Будь ласка, зверніться до [документації Helm](https://helm.sh/uk/docs/), щоб почати.

## Попередні вимоги {#prerequisites}

- Kubernetes 1.24+
- 6 ГБ вільної оперативної памʼяті для застосунку
- Helm 3.14+ (тільки для методу встановлення Helm)

## Встановлення за допомогою Helm (рекомендовано) {#install-using-helm-recommended}

Додайте репозиторій Helm OpenTelemetry:

```shell
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
```

Щоб встановити чарт з імʼям релізу my-otel-demo, виконайте наступну команду:

```shell
helm install my-otel-demo open-telemetry/opentelemetry-demo
```

{{% alert title="Примітка" color="info" %}}

Helm-чарт OpenTelemetry Demo не підтримує оновлення з однієї версії на іншу. Якщо вам потрібно оновити чарт, спочатку видаліть наявний реліз, а потім встановіть нову версію.

{{% /alert %}}

{{% alert title="Примітка" color="info" %}}

Для виконання всіх методів використання, згаданих нижче, потрібна версія Helm-чарту демо OpenTelemetry 0.11.0 або вище.

{{% /alert %}}

## Встановлення за допомогою kubectl {#install-using-kubectl}

Наступна команда встановить демонстраційний застосунок у ваш кластер Kubernetes.

```shell
kubectl apply --namespace otel-demo -f https://raw.githubusercontent.com/open-telemetry/opentelemetry-demo/main/kubernetes/opentelemetry-demo.yaml
```

{{% alert title="Примітка" color="info" %}}

Маніфести Kubernetes OpenTelemetry Demo не підтримують оновлення з однієї версії на іншу. Якщо вам потрібно оновити демо, спочатку видаліть наявні ресурси, а потім встановіть нову версію.

{{% /alert %}}

{{% alert title="Примітка" color="info" %}}

Ці маніфести генеруються з Helm-чарту і надаються для зручності. Рекомендується використовувати Helm-чарт для встановлення.

{{% /alert %}}

## Використання Demo {#use-the-demo}

Для використання демонстраційного застосунку необхідно, щоб сервіси були доступні за межами кластера Kubernetes. Ви можете зробити сервіси доступними для вашої локальної системи за допомогою команди `kubectl port-forward` або налаштувавши типи сервісів (наприклад, LoadBalancer) з опціонально розгорнутими ресурсами ingress.

### Експонування сервісів за допомогою kubectl port-forward {#expose-services-using-kubectl-port-forward}

Щоб відкрити сервіс frontendproxy, використовуйте наступну команду (замініть `my-otel-demo` на відповідну назву релізу Helm-чарту):

```shell
kubectl port-forward svc/my-otel-demo-frontendproxy 8080:8080
```

{{% alert title="Примітка" color="info" %}}

`kubectl port-forward` проксіює порт до завершення процесу. Можливо, вам знадобиться створити окремі сеанси термінала для кожного використання `kubectl port-forward` і використовувати <kbd>Ctrl-C</kbd> для завершення процесу після завершення.

{{% /alert %}}

З налаштованим port-forward для frontendproxy ви можете отримати доступ до:

- Вебмагазин: <http://localhost:8080/>
- Grafana: <http://localhost:8080/grafana/>
- Інтерфейс користувача генератора навантаження: <http://localhost:8080/loadgen/>
- Інтерфейс користувача Jaeger: <http://localhost:8080/jaeger/ui/>
- Інтерфейс користувача конфігуратора Flagd: <http://localhost:8080/feature>

### Експонування компонентів Demo за допомогою конфігурацій сервісів або ingress {#expose-demo-components-using-service-or-ingress-configurations}

{{% alert title="Примітка" color="info" %}} Ми рекомендуємо використовувати файл значень при встановленні Helm-чарту для вказівки додаткових параметрів конфігурації. {{% /alert %}}

#### Налаштування ресурсів ingress {#configure-ingress-resources}

{{% alert title="Примітка" color="info" %}}

Кластери Kubernetes можуть не мати необхідних інфраструктурних компонентів для підтримки типів сервісів LoadBalancer або ресурсів ingress. Переконайтеся, що ваш кластер має необхідну підтримку перед використанням цих параметрів конфігурації.

{{% /alert %}}

Кожен компонент демо (наприклад, frontendproxy) пропонує спосіб налаштування типу сервісу Kubernetes. Стандартно вони не будуть створені, але ви можете увімкнути та налаштувати їх через властивість `ingress` кожного компонента.

Щоб налаштувати компонент frontendproxy для використання ресурсу ingress, ви повинні вказати наступне у вашому файлі значень:

```yaml
components:
  frontendProxy:
    ingress:
      enabled: true
      annotations: {}
      hosts:
        - host: otel-demo.my-domain.com
          paths:
            - path: /
              pathType: Prefix
              port: 8080
```

Деякі контролери ingress вимагають спеціальних анотацій або типів сервісів. Зверніться до документації вашого контролера ingress для отримання додаткової інформації.

#### Налаштування типів сервісів {#configure-service-types}

Кожен компонент демо (наприклад, frontendproxy) пропонує спосіб налаштування типу сервісу Kubernetes. Стандартно вони будуть `ClusterIP`, але ви можете змінити кожен з них за допомогою властивості `service.type` кожного компонента.

Щоб налаштувати компонент frontendproxy для використання типу сервісу LoadBalancer, ви повинні вказати наступне у вашому файлі значень:

```yaml
components:
  frontendProxy:
    service:
      type: LoadBalancer
```

#### Налаштування телеметрії оглядача {#configure-browser-telemetry}

Для правильного збору відрізків з оглядача вам також потрібно вказати місце, де буде розташований OpenTelemetry Collector. Компонент frontendproxy визначає маршрут для колектора з префіксом шляху `/otlp-http`. Ви можете налаштувати точку доступу колектора, встановивши наступну змінну середовища на компоненті frontend:

```yaml
components:
  frontend:
    envOverrides:
      - name: PUBLIC_OTEL_EXPORTER_OTLP_TRACES_ENDPOINT
        value: http://otel-demo.my-domain.com/otlp-http/v1/traces
```

## Використання власного бекенду {#bring-your-own-backend}

Ймовірно, ви хочете використовувати вебмагазин як демонстраційний застосунок для наявного бекенду спостережуваності (наприклад, наявного екземпляра Jaeger, Zipkin або одного з [вибраних вами постачальників](/ecosystem/vendors/)).

Конфігурація OpenTelemetry Collector доступна в Helm-чарті. Будь-які додавання, які ви зробите, будуть обʼєднані зі стандартною конфігурацією. Ви можете використовувати це для додавання власних експортерів і додавання їх до потрібних конвеєрів.

```yaml
opentelemetry-collector:
  config:
    exporters:
      otlphttp/example:
        endpoint: <your-endpoint-url>

    service:
      pipelines:
        traces:
          exporters: [spanmetrics, otlphttp/example]
```

{{% alert title="Примітка" color="info" %}} При обʼєднанні значень YAML з Helm обʼєкти обʼєднуються, а масиви замінюються. Експортер `spanmetrics` повинен бути включений у масив експортерів для конвеєра `traces`, якщо він перевизначений. Не включення цього експортера призведе до помилки. {{% /alert %}}

Постачальники бекендів можуть вимагати додавання додаткових параметрів для автентифікації, будь ласка, зверніться до їх документації. Деякі бекенди вимагають різних експортерів, ви можете знайти їх і їх документацію на [opentelemetry-collector-contrib/exporter](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter).

Щоб встановити Helm-чарт з власним файлом значень `my-values-file.yaml`, використовуйте:

```shell
helm install my-otel-demo open-telemetry/opentelemetry-demo --values my-values-file.yaml
```