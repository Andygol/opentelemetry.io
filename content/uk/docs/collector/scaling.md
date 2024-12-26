---
title: Масштабування Колектора
weight: 26
# prettier-ignore
cSpell:ignore: fluentd hostmetrics loadbalancer loadbalancing sharded statefulset
---

Плануючи вашу систему спостереження з OpenTelemetry Collector, слід враховувати способи масштабування системи у міру збільшення збору телеметрії.

Наступні розділи допоможуть вам у плануванні, даючи огляд того, які компоненти масштабувати, як визначити, коли настав час масштабуватися, і як виконати план.

## Що масштабувати {#what-to-scale}

Хоча OpenTelemetry Collector обробляє всі типи сигналів телеметрії в одному бінарному файлі, насправді кожен тип може мати різні потреби в масштабуванні та може вимагати різних стратегій масштабування. Почніть з аналізу вашого робочого навантаження, щоб визначити, який тип сигналу очікує мати найбільшу частку навантаження і які формати очікуються до отримання Колектором. Наприклад, масштабування отримання телеметрії з кластера значно відрізняється від масштабування приймачів логів. Також подумайте про те, наскільки еластичне ваше навантаження: чи є піки в певний час доби, чи навантаження однакове протягом усіх 24 годин? Зібравши цю інформацію, ви зрозумієте, що потрібно масштабувати.

Наприклад, припустимо, у вас є сотні точок доступу Prometheus для отримання даних, терабайт логів, що надходять від fluentd кожну хвилину, і деякі метрики застосунків та трасування, що надходять у форматі OTLP від ваших нових мікросервісів. У такому випадку вам знадобиться архітектура, яка може масштабувати кожен сигнал окремо: масштабування приймачів Prometheus вимагає координації між скраперами для визначення, який скрапер спілкується з якою точкою доступу. Навпаки, ми можемо горизонтально масштабувати stateless приймачі логів за потреби. Маючи приймач OTLP для метрик і трасувань у третьому кластері Колекторів, ми можемо ізолювати збої та швидше ітеративно працювати без страху перезапуску зайнятого конвеєра. Оскільки приймач OTLP дозволяє приймати всі типи телеметрії, ми можемо зберігати метрики застосунків і трасування на одному екземплярі, масштабуючи їх горизонтально за потреби.

## Коли масштабувати {#when-to-scale}

Знову ж таки, ми повинні розуміти наше робоче навантаження, щоб вирішити, коли настав час збільшувати чи зменшувати масштаб, але кілька метрик, що випромінюються Колектором, можуть дати вам хороші підказки, коли діяти.

Однією з корисних підказок, яку може дати Колектор, коли процесор memory_limiter є частиною конвеєра, є метрика `otelcol_processor_refused_spans`. Цей процесор дозволяє обмежити кількість памʼяті, яку може використовувати Колектор. Хоча Колектор може споживати трохи більше, ніж максимальна кількість, налаштована в цьому процесорі, нові дані зрештою будуть заблоковані від проходження через конвеєр memory_limiter, який зафіксує цей факт у цій метриці. Така ж метрика існує для всіх інших типів телеметричних даних. Якщо дані занадто часто отримують відмову входу в конвеєр, вам, ймовірно, потрібно масштабувати кластер Колектора. Ви можете зменшити масштаб, коли споживання памʼяті на вузлах значно нижче за межу, встановлену в цьому процесорі.

