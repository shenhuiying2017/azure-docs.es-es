<properties
	pageTitle="Introducción a la Base de datos SQL | Microsoft Azure"
	description="Cree la primera base de datos en la nube en cuestión de minutos con Base de datos SQL de Azure, el servicio de administración de bases de datos relacionales de Microsoft (RDBMS) en la nube mediante el portal de Azure y la base de datos de ejemplo AdventureWorks."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/15/2015"
	ms.author="genemi"/>


# Creación de la primera Base de datos SQL de Azure


En este artículo se muestra cómo crear una Base de datos de SQL de Azure de ejemplo en menos de cinco minutos. Obtenga información sobre cómo:


- Aprovisionar un servidor lógico mediante el [Portal de Azure](http://portal.azure.com/).
- Crear una base de datos que se rellena con datos de ejemplo.
- Establecer una regla de firewall para que la base de datos configure las direcciones IP que puede acceder a la base de datos.


En este tutorial se supone que tiene una suscripción a Azure. Si no tiene una, puede registrarse para obtener una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/).


## Paso 1: Inicio de sesión


1. Inicie sesión en el [Portal de Azure](http://portal.azure.com/).
2. Haga clic en **Nuevo** > **Datos + almacenamiento** > **Base de datos SQL**.


![New SQL Database][1]


## Paso 2: Creación del servidor lógico



1. En la hoja **Base de datos SQL**, elija un **nombre** para la base de datos, en este ejemplo **AdventureWorks**.
2. Para crear el servidor lógico de la base de datos, haga clic en **Servidor** y luego en **Crear un nuevo servidor**.


## Paso 3: Configuración del servidor


1. En la hoja **Servidor**, escriba el **nombre del servidor** como un nombre único que sea fácil de recordar.
2. Escriba el **inicio de sesión de administrador de servidor** como **AdventureAdmin**.
3. Escriba el valor correcto en **Contraseña** y **Confirmar contraseña**.
4. Seleccione la **ubicación** geográfica preferida. Normalmente, la ubicación debe estar cerca de donde se ejecuta la aplicación.
5. Haga clic en **Aceptar**.


![Crear un servidor][2]


## Paso 4: Creación de la base de datos


1. En la hoja **Base de datos SQL**, especifique el origen de la base de datos haciendo clic en **Seleccionar origen**.
 - Si omite este paso, se crea una base de datos vacía.
2. Seleccione **Ejemplo**.
 - Se crea una base de datos que es copia de la base de datos de ejemplo estándar denominada **AdventureWorks**.
 - En Base de datos SQL de Azure se utiliza la edición de *esquema ligero* de AdventureWorks.
3. Haga clic en **Crear** en la parte inferior de la hoja.


## Paso 5: Configuración del firewall


En los pasos siguientes se muestra cómo especificar qué intervalos de direcciones IP pueden acceder a la base de datos.


![Examinar servidor][3]


1. En la cinta de opciones en el lado izquierdo de la pantalla, haga clic en **Examinar** y, a continuación, en **Servidores SQL Server**.
2. Entre las opciones disponibles, haga clic en el servidor SQL Server que creó anteriormente.
3. Haga clic en **configuración** y luego en **Firewall**.
4. Obtenga la dirección IP actual de [Bing](http://www.bing.com/search?q=my%20ip%20address).
5. En la configuración del firewall, especifique un **nombre de regla** y pegue la dirección IP pública del paso anterior en los campos **IP inicial** e **IP final**.
6. Cuando haya finalizado, haga clic en **Guardar** en la parte superior de la página.


![Firewall][4]


## Pasos siguientes


Ahora está preparado para escribir un pequeño programa cliente que puede conectarse a la base de datos. Para un ejemplo de código de inicio rápido, haga clic en uno de los vínculos siguientes:


- [Conexión y consultas a Base de datos SQL con C#](sql-database-connect-query.md)
- *Próximamente:* Ejemplos de código de desarrollo de cliente y de inicio rápido para Base de datos SQL


<!-- Media references. -->
[1]: ./media/sql-database-get-started/GettingStarted_NewDB.PNG
[2]: ./media/sql-database-get-started/GettingStarted_CreateServer.png
[3]: ./media/sql-database-get-started/GettingStarted_BrowseServer.png
[4]: ./media/sql-database-get-started/GettingStarted_FireWall.png

<!---HONumber=August15_HO8-->