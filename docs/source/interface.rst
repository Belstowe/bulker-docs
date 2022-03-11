Интерфейс
=========

.. default-domain:: csharp

.. namespace:: bulker.Player

.. class:: Player

    *(1)* Запись игрока.

    .. property:: Guid id { get; }

    .. property:: DateTimeOffset dateCreated { get; }

    .. property:: Color color { get; }
    
    .. property:: String name { get; set; }

    .. property:: bool isAlive { get; set; }


.. class:: IPlayerService

    Сервис для взаимодействия с записями игроков.

    .. method:: *(1)* Guid CreatePlayer(string Name)

        Генерирует запись для игрока с именем ``Name``. Возвращает его ID.

    .. method:: *(1)* Player GetPlayer(Guid ID)

        Получает общую информацию по игроку (т.е. исключая его игровые характеристики).

    .. method:: *(1)* IEnumerable<Player> GetPlayers()

        Получает общую информацию по всем игрокам.

    .. method:: *(1)* void UpdatePlayer(Player player)

        Заменяет в БД/словаре запись с соответствующим ID объектом ``player``.

    .. method:: *(1)* void DeletePlayer(Guid ID)

        Удаляет в БД/словаре запись с соответствующим ID.

    .. method:: *(2)* Dictionary<string, string> GetTraits()

        Получает все возможные характеристики игрока. Ключом здесь выступает кодовое название (далее ``TraitKey``), значением - внешнее название.

    .. method:: *(2)* (string Value, string Tooltip) GetPlayerTrait(Guid ID, string TraitKey)

        Получает значение соответствующей черты ``TraitKey`` у игрока ``ID``.

    .. method:: *(2)* void SwapPlayerTrait(Guid subject, Guid object, string TraitKey)

        Меняет местами черту ``TraitKey`` у игроков ``subject`` и ``object``.
        *Не забудьте обновить черту, если она раскрыта, через :meth:`GetPlayerTrait`!*

    .. method:: *(2)* void RandomizePlayerTrait(Guid player, string TraitKey)

        Генерирует новое значение для черты ``TraitKey`` для игрока ``player``.
        *Не забудьте обновить черту, если она раскрыта, через :meth:`GetPlayerTrait`!*