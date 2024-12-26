---
title: Атрибути відрізків, створених вручну
aliases: [manual_span_attributes, ../manual-span-attributes]
cSpell:ignore: featureflag
---

Ця сторінка містить перелік атрибутів відрізків, створених вручну, що використовуються в демонстрації:

## AdService

| Назва                       | Тип    | Опис                                  |
| --------------------------- | ------ | ------------------------------------- |
| `app.ads.category`          | string | Категорія для поверненої реклами      |
| `app.ads.contextKeys`       | string | Ключі контексту, використані для пошуку пов'язаної реклами |
| `app.ads.contextKeys.count` | number | Кількість унікальних ключів контексту |
| `app.ads.count`             | number | Кількість реклами, поверненої користувачу |
| `app.ads.ad_request_type`   | string | Або `targeted`, або `not_targeted`    |
| `app.ads.ad_response_type`  | string | Або `targeted`, або `random`          |

## CartService

| Назва                  | Тип    | Опис                             |
| ---------------------- | ------ | ------------------------------ |
| `app.cart.items.count` | number | Кількість унікальних предметів у кошику |
| `app.product.id`       | string | Ідентифікатор продукту для предмета у кошику |
| `app.product.quantity` | string | Кількість предметів у кошику    |
| `app.user.id`          | string | Ідентифікатор користувача       |

## CheckoutService

| Назва                        | Тип    | Опис                             |
| ---------------------------- | ------ | ------------------------------- |
| `app.cart.items.count`       | number | Загальна кількість предметів у кошику |
| `app.order.amount`           | number | Сума замовлення                 |
| `app.order.id`               | string | Ідентифікатор замовлення        |
| `app.order.items.count`      | number | Кількість унікальних предметів у замовленні |
| `app.payment.transaction.id` | string | Ідентифікатор платіжної транзакції |
| `app.shipping.amount`        | number | Сума доставки                   |
| `app.shipping.tracking.id`   | string | Ідентифікатор відстеження доставки |
| `app.user.currency`          | string | Валюта користувача              |
| `app.user.id`                | string | Ідентифікатор користувача       |

## CurrencyService

| Назва                            | Тип    | Опис                             |
| ------------------------------ | ------ | ----------------------------- |
| `app.currency.conversion.from` | string | Код валюти для конвертації з   |
| `app.currency.conversion.to`   | string | Код валюти для конвертації в   |

## EmailService

| Назва                 | Тип    | Опис                                  |
| --------------------- | ------ | --------------------------------- |
| `app.email.recipient` | string | Електронна пошта для підтвердження замовлення |
| `app.order.id`        | string | Ідентифікатор замовлення           |

## Frontend

| Назва                    | Тип    | Опис                             |
| ------------------------ | ------ | ----------------------------- |
| `app.cart.size`          | number | Загальна кількість предметів у кошику |
| `app.cart.items.count`   | number | Кількість унікальних предметів у кошику |
| `app.cart.shipping.cost` | number | Вартість доставки кошика       |
| `app.cart.total.price`   | number | Загальна вартість кошика       |
| `app.currency`           | string | Валюта користувача              |
| `app.currency.new`       | string | Нова валюта для встановлення   |
| `app.order.total`        | number | Загальна вартість замовлення   |
| `app.product.id`         | string | Ідентифікатор продукту         |
| `app.product.quantity`   | number | Кількість продукту             |
| `app.products.count`     | number | Загальна кількість продуктів, що відображаються |
| `app.request.id`         | string | Ідентифікатор запиту           |
| `app.session.id`         | string | Ідентифікатор сесії            |
| `app.user.id`            | string | Ідентифікатор користувача      |

## LoadGenerator

| Назва    | Тип | Опис       |
| -------- | ---- | ----------- |
| Поки що немає |      |             |

## PaymentService

| Назва                    | Тип     | Опис                                           |
| ------------------------ | ------- | ----------------------------------------------------- |
| `app.payment.amount`     | number  | Загальна сума платежу                                  |
| `app.payment.card_type`  | string  | Тип картки, використаної для платежу                         |
| `app.payment.card_valid` | boolean | Чи була використана картка дійсною                               |
| `app.payment.charged`    | boolean | Чи був платіж успішним (false з генератором навантаження) |

## ProductCatalogService

| Назва                       | Тип    | Опис                                  |
| --------------------------- | ------ | ------------------------------------- |
| `app.product.id`            | string | Ідентифікатор продукту                |
| `app.product.name`          | string | Назва продукту                        |
| `app.products.count`        | number | Кількість продуктів у каталозі        |
| `app.products_search.count` | number | Кількість продуктів, повернених у пошуку |

## QuoteService

| Назва                   | Тип    | Опис                             |
| ----------------------- | ------ | -------------------- |
| `app.quote.items.count` | number | Загальна кількість предметів для доставки  |
| `app.quote.cost.total`  | number | Загальна вартість доставки |

## RecommendationService

| Назва                            | Тип     | Опис                                  |
| -------------------------------- | ------- | --------------------------------------- |
| `app.filtered_products.count`    | number  | Кількість повернених відфільтрованих продуктів    |
| `app.products.count`             | number  | Кількість продуктів у каталозі           |
| `app.products_recommended.count` | number  | Кількість повернених рекомендованих продуктів |
| `app.cache_hit`                  | boolean | Чи був доступ до кешу            |

## ShippingService

| Назва                      | Тип    | Опис                             |
| -------------------------- | ------ | ----------------------------- |
| `app.shipping.cost.total`  | number | Загальна вартість доставки           |
| `app.shipping.items.count` | number | Загальна кількість предметів для доставки           |
| `app.shipping.tracking.id` | string | Ідентифікатор відстеження доставки          |
| `app.shipping.zip_code`    | string | Поштовий індекс, використаний для доставки предметів |