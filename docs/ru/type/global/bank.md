Тип `Cubux.Bank`
================

Объект со следующими полями:

Поле           | Тип          | Описание
-------------- | ------------ | --------
**`uuid`** PK  | `uuid`       | UUID
`name`         | `string`     | Название
`country_code` | `string(3)`  | Код страны [`Cubux.Country`][Cubux.Country] из поля `code`
`provider_id`  | `uint`, NULL | ID провайдера [`Cubux.Provider`][Cubux.Provider] для связи с реальным банком
`logo`         | `url`, NULL  | Ссылка на логотип

Название банка указано на языке страны, в которой он находится, или
на английском.

Если поле `provider_id` заполнено, то для счетов этого банка возможно настроить
связь с соответствующим реальным банком, информация о котором хранится в данных
о провайдерах [`Cubux.Provider`][Cubux.Provider].
См. [Связь с реальными банками][bank-integration].


[Cubux.Country]: country.md
[Cubux.Provider]: provider.md
[bank-integration]: ../../bank-integration/README.md
