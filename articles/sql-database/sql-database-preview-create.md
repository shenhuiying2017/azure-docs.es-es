<properties 
	pageTitle="Creación de una base de datos en la versión más reciente de SQL Database Update V12 (vista previa)" 
	description="Creación de una base de datos en la versión más reciente de SQL Database Update V12 (vista previa)" 
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


# Creación de una base de datos en la versión más reciente de SQL Database Update V12 (vista previa)

[Regístrese](https://portal.azure.com) para que la versión más reciente de SQL Database Update V12 aproveche las ventajas de la próxima generación de Base de datos SQL en Microsoft Azure. Para empezar, necesita una suscripción a Microsoft Azure. Regístrese para conseguir una [prueba gratuita de Azure](http://azure.microsoft.com/pricing/free-trial) y consultar la [información](http://azure.microsoft.com/pricing/details/sql-database) sobre los precios. 


| Crear base de datos | Captura de pantalla |
| :--- | :--- |
| 1. Inicie sesión en [http://portal.azure.com/](http://portal.azure.com/). | ![New Azure Portal][1] |
| 2. En la parte inferior izquierda de la página, haga clic en **Nuevo**. | ![Initiate New service][2]|
| 3. Haga clic en **Base de datos SQL**.| ![Different services to select from][3] |
| 4. Se abre el cuadro **Base de datos SQL**. En el campo **Nombre**, especifique un nombre de base de datos. | ![Name the database][4] |
| 5. En **Hoja de base de datos SQL**, haga clic en **SERVIDOR**. Se abre un cuadro **Servidor** que proporciona dos opciones: puede crear un nuevo servidor o usar un servidor existente.| ![select type of server][4] |
|5a. Si selecciona la opción **Usar servidor existente**, seleccione el servidor que desee y haga clic en **Seleccionar**. A continuación, realice todas las acciones a partir del paso 6 en adelante.| ![select a server from the list][5]| 
|5b.   Si selecciona **Crear un servidor nuevo**, se abre la hoja **Nuevo servidor**. Especifique el nombre del servidor, el inicio de sesión del administrador del servidor y la contraseña. Haga clic en **Ubicación**   para seleccionar la ubicación del servidor. | ![Complete create new server options][9]| 
|5C. La hoja **Nuevo servidor** le ofrece la opción de crear el nuevo servidor con actualizaciones de V12. Para obtener más información acerca de las características de los servidores V12, revise [Novedades de la base de datos SQL de Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/).| ![Select V12 server][6]|
|5d. Realice las selecciones en la hoja **Nuevo servidor** y haga clic en **Aceptar**. Esto le llevará de nuevo a la hoja **Base de datos SQL** a fin de que lleve a cabo el resto de las acciones para crear una base de datos. | ![Complete New Server blade actions][8]|
|6. Haga clic en **Seleccionar origen**. Puede seleccionar diferentes tipos de orígenes para crear una base de datos: una base de datos en blanco, una base de datos de ejemplo o una copia de seguridad de una base de datos.| ![Select the source for the database][10]|
|7. A continuación, en la hoja **Base de datos SQL**, haga clic en **NIVEL DE PRECIOS**. Puede seleccionar el nivel de precios recomendado o examinar todos los niveles de precios disponibles. Después de realizar la selección, haga clic en **Seleccionar**. <p> Para obtener más información acerca de los niveles de precios, consulte [Actualizar las bases de datos SQL Web o Business a niveles de servicio nuevos](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/) y [Niveles de servicio y de rendimiento de la Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn741336.aspx). |![Select a pricing tier][7]
| 8. A continuación, en la hoja **Base de datos SQL**, haga clic en **Configuración opcional**, efectúe las selecciones y haga clic en **Aceptar**. 
| 9. En la hoja **Base de datos SQL**, haga clic en **Grupo de recursos**. Cree un nuevo grupo de recursos o seleccione un grupo de recursos existente en la lista. Haga clic en **OK**. Para obtener más información, consulte [Uso de los grupos de recursos para administrar los recursos de Azure.](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups)|![Specify Resource group][11]
| 10. En la hoja **Base de datos SQL**, haga clic en **SUSCRIPCIÓN**. Se abre la hoja **Suscripción**. Seleccione una suscripción a Azure que admita la selección de servidor que haya realizado. Tenga en cuenta que, según la selección de suscripción, es posible que cambie la configuración del servidor.| ![Select subscription.][13]
| 11. Haga clic en **Crear**. Se crea una base de datos nueva con las características de SQL Database Update V12. |![Creates a new database][12]

# Vínculos relacionados  #

-  [Novedades de la base de datos SQL de Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [Planeación y preparación para actualizar a SQL Database V12 de Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)

<!--Image references-->
[1]: ./media/sql-database-preview-create/firstscreenportal.png
[2]: ./media/sql-database-preview-create/new.png
[3]: ./media/sql-database-preview-create/sqldatabase.png
[4]: ./media/sql-database-preview-create/databasename.png
[5]: ./media/sql-database-preview-create/useexistingserver.PNG
[6]: ./media/sql-database-preview-create/v12server.PNG
[7]: ./media/sql-database-preview-create/pricingtierdetails.png
[8]: ./media/sql-database-preview-create/finishnewserverblade.png
[9]: ./media/sql-database-preview-create/createnewserver.png
[10]: ./media/sql-database-preview-create/selectsource.png
[11]: ./media/sql-database-preview-create/resourcegroup.png
[12]: ./media/sql-database-preview-create/create.png
[13]: ./media/sql-database-preview-create/subscription.PNG


<!--HONumber=47-->
 