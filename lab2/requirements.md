## 1. EARS-вимоги

- **REQ-01** (Event): WHEN Guest або Admin надсилає правильні email і пароль, система SHALL автентифікувати його й надати доступ відповідно до його ролі. - UC2
- **REQ-02** (Unwanted): IF Admin створює Book з isbn, який уже існує, THEN система SHALL відхилити створення й показати повідомлення про помилку. - UC10
- **REQ-03** (Event): WHEN Authenticated User додає до замовлення книгу, яка вже є в ньому, система SHALL збільшити quantity в існуючому OrderBook замість створення дубліката. - UC4, UC5
- **REQ-04** (Event): WHEN Authenticated User ініціює оплату замовлення зі статусом PROCESSED, система SHALL передати Payment Service суму Order.totalAmount. - UC7, UC8
- **REQ-05** (Event): WHEN Admin змінює Book.stockQuantity на невід'ємне число, система SHALL зберегти нове значення. - UC11
- **REQ-06** (Ubiquitous): Система SHALL не допускати створення Order або OrderBook, що посилаються на неіснуючих User чи Book. - UC6
