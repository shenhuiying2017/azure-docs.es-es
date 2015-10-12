<properties 
	pageTitle="Crear una base de datos en la actualización a base de datos SQL V12" 
	description="Muestra cómo crear una base de datos en la actualización a base de datos de SQL V12 de Azure" 
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
	ms.date="04/28/2015" 
	ms.author="sonalm"/>


# Crear una base de datos SQL V12


<!--
True author is: authors="sonalmm" , ms.author="sonalm".
-->


[Regístrese](https://portal.azure.com) para obtener una base de datos SQL V12 [(en vista previa en algunas regiones)](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable) para aprovechar las ventajas de la siguiente de generación de bases de datos SQL en Microsoft Azure. Para comenzar, necesitará una suscripción a Microsoft Azure. Regístrese para conseguir una [prueba gratuita de Azure](http://azure.microsoft.com/pricing/free-trial) y revise la información sobre los [precios](http://azure.microsoft.com/pricing/details/sql-database).


| Crear base de datos | Captura de pantalla |
| :--- | :--- |
| 1\. Inicie sesión en [http://portal.azure.com/](http://portal.azure.com/). | ![Nuevo portal de Azure][1] |
| 2\. En la parte inferior de la página, en la izquierda, haga clic en **Nuevo**. | ![Inicie el nuevo servicio.][2]|
| 3\. Haga clic en **Base de datos SQL**.| ![Seleccione un servicio de los disponibles.][3] |
| 4\. Se abrirá la hoja **Base de datos SQL**. En el campo **Nombre**, especifique un nombre para la base de datos. | ![Asigne un nombre a la base de datos.][4] |
| 5\. En la **hoja base de datos SQL**, haga clic en **SERVIDOR**. Se abrirá la hoja **Servidor** que ofrece dos opciones: puede crear un nuevo servidor o usar uno existente.| ![Seleccione el tipo de servidor.][4] |
|5a. Si selecciona la opción **Usar servidor existente**, seleccione el servidor que desee y haga clic en **Seleccionar**. A continuación, realice todas las acciones a partir del paso 6 en adelante.| ![Seleccione un servidor de la lista.][5]| 
|5b. Si selecciona **Crear un nuevo servidor**, se abrirá la hoja **Nuevo servidor**. Especifique el nombre del servidor, el inicio de sesión del administrador del servidor y la contraseña. Haga clic en **Ubicación** para seleccionar la ubicación del servidor. | ![Cree completamente las nuevas opciones de servidor.][9]| 
|5C. La hoja **Nuevo servidor** le ofrece la opción de crear el nuevo servidor con las actualizaciones de V12. Para obtener más información acerca de las características de los servidores V12, revise [Novedades de la base de datos SQL V12](sql-database-v12-whats-new.md).| ![Seleccione el servidor V12.][6]|
|5d. Realice las selecciones en la hoja **Nuevo servidor** y haga clic en **Aceptar**. Esto le llevará de nuevo a la hoja **Base de datos SQL** donde podrá completar el resto de acciones necesarias para crear una base de datos. | ![Complete las acciones de la hoja Nuevo servidor.][8]|
|6\. Haga clic en **Seleccionar origen**. Puede seleccionar diferentes tipos de orígenes para crear una base de datos: una base de datos en blanco, una base de datos de ejemplo o una copia de seguridad de una base de datos.| ![Seleccione el origen de la base de datos.][10]|
|7\. A continuación, en la hoja **Base de datos SQL**, haga clic en **NIVEL DE PRECIOS**. Puede seleccionar uno de los niveles de precios recomendados o **Ver todos** los niveles de precios disponibles. Tras realizar la selección, haga clic en **Seleccionar**. <p> Para obtener más información acerca de los niveles de precios, vea [Actualizar las bases de datos SQL web o empresariales a los nuevos niveles de precios](./sql-database-upgrade-new-service-tiers/) y [Niveles de rendimiento y servicios de la Base de datos SQL de Azure](sql-database-service-tiers.md). |![Seleccione un nivel de precios.][7]
| 8\. A continuación, en la hoja **Base de datos SQL**, haga clic en **Configuración opcional**, efectúe las selecciones y haga clic en **Aceptar**. 
| 9\. Cuando selecciona un servidor existente, las opciones **Grupo de recursos** y **Suscripción** aparecerán seleccionadas.. En la hoja **Base de datos SQL**, se mostrará un icono de bloqueo junto a **Grupo de recursos** y **Suscripción**. Si crea un nuevo servidor, podrá seleccionar o crear un grupo de recursos. Para obtener más información, revise [Uso de los grupos de recursos para administrar los recursos de Azure](resource-group-overview.md).|![Especifique el grupo de recursos.][11]
| 10\. Haga clic en **Crear**. Se creará una nueva base de datos con las características de base de datos SQL V12. |![Permite crear una nueva base de datos.][12]

## Vínculos relacionados

- [Novedades de la base de datos de SQL V12](sql-database-v12-whats-new.md)
- [Planeación y preparación para la actualización a la base de datos SQL V12 de Azure](sql-database-v12-plan-prepare-upgrade.md)

<!--Image references-->
[1]: ./media/sql-database-create/firstscreenportal.png
[2]: ./media/sql-database-create/new.png
[3]: ./media/sql-database-create/sqldatabase.png
[4]: ./media/sql-database-create/databasename.png
[5]: ./media/sql-database-create/useexistingserver.PNG
[6]: ./media/sql-database-create/v12server.PNG
[7]: ./media/sql-database-create/pricingtierdetails.png
[8]: ./media/sql-database-create/finishnewserverblade.png
[9]: ./media/sql-database-create/createnewserver.png
[10]: ./media/sql-database-create/selectsource.png
[11]: ./media/sql-database-create/resourcegroup.png
[12]: ./media/sql-database-create/create.png

 

<!---HONumber=Oct15_HO1-->