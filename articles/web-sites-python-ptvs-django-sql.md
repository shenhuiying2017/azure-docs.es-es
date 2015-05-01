<properties 
	pageTitle="Django y Base de datos SQL en Azure con Python Tools 2.1 para Visual Studio" 
	description="Obtenga información acerca de cómo usar las herramientas de Python para Visual Studio para crear una aplicación de Django que almacene datos en una instancia de base de datos SQL y se pueda implementar en un sitio web." 
	services="app-service\web" 
	tags="python"
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Django y Base de datos SQL en Azure con Python Tools 2.1 para Visual Studio 

En este tutorial, usaremos las [herramientas de Python para Visual Studio][] a fin de crear una aplicación de sondeos sencilla mediante una de las plantillas de ejemplo de PTVS. Este tutorial también se encuentra disponible como [vídeo](https://www.youtube.com/watch?v=ZwcoGcIeHF4)

Vamos a aprender a usar un servicio base de datos SQL hospedado en Azure, configurar la aplicación para usar una base de datos SQL y publicar la aplicación en un sitio web de Azure.

Consulte el [Centro para desarrolladores de Python][] para tener acceso a más artículos que tratan sobre el desarrollo de Sitios web de Azure con PTVS mediante el uso de marcos web Bottle, Flask y Django, con MongoDB, Almacenamiento de tablas de Azure y los servicios de Base de datos MySQL y SQL.  Si bien estos artículos se centran en Sitios web de Azure, los pasos son similares a los que se aplican para desarrollar [Servicios en la nube de Azure][].

## Requisitos previos

 - Visual Studio 2012 o 2013
 - [Python Tools 2.1 para Visual Studio][]
 - [Python Tools 2.1 para archivos VSIX de ejemplo de Visual Studio][]
 - [Herramientas del SDK de Azure para VS 2013][] o [Herramientas del SDK de Azure para VS 2012][]
 - [Python 2.7 de 32 bits][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Creación del proyecto

En esta sección, vamos a crear un proyecto de Visual Studio con la utilización de una plantilla de ejemplo. Vamos a crear un entorno virtual e instalar los paquetes necesarios.  Vamos a crear una base de datos local con sqlite.  A continuación, ejecutaremos la aplicación localmente.

1.  En Visual Studio, seleccione **Archivo** y **Nuevo proyecto**.

1.  Las plantillas de proyecto de los archivos VSIX de ejemplo de PTVS se encuentran disponibles en **Python**, **Samples** (Ejemplos).  Seleccione **Polls Flask Web Project** (Proyecto web de Flask para sondeos) y haga clic en OK (Aceptar) para crear el proyecto.

  	![Cuadro de diálogo Nuevo proyecto](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Se le pedirá que instale paquetes externos.  Seleccione **Instalar en un entorno virtual**.

  	![Cuadro de diálogo Paquetes externos](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Seleccione **Python 2.7** como intérprete de base.

  	![Cuadro de diálogo Agregar entorno virtual](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Haga clic con el botón secundario en el nodo del proyecto y seleccione **Python**, **Django Sync DB** (Base de datos de sincronización de Django).

  	![Comando Base de datos de sincronización de Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  A continuación, se abrirá la consola de administración de Django.  Siga las indicaciones para crear un usuario.

    Se creará una base de datos sqlite en la carpeta del proyecto.

  	![Ventana de la Consola de administración de Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Presione <kbd>F5</kbd> para confirmar que la aplicación funciona.

1.  Haga clic en **Iniciar sesión** en la barra de navegación de la parte superior.

  	![Explorador web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Escriba las credenciales del usuario que ha creado al sincronizar la base de datos.

  	![Explorador web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Haga clic en **Create Sample Polls** (Crear sondeos de ejemplo).

  	![Explorador web](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Haga clic en un sondeo y vote.

  	![Explorador web](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## una Base de datos SQL

Para la base de datos, vamos a crear una base de datos SQL de Azure.

Siga estos pasos para crear una base de datos.

1.  Inicie sesión en el [Portal de administración de Azure][].

1.  En la parte inferior del panel de navegación, haga clic en **NEW**.

  	![Botón Nuevo](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png)

1.  Haga clic en **SERVICIOS DE DATOS**, en **BASE DE DATOS SQL** y, a continuación, en **CREACIÓN RÁPIDA**.

  	![Creación rápida de base de datos SQL](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png)

1.  Elija la opción de crear un servidor nuevo de Base de datos SQL.

1.  Seleccione un grupo de región/afinidad en el que ubicar la base de datos. Si va a usar la base de datos desde la aplicación de Azure, seleccione la misma región en la que implementará la aplicación.

## Configuración del proyecto

En esta sección, configuraremos la aplicación para usar la base de datos SQL 
que acabamos de crear.  Veremos cómo obtener la configuración de conexión desde el 
portal de Azure.  También instalaremos los paquetes de Python adicionales necesarios para usar 
bases de datos SQL con Django.  A continuación, ejecutaremos la aplicación localmente.

1.  En el [Portal de administración de Azure][], haga clic en **BASES DE DATOS SQL** y, a continuación, haga clic en la base de datos que ha creado anteriormente.

1.  Haga clic en **ADMINISTRAR**.

  	![Botón Administrar](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlManage.png)

1.  Se le solicitará que actualice las reglas del firewall. Haga clic en **SÍ**.  De esta forma, se permitirán las conexiones al servidor de la base de datos desde el equipo de desarrollo.

  	![Permitir conexiones](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlUpdateFirewall.png)

1.  Haga clic en **BASES DE DATOS SQL** y, a continuación, en **SERVIDORES**.  Haga clic en el servidor de la base de datos y, a continuación, en **CONFIGURAR**.

1.  En esta página, verá la dirección IP de cada equipo al que se le permite conectarse al servidor de la base de datos.  Debería aparecer la dirección IP del equipo.

    A continuación, en los **servicios permitidos**, asegúrese de que a los servicios de Azure se les permite obtener acceso al servidor.  Si la aplicación se ejecuta en un sitio web Azure (una cuestión que trataremos en la siguiente sección de este tutorial), se le permitirá conectarse a la base de datos.  Haga clic en **GUARDAR** para aplicar el cambio.

  	![Servicios permitidos](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlAllowedServices.png)

1.  En Visual Studio, abra **settings.py** desde la carpeta  *ProjectName*. Edite la definición de  `DATABASES`.

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<User>@<ServerName>',
                'PASSWORD': '<Password>',
                'HOST': '<ServerName>.database.windows.net',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

    `<DatabaseName>`, `<User>` y `<Password>` son los valores que especificó cuando creó la base de datos y el servidor.

    Los valores de `<ServerName>` y `<ServerPort>` los genera Azure cuando se crea el servidor y pueden encontrarse en la sección **Conexión a la base de datos**.

1.  En el Explorador de soluciones, en **Python Environments** (Entornos de Python), haga clic con el botón secundario en el entorno virtual y seleccione **Install Python Package** (Instalar paquete de Python).

1.  Instale el paquete `pyodbc` con **easy_install**.

  	![Cuadro de diálogo Instalar paquete de Python](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Instale el paquete `django-pyodbc-azure` con **pip**.

  	![Cuadro de diálogo Instalar paquete de Python](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Haga clic con el botón secundario en el nodo del proyecto y seleccione **Python**, **Django Sync DB** (Base de datos de sincronización de Django).  

    De esta forma, se crearán las tablas para la base de datos SQL que hemos creado en la sección anterior.  Siga las indicaciones para crear un usuario, que no tiene que coincidir con el usuario de la base de datos sqlite creada en la primera sección.

  	![Ventana de la Consola de administración de Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Presione <kbd>F5</kbd> para ejecutar la aplicación.  Los sondeos creados con **Create Sample Polls** (Crear sondeos de ejemplo) y los datos enviados al votar se serializarán en la base de datos SQL.


## Publicación en un sitio web de Azure

PTVS ofrece una forma fácil de implementar la aplicación web en un sitio web de Azure.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en el nodo del proyecto y seleccione **Publicar**.

  	![Cuadro de diálogo Publicación web](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  Haga clic en **Sitios web de Microsoft Azure**.

1.  Haga clic en **Nuevo** para crear un sitio nuevo.

1.  Seleccione un **Nombre de sitio** y una **Región** y haga clic en **Crear**.

  	![Cuadro de diálogo Crear sitio en Microsoft Azure](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png)

1.  Acepte todos los valores predeterminados y haga clic en **Publicar**.

1.  El explorador web se abrirá automáticamente en el sitio web publicado.  La aplicación ahora debe funcionar según lo previsto, con el uso de la base de datos **SQL** hospedada en Azure.

    ¡Enhorabuena!

  	![Explorador web](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## Pasos siguientes

Siga estos vínculos para obtener más información sobre las herramientas de Python para Visual Studio, Django y Base de datos SQL.

- [Documentación sobre Python Tools para Visual Studio][]
  - [Proyectos web][]
  - [Proyectos de servicio en la nube][]
  - [Depuración remota en Microsoft Azure][]
- [Documentación de Django][]
- [Base de datos SQL][]


<!--Link references-->
[Centro para desarrolladores de Python]: /develop/python/
[Servicios en la nube de Azure]: cloud-services-python-ptvs.md

<!--External Link references-->
[Portal de administración de Azure]: https://manage.windowsazure.com
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 para archivos VSIX de ejemplo de Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Herramientas del SDK de Azure para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Herramientas del SDK de Azure para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Documentación sobre Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation
[Depuración remota en Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Proyectos web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Proyectos de servicio en la nube]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Documentación de Django]: https://www.djangoproject.com/
[Base de datos SQL]: /documentation/services/sql-database/


<!--HONumber=52-->