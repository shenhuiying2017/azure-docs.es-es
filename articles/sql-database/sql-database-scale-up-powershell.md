---
title: Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL con PowerShell | Microsoft Docs
description: "El cambio del nivel de servicio y del nivel de rendimiento de una base de datos SQL de Azure muestra cómo escalar o reducir verticalmente dicha base de datos mediante PowerShell. Cambio del nivel de precios de una base de datos SQL de Azure mediante PowerShell."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 6f016c17-b048-4968-b82b-d2dcec954e54
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aea3bcbd6ac73a05b00b7b79b2dc47bf06d67f6f


---
# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL con PowerShell
> [!div class="op_single_selector"]
> * [Portal de Azure](sql-database-scale-up.md)
> * [**PowerShell**](sql-database-scale-up-powershell.md)
> 
> 

Los niveles de servicio y de rendimiento describen las características y los recursos disponibles para Base de datos SQL y pueden actualizarse a medida que cambien las necesidades de la aplicación. Si desea obtener detalles, consulte [Niveles de servicio](sql-database-service-tiers.md).

Tenga en cuenta que, al cambiar el nivel de servicio o de rendimiento de una base de datos, se crea una réplica de la base de datos original en el nuevo nivel de rendimiento y, a continuación, se cambian las conexiones a la réplica. Durante este proceso, no se pierde ningún dato; sin embargo, durante el breve momento en que se cambie a la réplica, las conexiones a la base de datos estarán deshabilitadas, por tanto, es posible que se reviertan algunas transacciones en curso. Este intervalo varía, pero de media dura menos de 4 segundos, y en más del 99 % de los casos es inferior a 30 segundos. Con muy poca frecuencia, especialmente si el número de transacciones en curso es elevado mientras las conexiones están deshabilitadas, este intervalo puede ser superior.  

La duración de todo el proceso de escalado vertical depende del nivel de servicio y del tamaño de la base de datos antes y después del cambio. Por ejemplo, el cambio de una base de datos de 250 GB dentro de un nivel de servicio Estándar, o bien desde o hacia este, se completará en 6 horas. Para una base de datos del mismo tamaño que cambie los niveles de rendimiento del nivel de servicio Premium, se completará en unas 3 horas.

* Para degradar una base de datos, esta no debe alcanzar el tamaño máximo permitido del nivel de servicio de destino. 
* Al actualizar una base de datos con la opción [Replicación geográfica](sql-database-geo-replication-portal.md) habilitada, primero es preciso actualizar sus bases de datos secundarias en el nivel de rendimiento deseado antes de actualizar la principal.
* Al realizar una degradación desde un nivel de servicio Premium, primero es preciso finalizar todas las relaciones de Replicación geográfica. Puede seguir los pasos que se describen en el tema [Recuperación de una base de datos SQL de Azure tras una interrupción](sql-database-disaster-recovery.md) para detener el proceso de replicación entre la base de datos principal y las bases de datos secundarias activas.
* Las ofertas del servicio de restauración son diferentes para los distintos niveles de servicio. Si cambia a un nivel inferior, puede perder la capacidad de restaurar a un momento dado o tener un período de retención de copias de seguridad más breve. Para obtener más información, consulte [Copia de seguridad y restauración de Base de datos SQL de Azure](sql-database-business-continuity.md).
* Las nuevas propiedades de la base de datos no se aplican hasta que se completan los cambios.

**Para completar este artículo, necesitará lo siguiente:**

* Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **CUENTA GRATUITA** de la parte superior de la página y, después, vuelva para finalizar este artículo.
* Una base de datos SQL de Azure. Si no tiene una base de datos SQL, cree una siguiendo los pasos de este artículo: [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md).
* Azure PowerShell.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>Cambio del nivel de servicio y del nivel de rendimiento de su base de datos SQL
Ejecute el cmdlet [Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) y establezca el elemento **-RequestedServiceObjectiveName** en el nivel de rendimiento del plan de tarifa que quiera; por ejemplo: *S0*, *S1*, *S2*, *S3*, *P1*, *P2*...

```
$ResourceGroupName = "resourceGroupName"

$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```






## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>Script de PowerShell de ejemplo para cambiar el nivel de servicio y el nivel de rendimiento de su base de datos SQL
Reemplace ```{variables}``` con sus valores (no incluya las llaves de cierre).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```



## <a name="next-steps"></a>Pasos siguientes
* [Escalar y reducir horizontalmente](sql-database-elastic-scale-get-started.md)
* [Conectarse a Base de datos SQL y consultar dicha base de datos con SSMS](sql-database-connect-query-ssms.md)
* [Exportar una base de datos SQL de Azure](sql-database-export-powershell.md)

## <a name="additional-resources"></a>Recursos adicionales
* [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
* [Documentación de Base de datos SQL](http://azure.microsoft.com/documentation/services/sql-database/)
* [Cmdlets de Azure SQL Database](http://msdn.microsoft.com/library/azure/mt574084https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx.aspx)




<!--HONumber=Nov16_HO3-->


