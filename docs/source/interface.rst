Интерфейс
=========

Здесь описан интерфейс обращения фронт-энда к бэк-энду.

``Задача ##``: доступность соответствующего метода при завершении бэк-эндом этой задачи.

.. default-domain:: csharp

.. namespace:: Bulker.Data

.. enum:: Gender

    *(Задание 1)* Пол игрока.

    .. value:: Male
    .. value:: Female


.. class:: Player

    *(Задание 1)* Запись игрока.

    .. property:: Guid id { get; }

    .. property:: DateTimeOffset dateCreated { get; }

    .. property:: String color { get; }
    
    .. property:: String name { get; set; }

    .. property:: bool isAlive { get; set; }


.. enum:: Status

    *(Задание 1)* Статус запроса.

    .. value:: OK

        Запрос успешно выполнен.

    .. value:: NotFound

        Не найден необходимый объект по аргументу.


.. class:: IPlayerService

    Сервис для взаимодействия с записями игроков.

    .. method:: Guid CreatePlayer (string Name)

        *(Задание 1)* Генерирует запись для игрока с именем ``Name``. Возвращает его ID.

    .. method:: Player GetPlayer (Guid ID)

        *(Задание 1)* Получает общую информацию по игроку (т.е. исключая его игровые характеристики).

    .. method:: IEnumerable<Player> GetPlayers ()

        *(Задание 1)* Получает общую информацию по всем игрокам.

    .. method:: Status UpdatePlayer (Player player)

        *(Задание 1)* Заменяет в БД/словаре запись с соответствующим ID объектом ``player``.

    .. method:: Status DeletePlayer (Guid ID)

        *(Задание 1)* Удаляет в БД/словаре запись с соответствующим ID.

    .. method:: Dictionary<string,string> GetTraits ()

        *(Задание 2)* Получает все возможные характеристики игрока. Ключом здесь выступает кодовое название (далее ``TraitKey``), значением - внешнее название.

    .. method:: (String,String) GetPlayerTrait (Guid ID, string TraitKey)

        *(Задание 2)* Получает значение соответствующей черты ``TraitKey`` у игрока ``ID``.

    .. method:: void SwapPlayerTrait (Guid subject, Guid object, string TraitKey)

        *(Задание 2)* Меняет местами черту ``TraitKey`` у игроков ``subject`` и ``object``.
        *Не забудьте обновить черту, если она раскрыта, через* ``GetPlayerTrait``!

    .. method:: void RandomizePlayerTrait (Guid player, string TraitKey)

        *(Задание 2)* Генерирует новое значение для черты ``TraitKey`` для игрока ``player``.
        *Не забудьте обновить черту, если она раскрыта, через* ``GetPlayerTrait``!


.. namespace:: Bulker.Struct

.. class:: ShuffleList<T>

    .. inherits:: List<T>

    *(Задание 1)* Класс, реализующий *тасуемый список*. Т.е. значения в нём перетасовываются, и реализуется функционал поочерёдного извлечения элементов.

    .. method:: ShuffleList<T> (IEnumerable<T> list)
    .. method:: ShuffleList<T> (params T[] list)

        Конструктор тасуемого списка. Все значения внутри `list` перетасовываются.
    
    .. method:: void Shuffle ()

        Перетасовывает список без обнуления счётчика. Используется при *инициализации* и *переполнении счётчика*.
        *Обратите внимание, что при добавлении элемента в список данный метод не используется!*

    .. method:: T Extract ()

        Поочерёдно извлекает элемент, начиная с первого, используя счётчик. Когда счётчик доходит до конца, список заново перетасовывается.