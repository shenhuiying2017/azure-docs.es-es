<properties 
    pageTitle="Configuración de la replicación geográfica activa para Base de datos SQL de Azure con PowerShell | Microsoft Azure " 
    description="replicación geográfica para Base de datos SQL con PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="04/27/2016"
    ms.author="sstein"/>

# Configuración de la replicación geográfica para Base de datos SQL de Azure con PowerShell



> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


En este artículo se muestra cómo configurar la replicación geográfica para Base de datos SQL de Azure con PowerShell.

Para iniciar la conmutación por error, vea [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure](sql-database-geo-replication-failover-powershell.md).

>[AZURE.NOTE] La replicación geográfica activa (bases de datos secundarias legibles) está ahora disponible para todas las bases de datos en todos los niveles de servicio. En abril de 2017 se retirará el tipo secundario no legible y las bases de datos no legibles existentes se actualizarán automáticamente a secundarias legibles.

Puede configurar hasta cuatro bases de datos secundarias legibles en las mismas ubicaciones de centros de datos o en otras (regiones). Las bases de datos secundarias están disponibles en caso de una interrupción del centro de datos o de imposibilidad para conectarse a la base de datos principal.

Para configurar la replicación geográfica, necesita lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **CUENTA GRATUITA** de la parte superior de la página y, después, vuelva para finalizar este artículo.
- Una base de datos SQL de Azure: la base de datos principal que quiere replicar en una región geográfica diferente.
- Azure PowerShell 1.0 o posterior. Para descargar e instalar los módulos de Azure PowerShell, siga las instrucciones de [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).


## Configuración de las credenciales y selección de la suscripción

En primer lugar debe establecer el acceso a su cuenta de Azure; por tanto, inicie PowerShell y luego ejecute el siguiente cmdlet. En la pantalla de inicio de sesión escriba el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.


	Login-AzureRmAccount

Después de iniciar sesión correctamente, verá información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.


### Selección de su suscripción a Azure

Para seleccionar la suscripción, necesita su id. de suscripción. Puede copiar el identificador de suscripción de la información que se muestra en el paso anterior o, si dispone de varias suscripciones y necesita más detalles, puede ejecutar el cmdlet **Get-AzureRmSubscription** y copiar la información de suscripción que desee del conjunto de resultados. El siguiente cmdlet usa el identificador de suscripción para establecer la suscripción actual:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Después de ejecutar correctamente **Select-AzureRMSubscription** volverá al símbolo del sistema de PowerShell.


## Agregar una base de datos secundaria


Los pasos siguientes crean otra base de datos secundaria en una asociación de replicación geográfica.
  
Para habilitar una base de datos secundaria debe ser el propietario o copropietario de la suscripción.

Puede usar el cmdlet **New-AzureRmSqlDatabaseSecondary** para agregar una base de datos secundaria en un servidor asociado a una base de datos local en el servidor al que está conectado (la base de datos principal).

Este cmdlet reemplaza **Start AzureSqlDatabaseCopy** por el parámetro **–IsContinuous**. Dará como resultado un objeto **AzureRmSqlDatabaseSecondary** que otros cmdlets pueden usar para identificar claramente un vínculo de replicación específico. Este cmdlet devolverá un resultado cuando la base de datos secundaria esté creada y totalmente inicializada. Puede tardar desde unos minutos hasta varias horas según el tamaño de la base de datos.

La base de datos replicada en el servidor secundario tendrá el mismo nombre que la base de datos en el servidor principal y, de forma predeterminada, tendrán el mismo nivel de servicio. La base de datos secundaria puede ser legible o no legible, y puede ser una base de datos única o elástica. Para más información, consulte [New-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) y [Niveles de servicio](sql-database-service-tiers.md). Después de crear e inicializar la base de datos secundaria, los datos comenzarán a replicarse desde la base de datos principal a la nueva base de datos secundaria. Los pasos siguientes describen cómo llevar a cabo esta tarea mediante PowerShell para crear bases de datos secundarias legibles y no legibles, con una base de datos única o una base de datos elástica.

Si la base de datos del asociado ya existe (por ejemplo, como resultado de la terminación de una relación de replicación geográfica anterior) se producirá un error en el comando.



### Incorporación de una base de datos secundaria no legible (base de datos única)

El comando siguiente crea una base de datos secundaria no legible de la base de datos "mydb" del servidor "srv2" en el grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### Incorporación de una base de datos secundaria legible (base de datos única)

El comando siguiente crea una base de datos secundaria legible de la base de datos "mydb" del servidor "srv2" en el grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### Incorporación de una base de datos secundaria no legible (base de datos elástica)

El comando siguiente crea una base de datos secundaria no legible de la base de datos "mydb" en el grupo de bases de datos elásticas “ElasticPool1” del servidor "srv2" en el grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### Incorporación de una base de datos secundaria legible (base de datos elástica)

El comando siguiente crea una base de datos secundaria legible de la base de datos "mydb" en el grupo de bases de datos elásticas “ElasticPool1” del servidor "srv2" en el grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## Elimine una base de datos secundaria

Use el cmdlet **Remove-AzureRmSqlDatabaseSecondary** para terminar definitivamente la asociación de replicación entre una base de datos secundaria y su principal. Después de terminar la relación, la base de datos secundaria se convierte en una base de datos de lectura y escritura. Si se interrumpe la conectividad con la base de datos secundaria, el comando se ejecuta correctamente pero la base de datos secundaria será de lectura y escritura después de restaurarse la conectividad. Para más información, consulte [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) y [Niveles de servicio](sql-database-service-tiers.md).

Este cmdlet reemplaza a Stop-AzureSqlDatabaseCopy para la replicación.

Esta eliminación equivale a una terminación forzada que quita el vínculo de replicación y deja la base de datos secundaria anterior como una base de datos independiente que no se replicó completamente antes de la terminación. Todos los datos de vínculo se limpiarán en la base de datos principal anterior y en la base de datos secundaria anterior, cuando estén disponibles, si lo están. Este cmdlet devolverá un resultado cuando se quite el vínculo de replicación.


Para quitar la base de datos secundaria, los usuarios deben tener acceso de escritura a las bases de datos principal y secundaria conforme a RBAC. Consulte Control de acceso basado en roles para obtener más información.

El código siguiente quita el vínculo de replicación de la base de datos "mydb" al servidor "srv2" del grupo de recursos "rg2".

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## Supervisión y mantenimiento de la configuración de la replicación geográfica

Las tareas de supervisión incluyen la supervisión de la configuración de replicación geográfica y la supervisión del mantenimiento de la replicación de los datos.

Puede usarse [Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) para recuperar la información sobre los vínculos de replicación de reenvío visibles en la vista de catálogo sys.geo\_replication\_links.

El comando siguiente recupera el estado del vínculo de replicación entre la base de datos principal "mydb" y la secundaria en el servidor "srv2" del grupo de recursos "rg2".

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


  

## Pasos siguientes

- [Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure](sql-database-geo-replication-failover-powershell.md)
- [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md)




## Recursos adicionales

- [Configuración de seguridad para Replicación geográfica activa o estándar](sql-database-geo-replication-security-config.md)
- [Lo más destacado de las nuevas funcionalidades de replicación geográfica](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [P+F de BCDR de Base de datos SQL](sql-database-bcdr-faq.md)
- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)

<!---HONumber=AcomDC_0504_2016-->