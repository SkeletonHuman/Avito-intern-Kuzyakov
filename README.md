# Avito-intern-Kuzyakov
Тестовое задание для стажёра QA-направления (весенняя волна 2025)
1. Неккоректная работа счетчика поиска подходящих вариантов после сортировки (Показывает 6 вариантов, по факту 9) - HIGH
2. В поле результатов поиска, найден вариант не подходящий под сортировку (Таунхаус 150кв.м2 аренда в месяц 45000), что не соответствует результату сортировки (Аренда посуточно) - HIGH
3. В поле результатов поиска, найден вариант не подходящий под сортировку (2к дом 45кв.м2), товар добавлен из другой категории товаров (товар с доставкой) - HIGH
4. В поле результатов поиска, найден вариант не подходящий под сортировку (Дом Мечты. Де Агостини), товар добавлен из другой категории товаров (на фотографии игрушечный дом ) - HIGH
5. В поле результатов поиска, найден вариант не подходящий под сортировку (5к дом 200кв.м2), товар добавлен из другой категории товаров (на фотографии продажа окон) - HIGH
6. Позиция (2к дом 45кв.м2) неккоректный подсчет аренды за весь период - HIGH
7. Button - Ничего не найдено - LOW
8. Пагинация снизу основной страницы - LOW
9. Ничего не найдено в выбранной области поиска (обяъвления были найдены) - LOW
10. Button - Найти - отсутствует буква И - LOW


# Тест-кейсы для проверки API микросервиса

## Версия 1 API

### 1. Создание объявления (POST /api/1/item)
- **Тест 1.1**: Успешное создание объявления с валидными данными.
  - Ожидаемый результат: HTTP 200, тело ответа содержит `id` и переданные данные.
- **Тест 1.2**: Попытка создания без обязательного поля `sellerID`.
  - Ожидаемый результат: HTTP 400, сообщение об ошибке.
- **Тест 1.3**: Неверный тип данных в поле `price` (строка вместо числа).
  - Ожидаемый результат: HTTP 400, сообщение об ошибке.

### 2. Получение объявления по ID (GET /api/1/item/{{itemID}})
- **Тест 2.1**: Успешное получение существующего объявления.
  - Ожидаемый результат: HTTP 200, тело ответа содержит данные объявления.
- **Тест 2.2**: Запрос несуществующего `id`.
  - Ожидаемый результат: HTTP 404.
- **Тест 2.3**: Невалидный формат `id` (например, символы вместо цифр).
  - Ожидаемый результат: HTTP 400.

### 3. Получение всех объявлений продавца (GET /api/1/{{SellerID}}/item)
- **Тест 3.1**: Успешный запрос для существующего `sellerID`.
  - Ожидаемый результат: HTTP 200, список объявлений.
- **Тест 3.2**: Запрос для несуществующего `sellerID`.
  - Ожидаемый результат: HTTP 200, пустой список.
- **Тест 3.3**: Неверный формат `sellerID` (например, строка).
  - Ожидаемый результат: HTTP 400.

### 4. Получение статистики (GET /api/1/statistic/{{itemID}})
- **Тест 4.1**: Успешный запрос для существующего `id`.
  - Ожидаемый результат: HTTP 200, данные статистики.
- **Тест 4.2**: Запрос несуществующего `id`.
  - Ожидаемый результат: HTTP 404.
- **Тест 4.3**: Серверная ошибка (имитация через невалидный ввод).
  - Ожидаемый результат: HTTP 500.

---

## Версия 2 API

### 1. Удаление объявления (DELETE /api/2/item/{{itemID}})
- **Тест 5.1**: Успешное удаление существующего объявления.
  - Ожидаемый результат: HTTP 200.
- **Тест 5.2**: Попытка удаления несуществующего `id`.
  - Ожидаемый результат: HTTP 404.
- **Тест 5.3**: Невалидный формат `id`.
  - Ожидаемый результат: HTTP 400.

### 2. Получение статистики (GET /api/2/statistic/{{itemID}})
- **Тест 6.1**: Успешный запрос для существующего `id`.
  - Ожидаемый результат: HTTP 200, данные статистики.
- **Тест 6.2**: Запрос несуществующего `id`.
  - Ожидаемый результат: HTTP 404.
 
# АВТОТЕСТЫ

Создание объявления (POST /api/1/item)

 **Тест 1.1**: Успешное создание объявления с валидными данными.
  - Ожидаемый результат: HTTP 200, тело ответа содержит `id` и переданные данные.

pre - request

// Генерируем случайный sellerID в диапазоне 111111-999999
const min = 111111;
const max = 999999;
const randomSellerID = Math.floor(Math.random() * (max - min + 1)) + min;

// Сохраняем в переменную коллекции с именем "SellerID"
pm.collectionVariables.set("SellerID", randomSellerID);

// Также сохраняем во временную переменную "randomSellerID" (для совместимости с телом запроса)
pm.variables.set("randomSellerID", randomSellerID);

// Для отладки
console.log("Сгенерирован SellerID:", randomSellerID);

post- responce 

// 1. Проверка статуса ответа
pm.test("Status 200: Объявление успешно создано", function() {
    pm.response.to.have.status(200);
    pm.response.to.have.jsonBody();
});

// 2. Проверка структуры ответа
const responseBody = pm.response.json();
pm.test("Ответ содержит поле 'status'", function() {
    pm.expect(responseBody).to.have.property('status');
});

