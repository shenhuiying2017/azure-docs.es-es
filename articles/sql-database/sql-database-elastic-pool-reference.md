<properties 
	pageTitle="Referencia de grupos elásticos de bases de datos SQL de Azure" 
	description="Esta referencia proporciona vínculos y detalles de artículos de grupos elásticos de bases de datos, así como información de programación." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/14/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Referencia de grupos elásticos de bases de datos SQL (vista previa)

Para desarrolladores de SaaS que tienen decenas, cientos o incluso miles de bases de datos, los grupos de bases de datos elásticas simplifican el proceso de creación, mantenimiento y administración del rendimiento y el coste de todo el grupo de bases de datos.

Esta referencia proporciona vínculos y detalles de artículos de grupos elásticos de bases de datos, así como información de programación.

## Información general

Un grupo de bases de datos elásticas es una colección de unidades de rendimiento de bases de datos (DTU) y almacenamiento (GB) que varias bases de datos comparten. Las bases de datos elásticas se pueden agregar y quitar del grupo en cualquier momento. Las bases de datos elásticas del grupo usan solo los recursos que necesitan, con lo que liberan recursos disponibles solo para las bases de datos activas que los necesiten. Para obtener ayuda para determinar si las bases de datos se beneficiarían de un grupo de bases de datos elásticas, consulte [Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md).



## Requisitos previos para crear y administrar grupos de bases de datos elásticas


