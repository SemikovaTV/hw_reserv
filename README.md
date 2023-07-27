# Домашнее задание к занятию «Резервное копирование баз данных» - Семикова Т.В. FOPS-9

### Задание 1. Резервное копирование

### Кейс
Финансовая компания решила увеличить надёжность работы баз данных и их резервного копирования. 

Необходимо описать, какие варианты резервного копирования подходят в случаях: 

1.1. Необходимо восстанавливать данные в полном объёме за предыдущий день.

### Ответ
В данном случае наиболее подойдет дифференциальный бэкап.

1.2. Необходимо восстанавливать данные за час до предполагаемой поломки.

### Ответ
В данном случае наиболее подойдет дифференциальный бэкап, сделанный на горячую, поскольку дает возможность откатиться до указанной точки времени.

1.3.* Возможен ли кейс, когда при поломке базы происходило моментальное переключение на работающую или починенную базу данных.

### Ответ
Да, такой вариант возможен - это использование репликации master-slave и автоматической настройки переключения статуса slave в master в случае падения основного master.

---

### Задание 2. PostgreSQL

2.1. С помощью официальной документации приведите пример команды резервирования данных и восстановления БД (pgdump/pgrestore).

### Ответ
Для создания дампа БД нужно использовать следующую команду:
```sql
pg_dump <parametrs> <db_name> > <way/to/save/dump_file.dump>
```
Например:
```sql
pg_dump sakila > /home/stv/dump/sakila.dump
```
Так же можно сжать дамп в архив, для экономии пространства и быстрой передачи по сети:
```sql
pg_dump sakila | gzip > /home/stv/dump/sakila.dump.gz
```

2.1.* Возможно ли автоматизировать этот процесс? Если да, то как?

### Ответ
Да, возможно, путем создания скрипта, выдачи ему прав на выполнение и постановки задания планировщику на определенное время выполнения
Текст скрипта:
```bash
#!/bin/bash
PATH=/etc:/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin

pathB=/backup/postgres

find $pathB \( -name "*-1[^5].*" -o -name "*-[023]?.*" \) -ctime +61 -delete

for dbname in `echo "SELECT datname FROM pg_database;" | psql | tail -n +3 | head -n -2 | egrep -v 'template0|template1|postgres'`; do
    pg_dump $dbname | gzip > $pathB/$dbname-$(date "+%Y-%m-%d").sql.gz
done;
```
---

### Задание 3. MySQL

3.1. С помощью официальной документации приведите пример команды инкрементного резервного копирования базы данных MySQL. 

3.1.* В каких случаях использование реплики будет давать преимущество по сравнению с обычным резервным копированием?

*Приведите ответ в свободной форме.*

---