Ще один набір метрик, на які слід звернути увагу, це ті, що стосуються розмірів черги для експортерів: `otelcol_exporter_queue_capacity` і `otelcol_exporter_queue_size`. Колектор буде ставити дані в чергу в памʼяті, очікуючи, поки працівник стане доступним для надсилання даних. Якщо працівників недостатньо або бекенд занадто повільний, дані починають накопичуватися в черзі. Коли черга досягає своєї місткості (`otelcol_exporter_queue_size` > `otelcol_exporter_queue_capacity`), вона відхиляє дані (`otelcol_exporter_enqueue_failed_spans`). Додавання більше працівників часто змушує Колектор експортувати більше даних, що не обовʼязково те, що ви хочете (див. [Коли НЕ треба масштабувати](#when-not-to-scale)).

Також варто ознайомитися з компонентами, які ви маєте намір використовувати, оскільки різні компоненти можуть створювати інші метрики. Наприклад, [експортер балансування навантаження буде записувати інформацію про час виконання операцій експорту](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter/loadbalancingexporter#metrics), відображаючи це як частину гістограми `otelcol_loadbalancer_backend_latency`. Ви можете витягти цю інформацію, щоб визначити, чи всі бекенди займають однакову кількість часу для обробки запитів: повільні окремі бекенди можуть вказувати на проблеми, не повʼязані з Колектором.

Для приймачів, що виконують скрапінг, таких як приймач Prometheus, скрапінг слід масштабувати або розподіляти, коли час, необхідний для завершення скрапінгу всіх цілей, часто стає критично близьким до інтервалу скрапінгу. Коли це відбувається, настав час додати більше скраперів, зазвичай нові екземпляри Колектора.

### Коли НЕ треба масштабувати {#when-not-to-scale}

Можливо, так само важливо, як знати, коли масштабувати, розуміти, які ознаки вказують на те, що операція масштабування не принесе жодної користі. Один приклад — коли база даних телеметрії не може впоратися з навантаженням: додавання Колекторів до кластера не допоможе без масштабування бази даних. Аналогічно, коли мережеве зʼєднання між Колектором і бекендом насичене, додавання більшої кількості Колекторів може спричинити шкідливий побічний ефект.

Знову ж таки, один зі способів виявити цю ситуацію — це перегляд метрик `otelcol_exporter_queue_size` і `otelcol_exporter_queue_capacity`. Якщо розмір черги постійно близький до місткості черги, це ознака того, що експорт даних повільніший, ніж отримання даних. Ви можете спробувати збільшити розмір черги, що змусить Колектор споживати більше памʼяті, але це також дасть деякий простір для дихання бекенду без постійного відкидання даних телеметрії. Але якщо ви продовжуєте збільшувати місткість черги, а розмір черги продовжує зростати в тій самій пропорції, це свідчить про те, що вам, можливо, потрібно подивитися за межі Колектора. Також важливо зазначити, що додавання більшої кількості працівників тут не буде корисним: ви лише збільшите тиск на систему, яка вже страждає від високого навантаження.

Ще одна ознака того, що бекенд може мати проблеми, — це збільшення метрики `otelcol_exporter_send_failed_spans`: це вказує на те, що надсилання даних до бекенду постійно зазнає невдачі. Масштабування Колектора, ймовірно, лише погіршить ситуацію, коли це постійно відбувається.

## Як масштабувати {#how-to-scale}

На цьому етапі ми знаємо, які частини нашого конвеєра потребують масштабування. Щодо масштабування, у нас є три типи компонентів: stateless, скрапери та stateful.

Більшість компонентів Колектора є stateless. Навіть якщо вони зберігають деякий стан у памʼяті, це не має значення для цілей масштабування.

Скрапери, такі як приймач Prometheus, налаштовані на отримання телеметричних даних із зовнішніх джерел. Приймач буде скрапити ціль за ціллю, вставляючи дані в конвеєр.

Компоненти, такі як процесор вибірки зразків наприкінці, не можуть бути легко масштабовані, оскільки вони зберігають деякий важливий стан у памʼяті для своєї роботи. Ці компоненти вимагають ретельного розгляду перед масштабуванням.

### Масштабування stateless Колекторів {#scaling-stateless-collectors}

Хороша новина полягає в тому, що більшість часу масштабування Колектора є простим, оскільки це просто питання додавання нових реплік і використання готового балансувальника навантаження. Коли для отримання даних використовується gRPC, ми рекомендуємо використовувати балансувальник навантаження, який розуміє gRPC. Інакше клієнти завжди будуть звертатися до одного і того ж Колектора.

Ви все ще повинні розглянути можливість розділення вашого конвеєра збору з урахуванням надійності. Наприклад, коли ваші робочі навантаження працюють в Kubernetes, ви можете використовувати DaemonSets, щоб мати Колектор на тому ж фізичному вузлі, що і ваші робочі навантаження, і віддалений центральний Колектор, відповідальний за попередню обробку даних перед надсиланням даних до сховища. Коли кількість вузлів низька, а кількість podʼів висока, sidecars можуть мати більше сенсу, оскільки ви отримаєте краще балансування навантаження для gRPC-зʼєднань між шарами Колектора без необхідності в gRPC-специфічному балансувальнику навантаження. Використання sidecar також має сенс, щоб уникнути зниження важливого компонента для всіх podʼів у вузлі, коли один pod DaemonSet зазнає невдачі.

Патерн sidecar полягає в додаванні контейнера до podʼа робочого навантаження. [Оператор OpenTelemetry](/docs/kubernetes/operator/) може автоматично додати це для вас. Для цього вам знадобиться CR OpenTelemetry Collector і вам потрібно буде анотувати ваш PodSpec або Pod, вказуючи оператору зробити інʼєкцію sidecar:

```yaml
---
apiVersion: opentelemetry.io/v1alpha1
kind: OpenTelemetryCollector
metadata:
  name: sidecar-for-my-workload
spec:
  mode: sidecar
  config: |
    receivers:
      otlp:
        protocols:
          grpc:
            endpoint: 0.0.0.0:4317
    processors:

    exporters:
      # Примітка: До v0.86.0 використовуйте `logging` замість `debug`.
      debug:

    service:
      pipelines:
        traces:
          receivers: [otlp]
          processors: []
          exporters: [debug]
---
apiVersion: v1
kind: Pod
metadata:
  name: my-microservice
  annotations:
    sidecar.opentelemetry.io/inject: 'true'
spec:
  containers:
    - name: my-microservice
      image: my-org/my-microservice:v0.0.0
      ports:
        - containerPort: 8080
          protocol: TCP
```

Якщо ви віддаєте перевагу обійти оператор і додати sidecar вручну, ось приклад:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-microservice
spec:
  containers:
    - name: my-microservice
      image: my-org/my-microservice:v0.0.0
      ports:
        - containerPort: 8080
          protocol: TCP
    - name: sidecar
      image: ghcr.io/open-telemetry/opentelemetry-collector-releases/opentelemetry-collector:0.69.0
      ports:
        - containerPort: 8888
          name: metrics
          protocol: TCP
        - containerPort: 4317
          name: otlp-grpc
          protocol: TCP
      args:
        - --config=/conf/collector.yaml
      volumeMounts:
        - mountPath: /conf
          name: sidecar-conf
  volumes:
    - name: sidecar-conf
      configMap:
        name: sidecar-for-my-workload
        items:
          - key: collector.yaml
            path: collector.yaml
```

### Масштабування скраперів {#scaling-scrapers}

Деякі приймачі активно отримують телеметричні дані для розміщення в конвеєрі, такі як приймачі hostmetrics і prometheus. Хоча отримання метрик хосту зазвичай не потребує масштабування, можливо, нам доведеться розділити завдання скрапінгу тисяч точок доступу для приймача Prometheus. І ми не можемо просто додати більше екземплярів з тією ж конфігурацією, оскільки кожен Колектор намагатиметься скрапити ті ж точки доступу, що й кожен інший Колектор у кластері, спричиняючи ще більше проблем, таких як зразки, що йдуть не за порядком.

Рішення полягає в розподілі точок доступу за екземплярами Колектора, щоб якщо ми додамо ще одну репліку Колектора, кожен з них буде діяти на різний набір точок доступу.

Один зі способів зробити це — мати один файл конфігурації для кожного Колектора, щоб кожен Колектор виявляв лише відповідні для цього Колектора точки доступу. Наприклад, кожен Колектор може бути відповідальним за один простір імен Kubernetes або певні мітки на робочих навантаженнях.

Інший спосіб масштабування приймача Prometheus — використовувати [Target Allocator](/docs/kubernetes/operator/target-allocator/): це додатковий бінарний файл, який можна розгорнути як частину оператора OpenTelemetry і який буде розподіляти цілі скрапінгу Prometheus для заданої конфігурації по кластеру Колекторів. Ви можете використовувати власний ресурс (CR), як показано нижче, щоб використовувати Target Allocator:

```yaml
apiVersion: opentelemetry.io/v1alpha1
kind: OpenTelemetryCollector
metadata:
  name: collector-with-ta
spec:
  mode: statefulset
  targetAllocator:
    enabled: true
  config: |
    receivers:
      prometheus:
        config:
          scrape_configs:
          - job_name: 'otel-collector'
            scrape_interval: 10s
            static_configs:
            - targets: [ '0.0.0.0:8888' ]

    exporters:
      # Примітка: До v0.86.0 використовуйте `logging` замість `debug`.
      debug:

    service:
      pipelines:
        traces:
          receivers: [prometheus]
          processors: []
          exporters: [debug]
```

Після узгодження оператор OpenTelemetry перетворить конфігурацію Колектора на наступну:

```yaml
exporters:
   # Примітка: До v0.86.0 використовуйте `logging` замість `debug`.
   debug: null
 receivers:
   prometheus:
     config:
       global:
         scrape_interval: 1m
         scrape_timeout: 10s
         evaluation_interval: 1m
       scrape_configs:
       - job_name: otel-collector
         honor_timestamps: true
         scrape_interval: 10s
         scrape_timeout: 10s
         metrics_path: /metrics
         scheme: http
         follow_redirects: true
         http_sd_configs:
         - follow_redirects: false
           url: http://collector-with-ta-targetallocator:80/jobs/otel-collector/targets?collector_id=$POD_NAME
service:
   pipelines:
     traces:
       exporters:
       - debug
       processors: []
       receivers:
       - prometheus
```

Зверніть увагу, як оператор додав розділ `global` і новий `http_sd_configs` до конфігурації скрапінгу `otel-collector`, вказуючи на екземпляр Target Allocator, який він створив. Тепер, щоб масштабувати колектори, змініть атрибут "replicas" CR, і Target Allocator розподілить навантаження відповідно, надаючи власний `http_sd_config` для кожного екземпляра колектора (podʼа).

### Масштабування stateful Колекторів {#scaling-stateful-collectors}

Деякі компоненти можуть зберігати дані в памʼяті, що призводить до різних результатів при масштабуванні. Це стосується процесора вибірки наприкінці, який зберігає відрізки в памʼяті для певного періоду, оцінюючи рішення про вибірку лише тоді, коли трасування вважається завершеним. Масштабування кластера Колектора шляхом додавання більшої кількості реплік означає, що різні колектори отримуватимуть відрізки для певного трасування, змушуючи кожен колектор оцінювати, чи слід вибирати це трасування, потенційно приходячи до різних відповідей. Ця поведінка призводить до відсутності відрізків у трасуваннях, що неправильно відображає те, що сталося в цій транзакції.

Схожа ситуація виникає при використанні процесора span-to-metrics для генерації сервісних метрик. Коли різні колектори отримують дані, повʼязані з одним і тим же сервісом, агрегування на основі імені сервісу буде неточним.

Щоб подолати це, ви можете розгорнути шар Колекторів, що містять експортер балансування навантаження, перед вашими Колекторами, які виконують вибірку наприкінці або обробку span-to-metrics. Експортер балансування навантаження буде хешувати ідентифікатор трасування або імʼя сервісу послідовно і визначати, який бекенд Колектора повинен отримувати відрізки для цього трасування. Ви можете налаштувати експортер балансування навантаження для використання списку хостів за даним записом DNS A, таким як headless сервіс Kubernetes. Коли розгортання, що підтримує цей сервіс, масштабується вгору або вниз, експортер балансування навантаження зрештою побачить оновлений список хостів. Альтернативно, ви можете вказати список статичних хостів, які будуть використовуватися експортером балансування навантаження. Ви можете масштабувати шар Колекторів, налаштованих з експортером балансування навантаження, збільшуючи кількість реплік. Зверніть увагу, що кожен Колектор потенційно виконуватиме DNS-запит у різний час, що призводить до різниці у вигляді кластера на кілька моментів. Ми рекомендуємо знизити значення інтервалу, щоб вигляд кластера відрізнявся лише на короткий період у високоеластичних середовищах.

Ось приклад конфігурації, що використовує запис DNS A (сервіс Kubernetes otelcol у просторі імен observability) як вхідні дані для інформації про бекенд:

```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317

processors:

exporters:
  loadbalancing:
    protocol:
      otlp:
    resolver:
      dns:
        hostname: otelcol.observability.svc.cluster.local

service:
  pipelines:
    traces:
      receivers:
        - otlp
      processors: []
      exporters:
        - loadbalancing
```