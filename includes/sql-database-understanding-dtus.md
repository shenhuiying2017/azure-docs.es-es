La unidad de transacción de base de datos (DTU) es la unidad de medida en Base de datos SQL que representa la potencia relativa de bases de datos en base a una medida del mundo real: la transacción de base de datos. Usamos un conjunto de operaciones que son típicas de una solicitud de procesamiento de transacciones en línea (OLTP) y después medimos cuántas transacciones se podrían completar por segundo en condiciones de carga total (esta es la versión resumida; puede leer los detalles en [formación general de la prueba comparativa](../articles/sql-database/sql-database-benchmark-overview.md)).

Una base de datos de tipo Básica tiene 5 DTU, lo que significa que puede completar 5 transacciones por segundo, mientras que una base de datos de tipo Premium P11 tiene 1750 DTU.

![DTU de base de datos únicas por nivel](./media/sql-database-understanding-dtus/single_db_dtus.png)

DTU para bases de datos únicas se convierte directamente en eDTU para bases de datos elásticas. Por ejemplo, una base de datos de un grupo de bases de datos elásticas de tipo Básico ofrece hasta 5 eDTU. Es el mismo rendimiento que una base de datos única de tipo Básica. La diferencia es que la base de datos flexible no consumirá ninguna eDTU del grupo hasta que tenga que hacerlo.

![Grupos elásticos por nivel](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

Un ejemplo sencillo ayuda. Use un grupo de bases de datos elásticas de tipo Básico con 1000 DTU y coloque 800 bases de datos en él. Siempre y cuando se usen solamente 200 de las 800 bases de datos en un momento dado (5 DTU X 200 = 1000), no se alcanzará la capacidad del grupo y el rendimiento de la base de datos no se degradará. Este es un ejemplo simplificado para mayor claridad. Los cálculos matemáticos reales son un poco más complicados. El portal realiza los cálculos matemáticos y hace una recomendación basándose en el uso histórico de la base de datos. Vea [Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](../articles/sql-database/sql-database-elastic-pool-guidance.md) para saber cómo funcionan las recomendaciones o para realizar los cálculos matemáticos por su cuenta.

<!---HONumber=Nov15_HO1-->