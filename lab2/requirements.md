## EARS-вимоги (оновлені)

- **REQ-01** (Event-driven): WHEN Authenticated User ініціює оплату для Order зі статусом PROCESSED та за відсутності пов'язаного Payment зі статусом SUCCESS або PENDING, система SHALL створити новий запис Payment зі статусом PENDING на суму Order.totalAmount і передати запит до Payment Service. (UC7)
- **REQ-02** (Unwanted behavior): IF Authenticated User намагається ініціювати оплату для Order зі статусом, відмінним від PROCESSED (DRAFT, PAID, DELIVERED, CANCELLED), THEN система SHALL відхилити запит із кодом помилки 409 Conflict та не створювати запис Payment. (UC7)
- **REQ-03** (Event-driven): WHEN Payment Service надсилає підтвердження успішної транзакції для існуючого Payment зі статусом PENDING, система SHALL змінити статус цього Payment на SUCCESS та перевести пов'язаний Order зі статусом PROCESSED у статус PAID. (UC8)
- **REQ-04** (Event-driven): WHEN Payment Service надсилає повідомлення про відхилення транзакції для існуючого Payment зі статусом PENDING, система SHALL змінити статус цього Payment на FAILED та залишити статус пов'язаного Order рівним PROCESSED. (UC8)
- **REQ-05** (State-driven): WHILE Order перебуває у статусі PROCESSED і всі попередні пов'язані записи Payment мають статус FAILED, система SHALL дозволяти Authenticated User повторно ініціювати оплату зі створенням нового незалежного запису Payment (зберігаючи зв'язок Order 1:N Payment). (UC7)

## Нефункціональні вимоги (NFR)

- **NFR-01 (Швидкодія):** Час відповіді сервера при ініціації оплати (UC7) та обробці відповіді від платіжного сервісу (UC8) SHALL не перевищувати 500 мс при навантаженні до 100 одночасних користувачів.
- **NFR-02 (Пристрої та браузери):** Вебінтерфейс магазину SHALL коректно працювати на смартфонах і комп'ютерах із шириною екрана від 360 px до 2560 px в останніх 2 версіях браузерів Chrome, Firefox, Safari та Edge.
- **NFR-03 (Мова та технології):** Інтерфейс і повідомлення про статус оплати SHALL відображатися українською мовою (ціни в гривнях UAH із точністю до 2 знаків після коми). Серверна частина SHALL бути реалізована на Node.js (TypeScript) / NestJS із базою даних PostgreSQL 15+.

## 2. Gherkin Scenarios (оновлені)

**S1**

- Як Authenticated User
- Я хочу ініціювати оплату оформленого замовлення та отримати підтвердження
- Щоб завершити покупку й перевести замовлення в статус оплаченого

```
Scenario: Успішна оплата замовлення переводить його в статус PAID
  Given Authenticated User має Order "ord-101" зі статусом PROCESSED і totalAmount = 450.00
  And для Order "ord-101" відсутні попередні успішні записи Payment
  When Authenticated User ініціює оплату замовлення "ord-101"
  Then система створює запис Payment зі статусом PENDING і сумою 450.00
  And передає до Payment Service запит на оплату суми 450.00 для "ord-101"
  When Payment Service повідомляє про успішний результат оплати для "ord-101"
  Then система оновлює статус запису Payment на SUCCESS
  And змінює статус Order "ord-101" на PAID
```

**S2**

- Як Authenticated User
- Я хочу бачити коректний статус замовлення при відмові в оплаті
- Щоб знати, що кошти не списано, а замовлення очікує на оплату

```
Scenario: Відхилена оплата фіксується як FAILED, а замовлення лишається PROCESSED
  Given Authenticated User має Order "ord-102" зі статусом PROCESSED і totalAmount = 320.00
  And користувач ініціював оплату, створено Payment зі статусом PENDING
  When Payment Service повідомляє про відхилення оплати (недостатньо коштів) для "ord-102"
  Then система оновлює статус запису Payment на FAILED
  And залишає статус Order "ord-102" незмінним (PROCESSED)
  And відображає користувачеві повідомлення про відхилення платежу
```

**S3**

- Як Authenticated User
- Я хочу повторити оплату замовлення, попередня спроба якого була відхилена
- Щоб все ж оплатити сформоване замовлення без повторного збирання кошика

```
Scenario: Повторна ініціація оплати створює новий запис Payment
  Given Authenticated User має Order "ord-102" зі статусом PROCESSED і totalAmount = 320.00
  And для Order "ord-102" вже існує один запис Payment зі статусом FAILED
  When Authenticated User повторно ініціює оплату замовлення "ord-102"
  Then система створює новий (другий) запис Payment зі статусом PENDING і сумою 320.00
  And зберігає попередній запис Payment зі статусом FAILED в історії транзакцій
  And передає до Payment Service новий запис на оплату суми 320.00
```

**S4**

- Як Authenticated User
- Я хочу, щоб система блокувала оплату чернеток або вже оплачених замовлень
- Щоб уникнути подвійного списання коштів або оплати неоформленого кошика

```
Scenario: Відхилення ініціації оплати для статусів, відмінних від PROCESSED
  Given Authenticated User має Order "ord-103" зі статусом DRAFT, PAID, DELIVERED або CANCELLED
  When Authenticated User намагається ініціювати оплату замовлення "ord-103"
  Then система відхиляє запит на ініціацію оплати
  And не створює новий запис Payment
  And не надсилає запит до Payment Service
  And повертає помилку про неприпустимий статус замовлення для оплати
```
