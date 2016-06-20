
<!--
includes/sql-data-warehouse-backup-retention-policies.md

Latest Freshness check:  2016-05-05 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-overview-expectations.md
articles/sql-data-warehouse/sql-data-warehouse-overview-backup-and-restore.md
-->
Almacenamiento de datos SQL crea instantáneas de todos los datos activos cada 8 horas como mínimo mediante instantáneas de Almacenamiento de Azure. Estas instantáneas se mantienen durante 7 días. Esto permite restaurar los datos a uno de los 21 puntos anteriores como mínimo de los últimos 7 días hasta la hora en que se tomó la última instantánea.

Almacenamiento de datos SQL toma una instantánea de la base de datos antes de eliminarla y la conserva durante siete días. Cuando esto pasa, ya no conserva las instantáneas de la base de datos activa. Esto permite restaurar una base de datos eliminada al punto en que se eliminó.

<!---HONumber=AcomDC_0608_2016-->