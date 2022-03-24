Задача #2: Макет игры. Махинации с игроками 
===========================================

После первого разогревочного задания идёт, конечно, самая сложная основная часть.

В данной задаче вами будет определён облик самой главной страницы, где будет протекать игра.

Макет главной страницы
----------------------

Следующий план я нарисовал в редакторе, так что HTML-скрипта под него у меня нет (но повторить его, думаю, не составит труда):

.. image:: img/02_layout.png

Можно отметить 3 панели: самая большая панель с игроками (занимает 80%/80% пространства), нижняя панель ведущего (80%/20%) и боковая панель условий (20%/100%).

В общем и целом такой интерфейс подобен тому, что был в оригинальном Булкере, но теперь он растягивается практически на весь экран (самые края (~5%) я бы всё же не задействовал, заштриховал и выделил бы полезную часть тенью).

Контейнер игрока
----------------

Несмотря на предыдущее заявление, что вряд ли лучше таблицы я что-нибудь придумаю, всё же что-нибудь более-менее приятное на глаз придумать хотелось *(ибо таблица, честно, смотрится не очень)*.

Мне совсем не нравилось, что виды характеристик находились в крайнем столбце, отчего приходилось метаться взглядом, чтобы найти подходящие столбец и строку. Да и вид был не очень, и вряд ли бы можно было это изменить.

Так что мною предлагаются 2 других подхода к дизайну:

"Каждому по контейнеру"
~~~~~~~~~~~~~~~~~~~~~~~

Такой подход даёт большой простор для фантазий, а соответственно для будущего дизайна; позволяет сам дизайн сделать более привлекательным; переделать затем контейнеры в таблицу легче, чем таблицу в набор контейнеров.

В качестве прототипа можно использовать следующий дизайн:

.. figure:: img/02_player_box_dead.png
    :width: 240 px
    :align: right

    Вид мёртвого игрока

.. figure:: img/02_player_box.png
    :width: 240 px
    :align: left

    Вид с раскрытыми хар-ками

.. figure:: img/02_player_box_crossed.png
    :width: 240 px

    Вид с нераскрытыми хар-ками

.. note::
    Используемый здесь шрифт: **Bahnschrift**.

С прототипом вы вольны делать что угодно. Сейчас я бы не сказал, к сожалению, что он хорошо помещается в экран игры. Его пропорции не позволяют ни разместить шаблон в 2 ряда, ни проставить достаточно в 1 ряд.

Так что можете либо *сократить высоту*, вместив `заголовок/значение` в один ряд или как-то объединив шапку с именем, либо *сократить ширину*, используя везде сжатые шрифты и одинаковое выравнивание.

"Таблица с заголовками на отдельной строке"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Спасибо Петраркиусу за идею. Эта стратегия мне стала нравиться куда больше, когда я ради интереса её реализовал.

Мы строим таблицу, подобно первому клиенту Булкера *(в данном случае только я заменил стандартную HTML-таблицу на CSS Grid)*, но названия характеристик не располагаются в отдельном боковом столбце: они находятся над рядом со значениями, растягиваясь на всю таблицу.

.. figure:: img/02_table_big.png
    
    Вид при большом разрешении

.. figure:: img/02_table_short.png

    Вид при пропорциях смартфона (каковы перспективы-то!)

Выходит достаточно красиво и современно; таблицей удобней пользоваться; занимает меньше места по сравнению с прошлым вариантом (и даже меньше, чем в прошлой реализации Булкера!) и масштабируется даже лучше.

Даже вопрос с переносимостью благодаря CSS Grid решается легко. Можно просто увеличить `gap` между контейнерами!

Ещё и код прототипа вам в помощь. Его можно найти в конце статьи.

"Таблица без заголовков"
~~~~~~~~~~~~~~~~~~~~~~~~

Ещё одна идея Петраркиуса. Названия характеристик располагаются поверх нераскрытых ячеек.

.. figure:: img/02_table_no_headers.png

Код прототипа тоже будет расположен ниже.

Интерфейс бэк-энда
~~~~~~~~~~~~~~~~~~

Характеристики определяются при инициализации сервером и передаются им через интерфейс ``Dictionary<string, string> IPlayerService.GetTraits()``. Здесь ключ - кодовое название, которое может передаваться как id черты, а значение - выводимое название.

Кроме того, в интерфейсе ``IEnumerable<Player> IPlayerService.GetPlayers()`` сервер передаёт в модели игрока следующие характеристики:

.. code-block:: csharp

    class Player {
        Guid id;
        DateTimeOffset dateCreated;
        string color;  // format: '#FFFFFF'
        String name;
        bool isAlive;
    }

