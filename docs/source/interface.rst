Интерфейс
=========

Здесь описан интерфейс обращения фронт-энда к бэк-энду.

``Задача ##``: доступность соответствующего метода при завершении бэк-эндом этой задачи.

.. default-domain:: csharp

.. namespace:: bulker.Player

.. class:: Player

    *(Задание 1)* Запись игрока.

    .. property:: Guid id { get; }

    .. property:: DateTimeOffset dateCreated { get; }

    .. property:: Color color { get; }
    
    .. property:: String name { get; set; }

    .. property:: bool isAlive { get; set; }


.. class:: IPlayerService

    Сервис для взаимодействия с записями игроков.

    .. method:: Guid CreatePlayer(string Name)

        *(Задание 1)* Генерирует запись для игрока с именем ``Name``. Возвращает его ID.

    .. method:: Player GetPlayer(Guid ID)

        *(Задание 1)* Получает общую информацию по игроку (т.е. исключая его игровые характеристики).

    .. method:: IEnumerable<Player> GetPlayers()

        *(Задание 1)* Получает общую информацию по всем игрокам.

    .. method:: void UpdatePlayer(Player player)

        *(Задание 1)* Заменяет в БД/словаре запись с соответствующим ID объектом ``player``.

    .. method:: void DeletePlayer(Guid ID)

        *(Задание 1)* Удаляет в БД/словаре запись с соответствующим ID.

    .. method:: Dictionary<string,string> GetTraits()

        *(Задание 2)* Получает все возможные характеристики игрока. Ключом здесь выступает кодовое название (далее ``TraitKey``), значением - внешнее название.

    .. method:: (string,string) GetPlayerTrait(Guid ID, string TraitKey)

        *(Задание 2)* Получает значение соответствующей черты ``TraitKey`` у игрока ``ID``.

    .. method:: void SwapPlayerTrait(Guid subject, Guid object, string TraitKey)

        *(Задание 2)* Меняет местами черту ``TraitKey`` у игроков ``subject`` и ``object``.
        *Не забудьте обновить черту, если она раскрыта, через* ``GetPlayerTrait``!

    .. method:: void RandomizePlayerTrait(Guid player, string TraitKey)

        *(Задание 2)* Генерирует новое значение для черты ``TraitKey`` для игрока ``player``.
        *Не забудьте обновить черту, если она раскрыта, через* ``GetPlayerTrait``!