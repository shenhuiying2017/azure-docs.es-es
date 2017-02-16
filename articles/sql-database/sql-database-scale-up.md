---
title: 'Azure Portal: Cambio del plan de tarifa de Azure SQL Database | Microsoft Docs'
description: "Obtenga información sobre cómo usar Azure Portal para cambiar el nivel de servicio y el nivel de rendimiento de una instancia de Azure SQL Database y escalar o reducir verticalmente los recursos y precios de su instancia de SQL Database."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: cbd67e88-08d5-40e2-a223-0fb0c718a782
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: 88104e0684281f77da497ef4dd608c6a0c64b274


---
# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-using-the-azure-portal"></a>Cambio del nivel de servicio y del nivel de rendimiento (plan de tarifa) de SQL Database con Azure Portal
> [!div class="op_single_selector"]
> * [**Azure Portal**](sql-database-scale-up.md)
> * [PowerShell](sql-database-scale-up-powershell.md)> 
> 

Los niveles de servicio y de rendimiento describen las características y los recursos disponibles para Base de datos SQL y pueden actualizarse a medida que cambien las necesidades de la aplicación. Si desea obtener detalles, consulte [Niveles de servicio](sql-database-service-tiers.md).

Tenga en cuenta que, al cambiar el nivel de servicio o de rendimiento de una base de datos, se crea una réplica de la base de datos original en el nuevo nivel de rendimiento y, a continuación, se cambian las conexiones a la réplica. Durante este proceso, no se pierde ningún dato; sin embargo, durante el breve momento en que se cambie a la réplica, las conexiones a la base de datos estarán deshabilitadas, por tanto, es posible que se reviertan algunas transacciones en curso. Este intervalo varía, pero de media dura menos de 4 segundos, y en más del 99 % de los casos es inferior a 30 segundos. Con muy poca frecuencia, especialmente si el número de transacciones en curso es elevado mientras las conexiones están deshabilitadas, este intervalo puede ser superior.  

La duración del proceso completo de escalado vertical durará, en la mayoría de los casos, menos de 5 minutos. 

Use la información de [Niveles de servicio de Azure SQL Database y niveles de rendimiento](sql-database-service-tiers.md) para determinar el nivel de servicio y el nivel de rendimiento adecuado para Azure SQL Database.

* Para degradar una base de datos, esta no debe alcanzar el tamaño máximo permitido del nivel de servicio de destino. 
* Al actualizar una base de datos con la opción [Replicación geográfica](sql-database-geo-replication-overview.md) habilitada, primero es preciso actualizar sus bases de datos secundarias en el nivel de rendimiento deseado antes de actualizar la principal.
* Al degradar un nivel de servicio, primero es preciso finalizar todas las relaciones de replicación geográfica. 
* Las ofertas del servicio de restauración son diferentes para los distintos niveles de servicio. Si cambia a un nivel inferior, puede perder la capacidad de restaurar a un momento dado o tener un período de retención de copias de seguridad más breve. Para obtener más información, consulte [Copia de seguridad y restauración de Base de datos SQL de Azure](sql-database-business-continuity.md).
* Cambiar el plan de tarifa de la base de datos no cambia el tamaño máximo de la base de datos. Para cambiar el tamaño máximo de la base de datos use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
* Las nuevas propiedades de la base de datos no se aplican hasta que se completan los cambios.

**Para completar este artículo, necesitará lo siguiente:**

* Una base de datos SQL de Azure. Si no tiene una base de datos SQL, cree una siguiendo los pasos de este artículo: [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md).

## <a name="change-the-service-tier-and-performance-level-of-your-database"></a>Cambio del nivel de servicio y del nivel de rendimiento de su base de datos
Abra la hoja Base de datos SQL correspondiente a la base de datos que desea escalar o reducir verticalmente:

1. En [Azure Portal](https://portal.azure.com), haga clic en **Más servicios** > **SQL Database**.
2. Haga clic en la base de datos que desee cambiar.
3. En la hoja **SQL Database**, haga clic en **Plan de tarifa (escalar DTU)**:
   
   ![Plan de tarifa][1]
4. Seleccione un nuevo nivel y haga clic en **Seleccionar**:
   
   Al hacer clic en **Seleccionar** se envía una petición de escala para cambiar el plan de tarifa. Según el tamaño de la base de datos, la operación de escala puede tardar algún tiempo en completarse (vea la información de la parte superior de este artículo).
   
   > [!NOTE]
   > Cambiar el plan de tarifa de la base de datos no cambia el tamaño máximo de la base de datos. Para cambiar el tamaño máximo de la base de datos use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
   > 
   > 
   
   ![seleccione nivel de precios][2]
5. Haga clic en el icono de notificación (campana), en la parte superior derecha:
   
   ![Notificaciones][3]
   
   Haga clic en el texto de notificación para abrir el panel de detalles donde puede ver el estado de la petición.

## <a name="next-steps"></a>Pasos siguientes
* Cambie el tamaño máximo de la base de datos usando [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
* [Escalar y reducir horizontalmente](sql-database-elastic-scale-get-started.md)
* [Conectarse a Base de datos SQL y consultar dicha base de datos con SSMS](sql-database-connect-query-ssms.md)
* [Exportar una base de datos SQL de Azure](sql-database-export.md)

## <a name="additional-resources"></a>Recursos adicionales
* [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
* [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->
[1]: ./media/sql-database-scale-up/new-tier.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png



<!--HONumber=Jan17_HO1-->


