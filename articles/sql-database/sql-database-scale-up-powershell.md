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
	ms.date="09/10/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL con PowerShell

**Base de datos única**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


En este artículo se muestra cómo cambiar el nivel de servicio y el nivel de rendimiento de la base de datos SQL con PowerShell.

Utilice la información de [Actualización de las bases de datos SQL Web o Business a niveles de servicio nuevos](sql-database-upgrade-new-service-tiers.md) y [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/dn741336.aspx) para determinar el nivel de capa y el rendimiento de servicio adecuado para la Base de datos SQL de Azure.

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
- Azure PowerShell. Puede descargar e instalar los módulos de Azure PowerShell mediante la ejecución del [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).

Los cmdlets para cambiar el nivel de servicio de bases de datos SQL de Azure se encuentran en el módulo del Administrador de recursos de Azure. Al iniciar Azure PowerShell, los cmdlets del módulo de Azure se importan de manera predeterminada. Para cambiar al módulo del Administrador de recursos de Azure, use el cmdlet Switch-AzureMode.

	Switch-AzureMode -Name AzureResourceManager

Si ejecuta **Switch-AzureMode** y ve la advertencia: El *cmdlet Switch-AzureMode está en desuso y se quitará en futuras versiones*, eso está bien; solo tiene que ir al paso siguiente para configurar sus credenciales.

Para obtener información detallada, vea [Uso de Windows PowerShell con el Administrador de recursos](powershell-azure-resource-manager.md).

## Configuración de las credenciales y selección de la suscripción

En primer lugar debe establecer el acceso a su cuenta de Azure; por tanto, inicie PowerShell y luego ejecute el siguiente cmdlet. En la pantalla de inicio de sesión escriba el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

	Add-AzureAccount

Después de iniciar sesión correctamente, verá información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.


### Selección de su suscripción a Azure

Para seleccionar la suscripción, necesitará el nombre o el identificador de suscripción (**-SubscriptionName**). Puede copiar el identificador de suscripción de la información mostrada en el paso anterior o, si dispone de varias suscripciones y necesita más detalles, puede ejecutar el cmdlet **Get-AzureSubscription** y copiar la información de suscripción que desee del conjunto de resultados. Cuando tenga su suscripción, ejecute el siguiente cmdlet:

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureSubscription -SubscriptionId $SubscriptionId

Después de ejecutar correctamente **Select-AzureSubscription** volverá al símbolo del sistema de PowerShell. Si tiene más de una suscripción, puede ejecutar **Get-AzureSubscription** y comprobar que la suscripción que desea usar muestra **IsCurrent: True**.


 


## Cambio del nivel de servicio y del nivel de rendimiento de su base de datos SQL

Ejecute el cmdlet **Set-AzureSqlDatabase** y establezca **-RequestedServiceObjectiveName** en el nivel de rendimiento del nivel de precios que desee; por ejemplo: *S0*, *S1*, *S2*, *S3*, *P1*, *P2*, ...

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## Script de PowerShell de ejemplo para cambiar el nivel de servicio y el nivel de rendimiento de su base de datos SQL

    
	Switch-AzureMode -Name AzureResourceManager
    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## Pasos siguientes

- [Escalar y reducir horizontalmente](sql-database-elastic-scale-get-started.md)
- [Conectarse a Base de datos SQL y consultar dicha base de datos con SSMS](sql-database-connect-query-ssms.md)
- [Exportar una base de datos SQL de Azure](sql-database-export-powershell.md)

## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Sept15_HO3-->