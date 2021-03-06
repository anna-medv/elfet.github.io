---
layout: post
title: Веб-консоль на PHP
date: 2013-04-03 20:32
comments_id: 189 http://elfet.ru/?p=189
---
Как-то я уже писал о <a href="http://elfet.ru/git-control-through-a-web-console-php" title="Управление GIT-ом через веб-консоль на PHP">веб-консоли</a> через которую можно управлять Git-ом. Теперь же я решил развить её и сделать универсальную консоль.

<img  class="center"  alt="" src="/assets/web-console-on-php.png" width="371" height="255">

<!--more-->
Консоль по прежнему представляет из себя всего один файл (<a href="https://github.com/elfet/console/blob/master/console.php" title="console.php" target="_blank">console.php</a>), который можно кинуть куда-угодно открыть его в браузере и пользоваться как обычным терминалом. 

Все настройки находятся вверху файла, либо вы можете создать отдельный файл с настройками console.config.php. 

В комментариях к предыдущей статье говорили о том что нужно добавить защиту от несанкцианированного доступа. Теперь консоль поддерживает Digest HTTP авторизацию.

Так же были улучшены автодополнение, история и мобильная версия консоли.

И появились новых 6 тем оформления из которых каждый найдёт подходящую для себя. 
Посмотреть можно тут: <a href="http://git.elfet.ru/test/console.php?theme=default" target="_blank">default</a>, <a href="http://git.elfet.ru/test/console.php?theme=white" target="_blank">white</a>, <a href="http://git.elfet.ru/test/console.php?theme=green" target="_blank">green</a>, <a href="http://git.elfet.ru/test/console.php?theme=grey" target="_blank">grey</a>, <a href="http://git.elfet.ru/test/console.php?theme=far" target="_blank">far</a>, <a href="http://git.elfet.ru/test/console.php?theme=ubuntu" target="_blank">ubuntu</a>. 

Форкайте и качайте на гитхабе: <a href="https://github.com/elfet/console">github.com/elfet/console</a>.