.. note::
    Стоит отдельно объяснить несколько вещей:

    * У игроков есть цветные шапки. У каждого игрока свой уникальный цвет, который генерируется сервером. Такой дизайн необходим для этапа голосования.
    * Характеристики с подробным описанием помечены пунктиром (описание выводится при наведении мышкой). Если у характеристики нет описания, в ``Tooltip`` передаётся пустая строка. 
    * Возраст (кстати, обозначенный Unicode-символом) и пол игрока объединены в единую характеристику `"bio"`, и раскрываются вместе (а в описании выводится фертильность).

События
-------

Выше было описано наведение на поле характеристики, которое подразумевает, что с ним можно взаимодействовать.

Кроме того, что характеристику можно *раскрыть*, ей можно *обменяться* либо *рандомизировать* (когда игрок обыгрывает , например).

Предлагаю следующие действия:

* Чтобы **раскрыть характеристику**, мышка зажимается на заштрихованной черте. Штриховка в это время, например, постепенно переходит в цвет фона. Используется метод ``(string Value, string Tooltip) IPlayerService.GetPlayerTrait(Guid, string TraitKey)``.
* Чтобы **обменять характеристики двух игроков**, мышка нажимается на черте одного игрока и отпускается на нужном игроке. Используется метод ``void IPlayerService.SwapPlayerTrait(Guid subject, Guid object, string TraitKey)``.
* Чтобы **сгенерировать новую черту**, на правой кнопке мыши вызывается контекстное меню с необходимым действием. Используется метод ``void IPlayerService.RandomizePlayerTrait(Guid, string TraitKey)``.

Панель условий
--------------

TBD

Экспорт
-------

TBD

Таймер
------

Таймером можно заняться, пока команда backend медлит.

Каких-то особых рекомендаций по таймеру у меня нет: единственное, рекомендую сделать ввод времени напрямую в таймер, а заморозку сделать просто отсчётом времени вперёд.

Задание
-------

#. Сделать переход с меню инициализации на новую страницу.
#. Сверстать в общих чертах главную страницу.
#. Сделать контейнер с информацией про игрока (можно пока только с основной информацией); определить, каким образом раскладывать в игре контейнеры; *протестировать с шаблонными именами, если backend так и не завершил свою работу*.
#. Добавить раскрытие характеристик; запустить циклически раунд с раскрытием характеристик.
#. Обеспечить нормальную работу с дополнительными характеристиками. *Требуется взаимодействие с backend.*
#. Написать события раскрытия, обмена и рандомизации черты. *Требуется взаимодействие с backend.*
#. Написать модуль таймера.

Статический макет
-----------------

