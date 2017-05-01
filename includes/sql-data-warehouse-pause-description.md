
<!--
includes/sql-data-warehouse-include-pause-description.md

Latest Freshness check:  2016-04-22 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-powershell.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-rest-api.md

-->
Para ahorrar costos, puede pausar y reanudar recursos de proceso a petición. Por ejemplo, si no va a usar la base de datos durante la noche y los fines de semana, puede pausarla durante esas horas y reanudarla durante el día. No se le cobrará por DWU mientras la base de datos se encuentre en pausa.

Al pausar una base de datos:

* Los recursos de memoria y proceso se devuelven al grupo de recursos disponibles en el centro de datos
* Durante la pausa, DWU no tiene costo alguno.
* El almacenamiento de datos no se ve afectado y sus datos permanecen intactos. 
* Almacenamiento de datos SQL cancela todas las operaciones de ejecución o en cola.

