<properties 
	pageTitle="Actualización a Base de datos SQL de Azure V12" 
	description="Explica cómo actualizar a Base de datos SQL de Azure V12 desde una versión anterior de Base de datos SQL de Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="sonalmm" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="05/15/2015" 
	ms.author="sonalm"/>


# Actualización a Base de datos SQL de Azure V12 in situ


[Regístrese](https://portal.azure.com) en Base de datos SQL V12 para aprovechar las ventajas de la próxima generación de Base de datos SQL en Microsoft Azure. En primer lugar, necesita una suscripción a Microsoft Azure. Regístrese para conseguir una [evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial) y revise la información sobre los [precios](http://azure.microsoft.com/pricing/details/sql-database).


## Pasos para realizar la actualización a Base de datos SQL V12


| Paso de actualización | Captura de pantalla |
| :--- | :--- |
| 1. Inicie sesión en [http://portal.azure.com/](http://portal.azure.com/). | ![Nuevo portal de Azure][1] |
| 2. Haga clic en **EXAMINAR**. | ![Examinar servicios][2] |
| 3. Haga clic en **Servidores SQL Server**. Se muestra una lista de nombres de SQL Server. | ![Seleccione un servicio SQL Server][3] |
| 4. Seleccione el servidor que quiere copiar a un servidor nuevo con SQL Database Update habilitado. | ![Muestra una lista de servidores SQL Server][4] |
| 5. Haga clic en **Versión más reciente de SQL Database Update V12**. | ![Característica de vista previa más reciente][5] |
| 6. Haga clic en **ACTUALIZAR ESTE SERVIDOR**. | ![Actualiza el servidor SQL Server a la vista previa][6] |


> [AZURE.NOTE]Una vez que seleccione la opción de actualización, se habilitarán tanto el servidor como las bases de datos del servidor con las características de Base de datos SQL V12 y dicha operación no se podrá revertir. Para actualizar los servidores a Base de datos SQL V12, se requiere un nivel de servicio Basic, Standard o Premium. Para obtener más información sobre los niveles de servicio, consulte [Actualización de las bases de datos SQL Web o Business a niveles de servicio nuevos](sql-database-upgrade-new-service-tiers.md).


> [AZURE.IMPORTANT]La replicación geográfica no es compatible con Base de datos SQL V12 (vista previa). Para obtener más información, consulte [Planeación y preparación para actualizar a Base de datos SQL de Azure V12 (vista previa)](sql-database-v12-plan-prepare-upgrade.md).


Al hacer clic en la opción **ACTUALIZAR ESTE SERVIDOR**, la hoja que se abre muestra un mensaje sobre un proceso de validación.


- El proceso de validación comprueba el nivel de servicio de la base de datos y comprueba si se ha habilitado la replicación geográfica. El cuadro mostrará los resultados una vez completada la validación. 
- Una vez completado el proceso de validación, verá una lista con los nombres de las bases de datos que requieren que se realice alguna acción para que cumplan los requisitos de la actualización a Base de datos SQL V12.
 - **Para poder realizar la actualización a Base de datos SQL V12, es preciso completar las acciones de cada una de dichas bases de datos**.
- Al hacer clic en el nombre de cada base de datos, un nuevo cuadro proporciona una recomendación de nivel precios de servicio según el uso actual. También puede examinar los distintos niveles de precios y seleccionar el que mejor se adapte a su entorno. Todas las bases de datos que están configuradas para replicación geográfica deben configurarse para detener la replicación. 
- Tenga en cuenta que no se mostrará una recomendación en el nivel de precios si no se encuentran suficientes datos. 


| Acción | Captura de pantalla |
| :--- | :--- |
| 7. Cuando haya completado las acciones que preparan al servidor para la actualización, escriba el nombre del servidor que desea actualizar y haga clic en **Aceptar**. | ![Confirmar el nombre del servidor que se va a actualizar][7] |
| 8. Se inicia el proceso de actualización. La actualización puede tardar hasta 24 horas. Durante este tiempo, todas las bases de datos en este servidor permanecerán en línea, pero se limitarán las acciones de administración de base de datos y servidor. Una vez que el proceso se haya completado, el estado **Habilitado** se muestra en la hoja del servidor. | ![Confirma que las características de vista previa están habilitadas][8] |


## cmdlets de PowerShell


Los cmdlets de PowerShell se pueden usar para iniciar, detener o supervisar una actualización a Base de datos SQL de Azure V12 desde V11, o desde cualquier otra versión anterior a la V12.


Para obtener documentación de referencia acerca de estos cmdlets de Powershell, consulte:


- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)


El cmdlet Stop- significa cancelar, no pausar. La única forma de reanudar una actualización es volver a iniciarla desde el principio. El cmdlet Stop- limpia y libera todos los recursos adecuados.


## Vínculos relacionados

-  [Novedades de Base de datos SQL V12](sql-database-v12-whats-new.md)
- [Planeación y preparación para realizar la actualización a Base de datos SQL V12](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-preview-upgrade/firstscreenportal.png
[2]: ./media/sql-database-preview-upgrade/browse.png
[3]: ./media/sql-database-preview-upgrade/sqlserver.png
[4]: ./media/sql-database-preview-upgrade/sqlserverlist.png
[5]: ./media/sql-database-preview-upgrade/latestprview.png
[6]: ./media/sql-database-preview-upgrade/upgrade.png
[7]: ./media/sql-database-preview-upgrade/typeservername.png
[8]: ./media/sql-database-preview-upgrade/enabled.png

<!---HONumber=58-->