"Таблица с заголовками на отдельной строке"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: html

    <!DOCTYPE html>
    <html>
        <head>
            <meta charset="UTF-8">
            <title>Тест таблицы</title>
            <style>
                html, input {
                    font-family: 'Bahnschrift', 'Trebuchet MS';
                    font-size: 1.2em;
                }

                body {
                    background: repeating-linear-gradient(-45deg, rgb(220, 220, 220, 1), rgb(220, 220, 220, 1) 1%, rgb(200, 200, 200, 1) 1%, rgb(200, 200, 200, 1) 2%) no-repeat;
                    min-height: 100vh;
                }

                .inner-body {
                    width: 90%;
                    min-height: 90%;
                    background-color: white;

                    position: absolute;
                    top: 5%;
                    left: 5%;

                    margin: auto;
                    padding: 1vh 1vw;

                    display: flex;
                    align-items: center;
                    align-content: center;
                    justify-content: flex-start;
                    flex-direction: column;

                    box-shadow: 0.5vh 0.5vh 1vh 1vh darkgray;
                }

                .player-grid {
                    display: grid;
                    width: minmax(30%, auto);
                    border: 2px solid black;
                    border-radius: 5px;
                    grid-template-columns: repeat(3, 1fr);
                    grid-auto-rows: auto;
                    box-shadow: 0.25vh 0.25vh 0.5vh darkgray;
                }

                .player-grid > div {
                    position: relative;
                    text-align: center;
                    display: flex;
                    align-items: center;
                    align-content: center;
                    justify-content: center;
                }

                .header-box {
                    padding: 0;
                    height: 12px;
                }

                .footer-box {
                    padding: 0;
                    height: 6px;
                }

                .title-box {
                    background-color: darkgray;
                    color: white;
                    font-weight: bold;
                    font-size: 90%;
                    grid-column-start: 1;
                    grid-column-end: 4;
                    padding: 2px;
                }

                .value-box {
                    border-left: 1px solid darkgray;
                    border-right: 1px solid darkgray;
                    background-color: lightgrey;
                    color: black;
                    padding: 6px;
                    min-height: 1.5em;
                    min-width: min(10vw, 12em);
                }

                .covered-child,
                .covered-before,
                .covered-after {
                    position: relative;
                }

                .covered,
                .covered-child > *,
                .covered-before::before,
                .covered-after::after {
                    position: absolute;
                    content: '';
                    display: block;
                    inset: 0;
                    background: repeating-linear-gradient(45deg, rgb(220, 220, 220, 1), rgb(220, 220, 220, 1) 10%, rgb(200, 200, 200, 1) 10%, rgb(200, 200, 200, 1) 20%);
                }

                .dead {
                    filter: invert(100%);
                    -webkit-filter: invert(100%);
                }

                .player-name-box {
                    border-left: 1px dashed darkgray;
                    border-right: 1px dashed darkgray;
                    background-color: white;
                    color: black;
                    padding: 12px;
                    font-size: 150%;
                }

                .color-header {
                    width: 100%;
                    height: 12px;
                }
            </style>
        </head>

        <body>
            <div class="inner-body">
                <div class="player-grid">

                    <div class="header-box" style="background-color: orange;"></div>
                    <div class="header-box" style="background-color: green;"></div>
                    <div class="header-box" style="background-color: blue;"></div>

                    <div class="player-name-box">Montferrat</div>
                    <div class="player-name-box">Mao</div>
                    <div class="player-name-box dead">Magnus ☦</div>

                    <div class="title-box">
                        Биологическая характеристика
                    </div>
                    <div class="value-box">
                        <span style="border-bottom: 2px dashed #000;">♂️ 25 лет</span>
                    </div>
                    <div class="value-box covered">
                    </div>
                    <div class="value-box covered dead">
                    </div>

                    <div class="title-box">
                        Профессия
                    </div>
                    <div class="value-box">
                        Эндокринолог
                    </div>
                    <div class="value-box">
                        Терапевт
                    </div>
                    <div class="value-box dead">
                        Программист
                    </div>

                    <div class="title-box">
                        Фобия
                    </div>
                    <div class="value-box">
                        <span style="border-bottom: 2px dashed #000;">Гелиофобия</span>
                    </div>
                    <div class="value-box">
                        Нет фобии
                    </div>
                    <div class="value-box covered dead">
                    </div>

                    <div class="title-box">
                        Хобби
                    </div>
                    <div class="value-box covered">
                    </div>
                    <div class="value-box covered">
                    </div>
                    <div class="value-box dead">
                        Лыжи
                    </div>

                    <div class="title-box">
                        Состояние здоровья
                    </div>
                    <div class="value-box covered">
                    </div>
                    <div class="value-box covered">
                    </div>
                    <div class="value-box covered dead">
                    </div>

                    <div class="title-box">
                        Дополнительная информация
                    </div>
                    <div class="value-box covered">
                    </div>
                    <div class="value-box">
                        Мазохист
                    </div>
                    <div class="value-box covered dead">
                    </div>

                    <div class="title-box">
                        Багаж
                    </div>
                    <div class="value-box">
                        Костюм для БДСМ
                    </div>
                    <div class="value-box covered">
                    </div>
                    <div class="value-box dead">
                        Шпага
                    </div>

                    <div class="footer-box" style="background-color: orange;"></div>
                    <div class="footer-box" style="background-color: green;"></div>
                    <div class="footer-box" style="background-color: blue;"></div>
                </div>
            </div>
        </body>
    </html>