// 3. Извлечение и валидация ID объявления
let itemID;
pm.test("ID объявления валиден", function() {
    const statusMessage = responseBody.status;
    pm.expect(statusMessage).to.include("Сохранили объявление - ");
    
    itemID = statusMessage.split(" - ")[1];
    pm.expect(itemID).to.match(/^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$/);
    
    // Сохраняем для последующих запросов
    pm.collectionVariables.set("itemID", itemID);
    console.log("Создано объявление с ID:", itemID);
});

// 4. Проверка SellerID
const savedSellerID = pm.collectionVariables.get("SellerID");
pm.test("SellerID валиден", function() {
    pm.expect(savedSellerID).to.be.a('number');
    pm.expect(savedSellerID).to.be.within(111111, 999999);
    console.log("Использован SellerID:", savedSellerID);
});


**Тест 1.2**: Попытка создания без обязательного поля `sellerID`.
  - Ожидаемый результат: HTTP 400, сообщение об ошибке.

// 1. Проверка статуса ответа
pm.test("Status 400: Обязательное поле отсутствует", function() {
    pm.response.to.have.status(400);
    pm.response.to.have.jsonBody();
});

// 2. Проверка структуры ошибки
const responseBody = pm.response.json();
pm.test("Ответ содержит информацию об ошибке", function() {
    pm.expect(responseBody).to.have.property('result');
    pm.expect(responseBody.result).to.have.property('message');
    pm.expect(responseBody).to.have.property('status', '400');
});

// 3. Проверка сообщения об ошибке
pm.test("Сообщение указывает на отсутствие sellerID", function() {
    const errorMessage = responseBody.result.message.toLowerCase();
    pm.expect(errorMessage).to.include("sellerid");
    pm.expect(errorMessage).to.include("обязательное");
    pm.expect(errorMessage).to.include("поле");
});


**Тест 1.3**: Неверный тип данных в поле `price` (строка вместо числа).
  - Ожидаемый результат: HTTP 400, сообщение об ошибке.
    
    // 1. Проверка статуса ответа
pm.test("Status 400: Неверный тип данных", function() {
    pm.response.to.have.status(400);
    pm.response.to.have.jsonBody();
});


Получение объявления по ID (GET /api/1/item/{{itemID}})

 **Тест 2.1**: Успешное получение существующего объявления.
  - Ожидаемый результат: HTTP 200, тело ответа содержит данные объявления

// 1. Проверка статуса ответа
pm.test("Status 200: Объявление получено", function() {
    pm.response.to.have.status(200);
    pm.response.to.have.jsonBody();
}); 
    // Проверка типа данных
    pm.expect(advertisement.name).to.be.a('string');
    pm.expect(advertisement.price).to.be.a('number');
    pm.expect(advertisement.sellerId).to.be.a('number');
});

**Тест 2.2**: Запрос несуществующего `id`.

pm.test("Status 404", function() {
    pm.response.to.have.status(404);
});

pm.test("Тело ответа содержит сообщение об ошибке", function() {
    const response = pm.response.json();
    pm.expect(response).to.have.property("result");
    pm.expect(response.result).to.include("not found");
});

Кейс 3. Некорректный ID (400 Bad Request)

pm.test("Статус код 400", function() {
    pm.response.to.have.status(400);
});

pm.test("Тело ответа содержит ошибку валидации", function() {
    const response = pm.response.json();
    pm.expect(response.result.messages.id).to.eql("Must be a valid UUID");
});




АВТОТЕСТ для GET-запроса "Получить статистику по объявлению"
Endpoint: {{baseUrl}}/api/1/statistic/{{itemID}}
Метод: GET

Кейс 1. Успешное получение статистики (200 OK)

// Проверка статуса
pm.test("Status 200: Статистика получена", () => {
    pm.response.to.have.status(200);
    pm.response.to.be.json;
});

// Проверка структуры
pm.test("Ответ содержит статистику (likes, viewCount, contacts)", () => {
    const stats = pm.response.json()[0]; // Берём первый элемент массива
    pm.expect(stats).to.have.all.keys("likes", "viewCount", "contacts");
    pm.expect(stats.likes).to.be.a("number").and.to.be.at.least(0);
    pm.expect(stats.viewCount).to.be.a("number").and.to.be.at.least(0);
    pm.expect(stats.contacts).to.be.a("number").and.to.be.at.least(0);
});

// Проверка времени ответа
pm.test("Время ответа < 300 мс", () => {
    pm.expect(pm.response.responseTime).to.be.below(300);
});

Кейс 2. Объявление не найдено (404 Not Found)

// 1. Проверка статус-кода
pm.test("Status Code: 404 Not Found", function() {
    pm.response.to.have.status(404);
});

// 2. Проверка структуры ответа
pm.test("Ответ содержит поле 'result' и 'status'", function() {
    const response = pm.response.json();
    pm.expect(response).to.have.property("result");
    pm.expect(response).to.have.property("status");
});


Кейс 3. Невалидный ID (400 Bad Request)

// 1. Проверка статус-кода
pm.test("Status Code: 400 Bad Request", function() {
    pm.response.to.have.status(400);
});

// 2. Проверка структуры ответа
pm.test("Ответ содержит поле 'result' и 'status'", function() {
    const response = pm.response.json();
    pm.expect(response).to.have.property("result");
    pm.expect(response).to.have.property("status");
});


Тест-кейсы для GET-запроса "Получить все объявления продавца"
Endpoint: {{baseUrl}}/api/1/{{SellerID}}/item
Метод: GET

