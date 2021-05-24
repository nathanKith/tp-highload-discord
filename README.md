# Discord

## MVP
 1. Авторизация/регистрация;
 2. Парные звонки;
 3. Групповые звонки.

## Целевая аудитория и нагрузка
 - Ежемесячный онлайн ~45 млн. пользователей. Месяный трафик голосового чата составляет около 15 петабайт (15340 терабайт);
 - Ежедневный онлайн ~8,9 млн. человек. Пиковое значение одновременно общающихся пользователей 4 млн;
 - 1,2 млрд. минут общения в день;
 - Северная Америка, Россия, Европа.

## Расчет нагрузки

Используя данные выше, посчитаем количество минут голосового общения в месяц:

    1,2 млрд. * 30 = 36 млрд. минут/месяц
    
Зная месяный трафик голосового чата, посчитаем суммарный суточный трафик соответственно:

    15340 * 1024 / 30 = 523605 Гбайт/сутки
    
Из устаревшей [статистики](https://habr.com/ru/post/423171/) "Discord способен обслуживать одновременно более 2,6 миллиона пользователей голосового чата с исходящим трафиком более 220 Гбит/с", получим пропорционально нашей пиковой суточной нагрузке:

    220 * 4 / 2,6 = 338 Гбит/с
    
Итого мы получаем по сетевому трафику:
 * Суммарный суточный - 523605 Гбайт/сутки;
 * Пиковое значение в течении суток - 338 Гбит/с.
    
Так как большее количество пользователей пользуются приложением десктопно или на мобильных устройствах, то можно предположить что в день пользователь 0,25 раз логинится, следовательно в день количество запросов на запись в хранилище сессий:

    8,9 млн. * 0,25 / 24 / 3600 = 26 RPS
    
Посчитаем количество звонков совершаемых в день, полагая, что средняя длительность звонка 2 часа:

    1,2 млрд / 120 = 10 млн. звонков/день

Допустим, что в одном звонке в среднем участвуют 4 человека. На 1 звонок приходится 10 операций: создание звонка, 4 подключения, 4 отключения, обрывание звонка.
Тогда получим, что нагрузка на хранилище звонков в среднем за сутки равна:

    10 млн * 10 / 24 / 3600 = 1157 RPS

Итого мы получаем по средней суточной нагрузке на хранилища:
 * Хранилище сессий - 26 RPS;
 * Хранилище звонков - 1157 RPS.

## Логическая схема БД

Сущности:
 1. Пользователь;
 2. Звонок;
 3. Сессия;
 4. ПользовательЗвонок.

![](https://sun9-40.userapi.com/impg/Io2p7G-xOIY0tZoFJVODAXPqFRUw5ACFQ4kjNg/WTQDJ5FewQg.jpg?size=878x444&quality=96&sign=434558b354f26b60f1c7e8994cb88c8c&type=album)

## Физическая схема БД

Нам нужно хранить сессии, текущие звонки и пользователей. Логично, что сессии мы будем хранить в in-memory БД, например, Redis. 
Так как нагрузка не очень большая, можем использовать PostgreSQL для хранения звонков и пользователей.


## Источники информации:
 - https://app2top.ru/industry/igrovoe-chat-prilozhenie-discord-ispol-zuyut-45-mln-chelovek-101159.html
 - https://discord.com/company
