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

инструкция по воспроизведению автотестов - JAVA SCRIPT.

1.Сохранить(Создать) объявление

pre-request

// Генерируем случайный sellerID в диапазоне 111111-999999
const min = 111111;
const max = 999999;
const randomSellerID = Math.floor(Math.random() * (max - min + 1)) + min;

// Устанавливаем переменную для использования в теле запроса
pm.variables.set("randomSellerID", randomSellerID);

// Для отладки (можно увидеть в Console)
console.log("Сгенерирован sellerID: ", randomSellerID);

post-responce

// Проверка успешного ответа
if (pm.response.code === 200) {
    const response = pm.response.json();
    
    // Извлекаем UUID из ответа
    const newItemId = response.id;
    
    // Сохраняем в переменную окружения (перезаписывается при каждом запросе)
    pm.environment.set("createdItemId", newItemId);
    
    // Для отладки
    console.log("Новый UUID:", newItemId);
} else {
    pm.test("Ошибка при создании объявления", () => false);
}

2.Получить объявление по айтем ID

pre-request

// Используем ID из предыдущего запроса
const itemId = pm.collectionVariables.get("createdItemId");
pm.variables.set("itemId", itemId);

post-responce

pm.test("Status code is 200", () => pm.response.to.have.status(200));

// Проверка структуры ответа
pm.test("Response has correct structure", () => {
    const response = pm.response.json();
    pm.expect(response).to.include.keys("id", "sellerId", "name", "price");
    pm.expect(response.id).to.eql(pm.environment.get("createdItemId"));
});

3. Получить статистику по ID
   
pre-request

// Используем ID созданного объявления
const itemId = pm.collectionVariables.get("createdItemId");
pm.variables.set("itemId", itemId);

post-responce

pm.test("Status code is 200", () => pm.response.to.have.status(200));

pm.test("Statistics fields exist", () => {
    const stats = pm.response.json();
    pm.expect(stats).to.include.keys("likes", "viewCount", "contacts");
});

4.Получить все объявления пользователя

post-responce

pm.test("Status code is 200", () => pm.response.to.have.status(200));

pm.test("All items belong to the seller", () => {
    const items = pm.response.json();
    items.forEach(item => {
        pm.expect(item.sellerId).to.eql(pm.environment.get("tempSellerID"));
    });
});

5.Удалить объявление по идентификатору

post -responce

// Проверка статуса удаления
pm.test("Удаление успешно (200)", () => pm.response.to.have.status(200));