"Таблица без заголовков"
~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: html

    <!DOCTYPE html>
        <html>
            <head>
                <meta charset="UTF-8">
                <title>Тест таблицы</title>
                <style>
                    html, input {
                        font-family: 'Bahnschrift', 'Trebuchet MS';
                        font-size: 1.25em;
                    }
        
                    body {
                        background: repeating-linear-gradient(-45deg, rgb(220, 220, 220, 1), rgb(220, 220, 220, 1) 1%, rgb(200, 200, 200, 1) 1%, rgb(200, 200, 200, 1) 2%) no-repeat;
                        min-height: 100vh;
                    }
        
                    .inner-body {
                        width: 90%;
                        min-height: 90%;
                        background-color: white;
        
                        position: absolute;
                        top: 5%;
                        left: 5%;
        
                        margin: auto;
                        padding: 1vh 1vw;
        
                        display: flex;
                        align-items: center;
                        align-content: center;
                        justify-content: flex-start;
                        flex-direction: column;
        
                        box-shadow: 0.5vh 0.5vh 1vh 1vh darkgray;
                    }
        
                    .player-grid {
                        display: grid;
                        width: fit-content;
                        border: 2px solid black;
                        border-radius: 5px;
                        grid-template-columns: repeat(4, 1fr);
                        grid-auto-rows: auto;
                        box-shadow: 0.25vh 0.25vh 0.5vh darkgray;
                    }
        
                    .player-grid > div {
                        position: relative;
                        text-align: center;
                        display: flex;
                        align-items: center;
                        align-content: center;
                        justify-content: center;
                    }
        
                    .header-box {
                        padding: 0;
                        height: 12px;
                    }
        
                    .footer-box {
                        padding: 0;
                        height: 6px;
                    }
        
                    .value-box {
                        border: 2px 1px solid rgb(190, 190, 190);
                        background-color: whitesmoke;
                        color: black;
                        padding: 6px;
                        min-height: 1.5em;
                        min-width: min(10vw, 12em);
                    }
        
                    .covered {
                        background: repeating-linear-gradient(45deg, rgb(220, 220, 220), rgb(220, 220, 220) 10%, rgb(200, 200, 200) 10%, rgb(200, 200, 200) 20%);
                        font-style: italic;
                        font-weight: bold;
                        font-stretch: semi-condensed;
                        font-size: 75%;
                        color: rgb(160, 160, 160);
                    }

                    .dead {
                        filter: invert(90%);
                        -webkit-filter: invert(90%);
                    }

                    .player-name-box {
                        background-color: white;
                        color: black;
                        padding: 10px;
                        font-size: 130%;
                        font-weight: bold;
                        font-stretch: semi-condensed;
                    }

                    .color-header {
                        width: 100%;
                        height: 12px;
                    }
                </style>
            </head>

            <body>
                <div class="inner-body">
                    <div class="player-grid">

                        <div class="header-box" style="background-color: hsl(0, 100%, 50%);"></div>
                        <div class="header-box" style="background-color: hsl(180, 100%, 50%);"></div>
                        <div class="header-box" style="background-color: hsl(90, 100%, 50%);"></div>
                        <div class="header-box" style="background-color: hsl(270, 100%, 50%);"></div>

                        <div class="player-name-box" style="background-color: hsl(0, 100%, 95%);">Montferrat</div>
                        <div class="player-name-box" style="background-color: hsl(180, 100%, 95%);">Mao</div>
                        <div class="player-name-box dead" style="background-color: hsl(90, 100%, 95%);">Magnus ☦</div>
                        <div class="player-name-box" style="background-color: hsl(270, 100%, 95%);">Ewenmait</div>

                        <div class="value-box">
                            <span style="border-bottom: 2px dashed #000;">♂️ 25 лет</span>
                        </div>
                        <div class="value-box covered">
                            Биологическая характеристика
                        </div>
                        <div class="value-box covered dead">
                            Биологическая характеристика
                        </div>
                        <div class="value-box">
                            <span style="border-bottom: 2px dashed #000;">♀️ 34 года</span>
                        </div>

                        <div class="value-box">
                            Эндокринолог
                        </div>
                        <div class="value-box">
                            Терапевт
                        </div>
                        <div class="value-box dead">
                            Программист
                        </div>
                        <div class="value-box covered">
                            Профессия
                        </div>

                        <div class="value-box">
                            <span style="border-bottom: 2px dashed #000;">Гелиофобия</span>
                        </div>
                        <div class="value-box">
                            Нет фобии
                        </div>
                        <div class="value-box covered dead">
                            Фобия
                        </div>
                        <div class="value-box">
                            <span style="border-bottom: 2px dashed #000;">Арахнофобия</span>
                        </div>

                        <div class="value-box covered">
                            Хобби
                        </div>
                        <div class="value-box covered">
                            Хобби
                        </div>
                        <div class="value-box dead">
                            Лыжи
                        </div>
                        <div class="value-box">
                            Фотография
                        </div>

                        <div class="value-box covered">
                            Состояние здоровья
                        </div>
                        <div class="value-box covered">
                            Состояние здоровья
                        </div>
                        <div class="value-box covered dead">
                            Состояние здоровья
                        </div>
                        <div class="value-box covered">
                            Состояние здоровья
                        </div>

                        <div class="value-box covered">
                            Дополнительная информация
                        </div>
                        <div class="value-box">
                            Мазохист
                        </div>
                        <div class="value-box covered dead">
                            Дополнительная информация
                        </div>
                        <div class="value-box covered">
                            Дополнительная информация
                        </div>

                        <div class="value-box">
                            Костюм для БДСМ
                        </div>
                        <div class="value-box covered">
                            Багаж
                        </div>
                        <div class="value-box dead">
                            Шпага
                        </div>
                        <div class="value-box covered">
                            Багаж
                        </div>

                        <div class="footer-box" style="background-color: hsl(0, 100%, 50%);"></div>
                        <div class="footer-box" style="background-color: hsl(180, 100%, 50%);"></div>
                        <div class="footer-box" style="background-color: hsl(90, 100%, 50%);"></div>
                        <div class="footer-box" style="background-color: hsl(270, 100%, 50%);"></div>
                    </div>
                </div>
            </body>
        </html>