- Los grupos de bases de datos elásticas solo están disponibles en los servidores de Base de datos SQL V12 de Azure.   
- La creación y administración de grupos de bases de datos elásticas se puede realizar con el [portal de Microsoft Azure](https://portal.azure.com), PowerShell y las API de REST del Administrador de recursos de Azure (ARM); no se admiten el [portal clásico](https://manage.windowsazure.com/) y los comandos de administración de servicio (RDFE). 


## Restricciones de la vista previa actual

- El nivel de precios de un grupos de bases de datos elásticas en la versión preliminar actual es Standard.  
- No se admite la importación de una base de datos directamente en un grupo. Puede importar a una base de datos independiente y, a continuación, mover la base de datos a un grupo. No se admite la exportación de una base de datos desde un grupo.
- Cada grupo puede tener un máximo 100 bases de datos.


## Lista de artículos

Los artículos siguientes le ayudarán a empezar a usar bases de datos y trabajos elásticos:

| Artículo | Descripción |
| :-- | :-- |
| [Grupos de bases de datos SQL elásticas](sql-database-elastic-pool.md) | Información general sobre los grupos elásticos |
| [Creación y administración de un grupo de bases de datos SQL elásticas con el portal de Azure](sql-database-elastic-pool-portal.md) | Creación y administración de un grupo elástico mediante el portal de Azure |
| [Creación y administración de un grupo de bases de datos SQL elásticas con PowerShell](sql-database-elastic-pool-powershell.md) | Creación y administración de un grupo elástico mediante cmdlets de PowerShell |
| [Información general de los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md) | Información general acerca del servicio de trabajos elásticos, que permite ejecutar secuencias de comandos de T-SQL en todas las bases de datos elásticas de un grupo. |
| [Instalación del componente de trabajo de la base de datos elástica](sql-database-elastic-jobs-service-installation.md) | Instalación del servicio de trabajo de base de datos elástica |
| [Creación del usuario necesario para el servicio de trabajos elásticos](sql-database-elastic-jobs-add-logins-to-dbs.md) | Para ejecutar una secuencia de comandos de trabajo de la base de datos elástica, deberá agregar un usuario con los permisos adecuados para cada base de datos del grupo. |
| [Desinstalación de los componentes de trabajo de la base de datos elástica](sql-database-elastic-jobs-uninstall.md) | Recuperación de errores al intentar instalar el servicio de trabajo de la base de datos elástica |



## Detalles del espacio de nombres y del extremo
Un grupo elástico es un recurso ARM de tipo "ElasticPool" en la base de datos SQL de Microsoft Azure.

- **Espacio de nombres**: Microsoft.Sql/ElasticPool
- **management-endpoint** para llamadas a la API de REST (Administrador de recursos de Azure): https://management.azure.com



## Propiedades del grupo de bases de datos elásticas

| Propiedad | Descripción |
| :-- | :-- |
| creationDate | Fecha en que se crea el grupo. |
| databaseDtuMax | Número máximo de DTU que puede usar una base de datos del grupo. El número máximo de DTU de la base de datos no es una garantía de recursos. El número máximo de DTU se aplica a todas las bases de datos del grupo. |
| databaseDtuMin | El número mínimo de DTU que puede usar una base de datos del grupo está garantizado. El número mínimo DTU de la base de datos se puede establecer en 0. El número mínimo de DTU se aplica a todas las bases de datos del grupo. Tenga en cuenta que el producto del número de bases de datos del grupo y el número mínimo de DTU de la base de datos no puede superar el número de DTU de la propia agrupación. |
| Dtu | Número de DTU compartidos por todas las bases de datos del grupo. |
| edition | Nivel de servicio del grupo. Todas las bases de datos del grupo tienen esta edición. |
| elasticPoolId | GUID de la instancia del grupo. |
| elasticPoolName | Nombre del grupo. El nombre es exclusivo en relación a su servidor principal. |
| location | Ubicación del centro de datos en el que se creó el grupo. |
| state | Estado es "Deshabilitado" si no se ha efectuado el pago de la factura para la suscripción. El estado será "Listo" en los demás casos. |
| storageMB | Límite de almacenamiento en MB del grupo. Cada base de datos del grupo tiene el límite de almacenamiento de la versión Standard Edition (250 GB); sin embargo, el almacenamiento total usado por todas las bases de datos del grupo no puede superar este límite de grupo. |


## Límites de DTU y de almacenamiento de grupos y bases de datos elásticas

El límite de almacenamiento del grupo se determina según la cantidad de DTU del grupo; cada DTU = 1 GB de almacenamiento. Por ejemplo, un grupo con 200 DTU tiene un límite de almacenamiento de 200 GB.

| . | Valor predeterminado | Valores válidos |
| :-- | :-- | :-- |
| Dtu | 100 | 100, 200, 400, 800, 1200 |
| databaseDtuMax | 100 | 10, 20, 50 100 |
| databaseDtuMin | 0 | 0, 10, 20, 50 |
| storageMB | 100 GB* | 100 GB, 200 GB, 400 GB, 800 GB, 1200 GB |

*Las unidades de API son MB y no GB.

## Límites de trabajadores y sesiones

El número máximo de sesiones y trabajadores simultáneos que se admiten para todas las bases de datos del grupo elástico depende del valor de DTU del grupo:

| DTU | N.º máximo de trabajadores simultáneos | N.º máximo de sesiones simultáneas |
| :-- | :-- | :-- |
| 100 | 200 | 2400 |
| 200 | 400 | 4800 |
| 400 | 800 | 9600 |
| 800 | 1600 | 19 200 |
| 1200 | 2400 | 28 800 |


## Limitaciones del Administrador de recursos de Azure

Los servidores de Base de datos SQL V12 de Azure se encuentran en grupos de recursos.

- Cada grupo de recursos puede tener un máximo de 800 servidores.
- Cada servidor puede tener un máximo de 800 grupos elásticos.


## Latencia de las operaciones de grupos elásticos

- El cambio de DTU garantizadas por base de datos (databaseDtuMin) o del número máximo de DTU por base de datos (databaseDtuMax) suele completarse en 5 minutos o menos. 
- El cambio de DTU o del límite de almacenamiento (storageMB) del grupo depende de la cantidad total de espacio que usen todas las bases de datos del grupo. Los cambios tienen un duración media de 90 minutos o menos por cada 100 GB. Por ejemplo, si el espacio total usado por todas las bases de datos del grupo es de 200 GB, la latencia esperada para el cambio de DTU/límite de almacenamiento es de 3 horas o menos. 



## Comandos de API de REST y cmdlets de PowerShell de grupos de bases de datos elásticos (solo para el Administrador de recursos de Azure)

A continuación se muestran los comandos de API de REST y los cmdlets de PowerShell que están disponibles para crear y administrar grupos elásticos:

| [Cmdlets de PowerShell](https://msdn.microsoft.com/library/mt125356.aspx) | [Comandos de las API de REST](https://msdn.microsoft.com/library/azure/mt163571.aspx) |
| :-- | :-- |
| Get-AzureSqlDatabase | Get Azure SQL database |
| Get-AzureSqLElasticPool | Get Azure SQL Database elastic database pool |
| Get-AzureSqlElasticPoolActivity | Get Azure SQL Database elastic database pool operations |
| Get-AzureSqlElasticPoolDatabase | Get Azure SQL Database elastic database |
| Get-AzureSqlElasticPoolDatabaseActivity | Get Azure SQL Database elastic database operations |
| Get-AzureSqlServer | Get Azure SQL Database server |
| Get-AzureSqlServerFirewallRule | Get Azure SQL Database server firewall rule |
| Get-AzureSqlServerServiceObjective | Get Azure SQL Database server service objective |
| New-AzureSqlDatabase | Create Azure SQL database |
| New-AzureSqlElasticPool | Create Azure SQL Database elastic database pool |
| New-AzureSqlServer | Create Azure SQL Database server |
| New-AzureSqlServerFirewallRule | Create Azure SQL Database server firewall rule) |
| Remove-AzureSqlDatabase | Remove Azure SQL database |
| Remove-AzureSqlElasticPool | Remove Azure SQL Database elastic database pool |
| Remove-AzureSqlServer | Remove Azure SQL Database server |
| Set-AzureSqlDatabase | Set Azure SQL database |
| Set-AzureSqlElasticPool | Set Azure SQL Database elastic database pool |
| Set-AzureSqlServer | Set Azure SQL Database server |
| Set-AzureSqlServerFirewallRule | Set Azure SQL Database server firewall rule |
| Get-Metrics | Get Metrics |


## Información de precios y facturación

Los grupos de bases de datos elásticos se facturan según la características siguientes:

- Los grupos elásticos se facturan desde su creación, incluso si no contiene ninguna base de datos. 
- Los grupos elásticos se facturan por horas. Se trata de la misma frecuencia de medición que la de los niveles de rendimiento de las bases de datos independientes. 
- Si se cambia el tamaño de un grupo elástico a una nueva cantidad de DTU, el grupo no se factura según la nueva cantidad de DTU hasta que se complete la operación de cambio de tamaño. Esto sigue el mismo patrón que el cambio de nivel de rendimiento de las bases de datos independientes. 


- El precio de un grupo elástico se basa en el número de DTU del grupo y el número de bases de datos del grupo.
- El precio se calcula de la manera siguiente: (número de DTU del grupo) x (precio unitario por DTU) + (número de bases de datos) x (precio unitario por base de datos).

El precio unitario de DTU de un grupo elástico es mayor que el precio unitario de DTU para una base de datos independiente del mismo nivel de servicio. Para obtener información detallada, vea [Precios de bases de datos SQL](http://azure.microsoft.com/pricing/details/sql-database/).

## Errores de grupos de bases de datos elásticos

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | El grupo elástico ha alcanzado su límite de almacenamiento. El uso del almacenamiento del grupo elástico no puede superar (%d) MB. | Límite de espacio del grupo elástico en MB. | Se ha intentado escribir datos en una base de datos cuando se ha alcanzado el límite de almacenamiento del grupo elástico. | Considere la posibilidad de incrementar el número de DTU del grupo elástico si es posible para aumentar su límite de almacenamiento, reduzca el almacenamiento usado por las bases de datos individuales del grupo elástico o quite bases de datos del grupo elástico. |
| 10929 | EX_USER | La garantía mínima de %s es de %d, el límite máximo es %d y el uso actual de la base de datos es %d. Sin embargo, el servidor está demasiado ocupado en estos momentos para admitir solicitudes mayores que %d para esta base de datos. Vea [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) para obtener ayuda. De lo contrario, Inténtelo de nuevo más tarde. | Número mínimo de DTU por cada base de datos; número máximo de DTU por base de datos. | El número total de trabajadores simultáneos (solicitudes) de todas las bases de datos del grupo elástico intentó superar el límite del grupo. | Considere la posibilidad de incrementar el número de DTU del grupo elástico si es posible para aumentar el límite de trabajadores, o bien quite bases de datos del grupo elástico. |
| 40844 | EX_USER | La base de datos '%ls' del servidor '%ls' es una base de datos de la versión '%ls' de un grupo elástico y no puede tener una relación de copia continua. | Nombre de la base de datos, versión de base de datos, nombre del servidor. | Se emitió un comando StartDatabaseCopy para una base de datos que no es Premium de un grupo elástico. | Próximamente |
| 40857 | EX_USER | Grupo elástico no encontrado para el servidor: '%ls', nombre del grupo elástico: '%ls'. | Nombre del servidor; nombre del grupo elástico. | El grupo elástico especificado no existe en el servidor especificado. | Especifique un nombre de grupo elástico válido. |
| 40858 | EX_USER | El grupo elástico '%ls' ya existe en el servidor: '%ls' | Nombre de grupo elástico, nombre del servidor. | El grupo elástico especificado ya existe en el servidor lógico especificado. | Proporcione un nuevo nombre de grupo elástico. |
| 40859 | EX_USER | El grupo elástico no admite el nivel de servicio '%ls'. | Nivel de servicio de grupo elástico. | El nivel de servicio especificado no se admite para el aprovisionamiento de grupo elástico. | Especifique la versión correcta o deje el nivel de servicio en blanco para usar el nivel de servicio predeterminado. |
| 40860 | EX_USER | La combinación del grupo elástico '%ls' y del objetivo de servicio '%ls' no es válida. | Nombre del grupo flexible; nombre del objetivo de nivel de servicio. | El grupo elástico y el objetivo de servicio pueden especificarse juntos solo si se especifica el objetivo de servicio como "ElasticPool". | Especifique la combinación correcta de grupo elástico y objetivo de servicio. |
| 40861 | EX_USER | La versión de la base de datos '%.*ls' no puede ser distinta del nivel de servicio del grupo elástico, que es '%.*ls'. | Versión de la base de datos, nivel de servicio del grupo elástico. | La versión de la base de datos es distinta del nivel de servicio del grupo elástico. | No especifique una versión de la base de datos distinta del nivel de servicio del grupo elástico. Tenga en cuenta que no es necesario especificar la versión de la base de datos. |
| 40862 | EX_USER | Debe especificarse el nombre del grupo elástico si se especifica el objetivo de servicio del grupo elástico. | None | El objetivo del servicio de grupo elástico no identifica de manera única un grupo elástico. | Especifique el nombre del grupo elástico si usa el objetivo del servicio de grupo elástico. |
| 40864 | EX_USER | El número de DTU del grupo elástico debe ser de al menos (%d) DTU para el nivel de servicio ' %.* ls'. | Número de DTU del grupo elástico; nivel de servicio del grupo elástico. | Se intentó establecer el número de DTU para el grupo elástico por debajo del límite mínimo. | Vuelva a intentar establecer el número de DTU para el grupo elástico al menos en el límite mínimo. |
| 40865 | EX_USER | El número de DTU del grupo elástico no puede superar (%d) DTU para el nivel de servicio ' %.* ls'. | Número de DTU del grupo elástico; nivel de servicio del grupo elástico. | Se intentó establecer el número de DTU para el grupo elástico por encima del máximo. | Vuelva a intentar establecer el número de DTU para el grupo elástico a un valor inferior al límite máximo. |
| 40867 | EX_USER | El número máximo de DTU por base de datos debe ser de al menos (%d) para el nivel de servicio '%.*ls'. | Número máximo de DTU por base de datos, nivel de servicio del grupo elástico. | Se intentó establecer el número máximo de DTU por base de datos por debajo del límite admitido. | Considere la posibilidad de utilizar el nivel de servicio del grupo elástico que admita la configuración deseada. |
| 40868 | EX_USER | El número máximo de DTU por base de datos no puede superar (%d) para el nivel de servicio '%.*ls'. | Número máximo de DTU por base de datos, nivel de servicio del grupo elástico. | Se intentó establecer el número máximo de DTU por base de datos por encima del límite admitido. | Considere la posibilidad de utilizar el nivel de servicio del grupo elástico que admita la configuración deseada. |
| 40870 | EX_USER | El número mínimo de DTU por base de datos no puede superar (%d) para el nivel de servicio '%.*ls'. | Número mínimo de DTU por base de datos, nivel de servicio del grupo elástico. | Se intentó establecer el número mínimo de DTU por base de datos por encima del límite admitido. | Considere la posibilidad de utilizar el nivel de servicio del grupo elástico que admita la configuración deseada. |
| 40873 | EX_USER | El número de bases de datos (%d) y el mínimo de DTU por base de datos (%d) no puede superar el número de DTU del grupo elástico (%d). | Número de bases de datos del grupo elástico; número mínimo de DTU por base de datos; número de DTU del grupo elástico. | Se intentó especificar un número mínimo de DTU para las bases de datos del grupo elástico que supera el número de DTU del grupo elástico. | Considere la posibilidad de aumentar el número de DTU del grupo elástico, reduzca el número mínimo de DTU por base de datos, o bien reduzca el número de bases de datos del grupo elástico. |
| 40877 | EX_USER | No se puede eliminar un grupo elástico a menos que no contenga ninguna base de datos. | None | El grupo elástico contiene una o más bases de datos y no se puede eliminar. | Quite las bases de datos del grupo elástico para poder eliminarlo. |
| 40881 | EX_USER | El grupo elástico ' %.* ls' ha alcanzado el límite de recuento de bases de datos. El límite de bases de datos del grupo elástico no puede superar (%d) para un grupo elástico con (%d) DTU. | Nombre del grupo elástico; límite de bases de datos del grupo elástico; número de DTU del grupo de recursos. | Se intentó crear una base de datos en el grupo elástico, o agregar una base de datos al grupo elástico, cuando se ha alcanzado el límite de bases de datos del grupo elástico. | Considere la posibilidad de incrementar el número de DTU del grupo elástico si es posible para aumentar el límite de bases de datos, o bien quite bases de datos del grupo elástico. |
| 40889 | EX_USER | El límite de DTU o de almacenamiento del grupo elástico ' %.* ls' no se puede reducir, puesto que no proporcionaría suficiente espacio de almacenamiento para sus bases de datos. | Nombre del grupo elástico. | Se intentó reducir el límite de almacenamiento del grupo elástico por debajo de su uso de almacenamiento. | Considere la posibilidad de reducir el uso de almacenamiento de bases de datos individuales del grupo elástico, o bien quite bases de datos del grupo para reducir su número de DTU o el límite de almacenamiento. |
| 40891 | EX_USER | El número mínimo de DTU por base de datos (%d) no puede superar el número máximo de DTU por base de datos (%d). | Número mínimo de DTU por base de datos; número máximo de DTU por base de datos. | Se intentó establecer el número mínimo de DTU por base de datos con un valor superior al número máximo de DTU por base de datos. | Asegúrese de que el número mínimo de DTU por base de datos no supere el número máximo de DTU por base de datos. |
| TBD | EX_USER | El tamaño de almacenamiento para una base de datos individual de un grupo elástico no puede superar el tamaño máximo permitido por el grupo elástico del nivel de servicio ' %.* ls'. | Nivel de servicio de grupo elástico. | El tamaño máximo de la base de datos supera el tamaño máximo permitido por el nivel de servicio del grupo elástico. | Establezca el tamaño máximo de la base de datos por debajo de los límites de tamaño máximo permitidos por el nivel de servicio del grupo elástico. |

<!---HONumber=July15_HO5-->