<properties 
	pageTitle="Actualización a la versión más reciente de SQL Database Update V12 (vista previa)" 
	description="Actualización a la versión más reciente de SQL Database Update V12 (vista previa)" 
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
	ms.date="12/11/2014" 
	ms.author="sonalm"/>



# Actualización a la versión más reciente de SQL Database Update V12 (vista previa)


[Regístrese](https://portal.azure.com) para que la versión más reciente de SQL Database Update V12 aproveche las ventajas de la próxima generación de la  Base de datos SQL en Microsoft Azure. En primer lugar, necesita una suscripción a Microsoft Azure. Regístrese para conseguir una [prueba gratuita de Azure](http://azure.microsoft.com/pricing/free-trial) y consultar la [información](http://azure.microsoft.com/pricing/details/sql-database) sobre los precios. 

## Actualización de un servidor in situ con la versión más reciente de SQL Database Update ##

| Actualizar  | Captura de pantalla |
| :--- | :--- |
| 1. Inicie sesión en [http://portal.azure.com/](http://portal.azure.com/). | ![New Azure Portal][1] |
| 2. Haga clic en **EXAMINAR**. | ![Browse Services][2] |
| 3.	Haga clic en **Servidores SQL Server**. Se muestra una lista de nombres de SQL Server. | ![Select SQL Server service][3] |
| 4. Seleccione el servidor que quiere copiar a un servidor nuevo con SQL Database Update habilitado. | ![Shows a list of SQL Servers][4] |
| 5. Haga clic en **SQL Database Update V12 más reciente**. | ![Latest preview feature][5] |
| 6. Haga clic en **ACTUALIZAR ESTE SERVIDOR**. | ![Upgrades the SQL Server to the preview][6] |

> [AZURE.NOTE] **IMPORTANTE**: Una vez que seleccione la opción de actualización, el servidor y las bases de datos en ese servidor se habilitarán con características de SQL Database Update V12 y no podrá revertirlo. Para actualizar los servidores a SQL Database Update V12, se requiere un nivel de servicio Basic, Standard o Premium. Para obtener más información sobre los niveles de servicio, consulte [Actualizar las bases de datos SQL Web o Business a niveles de servicio nuevos"](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/).

> **IMPORTANTE**: No se admite replicación geográfica con SQL Database Update V12 (vista previa). Para obtener más información, consulte [Planeación y preparación para actualizar a Base de datos SQL de Azure V12 (vista previa)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade).


Cuando se hace clic en la opción **ACTUALIZAR ESTE SERVIDOR**, el cuadro que se abre muestra un mensaje sobre un proceso de validación. 

- El proceso de validación comprueba el nivel de servicio de la base de datos y comprueba si se ha habilitado la replicación geográfica. El cuadro mostrará los resultados una vez completada la validación. 
- Una vez completado el proceso de validación, verá una lista de nombres de bases de datos que requieren que tome medidas para cumplir los requisitos de la actualización a SQL Database Update V12. **Deberá realizar las acciones para cada una de esas bases de datos para poder actualizar a SQL Database Update V12**.
- Al hacer clic en el nombre de cada base de datos, un nuevo cuadro proporciona una recomendación de nivel precios de servicio según el uso actual. También puede examinar los distintos niveles de precios y seleccionar el que mejor se adapte a su entorno. Todas las bases de datos que están configuradas para replicación geográfica deben configurarse para detener la replicación. 
- Tenga en cuenta que no se mostrará una recomendación en el nivel de precios si no se encuentran suficientes datos. 

| Acción | Captura de pantalla |
| :--- | :--- |
| 7. Cuando haya completado las acciones que preparan al servidor para la actualización, escriba el nombre del servidor a actualizar y haga clic en **Aceptar**. | ![Confirm the server name to upgrade][7] |
| 8. Se inicia el proceso de actualización. La actualización puede tardar hasta 24 horas. Durante este tiempo, todas las bases de datos en este servidor permanecerán en línea, pero se limitarán las acciones de administración de base de datos y servidor. Una vez que el proceso se haya completado, el estado **Habilitado** se muestra en el cuadro del servidor. | ![Confirms preview features are enabled][8] |
 

# Vínculos relacionados  #

-  [Novedades de SQL Database Update V12 (vista previa)](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [Planeación y preparación de la actualización a la versión más reciente de SQL Database Update V12 (vista previa)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)


<!--Image references-->
[1]: ./media/sql-database-preview-upgrade/firstscreenportal.png
[2]: ./media/sql-database-preview-upgrade/browse.png
[3]: ./media/sql-database-preview-upgrade/sqlserver.png
[4]: ./media/sql-database-preview-upgrade/sqlserverlist.png
[5]: ./media/sql-database-preview-upgrade/latestprview.png
[6]: ./media/sql-database-preview-upgrade/upgrade.png
[7]: ./media/sql-database-preview-upgrade/typeservername.png
[8]: ./media/sql-database-preview-upgrade/enabled.png
<!--HONumber=47-->
