# Spec for data: Book Shop

## 1. Intention. Намір

## 2. Entities and attributes. Сутності та атрибути

1. User
   - `id` _(PK, string)_: ідентифікатор
   - `fullname` _(string)_: ім'я користувача
   - `password` _(string)_: пароль
   - `email` _(string)_: електронна пошта
   - `phoneNumber` _(string)_: номер телефону
   - `createdAt` _(date)_: дата створення акаунту
   - `birthDate` _(date)_: дата народження

2. Category
   - `id` _(PK, string)_: ідентифікатор
   - `name` _(string)_: назва категорії
   - `description` _(string)_: опис категорії

3. Book
   - `id` _(PK, string)_: ідентифікатор
   - `isbn` _(string, unique)_: міжнародний книжковий номер
   - `category` _(FK: Category.name, string)_: категорія
   - `title` _(string)_: назва
   - `description` _(string)_: опис
   - `publishingHouse` _(string)_: назва видавництва
   - `price` _(number)_: ціна
   - `author` _(FK: Authors.fullname, string)_: автор
   - `stockQuantity` _(number)_: кількість у наявності

4. Author
   - `id` _(PK, string)_: ідентифікатор
   - `fullname` _(string)_: ім'я автора
   - `biography` _(string)_: біографія

5. Order
   - `id` _(PK, string)_: ідентифікатор
   - `userId` _(FK: User.id, string)_: ідентифікатор користувача
   - `totalAmount` _(number)_: сума замовлення
   - `status` _(string)_: статус замовлення [PROCESSED, DELIVERED, CANCELLED]
   - `createdAt` _(date)_: дата створення замовлення

6. OrderBook
   - `id` _(PK, string)_: ідентифікатор
   - `orderId` _(FK: Order.id, string)_: ідентифікатор замовлення
   - `bookId` _(FK: Book.id, sting)_: ідентифікатор книги
   - `quantity` _(number)_: кількість одиниць книг
   - `priceTotal` _(number)_: ціна за дану позицію

## 3. Relationships

- `User` 1 to N `Order`: у одного користувача може бути багато замовлень
- `Category` N to N `Book`: у одної категорії може бути багато книг і у одної книги може бути багато категорій (наприклад у одної книги може бути декілька жанрів: детектив, трилер)
- `Author` 1 to N `Book`: у одного автора може бути багато книг, але у книги один автор
- `Order` 1 to N `OrderBook`: у одного замовлення може бути багато позицій книг
- `Book` 1 to N `OrderBook`: одна книга може фігурувати у багатьох замовленнях

## 4. Acceptance Criteria
