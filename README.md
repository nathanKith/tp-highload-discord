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
    
Так как большее количество пользователей пользуются приложением десктопно или на мобильных устройствах, то можно предположить что в день пользователь 1 раз логинится и 1 раз разлогинивается, следовательно в день количество запросов на запись в хранилище сессий:

    8,9 млн. * 2 / 24 / 3600 = 206 RPS
    
Посчитаем количество звонков совершаемых в день, полагая, что средняя длительность звонка 2 часа:

    1,2 млрд / 120 = 10 млн. звонков/день

Допустим, что в одном звонке в среднем участвуют 4 человека. На 1 звонок приходится 10 операций: создание звонка, 4 подключения, 4 отключения, обрывание звонка.
Тогда получим, что нагрузка на хранилище звонков в среднем за сутки равна:

    10 млн * 10 / 24 / 3600 = 1157 RPS

Итого мы получаем по средней суточной нагрузке на хранилища:
 * Хранилище сессий - 206 RPS;
 * Хранилище звонков - 1157 RPS.

## Логическая схема БД

Сущности:
 1. Пользователь;
 2. Звонок;
 3. Сессия.

![](https://sun1-23.userapi.com/impg/6xFvILMezTQu6dABu04MNF_vkYdRMLFdMqanEg/3CAYT_YjwVE.jpg?size=827x498&quality=96&sign=d4b60b7a9071ec5df293edce471b6337&type=album)

## Физическая схема БД

Нам нужно хранить сессии, текущие звонки и пользователей. Логично, что сессии мы будем хранить в in-memory БД, но что насчет пользователей и текущих звонках? Информация о текущих звонках должна довольно быстро создавать, быстро удалять, так как живут эти данные недолго, следовательно логичным выбором видится in-memory БД, но если говорить о пользователях, нет веской причины хранить их в оперативной памяти, и более логичным решение было бы хранить их на диске. Так вот нам нужно какое-то универсальное решение, чтобы хранить эти данные.

Логичным выбором в этой ситуации выглядит Tarantool. Для хранения пользователей на диске будем использовать движок vinyl. 


## Источники информации:
 - https://app2top.ru/industry/igrovoe-chat-prilozhenie-discord-ispol-zuyut-45-mln-chelovek-101159.html
 - https://discord.com/company
