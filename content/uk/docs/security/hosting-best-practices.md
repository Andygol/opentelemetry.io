---
title: Найкращі практики хостингу Collector
linkTitle: Хостинг Collector
weight: 115
---

Під час налаштування хостингу для OpenTelemetry (OTel) Collector враховуйте ці найкращі практики для кращого захисту вашого хостинг-екземпляра.

## Зберігайте дані безпечно {#store-data-safely}

Ваш файл конфігурації Collector може містити конфіденційні дані, включаючи токени автентифікації або сертифікати TLS. Ознайомтеся з найкращими практиками щодо [захисту вашої конфігурації](../config-best-practices/#create-secure-configurations).

Якщо ви зберігаєте телеметрію для обробки, переконайтеся, що доступ до цих тек обмежений, щоб запобігти втручанню в необроблені дані.

## Зберігайте свої секрети в безпеці {#keep-your-secrets-safe}

Kubernetes [секрети](https://kubernetes.io/docs/concepts/configuration/secret/) це облікові дані, які містять конфіденційну інформацію. Вони автентифікують і авторизують привілейований доступ. Якщо ви використовуєте розгортання Kubernetes для вашого Collector,
дотримуйтесь цих [рекомендованих практик](https://kubernetes.io/docs/concepts/security/secrets-good-practices/) для покращення безпеки ваших кластерів.

## Застосовуйте принцип найменших привілеїв {#apply-principle-of-least-privilege}

Collector не повинен вимагати привілейованого доступу, за винятком випадків, коли дані, які він збирає, знаходяться в привілейованому місці. Наприклад, у розгортанні Kubernetes, системні журнали, журнали застосунків і журнали контейнерного середовища часто зберігаються в обсязі вузла, який вимагає спеціального дозволу на доступ. Якщо ваш Collector працює як daemonset на вузлі, переконайтеся, що надано лише ті дозволи на монтування томів, які йому потрібні для доступу до цих журналів, і не більше. Ви можете налаштувати привілейований доступ за допомогою контролю доступу на основі ролей (RBAC). Дивіться [хороші практики RBAC](https://kubernetes.io/docs/concepts/security/rbac-good-practices/) для отримання додаткової інформації.

## Контролюйте доступ до компонентів, схожих на сервери {#control-access-to-server-like-components}

Деякі компоненти Collector, такі як приймачі та експортери, можуть функціонувати як сервери. Щоб обмежити доступ авторизованих користувачів, ви повинні:

- Увімкніть автентифікацію за допомогою розширень автентифікації за допомогою токенів і розширень базової автентифікації, наприклад.
- Обмежте IP-адреси, на яких працює ваш Collector.

## Захистіть використання ресурсів {#safeguard-resource-utilization}

Використовуйте власну [внутрішню телеметрію](/docs/collector/internal-telemetry/) Collector для моніторингу його продуктивності. Збирайте метрики з Collector про використання його ЦП, памʼяті та пропускної здатності та встановлюйте сповіщення про вичерпання ресурсів.

Якщо досягнуто межі ресурсів, розгляньте можливість горизонтального [масштабування Collector](/docs/collector/scaling/) шляхом розгортання кількох екземплярів у конфігурації з балансуванням навантаження. Масштабування вашого Collector розподіляє вимоги до ресурсів і запобігає вузьким місцям.

Після того, як ви забезпечите використання ресурсів у вашому розгортанні, переконайтеся, що ваш екземпляр Collector також використовує [запобіжні заходи в його конфігурації](../config-best-practices/#safeguard-resource-utilization).