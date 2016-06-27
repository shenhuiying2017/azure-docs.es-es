<properties
	pageTitle="Copia de seguridad de bases de datos habilitadas para Stretch | Microsoft Azure"
	description="Obtenga información sobre cómo realizar una copia de seguridad de bases de datos habilitadas para Stretch."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Copia de seguridad y restauración de bases de datos habilitadas para Stretch

Gracias a las copias de seguridad de bases de datos, podrá recuperarse de muchos tipos de errores y desastres.

-   Tiene que realizar una copia de seguridad de las bases de datos de SQL Server habilitadas para Stretch.  

-   Microsoft Azure realiza automáticamente copias de seguridad de los datos remotos que Stretch Database ha migrado de SQL Server a Azure.

>    [AZURE.NOTE] La copia de seguridad solo es uno de los componentes que conforman una solución de continuidad empresarial y de alta disponibilidad. Para obtener más información sobre la alta disponibilidad, consulte [Soluciones de alta disponibilidad (SQL Server)](https://msdn.microsoft.com/library/ms190202.aspx).

## Copia de seguridad de los datos de SQL Server  

Para realizar una copia de seguridad de las bases de datos de SQL Server habilitadas para Stretch, puede seguir usando los métodos de copia de seguridad de SQL Server que utiliza actualmente. Para obtener más información, consulte [Realizar copias de seguridad y restaurar bases de datos de SQL Server](https://msdn.microsoft.com/library/ms187048.aspx).

Las copias de seguridad de una base de datos de SQL Server contienen únicamente datos locales y datos elegibles que pueden migrarse a un momento dado cuando se ejecuta la copia de seguridad (Los datos elegibles constituyen información que aún no se ha migrado, pero que se migrará a Azure según la configuración de migración de las tablas). Este proceso se conoce como "copia de seguridad **superficial**" y no incluye los datos que ya se han migrado a Azure.

## Copia de seguridad de los datos de Azure remotos   

Microsoft Azure realiza automáticamente copias de seguridad de los datos remotos que Stretch Database ha migrado de SQL Server a Azure.

### Azure reduce el riesgo de pérdida de datos gracias a las copias de seguridad automáticas  
El servicio SQL Server Stretch Database de Azure protege las bases de datos remotos con instantáneas de almacenamiento automático cada 8 horas como mínimo. Conserva cada instantánea durante 7 días para proporcionar diferentes puntos de restauración posibles.

### Azure reduce el riesgo de pérdida de datos gracias a la redundancia geográfica  
Las copias de seguridad de bases de datos de Azure se almacenan en el Almacenamiento de Azure con redundancia geográfica (RA-GRS) y, por tanto, tienen esta característica de forma predeterminada. El almacenamiento con redundancia geográfica replica sus datos a una región secundaria que se encuentra a cientos de kilómetros de distancia de la región principal. Los datos se replican tres veces en cada región primaria y secundaria, en dominios de error y en dominios de actualización independientes. Esto garantiza que los datos perduren incluso en el caso de un apagón regional completo o un desastre que haga que una de las regiones de Azure deje de estar disponible.

### <a name="stretchRPO"></a>Stretch Database reduce el riesgo de pérdida de los datos de Azure al conservar filas migradas temporalmente
Una vez que Stretch Database migre las filas elegibles de SQL Server a Azure, conserva estas filas de la tabla de almacenamiento provisional durante un periodo mínimo de 8 horas. Si restaura una copia de seguridad de la base de datos de Azure, Stretch Database utilizará las filas que se guardan en la tabla de almacenamiento provisional para conciliar las bases de datos de SQL Server y de Azure.

Después de restaurar una copia de seguridad de los datos de Azure, tendrá que ejecutar el procedimiento almacenado [sys.sp\_rda\_reauthorize\_db](https://msdn.microsoft.com/library/mt131016.aspx) para volver a establecer la conexión entre la base de datos de SQL Server habilitada para Stretch y la de Azure remota. Al ejecutar **sys.sp\_rda\_reauthorize\_db**, Stretch Database conciliará automáticamente las bases de datos de SQL Server y Azure.

Para aumentar el número de horas de los datos migrados que Stretch Database conserva temporalmente en la tabla de almacenamiento provisional, ejecute el procedimiento almacenado [sys.sp\_rda\_set\_rpo\_duration](https://msdn.microsoft.com/library/mt707766.aspx) y especifique un número de horas mayor que 8. Para decidir la cantidad de datos que conservará, tenga en cuenta los siguientes factores:
-   La frecuencia de las copias de seguridad de Azure automáticas (al menos, cada 8 horas).
-   Después de que se produzca un problema, el tiempo que se necesita en detectarlo y decidir restaurar una copia de seguridad.
-   La duración de la operación de restauración de Azure.

> [AZURE.NOTE] Al aumentar la cantidad de datos que Stretch Database conserva temporalmente en la tabla de almacenamiento provisional, se incrementa el espacio necesario en la instancia de SQL Server.

Para comprobar el número de horas que Stretch Database conserva en estos momentos temporalmente en la tabla de almacenamiento provisional, ejecute el procedimiento almacenado [sys.sp\_rda\_get\_rpo\_duration](https://msdn.microsoft.com/library/mt707767.aspx).

## Consulte también

[Administración y solución de problemas de Stretch Database](sql-server-stretch-database-manage.md)

[sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Copia de seguridad y restauración de bases de datos de SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)

<!---HONumber=AcomDC_0615_2016-->