Загрузка файлов (устаревший способ)
===================================

> **Важно!** Этот способ является устаревшим и оставлен для обратной
> совместимости. Используйте актуальную [загрузку изображений][images].

Файлы пользователей хранятся на отдельном сервере с отдельным адресом.
При загрузке файлы подвергаются обработке согласно их назначению.

В связи с этим настоящая загрузка файлов выполняется с помощью запроса
непосредственно на файловый сервер. Загрузка осуществляется в _бинарном
виде_.

Однако, перез непосредственной загрузкой неодходимо получить данные для
загрузки у основного API сервера.

1.  [Подготовка загрузки файлов](api/uploader.old.md) для объектов
    конкретного типа в конкретном контексте. Например, загрузка чеков
    в конкретную команду. В ответе приходят параметры для следующего
    запроса.
2.  [Загрузка файлов](api/upload.old.md) на файловый сервер. В ответе на
    запрос из предыдущего пункта приходит URL адрес. Одни и те
    же параметры могут быть использованы для загрузки нескольких файлов.
    Все файлы будут обрабатываться на сервере одинаково согласно типу
    объектов, для которых они предназначены.
    Каждый файл отправляется отдельным запросом PUT в бинаром виде.
    В ответе приходит код, который затем можно использовать в данных
    в основном API для ссылки на нужный файл.

#### Пример

Представим, что нам необходимо загрузить два чека в одну команду.

1.  Подготовка:

        POST /api/v2/sync/team/ea30ef9d-dc5c-4a48-9325-a005145307ee/uploader HTTP/1.1
        Host: app.cubux.net
        Authorization: Bearer bd21026244e41a126d69f15ea9d1ac76579f69e3
        Content-Type: application/json; charset=UTF-8

        {
            "node_type": "draft"
        }

    Ответ

        HTTP/1.1 200 OK
        Content-Type: application/json: charset=UTF-8

        {
            "uri":     "https://file.cubux.net/api/upload?...",
            "expires": "2017-10-19T06:21:47Z"
        }

    URI из данного ответа используется далее для загрузки
    файлов. Служебные данные в URI содержат данные для авторизации,
    привязаны к конкретной команде и имеют ограниченый срок жизни.

2.  Загрузка файлов. Адрес берётся из результатов запроса выше.
    **Обратите внимание**, что заголовок `Authorization` здесь
    **не должен** использоваться, поскольку это отдельный сервис.

        PUT /api/upload?... HTTP/1.1
        Host: file.cubux.net
        Content-Type: application/octet-stream
        Content-Length: ...

        бинарное тело файла...

    Ответ:

        HTTP/1.1 200 OK
        Content-Type: application/json: charset=UTF-8

        {
            "blobKey": "Bbrno6_wrYTQlHuVBe23ULDauANXpWge"
        }

    В ответе содержится код файла который нужно использовать далее в
    основном API синхронизации.

    Обратите внимание, что загруженые, но ещё не использованые далее
    файлы имеют ограниченый срок жизни. См. [Ограничения][limitations].

    Запрос необходимо повтрить для всех файлов чеков, который требуется
    отправить в данную команду (в нашем примере 2 чека, т.е. два
    запроса).

3.  Отправка изменений (создание чеков) в основном API. В поле
    `image_blob` у чеков используется значение поля `blobKey` из ответа
    на запрос выше. У каждого чека своя картинка.

        POST /api/v2/team/ea30ef9d-dc5c-4a48-9325-a005145307ee HTTP/1.1
        Host: app.cubux.net
        Authorization: Bearer bd21026244e41a126d69f15ea9d1ac76579f69e3
        Content-Type: application/json; charset=UTF-8

        {
            "objects": {
                "drafts": [
                    {
                        "uuid":       "69e5905c-ec81-48ca-b37a-8b36db4699c1",
                        "image_blob": "Bbrno6_wrYTQlHuVBe23ULDauANXpWge"
                    },
                    {
                        "uuid":       "b7b46d23-037e-49f7-917f-77fa1e3ef2ec",
                        "image_blob": "IjTVnVsWP8QVj677iB4USFUM3z1Z3zIp"
                    }
                ]
            }
        }

    Это запрос соответствует п.1 [алгоритму][workflow] выполнения
    сценария синхронизации. Продолжение по алгоритму.


[limitations]: 20-limitations.md
[workflow]: 04-workflow.md
[images]: ./10-images.md