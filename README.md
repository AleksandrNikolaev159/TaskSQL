# TaskSQL

Задание на изучение некоторых DDL и DML SQL команд.
Выполнялось в рамках лабораторных работ по дисциплине "Базы данных". В последствии был выполнен курсовой проект с использованием СУБД Microsoft Access

Образец того как должны выглядеть таблицы
![image](https://user-images.githubusercontent.com/55660567/212573880-f4d359b9-7c12-4a15-8281-8b0525561b69.png)


Пример выполнения мной задания на написание DDL, DML sql запросов к БД.

# Создание таблиц в БД

CREATE TABLE devices(
device_id INTEGER NOT NULL,
device_type_id INTEGER,
parent_device_id INTEGER,
device_name VARCHAR,
setup_date DATE,
warehouse_id INTEGER)
PRIMARY KEY (device_id),
FOREIGN KEY (device_type_id) REFERENCES device_types (device_type_id),
FOREIGN KEY (warehouse_id) REFERENCES warehouses (warehouse_id );

CREATE TABLE device_types(
device_type_id INTEGER NOT NULL,
device_class_id INTEGER,
device_type_name VARCHAR,
device_cost INTEGER),
PRIMARY KEY (device_type_id),
FOREIGN KEY (device_class_id) REFERENCES device_classes (device_class_id);

CREATE TABLE warehouses(
warehouse_id INTEGER NOT NULL,
warehouse_name VARCHAR),
PRIMARY KEY (warehouse_id);

CREATE TABLE device_classes(
device_class_id INTEGER NOT NULL,
device_class_name VARCHAR),
PRIMARY KEY (device_class_id);

# Наполнение таблиц данным
NSERT  INTO devices
VALUES(123,15,NULL,’ коммутатор D-Link на ленина, 1’,’01.02.2018’),(…………)…….;

INSERT  INTO device_types
VALUES(15,7,NULL,’ коммутатор D-Link’,10200),(…………)…….;

INSERT  INTO warehouses
VALUES(22,’ ленина, 1’),(…………)…….;

NSERT  INTO device_classes
VALUES(7,’ коммутатор’),(…………)…….;

# Задание 1. Вывести оборудование, которое поставлено до 01.09.2018

SELECT device_id,device_name,setup_date 
FROM devices
WHERE setup_date <'01.09.2018'

# Задание 2. Вывести все маршрутизаторы, стоящие на сети.

SELECT device_type_name,device_name,device_cost,setup_date,warehouse_name
FROM devices d
JOIN device_types dt
ON dt.device_type_id=d.device_type_id
JOIN warehouses w
ON  w.warehouse_id=d.warehouse_id
WHERE device_type_name LIKE 'маршрутизатор%'

# Задание 3. Вывести устройства из класса "Маршрутизатор", стоящие на складе "ленина, 1"

SELECT d.device_id,d.device_name
FROM devices d
JOIN warehouses w 
ON d.warehouse_id=w.warehouse_id
WHERE w.warehouse_name='ленина, 1'

# Задание 4. Подсчитать общую стоимость оборудования, стоящего на сети.

SELECT SUM(device_cost) AS SUM
FROM device_types dt
JOIN devices d
ON d.device_type_id=dt.device_type_id

# Задание 5. Рассчитать стоимость всего оборудования на сети в разрезе классов.

SELECT dc.device_class_name,SUM(device_cost) AS Общая_стоимость
FROM device_types dt
JOIN device_classes dc
ON dc.device_class_id=dt.device_class_id
JOIN devices d
ON d.device_type_id=dt.device_type_id
GROUP BY device_class_name

# Задание 6. Вывести иерархическую структуру устройств.
SELECT d.device_name,
CASE 
WHEN (d.parent_device_id is null)
THEN 1
WHEN (d.parent_device_id >ANY(SELECT device_id FROM devices) )
THEN 3
ELSE 2
END device_level
FROM devices d 

# Задание 7. Вывести склады, которые были пустыми до 01.06.2018.
SELECT w.warehouse_id,w.warehouse_name
FROM warehouses w
JOIN devices d
ON d.warehouse_id=w.warehouse_id
WHERE d.setup_date> '01.06.2018'

# Задание 8. Вывести устройства в формате.device_id | device_name | setup_date | Is_kmu
поле Is_kmu имеет значение 1 если устройство из класса коммутатор, иначе 0
ELECT d.device_id,d.device_name,d.setup_date,
CASE 
WHEN dc.device_class_name='коммутатор' 
THEN 1
ELSE 0
END Is_kmu
FROM device_classes dc
JOIN device_types dt
ON dt.device_class_id=dc.device_class_id
JOIN devices d
ON d.device_type_id=dt.device_type_id




