DEFENSE — Завдання 2: Вимоги та Use Cases

Намір і критерії (1–2 речення):  
Розробка структурованих вимог (EARS), user stories, Gherkin-сценаріїв, use case діаграми та матриці трасування для книжкового магазину на основі моделі даних завдання 1\.

Топ-3 розбіжності (spec ↔ артефакт) \+ коміт-виправлення:  
Розбіжність 1:

* REQ-01 розрізняє Authenticated User і Admin, а в моделі лаби 1 не було поля, яке їх розрізняє. Додано User.role   
* Коміт: https\://github.com/wameny/KPI2year/commit/92d44b9739702c357738bcf437d7ca885ef79381

Розбіжність 2:

* REQ-03 вимагає замовлення-кошика, а REQ-04 оплаченого замовлення, але в моделі лаби 1 були лише статуси PROCESSED, DELIVERED, CANCELLED. Додано DRAFT і PAID  
* Коміт: https\://github.com/wameny/KPI2year/commit/1d8ad5ffe98a9e4519488b03ab3158ada89fc978

Розбіжність 3:

* У spec лаби 2 є зовнішній актор Payment Service, але в моделі даних лаби 1 не було сутності, яка зберігає результат оплати. Order.status показує лише стан замовлення, а відхилена оплата ніде не фіксувалась. Додано сутність Payment зі зв'язком Order 1:N Payment.  
* Коміт: [https\://github.com/wameny/KPI2year/commit/9b66bfd61b5410266a09dff39a57c1398b3de09a](https://github.com/wameny/KPI2year/commit/9b66bfd61b5410266a09dff39a57c1398b3de09a)

Виправлення за результатом перевірки:

Виправлення 1: Додано NFR вимоги. Коміт: [https\://github.com/wameny/KPI2year/commit/060324db7162349d88d54ce2cff46ffa00615624](https://github.com/wameny/KPI2year/commit/060324db7162349d88d54ce2cff46ffa00615624)

Виправлення 2: Оновлено EARS-вимоги та Gherkin сценарії для UC вглиб. Також оновлено матрицю трасування згідно вимогам. Коміт: [https\://github.com/wameny/KPI2year/commit/509bc7241740ab48bea904076534c2a69439e204](https://github.com/wameny/KPI2year/commit/509bc7241740ab48bea904076534c2a69439e204)

Виправлення 3: Оновлено задачу Payment Service. Коміт: [https\://github.com/wameny/KPI2year/commit/ab14ddc3f9624cc302811937d6247cbadd6ff90b](https://github.com/wameny/KPI2year/commit/ab14ddc3f9624cc302811937d6247cbadd6ff90b)

Виправлення 4: додано розділ “Поза обсягом” з поясненнями, а також критерії прийняття. Коміт: [https\://github.com/wameny/KPI2year/commit/8f9d39d35f92a91f4f8e5d29395e57dba130273f](https://github.com/wameny/KPI2year/commit/8f9d39d35f92a91f4f8e5d29395e57dba130273f)

Ключове рішення — які альтернативи зважив і чому обрав цю (ADR):

1. Сутність Order використовується у якості кошика.  
   1. Альтернатива: окремі сутності Cart і CartItem.   
   2. Чому обрано це: Order і OrderBook уже зберігають позиції та кількість, тому окремий кошик дублював би їхню структуру й вимагав би нових сутностей, UC і вимог. Статус DRAFT дає той самий результат, а після оформлення замовлення просто змінює статус.   
2. Admin як окремий актор без успадкування від Authenticated User.  
   1. Альтернатива: Admin наслідує Authenticated User   
   2. Чому обрано це: адміністратор керує магазином, а не купує, тому йому не потрібні функції покупця..   
3. Authenticated User як спеціалізація Guest.  
   1. Альтернатива: два незалежні актори з повторенням спільних UC.  
   2. Чому обрано це: авторизований користувач уміє все, що гість (перегляд каталогу), плюс замовлення. Генералізація прибирає дублювання стрілок і чітко показує, що додається після входу. 

Перевірка — узгодженість із попередньою моделлю (як перевірив):  
Звірила spec завдання 2 з моделлю завданням 1: актори (Authenticated User, Admin, Payment Service) із сутністю User і платіжним процесом, UC і вимоги з атрибутами та статусами Order, OrderBook і Book. Усі невідповідності, що знайшла, виправлені у lab1 окремими комітами (розбіжності 1-3).

Здача: (а) посилання на GitHub PR [https\://github.com/wameny/KPI2year/tree/master/lab2](https://github.com/wameny/KPI2year/tree/master/lab2)

 (б) цей заповнений DEFENSE.