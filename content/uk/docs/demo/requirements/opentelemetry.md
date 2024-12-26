---
title: Вимоги до OpenTelemetry
linkTitle: Вимоги OTel
aliases: [opentelemetry_requirements]
---

Наступні вимоги були визначені для того, щоб визначити, які сигнали OpenTelemetry (OTel) буде генерувати застосунок і коли слід додавати підтримку майбутніх SDK:

1. Демонстрація повинна генерувати журнали OTel, трасування та метрики з коробки для мов, які мають GA SDK.
2. Мови, які мають доступний Beta SDK, можуть бути включені, але не є обовʼязковими як GA SDK.
3. Нативні метрики OTel повинні вироблятися, де це можливо.
4. Як ручна інструменталізація, так і бібліотеки інструменталізації (автоінструменталізація) повинні бути продемонстровані для кожної мови.
5. Всі дані повинні спочатку експортуватися до Колектора.
6. Колектор повинен бути налаштований для забезпечення різноманітних варіантів споживання, але для кожного сигналу повинні бути обрані стандартні інструменти.
7. Архітектура демонстраційного застосунку з використанням Колектора повинна бути розроблена як архітектура з використанням найкращих практик.