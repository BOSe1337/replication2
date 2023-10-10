# Домашнее задание к занятию «Репликация и масштабирование. Часть 2»

Задание 1 Опишите основные преимущества использования масштабирования методами:

- активный master-сервер и пассивный репликационный slave-сервер

- master-сервер и несколько slave-серверов:
Данный тип масштабирования позволяет увеличить скорость чтения данных за счет увеличения количества реплик - slave серверов и защитить основной сервер - master от конфликта при вводе или изменении данных, поскольку только на нем возможны и запись и чтение.
Так же это является одним из механзмов отказоуствойчивости - поскольку в случае если основной master сервер выйдет из строя, один из slave серверов может принять на себя функцию master и продолжать работу без отказа всей системы. Кроме того, некоторые тяжелые запросы можно производить на репликах, не затрагивая работу основного сервера.  
  
- активный сервер со специальным механизмом репликации — distributed replicated block device (DRBD);
DRBD можно использовать для обеспечения доступности в случае сбоя. MySQL настроен для хранения информации на блочном устройстве DRBD, при этом один сервер действует как основной, а вторая машина доступна для немедленной замены в случае сбоя.
Необходимо чтобы файлы основной базы данных, файлы табличного пространства и двоичные журналы находились на томе DRBD.
  
- SAN-кластер.
SAN позволяет обеспечить высокую отказоустойчивость серверов, поскольку данные хранятся на отдельном сервере, а основной и/или резервные - монтируют по сети к себе раздел файловой системы хранилища и работают с данными.
Однако существует риск потери всех данных в случае выхода из строя сервера-хранилища.


Задание 2 Разработайте план для выполнения горизонтального и вертикального шаринга базы данных. База данных состоит из трёх таблиц:
пользователи,
книги,
магазины (столбцы произвольно).

Опишите принципы построения системы и их разграничение или разбивку между базами данных.

Вертикальный шардинг - механизм распределения данных, при котором разные таблицы находятся на разных машинах/серверах.
Таким образом для заданного условия распределим таблицы users, books и store на три отдельных сервера.

![alt text](https://github.com/BOSe1337/replication2/blob/main/1.JPG)

Горизонтальный шардинг - механизм распределения данных, при котором обьемные таблица могут быть разбиты на несколько машин/серверов.
В нашем случае наиболее обьемной будет таблица users, разобьем ее по первичному ключу user_id, где информация о пользователях 1-500 хранится на одном сервере, а пользователи 500-1000 хранятся на другом сервере. Так же таблица books тоже может иметь значительный размер, поэтому так же разнесем ее по названию title A-M и title N-Z на разные сервера.
Таблицу store вынесем на отдельную машину.

![alt text](https://github.com/BOSe1337/replication2/blob/main/2.JPG)

В обоих случаях сервера работают в режиме master, можно использовать реплики, но это увеличит затраты на железо

