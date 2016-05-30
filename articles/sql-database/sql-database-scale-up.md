<properties
	pageTitle="Cambio del nivel de servicio y del nivel de rendimiento de una base de datos SQL de Azure"
	description="El cambio del nivel de servicio y del nivel de rendimiento de una base de datos SQL de Azure muestra cómo escalar o reducir verticalmente dicha base de datos. Cambio del nivel de precios de una base de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/29/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


Los niveles de servicio y de rendimiento describen las características y los recursos disponibles para Base de datos SQL y pueden actualizarse a medida que cambien las necesidades de la aplicación. Si desea obtener detalles, consulte [Niveles de servicio](sql-database-service-tiers.md).

Tenga en cuenta que, al cambiar el nivel de servicio o de rendimiento de una base de datos, se crea una réplica de la base de datos original en el nuevo nivel de rendimiento y, a continuación, se cambian las conexiones a la réplica. Durante este proceso, no se pierde ningún dato; sin embargo, durante el breve momento en que se cambie a la réplica, las conexiones a la base de datos estarán deshabilitadas, por tanto, es posible que se reviertan algunas transacciones en curso. Este intervalo varía, pero de media dura menos de 4 segundos, y en más del 99 % de los casos es inferior a 30 segundos. Con muy poca frecuencia, especialmente si el número de transacciones en curso es elevado mientras las conexiones están deshabilitadas, este intervalo puede ser superior.

La duración de todo el proceso de escalado vertical depende del nivel de servicio y del tamaño de la base de datos antes y después del cambio. Por ejemplo, el cambio de una base de datos de 250 GB dentro de un nivel de servicio Estándar, o bien desde o hacia este, se completará en 6 horas. Para una base de datos del mismo tamaño que cambie los niveles de rendimiento del nivel de servicio Premium, se completará en unas 3 horas.


Utilice la información de [Actualización de las bases de datos SQL Web o Business a niveles de servicio nuevos](sql-database-upgrade-server-portal.md) y [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure](sql-database-service-tiers.md) para determinar el nivel de capa y el rendimiento de servicio adecuado para la Base de datos SQL de Azure.

- Para degradar una base de datos, esta no debe alcanzar el tamaño máximo permitido del nivel de servicio de destino. 
- Al actualizar una base de datos con la opción [Replicación geográfica](sql-database-geo-replication-overview.md) habilitada, primero es preciso actualizar sus bases de datos secundarias en el nivel de rendimiento deseado antes de actualizar la principal.
- Al degradar un nivel de servicio, primero es preciso finalizar todas las relaciones de Replicación geográfica. 
- Las ofertas del servicio de restauración son diferentes para los distintos niveles de servicio. Si cambia a un nivel inferior, puede perder la capacidad de restaurar a un momento dado o tener un período de retención de copias de seguridad más breve. Para obtener más información, consulte [Copia de seguridad y restauración de Base de datos SQL de Azure](sql-database-business-continuity.md).
- Cambiar el plan de tarifa de la base de datos no cambia el tamaño máximo de la base de datos. Para cambiar el tamaño máximo de la base de datos, use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- Las nuevas propiedades de la base de datos no se aplican hasta que se completan los cambios.



**Para completar este artículo, necesitará lo siguiente:**

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- una base de datos SQL de Azure. Si no tiene una base de datos SQL, cree una siguiendo los pasos de este artículo: [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md).


## Cambio del nivel de servicio y del nivel de rendimiento de su base de datos


Abra la hoja Base de datos SQL correspondiente a la base de datos que desea escalar o reducir verticalmente:

1.	Vaya al [Portal de Azure](https://portal.azure.com).
2.	Haga clic en **EXAMINAR TODO**.
3.	Haga clic en **Bases de datos SQL**.
2.	Haga clic en la base de datos que desee cambiar.
3.	En la hoja de la Base de datos SQL, haga clic en **Toda la configuración** y luego en **Plan de tarifa (escalar DTU)**.

    ![plan de tarifa][1]


1.  Seleccione un nuevo nivel y haga clic en **Seleccionar**:

    Al hacer clic en **Seleccionar** se envía una solicitud de escala para cambiar el nivel de base de datos. Según el tamaño de la base de datos, la operación de escala puede tardar algún tiempo en completarse. Haga clic en la notificación para obtener detalles y el estado de la operación de escala.

    > [AZURE.NOTE] Cambiar el plan de tarifa de la base de datos no cambia el tamaño máximo de la base de datos. Para cambiar el tamaño máximo de la base de datos use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).

    ![seleccione nivel de precios][2]

3.	En la cinta de la izquierda, haga clic en **Notificaciones**:

    ![notificaciones][3]

## Compruebe que la base de datos está en el nivel de precios seleccionado

   Una vez completada la operación de escalado inspeccione y confirme que la base de datos está en el nivel deseado:

2.	Haga clic en **EXAMINAR TODO**.
3.	Haga clic en **Bases de datos SQL**.
2.	Haga clic en la base de datos que cargó.
3.	Compruebe el **Plan de tarifa** y confirme que está establecido en el nivel correcto.

    ![nuevo precio][4]


## Pasos siguientes

- Para cambiar el tamaño máximo de la base de datos, use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- [Escalar y reducir horizontalmente](sql-database-elastic-scale-get-started.md)
- [Conectarse a Base de datos SQL y consultar dicha base de datos con SSMS](sql-database-connect-query-ssms.md)
- [Exportar una base de datos SQL de Azure](sql-database-export.md)

## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/pricing-tile.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png

<!---HONumber=AcomDC_0518_2016-->