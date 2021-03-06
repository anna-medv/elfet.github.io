---
layout: post
title: Создание PHP-AJAX чата
date: 2009-10-05 01:27
comments_id: 19 http://elfet.ru/?p=19
---
<!--more-->
<br>

<blockquote>
    <strong>Новая статья о создании чата на PHP находится <a href="http://elfet.ru/create-chat-on-php/">тут</a>.</strong>
</blockquote>
<br>
<br>

    Приветствую!
<br>
    Эта статья для тех кто желает сам сделать свой чат на php с применением ajax. В ней я расскажу о том как самому сделать простой чат.
<br>
<br>

    Необходимые знания:
<br>
    Начальные знания в php. Такие как: Как подключиться к базе данных mysql?<br>
    Начальные знания в html и css. <br>
    Начальные знания в JavaScript и jQuery. (даже если вы только слышали о jQuery, но не работали с ним) <br>
    Итак начнём! <br>
<br>
<br>

    Для начала создадим новую базу данных в MySQL и выполним SQL запрос: <br>

<pre><code data-language="sql">CREATE TABLE `messages` (
    `id` int(5) NOT NULL AUTO_INCREMENT,
    `name` char(255) character SET utf8 NOT NULL,
    `text` text character SET utf8,
    PRIMARY KEY  (`id`)
    );</code></pre>
    В этой таблице у нас будут храниться сообщения чата. <br>
    id – номер сообщения, он должен быть помечен как AUTO_INCREMENT для того что бы для каждого сообщения создавался уникальный индекс. <br>
    name – имя пользователя отправившего сообщение <br>
    text – само сообщение <br>
    Можно так же расширить список полей, например время сообщения, и так далее. <br>
<br>
    Теперь приступим к созданию клиентской части чата. Она у нас будет реализована в файле index.php: <br>

