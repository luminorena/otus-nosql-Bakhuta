Создана графовая таблица, сделаны запросы в MS SQL. 
В SQL Server поддерживаются только однонаправленные связи, после создания таблиц невозможно обновить столбцы $from_id и $to_id через UPDATE, 
а также нет прямого способа преобразования обычных таблиц в графовые. Плюс лично для меня неудобно, что нет пространственного изображения графа, 
надо додумываться и строить модель в голове (или если база огромна, то без CASE-средств визуализации не обойтись точно), откуда идут какие связи, 
поэтому сложнее. А в neo4j там все видно и как-то проще. 

``` SQL

create database graph_db

use graph_db


/*
Создадим таблицу Employees, идентификатором id и столбцом head - ссылкой на линейного
руководителя сотрудника. 
*/

CREATE TABLE EMPLOYEES 
(ID INT NOT NULL,
NAME NVARCHAR(100),
EXPERIENCE INT,
POST NVARCHAR(40),
HEAD INT,
WAGES INT)

INSERT INTO EMPLOYEES VALUES
(766, N'Николай',5,N'Аналитик',121,25000),
(423, N'Петр',2,N'Разработчик',121,30000),
(121,N'Ольга',10,N'Менеджер направления',876,50000),
(342,N'Вера',4,N'Тестировщик',121,30000),
(876,N'Тимофей',23,N'Начальник отдела',789,70000),
(351,N'Сергей',15,N'Аналитик',343,25000),
(343,N'Аркадий',17,N'Менеджер направления',876,50000),
(123,N'Иван',9,N'Разработчик',343,30000),
(789,N'Владимир',27,N'Заместитель директора',126,70000),
(126,N'Мирослав',39,N'Директор',NULL,100000)


select * from EMPLOYEES

/*
Теперь представим эти данные в формате графа. Создадим таблицу узлов TableNode, 
и к обычному выражению создания таблицы в конец добавим AS NODE.
*/

CREATE TABLE TableNode (
ID int identity(1,1),
EMPLOYEE_ID NUMERIC(3) NOT NULL,
NAME NVARCHAR(100),
POST NVARCHAR(100),
HEAD NUMERIC(3),
WAGES INT) 
AS NODE

insert into TableNode(EMPLOYEE_ID,NAME,POST,HEAD,WAGES) select ID,NAME,POST,HEAD,WAGES
from EMPLOYEES

-- посмотрим содержимое таблицы узлов

select * from TableNode

/*
Таблица узлов содержит специальную колонку $node_id, которая хранит признак 
узла в формате JSON. Остальные столбцы содержат атрибуты данного узла.
*/

-- Следующим шагом создадим ребра. 
--Для этого аналогично предыдущему шагу после выражения добавим AS EDGE.

CREATE TABLE TableEdge(WEIGHT int) AS EDGE


select * from TableEdge

/*
Таблица ребер содержит три столбца. Первый $edge_id – признак ребра в формате JSON, 
столбцы $from_id и $to_id определяют связь между узлами.
Также стоит отметить, что ребра могут иметь дополнительные свойства, например, WEIGHT.
*/

-- определим связи подчинения

insert into TableEdge VALUES ((SELECT $node_id from TableNode WHERE ID = 8), 
(SELECT $node_id from TableNode where id = 9), 15)

select * from TableNode

--напишем запросы
-- узнаем руководителя Николая

SELECT n.id,n.[name],n.post,n.head, n1.id,n1.[name],n1.post,n1.head
FROM 
TableNode n, TableNode n1, TableEdge e
WHERE
MATCH(n-(e)->n1)
and n.[name] = N'Николай'

-- увеличим уровень на один

SELECT n.id,n.[name],n.post,n.head, n1.id,n1.[name],
n1.post,n1.head, n2.id,n2.[name],n2.post,n2.head
FROM 
TableNode n, TableNode n1, TableEdge e, TableEdge e1, TableNode n2
WHERE
MATCH(n-(e)->n1-(e1)->n2)
and n.name = N'Николай'
```
