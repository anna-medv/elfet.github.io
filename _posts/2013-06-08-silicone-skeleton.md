---
layout: post
title: Silicone Skeleton — настроенный Silex
date: 2013-06-08 23:42
comments_id: 232 http://elfet.ru/?p=232
---
Если вы не один раз начинали новые проекты с использованием Silex, то вы знаете что каждый раз в начале нужно настроить его под себя: добавить провайдеры, переопределить некоторые сервисы, определить структуру каталогов, и т.д. Со временем у вас появляется базовый набор для Silex которые вы используете для создания нового проекта. <br>
Но если у вас его нету, предлагаю вам ознакомиться с моим: <a href="https://github.com/elfet/silicone-skeleton">Silicone Skeleton</a>.<br>
<!--more-->
В Silicone Skeleton включены следующие компоненты:<br>
<br>
<ul>
<li>HttpCache — работает только в prod окружении.</li>
<li>Class Controllers — вы можете размещать код контроллеров не только в функциях, но и в методах классов.</li>
<li>Doctrine Common — вынесен отдельно т.к. используется в нескольких не зависимых провайдерах.</li>
<li>Doctrine ORM — вы можете пользоваться полноценной ORM (а не только DBAL). Для работы были добавлены следующие команды:<br>
<ol>
<li>database:create</li>
<li>database:drop</li>
<li>schema:create</li>
<li>schema:update</li>
<li>schema:drop</li>
</ol><br>
</li>
<li>Monolog — логи пишутся в app/open/log/log.txt</li>
<li>Session</li>
<li>Twig — шаблоны лежат в app/view/</li>
<li>Translation — языковые файлы(yml, xliff) лежат в app/lang/[domain].[locale].yml</li>
<li>Validators — добавлен недостающий валидатор UniqueEntityValidator для Doctrine Orm</li>
<li>Forms</li>
<li>Security — с регистрация и авторизацией пользователей</li>
<li>Annotation Routes — можно использовать аннотации для роутов и ORM.</li>
<li>Console — необходимые команды для ORM и очистки кэша.</li>
</ul><br>
<br>
Структура каталогов очень близка с Symfony<br>
<pre>
app/
    config/  -- Настройки
    lang/    -- Языковые файлы
    open/    -- Кэш, логи
    src/     -- Исходники
    vendor/  -- Вендоры
    view/    -- Шаблоны
    console  -- Консоль
web/
    index.php
</pre>
<br>
<br>
Вы можете использовать обычные Silex контроллеры: $app-&gt;get(...) вместе с такими контроллерами:<br>


~~~ php
<?php
class Blog extends Controller
{
    /**
     * @Route("/blog/{post}")
     */
    public function post($post)
    {
        return $this->render('post.twig');
}
}
~~~

<br>
<br>
Так же в Silicone Skeleton полностью настроен Security Provider. И контроллер входа и регистрации. <br>
<br>
Для установки используйте Composer:<br>
<pre>composer create-project elfet/silicone-skeleton your/app/path</pre>
<br>
Все желающие помочь с развитием приветствуются! 
