<properties 
    pageTitle="Cambio del nivel de servicio y del nivel de rendimiento de una base de datos SQL de Azure mediante PowerShell" 
    description="El cambio del nivel de servicio y del nivel de rendimiento de una base de datos SQL de Azure muestra cómo escalar o reducir verticalmente dicha base de datos mediante PowerShell. Cambio del nivel de precios de una base de datos SQL de Azure mediante PowerShell." 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/01/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL con PowerShell

**Base de datos única**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


En este artículo se muestra cómo cambiar el nivel de servicio y el nivel de rendimiento de la base de datos SQL con PowerShell.

Utilice la información de [Actualización de las bases de datos SQL Web o Business a niveles de servicio nuevos](sql-database-upgrade-new-service-tiers.md) y [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure](sql-database-service-tiers.md) para determinar el nivel de capa y el rendimiento de servicio adecuado para la Base de datos SQL de Azure.

> [AZURE.IMPORTANT]El cambio del nivel de servicio y del nivel de rendimiento de una base de datos SQL es una operación en línea. Esto significa que la base de datos permanecerá en línea y disponible durante toda la operación sin tiempo de inactividad.

- Para degradar una base de datos, esta no debe alcanzar el tamaño máximo permitido del nivel de servicio de destino. 
- Al actualizar una base de datos con [Replicación geográfica estándar](https://msdn.microsoft.com/library/azure/dn758204.aspx) o [Replicación geográfica activa](https://msdn.microsoft.com/library/azure/dn741339.aspx) habilitado, primero debe actualizar sus bases de datos secundarias en el nivel de rendimiento deseado antes de actualizar la base de datos principal.
- Al realizar una degradación desde un nivel de servicio Premium, primero es preciso finalizar todas las relaciones de Replicación geográfica. Puede seguir los pasos que se describen en el tema [Terminar una relación de copia continua](https://msdn.microsoft.com/library/azure/dn741323.aspx) para detener el proceso de replicación entre la base de datos principal y las bases de datos secundarias activas.
- Las ofertas del servicio de restauración son diferentes para los distintos niveles de servicio. Si cambia a un nivel inferior, puede perder la capacidad de restaurar a un momento dado o tener un período de retención de copias de seguridad más breve. Para obtener más información, consulte [Copia de seguridad y restauración de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/jj650016.aspx).
- Puede realizar un máximo de cuatro cambios de bases de datos individuales (nivel de servicio o niveles de rendimiento) en un periodo de 24 horas.
- Las nuevas propiedades de la base de datos no se aplican hasta que se completan los cambios.



**Para completar este artículo, necesitará lo siguiente:**

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- una base de datos SQL de Azure. Si no tiene una base de datos SQL, cree una siguiendo los pasos de este artículo: [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md).
- Azure PowerShell.

> [AZURE.IMPORTANT]Tenga en cuenta que el cmdlet Switch-AzureMode ya no está disponible a partir de la versión Vista previa de Azure PowerShell 1.0, y que los cmdlets que estaban en el módulo de Azure ResourceManager han cambiado de nombre. En los ejemplos de este artículo usaremos la nueva convención de nomenclatura de Vista previa de PowerShell 1.0. Para obtener más información detallada, consulte [Degradación del cmdlet Switch-AzureMode en Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).


Para ejecutar los cmdlets de PowerShell, necesitará tener Azure PowerShell instalado y en marcha; asimismo, debido a la eliminación del cmdlet Switch-AzureMode, deberá descargar e instalar la versión más reciente de Azure PowerShell ejecutando el [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).



## Configuración de las credenciales y selección de la suscripción

En primer lugar debe establecer el acceso a su cuenta de Azure; por tanto, inicie PowerShell y luego ejecute el siguiente cmdlet. En la pantalla de inicio de sesión escriba el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

	Add-AzureAccount

Después de iniciar sesión correctamente, verá información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.


### Selección de su suscripción a Azure

Para seleccionar la suscripción, necesitará el nombre o el identificador de suscripción (**-SubscriptionName**). Puede copiar el identificador de suscripción de la información mostrada en el paso anterior o, si dispone de varias suscripciones y necesita más detalles, puede ejecutar el cmdlet **Get-AzureSubscription** y copiar la información de suscripción que desee del conjunto de resultados. Cuando tenga su suscripción, ejecute el siguiente cmdlet:

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureSubscription -SubscriptionId $SubscriptionId

Después de ejecutar correctamente **Select-AzureSubscription** volverá al símbolo del sistema de PowerShell. Si tiene más de una suscripción, puede ejecutar **Get-AzureSubscription** y comprobar que la suscripción que desea usar muestra el elemento **IsCurrent: True**.


 


## Cambio del nivel de servicio y del nivel de rendimiento de su base de datos SQL

Ejecute el cmdlet **Set-AzureRMSqlDatabase** y establezca el elemento **-RequestedServiceObjectiveName** en el nivel de rendimiento del plan de tarifa que desee; por ejemplo: *S0*, *S1*, *S2*, *S3*, *P1*, *P2*, ...

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## Script de PowerShell de ejemplo para cambiar el nivel de servicio y el nivel de rendimiento de su base de datos SQL

    

    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureRMSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## Pasos siguientes

- [Escalar y reducir horizontalmente](sql-database-elastic-scale-get-started.md)
- [Conectarse a Base de datos SQL y consultar dicha base de datos con SSMS](sql-database-connect-query-ssms.md)
- [Exportar una base de datos SQL de Azure](sql-database-export-powershell.md)

## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [Cmdlets de la Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/mt163521.aspx)

<!---HONumber=AcomDC_1203_2015-->