**Тест 1.2**: Попытка создания без обязательного поля `sellerID`.
  - Ожидаемый результат: HTTP 400, сообщение об ошибке.
FAILED
Status 400: Обязательное поле отсутствует | AssertionError: expected response to have status code 400 but got 200
FAILED
Ответ содержит информацию об ошибке | AssertionError: expected { Object (status) } to have property 'result'
FAILED
Сообщение указывает на отсутствие sellerID | TypeError: Cannot read properties of undefined (reading 'message')


 **Тест 2.1**: Успешное получение существующего объявления.
FAILED
"name": "item 'а вот тут ошибка=)'", ошибка в имени.
        "price": 3000, данные из созданного объявления умножаются x2.

3.Получить все объявления пользователя
FAILED
All items belong to the seller | AssertionError: expected 1000 to deeply equal undefined
