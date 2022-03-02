Работа с Git
============

.. figure:: img/github.png
    :width: 64 px
    :align: right

    `Ссылка на GitHub-репозиторий проекта <https://github.com/mmm-development/bulker>`_.

Склонировать репозиторий:

.. code-block:: sh

    git clone https://github.com/mmm-development/bulker

Сразу после этого переключитесь на ветку ``develop``:

.. code-block:: sh

    git checkout develop

Для каждой новой фичи/подзадания делайте новую ветку:

.. code-block:: sh

    git checkout -B {краткое-описание-на-английском}

После этого старайтесь как можно чаще делать коммиты. Поводом для каждого коммита будет изменение в коде и рабочее состояние проекта.

.. code-block:: sh

    git commit -a

Если требуется помощь с кодом, либо вы закончили разработку, заливайте ветку на репозиторий:

.. code-block:: sh

    git push -u origin {название-вашей-ветки}

После этого делайте pull request на GitHub со своей ветки на ``develop``.