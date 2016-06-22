<properties
	pageTitle="Conexión a Base de datos SQL con una consulta en C# | Microsoft Azure"
	description="Escribir un programa en C# para realizar consultas y conectarse a Base de datos SQL. Información acerca de direcciones IP, cadenas de conexión, inicio de sesión seguro y Visual Studio gratuito."
	services="sql-database"
	keywords="consulta de base de datos en c#, consulta en c#, conectarse a base de datos, SQL C#"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="04/25/2016"
	ms.author="annemill"/>


# Conexión a una base de datos SQL con Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Aprenda a conectarse a una base de datos SQL de Azure con Visual Studio.

## Requisitos previos


Para conectarse a una base de datos SQL con Visual Studio, necesita:


- Una cuenta y una suscripción de Azure. Puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).


- Una base de datos de demostración **AdventureWorksLT** en el servicio Base de datos SQL de Azure.
 - [Creación de la base de datos de demostración](sql-database-get-started.md) en minutos.


- Visual Studio 2013, actualización 4 o posterior. Microsoft ahora ofrece Visual Studio Community *gratis*.
 - [Visual Studio Community, descarga](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Más opciones para Visual Studio gratis](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - O bien, deje que el [paso](#InstallVSForFree) posterior de este tema describa la forma en que el [Portal de Azure](https://portal.azure.com/) le guía en la instalación de Visual Studio.


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## Paso 1: Instalación de Visual Studio Community gratis


Si necesita instalar Visual Studio, puede:

- Para instalar Visual Studio Community gratis, en el explorador, vaya a las páginas web del producto Visual Studio en las que puede descargarlo gratis y otras opciones; o bien
- Deje que el [Portal de Azure](https://portal.azure.com/) le guíe a la página web de descarga, que se describe a continuación.


### Visual Studio a través del Portal de Azure


1. Inicie sesión mediante el [Portal de Azure](https://portal.azure.com/), http://portal.azure.com/.

2. Haga clic en **EXAMINAR* TODO** > **Bases de datos SQL**. Se abre una hoja que busca bases de datos.

3. En el cuadro de texto de filtro que se encuentra cerca de la parte superior, empiece a escribir el nombre de la base de datos **AdventureWorksLT**.

4. Cuando vea la fila de la base de datos en el servidor, haga clic en la fila. Se abre una hoja para la base de datos.

5. Para mayor comodidad, haga clic en el control de minimizar de cada una de las hojas anteriores.

6. Haga clic en el botón **Abrir en Visual Studio**, cerca de la parte superior de la hoja de la base de datos. Se abre una nueva hoja acerca de Visual Studio con vínculos para instalar ubicaciones para Visual Studio.

	![Botón Abrir en Visual Studio][20-OpenInVisualStudioButton]

7. Haga clic en el vínculo **Community (gratis)** o un vínculo similar. Se agrega una página web nueva.

8. Use los vínculos de la página web nueva para instalar Visual Studio.

9. Después de instalar Visual Studio, en la hoja **Abrir en Visual Studio**, haga clic en el botón **Abrir en Visual Studio**. Se abre Visual Studio.

10. Para aprovechar su panel **Explorador de objetos de SQL Server**, Visual Studio le pedirá que rellene los campos de las cadenas de conexión en un cuadro de diálogo.
 - Elija **Autenticación de SQL Server**, no **Autenticación de Windows**.
 - No olvide especificar su base de datos **AdventureWorksLT** (**Opciones** > **Propiedades de conexión** en el cuadro de diálogo).

11. En el **Explorador de objetos de SQL Server**, expanda el nodo de la base de datos.


## Paso 2: Ejecución de consultas de ejemplo

Después de conectarse al servidor lógico, puede conectarse a una base de datos y ejecutar una consulta de ejemplo.

1. En el **Explorador de objetos**, acceda a una base de datos del servidor en la que tenga permiso (como la base de datos de ejemplo **AdventureWorks**).
2. Haga clic con el botón derecho en la base de datos y seleccione **Nueva consulta**.

	![Nueva consulta. Conexión a servidor de Base de datos SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. En la ventana de consulta, copie y pegue el código siguiente.

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Haga clic en el botón **Ejecutar**. En la siguiente captura de pantalla se muestra una consulta correcta.

	![Correcta. Conexión a un servidor de Base de datos SQL: SVisual Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Pasos siguientes

[Conexión a Base de datos SQL mediante .NET (C#)](sql-database-develop-dotnet-simple.md)


<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

<!---HONumber=AcomDC_0615_2016-->