<pre><code data-language="php">&lt;!DOCTYPE HTML&gt;
    &lt;html&gt;
    &lt;head&gt;
        &lt;title&gt;PhpAjaxChat&lt;/title&gt;
        &lt;!-- У нас всё работает в UTF-8 --&gt;
        &lt;meta http-equiv=&quot;content-type&quot; content=&quot;text/html; charset=UTF-8&quot;&gt;

        &lt;!-- Загружаем стили для чата --&gt;
        &lt;link rel=&quot;stylesheet&quot; type=&quot;text/css&quot; media=&quot;screen&quot; href=&quot;css.css&quot; /&gt;

        &lt;!-- Подключаем jQuery --&gt;
        &lt;script type=&quot;text/javascript&quot; src=&quot;jquery.js&quot;&gt;&lt;/script&gt;

        &lt;!-- Сам код нашего чата --&gt;
        &lt;script type=&quot;text/javascript&quot;&gt;

            $(document).ready(function () {
                $(&quot;#pac_form&quot;).submit(Send); // вешаем на форму с именем и сообщением событие которое срабатывает когда нажата кнопка &quot;Отправить&quot; или &quot;Enter&quot;
                $(&quot;#pac_text&quot;).focus(); // по поле ввода сообщения ставим фокус
                setInterval(&quot;Load();&quot;, 2000); // создаём таймер который будет вызывать загрузку сообщений каждые 2 секунды (2000 миллисекунд)
            });

            // Функция для отправки сообщения
            function Send() {
                // Выполняем запрос к серверу с помощью jquery ajax: $.post(адрес, {параметры запроса}, функция которая вызывается по завершению запроса)
                $.post(&quot;ajax.php&quot;,
                        {
                            act: &quot;send&quot;,  // указываем скрипту, что мы отправляем новое сообщение и его нужно записать
                            name: $(&quot;#pac_name&quot;).val(), // имя пользователя
                            text: $(&quot;#pac_text&quot;).val() //  сам текст сообщения
                        },
                        Load ); // по завершению отправки вызываем функцию загрузки новых сообщений Load()

                $(&quot;#pac_text&quot;).val(&quot;&quot;); // очистим поле ввода сообщения
                $(&quot;#pac_text&quot;).focus(); // и поставим на него фокус

                return false; // очень важно из Send() вернуть false. Если этого не сделать то произойдёт отправка нашей формы, те страница перезагрузится
            }

            var last_message_id = 0; // номер последнего сообщения, что получил пользователь
            var load_in_process = false; // можем ли мы выполнять сейчас загрузку сообщений. Сначала стоит false, что значит - да, можем

            // Функция для загрузки сообщений
            function Load() {
                // Проверяем можем ли мы загружать сообщения. Это сделано для того, чтобы мы не начали загрузку заново, если старая загрузка ещё не закончилась.
                if(!load_in_process)
                {
                    load_in_process = true; // загрузка началась
                    // отсылаем запрос серверу, который вернёт нам javascript
                    $.post(&quot;ajax.php&quot;,
                            {
                                act: &quot;load&quot;, // указываем на то что это загрузка сообщений
                                last: last_message_id, // передаём номер последнего сообщения который получил пользователь в прошлую загрузку
                                rand: (new Date()).getTime()
                            },
                            function (result) { // в эту функцию в качестве параметра передаётся javascript код, который мы должны выполнить
                                $(&quot;.chat&quot;).scrollTop($(&quot;.chat&quot;).get(0).scrollHeight); // прокручиваем сообщения вниз
                                load_in_process = false; // говорим что загрузка закончилась, можем теперь начать новую загрузку
                            });
                }
            }
        &lt;/script&gt;

    &lt;body&gt;
    &lt;div style=&quot;padding: 100px;&quot;&gt;
        &lt;h1&gt;Php Ajax Chat&lt;/h1&gt;
        &lt;!-- Вот в этих 2-х div-ах будут идти наши сообщения из чата --&gt;
        &lt;div class=&quot;chat r4&quot;&gt;
            &lt;div id=&quot;chat_area&quot;&gt;&lt;!-- Сюда мы будем добавлять новые сообщения --&gt;&lt;/div&gt;
        &lt;/div&gt;
        &lt;form id=&quot;pac_form&quot; action=&quot;&quot;&gt;&lt;!-- Наша форма с именем, сообщением и кнопкой для отправки --&gt;
            &lt;table style=&quot;width: 100%;&quot;&gt;
                &lt;tr&gt;
                    &lt;td&gt;Имя:&lt;/td&gt;
                    &lt;td&gt;Сообщение:&lt;/td&gt;
                    &lt;td&gt;&lt;/td&gt;
                &lt;/tr&gt;
                &lt;tr&gt;
                    &lt;!-- Поле ввода имени --&gt;
                    &lt;td&gt;&lt;input type=&quot;text&quot; id=&quot;pac_name&quot; class=&quot;r4&quot; value=&quot;Гость&quot;&gt;&lt;/td&gt;

                    &lt;!-- Поле ввода сообщения --&gt;
                    &lt;td style=&quot;width: 80%;&quot;&gt;&lt;input type=&quot;text&quot; id=&quot;pac_text&quot; class=&quot;r4&quot; value=&quot;&quot;&gt;&lt;/td&gt;

                    &lt;!-- Кнопка &quot;Отправить&quot; --&gt;
                    &lt;td&gt;&lt;input type=&quot;submit&quot; value=&quot;Отправить&quot;&gt;&lt;/td&gt;
                &lt;/tr&gt;
            &lt;/table&gt;
        &lt;/form&gt;

    &lt;/div&gt;
    &lt;/body&gt;
    &lt;/html&gt;</code></pre>    
    <b>css.css:</b><br>

<pre><code data-language="php">* {
    margin: 0;
    padding: 0;
    }

    body {
    font: normal normal normal 16px &quot;Trebuchet MS&quot;, Arial, Times;
    color: #000000;
    }

    /* Важное свойство */
    .chat {
    height: 500px;
    overflow: auto; /* Это позволяет отображать полосу прокрутки */
    position: relative; /* Это позволяет съезжать тексту в слое, не растягивая страницу */
    text-align: left;
    border: solid #818181 1px;
    }

    .chat div {
    position: absolute; /* Страница остаётся того же размера */
    }

    .chat span {
    display: block;
    }

    input[type=text],textarea {
    width: 100%;
    font: normal normal normal 16px &quot;Trebuchet MS&quot;, Arial, Times;
    border: solid #818181 1px;
    }

    /* Для CSS 3 */
    .r4 {
    -moz-border-radius: 4px;
    -khtml-border-radius: 4px;
    -webkit-border-radius: 4px;
    border-radius: 4px;
    }</code></pre>    Теперь приступим к созданию серверной части чата ajax.php:<br>
<pre><code data-language="php">&lt;?php
// настройки для подключения к MySQl
$config = array(&#039;hostname&#039; =&gt; &#039;localhost&#039;, &#039;username&#039; =&gt; &#039;root&#039;, &#039;password&#039; =&gt; &#039;&#039;, &#039;dbname&#039; =&gt; &#039;pacdb&#039;);

// подключаемся к MySQL, если не вышло то выходим
if (!mysql_connect($config[&#039;hostname&#039;], $config[&#039;username&#039;], $config[&#039;password&#039;])) {
    exit();
}
// Выбираем базу данных, если не вышло то выходим
if (!mysql_select_db($config[&#039;dbname&#039;])) {
    exit();
}
mysql_query(&quot;SET NAMES &#039;utf8&#039;&quot;); // говорим MySQl&#039;у то что мы будем работать с UTF-8

Header(&quot;Cache-Control: no-cache, must-revalidate&quot;); // говорим браузеру что-бы он не кешировал эту страницу
Header(&quot;Pragma: no-cache&quot;);

Header(&quot;Content-Type: text/javascript; charset=utf-8&quot;); // говорим браузеру что это javascript в кодировке UTF-8

// проверяем есть ли переменная act (send или load), которая указываем нам что делать
if (isset($_POST[&#039;act&#039;])) {
    // $_POST[&#039;act&#039;] - существует
    switch ($_POST[&#039;act&#039;]) {
        case &quot;send&quot; : // если она равняется send, вызываем функцию Send()
            Send();
            break;
        case &quot;load&quot; : // если она равняется load, вызываем функцию Load()
            Load();
            break;
        default : // если ни тому и не другому  - выходим
            exit();
    }
}

// Функция выполняем сохранение сообщения в базе данных
function Send()
{
    // тут мы получили две переменные переданные нашим java-скриптом при помощи ajax
    // это:  $_POST[&#039;name&#039;] - имя пользователя
    // и $_POST[&#039;text&#039;] - сообщение

    $name = substr($_POST[&#039;name&#039;], 0, 200); // обрезаем до 200 символов
    $name = htmlspecialchars($name); // заменяем опасные теги (&lt;h1&gt;,&lt;br&gt;, и прочие) на безопасные
    $name = mysql_real_escape_string($name); // функция экранирует все спец-символы в unescaped_string , вследствие чего, её можно безопасно использовать в mysql_query()

    $text = substr($_POST[&#039;text&#039;], 0, 200); // обрезаем до 200 символов
    $text = htmlspecialchars($text); // заменяем опасные теги (&lt;h1&gt;,&lt;br&gt;, и прочие) на безопасные
    $text = mysql_real_escape_string($text); // функция экранирует все спец-символы в unescaped_string , вследствие чего, её можно безопасно использовать в mysql_query()

    // добавляем новую запись в таблицу messages
    mysql_query(&quot;INSERT INTO messages (name,text) VALUES (&#039;&quot; . $name . &quot;&#039;, &#039;&quot; . $text . &quot;&#039;)&quot;);
    }
<br>

    // функция выполняем загрузку сообщений из базы данных и отправку их пользователю через ajax виде java-скрипта
    function Load()
    {
    // тут мы получили переменную переданную нашим java-скриптом при помощи ajax
    // это:  $_POST[&#039;last&#039;] - номер последнего сообщения которое загрузилось у пользователя

    $last_message_id = intval($_POST[&#039;last&#039;]); // возвращает целое значение переменной

    // выполняем запрос к базе данных для получения 10 сообщений последних сообщений с номером большим чем $last_message_id
    $query = mysql_query(&quot;SELECT * FROM messages WHERE ( id &gt; $last_message_id ) ORDER BY id DESC LIMIT 10&quot;);

    // проверяем есть ли какие-нибудь новые сообщения
    if (mysql_num_rows($query) &gt; 0) {
    // начинаем формировать javascript который мы передадим клиенту
    $js = &#039;var chat = $(&quot;#chat_area&quot;);&#039;; // получаем &quot;указатель&quot; на div, в который мы добавим новые сообщения

    // следующий конструкцией мы получаем массив сообщений из нашего запроса
    $messages = array();
    while ($row = mysql_fetch_array($query)) {
    $messages[] = $row;
    }

    // записываем номер последнего сообщения
    // [0] - это вернёт нам первый элемент в массиве $messages, но так как мы выполнили запрос с параметром &quot;DESC&quot; (в обратном порядке),
    // то это получается номер последнего сообщения в базе данных
    $last_message_id = $messages[0][&#039;id&#039;];

    // переворачиваем массив (теперь он в правильном порядке)
    $messages = array_reverse($messages);

    // идём по всем элементам массива $messages
    foreach ($messages as $value) {
    // продолжаем формировать скрипт для отправки пользователю
    $js .= &#039;chat.append(&quot;&lt;span&gt;&#039; . $value[&#039;name&#039;] . &#039;&amp;raquo; &#039; . $value[&#039;text&#039;] . &#039;&lt;/span&gt;&quot;);&#039;; // добавить сообщние (&lt;span&gt;Имя &amp;raquo; текст сообщения&lt;/span&gt;) в наш div
    }

    $js .= &quot;last_message_id = $last_message_id;&quot;; // запишем номер последнего полученного сообщения, что бы в следующий раз начать загрузку с этого сообщения

    // отправляем полученный код пользователю, где он будет выполнен при помощи функции eval()
    echo $js;
    }
}

?&gt;</code></pre><br>
        Чат готов!
    <br>
    <br>
