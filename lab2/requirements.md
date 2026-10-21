## 1. EARS-вимоги

- **REQ-01** (Event): WHEN Guest або Admin надсилає правильні email і пароль, система SHALL автентифікувати його й надати доступ відповідно до його ролі. - UC2
- **REQ-02** (Unwanted): IF Admin створює Book з isbn, який уже існує, THEN система SHALL відхилити створення й показати повідомлення про помилку. - UC10
- **REQ-03** (Event): WHEN Authenticated User додає до замовлення книгу, яка вже є в ньому, система SHALL збільшити quantity в існуючому OrderBook замість створення дубліката. - UC4, UC5
- **REQ-04** (Event): WHEN Authenticated User ініціює оплату замовлення зі статусом PROCESSED, система SHALL передати Payment Service суму Order.totalAmount. - UC7, UC8
- **REQ-05** (Event): WHEN Admin змінює Book.stockQuantity на невід'ємне число, система SHALL зберегти нове значення. - UC11
- **REQ-06** (Ubiquitous): Система SHALL не допускати створення Order або OrderBook, що посилаються на неіснуючих User чи Book. - UC4, UC6

## 2. Gherkin Scenarios

**S1** Вхід у акаунт

- Як Guest
- Я хочу увійти у свій акаунт
- Щоб отримати доступ до функцій акаунту, які мені доступні

```
Scenario: Успішний вхід в акаунт
  Given існує User з email "reader@mail.com" і роллю USER
  When Guest надсилає email "reader@mail.com" і правильний пароль
  Then система автентифікує його як Authenticated User
  And надає доступ до оформлення замовлень
```

**S2**

- Як Admin
- Я хочу бачити помилку про створення вже існуючої книги
- Щоб не було дублікатів у каталозі

```
Scenario: Admin не може створити книгу з уже існуючим isbn
  Given у каталозі є Book з isbn 9786171512122
  When Admin створює нову Book з isbn 9786171512122
  Then система відхиляє створення
  And показує повідомлення про помилку
  And кількість Book у каталозі не змінюється
```

**S3**

- Як Authenticated User
- Я хочу додавати до замовлення книги
- Щоб у замовленні не було зайвих дублікатів

```
Scenario: Повторне додавання тієї самої книги збільшує кількість
  Given Authenticated User має Order зі статусом DRAFT
  And в Order є OrderBook з "book-1" і quantity = 1
  When він додає до замовлення "book-1" ще раз
  Then в Order залишається один OrderBook для "book-1"
  And його quantity дорівнює 2
```

**S4**

- Як Authenticated User
- Я хочу ініціювати оплату замовлення
- Щоб завершити оформлення замовлення

```
Scenario: Ініціювання оплати передає суму платіжному сервісу
  Given Authenticated User має Order зі статусом PROCESSED і totalAmount = 450.00
  When він ініціює оплату замовлення
  Then система передає Payment Service суму totalAmount = 450.00
```

**S5**

- Як Admin
- Я хочу змінити к-сть книг на складі
- Щоб запобігти розбіжності даних в реальності та на складі

```
Scenario: Admin змінює залишок книги
  Given у каталозі є Book "book" зі stockQuantity = 10
  When Admin встановлює stockQuantity = 20
  Then stockQuantity книги "book" дорівнює 20
```
