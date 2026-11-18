## Учасники (Actors та Components)

- **Authenticated User** — авторизований користувач, що ініціює оплату.
- **Client UI** — фронтенд-інтерфейс для відправки запитів і відображення статусів.
- **PaymentService** — серверна бізнес-логіка, що перевіряє правила та керує транзакціями.
- **Order** — сутність замовлення з ER-моделі.
- **Payment** — сутність платежу з ER-моделі.
- **External Payment Gateway** — зовнішній платіжний сервіс.

## Кроки виконання сценаріїв

### S1: Успішна оплата замовлення

1. **Authenticated User** натискає кнопку оплати в **Client UI**.
2. **Client UI** надсилає запит на ініціацію платежу до **PaymentService**.
3. **PaymentService** запитує статус замовлення у **Order** (перевіряє, чи статус `PROCESSED`) та відсутність активних платежів у **Payment**.
4. **PaymentService** створює новий запис у **Payment** зі статусом `PENDING` та сумою замовлення (`totalAmount`).
5. **PaymentService** передає запит на транзакцію до **External Payment Gateway**.
6. **External Payment Gateway** обробляє платіж та повертає підтвердження успіху (`SUCCESS`).
7. **PaymentService** оновлює статус запису в **Payment** на `SUCCESS`.
8. **PaymentService** змінює статус замовлення в **Order** з `PROCESSED` на `PAID`.
9. **Client UI** відображає користувачеві повідомлення про успішну оплату.

### S2: Відхилена оплата

1. **Authenticated User** натискає кнопку оплати в **Client UI**.
2. **Client UI** надсилає запит на ініціацію платежу до **PaymentService**.
3. **PaymentService** запитує статус замовлення у **Order** (перевіряє, чи статус `PROCESSED`) та відсутність активних платежів у **Payment**.
4. **PaymentService** створює новий запис у **Payment** зі статусом `PENDING` та сумою замовлення (`totalAmount`).
5. **PaymentService** передає запит на транзакцію до **External Payment Gateway**.
6. **[Розгалуження помилок платіжного шлюзу]:**
   - _Альтернатива А (Відмова банку / Declined):_ **External Payment Gateway** повертає статус `DECLINED` (наприклад, картку заблоковано або ліміт перевищено). **PaymentService** оновлює статус запису в **Payment** на `DECLINED`, а замовлення в **Order** залишає без змін (`PROCESSED`).
   - _Альтернатива Б (Недостатньо коштів / Insufficient Funds):_ **External Payment Gateway** повертає статус `INSUFFICIENT_FUNDS`. **PaymentService** оновлює статус запису в **Payment** на `FAILED`, а замовлення в **Order** залишає у статусі `PROCESSED`.
   - _Альтернатива В (Технічний збій / Unknown):_ **External Payment Gateway** не відповідає вчасно або повертає `UNKNOWN`. **PaymentService** фіксує статус запису в **Payment** як `UNKNOWN`, залишаючи **Order** у статусі `PROCESSED` для можливості повторної перевірки або спроби.
7. **Client UI** виводить користувачеві деталізоване повідомлення про причину відхилення платежу.

### S3: Повторна ініціація оплати

1. **Authenticated User** повторно натискає «Оплатити» в **Client UI** для замовлення, де попередній платіж має статус `FAILED`
2. **Client UI** передає запит до **PaymentService**.
3. **PaymentService** перевіряє в **Order**, що замовлення все ще у статусі `PROCESSED`, а в **Payment** — що попередні спроби мають статус `FAILED`
4. **PaymentService** створює новий незалежний запис у **Payment** зі статусом `PENDING` (зберігаючи старий `FAILED` в історії)
5. **PaymentService** відправляє новий запит на оплату до **External Payment Gateway**.
6. **[Розгалуження результату транзакції]:**
   - _Альтернатива А (Успіх):_ **External Payment Gateway** повертає підтвердження (`SUCCESS`), після чого **PaymentService** оновлює поточний `Payment` на `SUCCESS`, а `Order` переводить у статус `PAID`.
   - _Альтернатива Б (Відмова):_ **External Payment Gateway** повертає відмову, після чого **PaymentService** оновлює поточний `Payment` на `FAILED`, залишаючи `Order` у статусі `PROCESSED`.
7. **Client UI** відображає користувачеві фінальний результат нової спроби оплати.

### S4: Відхилення ініціації оплати для неприпустимих статусів

1. **Authenticated User** намагається ініціювати оплату замовлення у статусі `DRAFT`, `PAID`, `DELIVERED` або `CANCELLED` через **Client UI**.
2. **Client UI** передає запит до **PaymentService**.
3. **PaymentService** отримує поточний статус із **Order** і виявляє, що він не дорівнює `PROCESSED`.
4. **PaymentService** відхиляє запит із кодом помилки `409 Conflict` (не створюючи запис у **Payment** і не звертаючись до **External Payment Gateway**).
5. **Client UI** відображає помилку про неприпустимий статус замовлення для оплати[cite: 1].
