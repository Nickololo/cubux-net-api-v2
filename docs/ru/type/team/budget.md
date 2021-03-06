Тип `Cubux.Budget`
==================

Объект со следующими полями:

Поле | Тип | Описание
---- | --- | --------
**`month`** PK         | `month` | **Обязательно**. Месяц
**`category_uuid`** PK | `uuid`  | **Обязательно**. UUID категории
`amount`               | `float` | **Обязательно**. Сумма (в том числе и `0`)

**Замечание**: Бюджет родительской категории на данный месяц должен
включать в себя (т.е. быть больше или равным) сумме бюджетов дочерних
категорий на этот же месяц.

Например:

    Еда          2500 ≥ 500 + 2000
        Хлеб      500
        Мясо     2000

При обновлении бюджета дочерних категорий в случае превышения суммарного
бюджета родительской категории сумма последней будет увеличена, но
только если она не задаётся одновременно с этим обновлением.

Так, для примера выше, обновление только бюджета "Мясо" до `2100`
увеличит также и бюджет "Еда" до `2600 = 500 + 2100`. Если обновлять
одновременно бюджеты "Хлеб" до `550` И "Мясо" до `2100`, то бюджет "Еда"
увеличится до `2650 = 550 + 2100`.

Но если, например, попыпаться изменить только бюджет "Еда" до `2450`
(получим `2450 < 500 + 2000`) или одновременно "Еда" до `2550` и "Хлеб"
до `600` (получим `2550 < 600 + 2000`), то такое обновление будет
отклонено.

**Замечание**: Если на данный месяц для данной категории данные о
бюджете отсутствуют явно, то они наследуются из ближайшего прошлого
месяца для каждой категории соответственно.
