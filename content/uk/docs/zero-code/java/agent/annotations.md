---
title: Анотації
description: Використання анотацій інструментування з Java агентом.
aliases: [/docs/instrumentation/java/annotations]
weight: 20
cSpell:ignore: Flowable javac reactivestreams reactivex
---

Для більшості користувачів достатньо інструментування з коробки, і нічого більше робити не потрібно. Однак іноді користувачі бажають створювати [відрізки](/docs/concepts/signals/traces/#spans) для свого власного коду без значних змін у коді.

## Залежності {#dependencies}

Щоб використовувати анотацію `@WithSpan`, вам потрібно додати залежність від бібліотеки `opentelemetry-instrumentation-annotations`.

### Maven

```xml
<dependencies>
  <dependency>
    <groupId>io.opentelemetry.instrumentation</groupId>
    <artifactId>opentelemetry-instrumentation-annotations</artifactId>
    <version>{{% param vers.instrumentation %}}</version>
  </dependency>
</dependencies>
```

### Gradle

```groovy
dependencies {
    implementation('io.opentelemetry.instrumentation:opentelemetry-instrumentation-annotations:{{% param vers.instrumentation %}}')
}
```

## Створення відрізків навколо методів з `@WithSpan` {#creating-spans-around-methods-with-withspan}

Щоб створити [відрізок](/docs/concepts/signals/traces/#spans), що відповідає одному з ваших методів, анотуйте метод за допомогою `@WithSpan`.

```java
import io.opentelemetry.instrumentation.annotations.WithSpan;

public class MyClass {
  @WithSpan
  public void myMethod() {
      <...>
  }
}
```

Кожного разу, коли застосунок викликає анотований метод, створюється відрізок, який позначає його тривалість і надає будь-які помилки, що зʼявляються. Стандартно імʼя відрізка буде `<className>.<methodName>`, якщо імʼя не вказано як аргумент до анотації.

Якщо тип повернення методу, анотованого `@WithSpan`, є одним із типів, схожих на [future або promise](https://en.wikipedia.org/wiki/Futures_and_promises), зазначених нижче, то відрізок не буде завершено, доки future не завершиться.

- [java.util.concurrent.CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html)
- [java.util.concurrent.CompletionStage](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletionStage.html)
- [com.google.common.util.concurrent.ListenableFuture](https://guava.dev/releases/10.0/api/docs/com/google/common/util/concurrent/ListenableFuture.html)
- [org.reactivestreams.Publisher](https://www.reactive-streams.org/reactive-streams-1.0.1-javadoc/org/reactivestreams/Publisher.html)
- [reactor.core.publisher.Mono](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Mono.html)
- [reactor.core.publisher.Flux](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html)
- [io.reactivex.Completable](https://reactivex.io/RxJava/2.x/javadoc/index.html?io/reactivex/Completable.html)
- [io.reactivex.Maybe](https://reactivex.io/RxJava/2.x/javadoc/index.html?io/reactivex/Maybe.html)
- [io.reactivex.Single](https://reactivex.io/RxJava/2.x/javadoc/index.html?io/reactivex/Single.html)
- [io.reactivex.Observable](https://reactivex.io/RxJava/2.x/javadoc/index.html?io/reactivex/Observable.html)
- [io.reactivex.Flowable](https://reactivex.io/RxJava/2.x/javadoc/index.html?io/reactivex/Flowable.html)
- [io.reactivex.parallel.ParallelFlowable](https://reactivex.io/RxJava/2.x/javadoc/index.html?io/reactivex/parallel/ParallelFlowable.html)

## Додавання атрибутів до відрізку за допомогою `@SpanAttribute` {#adding-attributes-to-the-span-with-spanattribute}

Коли створюється [відрізок](/docs/concepts/signals/traces/#spans) для анотованого методу, значення аргументів до виклику методу можуть автоматично додаватися як [атрибути](/docs/concepts/signals/traces/#attributes) до створеного відрізка, анотуючи параметри методу за допомогою анотації `@SpanAttribute`.

```java
import io.opentelemetry.instrumentation.annotations.SpanAttribute;
import io.opentelemetry.instrumentation.annotations.WithSpan;

public class MyClass {

    @WithSpan
    public void myMethod(@SpanAttribute("parameter1") String parameter1,
        @SpanAttribute("parameter2") long parameter2) {
        <...>
    }
}
```

Якщо не вказано як аргумент до анотації, імʼя атрибута буде виведено з формальних імен параметрів, якщо вони скомпільовані у файли `.class` за допомогою параметра `-parameters` до компілятора `javac`.

## Придушення інструментування `@WithSpan` {#suppressing-withspan-instrumentation}

Придушення `@WithSpan` корисне, якщо у вас є код, який надмірно інструментований за допомогою `@WithSpan`, і ви хочете придушити деякі з них без зміни коду.

{{% config_option
  name="otel.instrumentation.opentelemetry-instrumentation-annotations.exclude-methods" %}} Придушити інструментування `@WithSpan` для конкретних методів. Формат: `my.package.MyClass1[method1,method2];my.package.MyClass2[method3]`.
{{% /config_option %}}

## Створення відрізків навколо методів за допомогою `otel.instrumentation.methods.include` {#creating-spans-around-methods-with-otelinstrumentationmethodsinclude}

У випадках, коли ви не можете змінити код, ви все ще можете налаштувати Java агент для захоплення відрізків навколо конкретних методів.

{{% config_option name="otel.instrumentation.methods.include" %}} Додати інструментування для конкретних методів замість `@WithSpan`. Формат: `my.package.MyClass1[method1,method2];my.package.MyClass2[method3]`. {{% /config_option %}}

Якщо метод перевантажений (зʼявляється більше одного разу в одному класі з однаковим імʼям, але з різними параметрами), всі версії методу будуть інструментовані.

## Наступні кроки {#next-steps}

Крім використання анотацій, API OpenTelemetry дозволяє отримати трасувальник, який можна використовувати для [ручного інструментування](/docs/languages/java/instrumentation/) і виконувати код у межах цього відрізка.