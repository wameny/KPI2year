# Spec for data: Book Shop

## 1. Intention. Намір

## 2. Entities and attributes. Сутності та атрибути

1. User
   - id _(PK, string)_: ідентифікатор
   - fullname _(string)_: ім'я користувача
   - password _(string)_: пароль
   - email _(string)_: електронна пошта
   - phoneNumber _(string)_: номер телефону
   - createdAt _(date)_: дата створення акаунту
   - birthDate _(date)_: дата народження

2. Category
   - id _(PK, string)_: ідентифікатор
   - name _(string)_: назва категорії
   - description _(string)_: опис категорії

3. Book
   - id _(PK, string)_: ідентифікатор
   - isbn _(string, unique)_: міжнародний книжковий номер
   - category _(FK: Category.name, string)_: категорія
   - title _(string)_: назва
   - description _(string)_: опис
   - publishingHouse _(string)_: назва видавництва
   - price _(number)_: ціна
   - author _(FK: Authors.fullname, string)_: автор
   - stockQuantity _(number)_: кількість у наявності

4. Author
   - id _(PK, string)_: ідентифікатор
   - fullname _(string)_: ім'я автора
   - biography _(string)_: біографія

5. Order
   - id _(PK, string)_: ідентифікатор
   - userId _(FK: User.id, string)_: ідентифікатор користувача
   - totalAmount _(number)_: сума замовлення
   - status _(string)_: статус замовлення [PROCESSED, DELIVERED, CANCELLED]
   - createdAt _(date)_: дата створення замовлення

6. OrderItem
   - id _(PK, string)_: ідентифікатор
   - orderId _(FK: Order.id, string)_: ідентифікатор замовлення
   - bookId _(FK: Book.id, sting)_: ідентифікатор книги
   - quantity _(number)_: кількість одиниць книг
   - priceTotal _(number)_: ціна за дану позицію

## 3. Relationships

## 4. Acceptance Criteria
