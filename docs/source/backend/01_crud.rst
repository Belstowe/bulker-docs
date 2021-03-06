Задача #1: Генерация игроков, базовые CRUD-операции
===================================================

База данных
-----------

.. container:: strike

    В качестве БД решено использовать **SQLite3**. Это встраиваемая SQL-база, которая позволяет не использовать отдельный DB-клиент (например, с доступом из Docker-контейнера). Для начала отличный вариант.

    Причины, почему данные хочется расположить именно в БД:

    * Сохраняемость данных;
    * Использование таких возможностей, как Foreign Key (отношения одного объекта к другим) и полей Related (позволяющие вывести связанные объекты).

    Как установить SQLite3, есть в материалах для ознакомления.

Было условлено, что необходимости в БД сейчас нет. Потребуется - сделаем просто новую реализацию.

Игроки
------

В начале игрок имеет следующие поля:

.. code-block:: csharp

    enum Gender { Male, Female };

    public record Player {
        Guid id { get; init; };
        DateTimeOffset dateCreated { get; init; };
        Color color { get; init; };
        String name { get; set; };
        bool isAlive { get; set; };
        int age { get; set; };
        Gender gender { get; set; };
        bool isFertile { get; set; };
    }

CRUD
----

CRUD-операции включают в себя операции **создания** *(Create)*, **чтения** *(Read)*, **обновления** *(Update)* и **удаления** *(Delete)*.

Для создания игрока достаточно поля ``name``. Остальное генерируется сервером, либо определяется условием (``isFertile``).

Запросить информацию можно как по всем игрокам, так и по одному определённому (по его ``id``).

Удалить игрока можно, используя его ``id``.

Игрока можно обновить, указывая его ``id`` и затем обновляемые поля.

Интерфейс
---------

Ниже указан пример предлагаемого интерфейса для CRUD-операций:

.. code-block:: csharp

    public interface IPlayerService
    {
        Guid CreatePlayer(string);
        Player GetPlayer(Guid);
        IEnumerable<Player> GetPlayers();
        void UpdatePlayer(Player);
        void DeletePlayer(Guid);
    }

Сериализовывать структуру Player, например, в словарь или JSON нет необходимости – монолитная архитектура приложения предполагает использование реализованной структуры.

Единственное замечание: в ``UpdatePlayer()`` даётся *целая* структура ``Player``. Метод извлекает ``id`` из структуры, находит запись по ``id`` в бд и полностью перезаписывает эту запись входной структурой, *исключая поля только для чтения*.

Задание
-------

#. Определить объект игрока и интерфейс взаимодействий с ним;
#. Написать CRUD-операции.

Материалы для ознакомления
--------------------------

* `YouTube: Введение в серверную часть Blazor <https://www.youtube.com/watch?v=8DNgdphLvag>`_
* `ExecuteCommands: Basic CRUD operations in Blazor using SQLite as the database <https://executecommands.com/crud-in-blazor-using-sqlite-entity-framework/>`_