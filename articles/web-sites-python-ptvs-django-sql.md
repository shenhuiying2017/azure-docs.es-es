<properties linkid="web-sites-python-ptvs-django-sql" title="Django and SQL Database on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Django y Base de datos SQL en Azure con Python Tools 2.1 para Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a SQL database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Django y Base de datos SQL en Azure con Python Tools 2.1 para Visual Studio 

En este tutorial, crearemos una sencilla aplicación de sondeos mediante una de las plantillas de ejemplo de PTVS.

Vamos a aprender a usar un servicio base de datos SQL hospedado en Azure, configurar la aplicación para usar una base de datos SQL y publicar la aplicación en un sitio web de Azure.

Consulte el [Centro para desarrolladores de Python][] para ver más artículos que tratan el desarrollo de sitios web de Azure con PTVS utilizando marcos web de Bottle, Flask y Django con servicios de MongoDB, Almacenamiento de Tabla de Azure, MySQL y Base de datos SQL.  Aunque este artículo se centra en Sitios web Azure, los pasos son similares al desarrollo de [Servicios en la nube de Azure][].

+ [Requisitos previos](#prerequisites)
+ [Creación del proyecto](#create-the-project)
+ [una Base de datos SQL](#create-a-sql-database)
+ [Configuración del proyecto](#configure-the-project)
+ [Publicación en un sitio web de Azure](#publish-to-an-azure-website)
+ [Pasos siguientes](#next-steps)

##<a name="prerequisites"></a>Requisitos previos

 - Visual Studio 2012 o 2013
 - [Python Tools 2,1 para Visual Studio (en inglés)][]
 - [VSIX de ejemplo de Python Tools 2.1 para Visual Studio][]
 - [Herramientas del SDK de Azure para VS 2013][] o [Herramientas del SDK de Azure para VS 2012][]
 - [Python 2.7 32 bits][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Creación del proyecto

En esta sección, crearemos un proyecto de Visual Studio mediante una plantilla de ejemplo. Crearemos un entorno virtual e instalaremos los paquetes necesarios.  Vamos a crear una base de datos local con sqlite.  Entonces podremos ejecutar la aplicación localmente.

1.  En Visual Studio, seleccione **Archivo**, **Nuevo proyecto**.

1.  Las plantillas de proyecto de los VSIX de ejemplo de PTVS están disponibles en **Python**, **Samples**.  Seleccione **Polls Django Web Project** y haga clic en OK para crear el proyecto.

  	![New Project Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Se le pedirá que instale paquetes externos.  Seleccione **Install into a virtual environment**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Seleccione **Python 2.7** como intérprete de base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Haga clic con el botón derecho en el nodo del proyecto y seleccione **Python**, **Django Sync DB**.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Se abrirá una consola de administración de Django.  Siga las indicaciones para crear un usuario.

    Esto creará una base de datos sqlite en la carpeta del proyecto.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Confirme que la aplicación funciona presionando <kbd>F5</kbd>.

1.  Haga clic en **Log in** en la barra de navegación en la parte superior.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Escriba las credenciales del usuario que creó al sincronizar la base de datos.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Haga clic en **Create Sample Polls**.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Haga clic en un sondeo y vote.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-sql-database"></a>una Base de datos SQL

En cuanto a la base de datos, vamos a crear una base de datos SQL de Azure.

Siga estos pasos para crear una base de datos.

1.  Inicie sesión en el [Portal de administración de Azure][].

1.  En la parte inferior del panel de navegación, haga clic en **NUEVO**.

  	![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png)

1.  Haga clic en **SERVICIOS DE DATOS**, en **BASE DE DATOS SQL** y, a continuación, en **CREACIÓN RÁPIDA**.

  	![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png)

1.  Elija crear un Nuevo servidor de bases de datos SQL.

1.  Seleccione un valor de Región/grupo de afinidad en el que ubicar la base de datos. Si va a usar la base de datos desde la aplicación de Azure, seleccione la misma región en la que implementará la aplicación.

##<a name="configure-the-project"></a>Configuración del proyecto

En esta sección, configuraremos nuestra aplicación para que utilice la base de datos SQL que acabamos de crear.  Veremos cómo obtener la configuración de conexión desde el portal de Azure.  Instalaremos también los paquetes de Python adicionales necesarios para utilizar las bases de datos SQL con Django.  Entonces podremos ejecutar la aplicación localmente.

1.  En el [Portal de administración de Azure][], haga clic en **BASES DE DATOS SQL** y, a continuación, haga clic en la base de datos que creó anteriormente.

1.  Haga clic en **ADMINISTRAR**.

  	![Manage Button](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlManage.png)

1.  Se le pedirá que actualice las reglas de firewall. Haga clic en **SÍ**.  Esto permitirá las conexiones al servidor de base de datos desde el equipo de desarrollo.

  	![Allow Connections](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlUpdateFirewall.png)

1.  Haga clic en **BASES DE DATOS SQL** y, a continuación, en **SERVIDORES**.  Haga clic en el servidor de la base de datos, a continuación, en **CONFIGURAR**..

1.  En esta página, verá la dirección IP de cada equipo que tiene permiso para conectarse al servidor de base de datos.  Debería ver la dirección IP de su equipo.

A continuación, en **servicios permitidos**, asegúrese de que los servicios de Azure tienen acceso permitido al servidor.  Cuando la aplicación se ejecuta en un sitio web de Azure (lo que haremos en la sección siguiente de este tutorial), podrá conectarse a la base de datos.  Haga clic en **GUARDAR** para aplicar el cambio.

  	![Allowed Services](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlAllowedServices.png)

1.  En Visual Studio, abra **settings.py** desde la carpeta *ProjectName*. Edite la definición de `DATABASES`.

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

    `<DatabaseName>`, `<User>` y `<Password>` son los valores especificados al crear la base de datos y el servidor.

    Los valores de `<ServerName>` y `<ServerPort>` se generan por Azure cuando se crea el servidor, y se pueden encontrar en la sección **Conecte su base de datos**.

1.  En el Explorador de soluciones, en **Python Environments**, haga doble clic en el entorno virtual y seleccione **Install Python Package**.

1.  Instale el paquete `pyodbc` con **easy_install**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Instale el paquete `django-pyodbc-azure` con **pip**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Haga clic con el botón derecho en el nodo del proyecto y seleccione **Python**, **Django Sync DB**.  

Esto creará las tablas de la base de datos SQL que creamos en la sección anterior.  Siga las instrucciones para crear un usuario, que no tiene que coincidir con el usuario de la base de datos de sqlite que se creó en la primera sección.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Ejecute la aplicación con <kbd>F5</kbd>.  Los sondeos que se crean con **Create Sample Polls** y los datos enviados con la votación se serializarán en la base de datos SQL.


##<a name="publish-to-an-azure-website"></a>Publicación en un sitio web de Azure

PTVS proporciona una manera fácil de implementar la aplicación web en un sitio web de Azure.

1.  En el **Explorador de soluciones**, haga clic con el botón derecho en el nodo del proyecto y seleccione **Publicar**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  Haga clic en **Sitios web Azure de Microsoft**.

1.  Haga clic en **Nuevo** para crear un sitio nuevo.

1.  Seleccione un **Nombre de sitio** y una **Región** y haga clic en **Crear**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png)

1.  Acepte todos los demás valores predeterminados y haga clic en **Publicar**.

1.  El explorador web se abrirá automáticamente en el sitio publicado.  Debería ver la aplicación en funcionamiento del modo esperado, utilizando la base de datos **SQL** hospedada en Azure.

    ¡Enhorabuena!

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Pasos siguientes

Siga estos vínculos para obtener más información acerca de Python Tools para Visual Studio, Django y Base de datos SQL.

- [Documentación de Python Tools para Visual Studio][]
  - [Proyectos web][]
  - [Proyectos de servicio en la nube][]
  - [Depuración remota en Microsoft Azure][]
- [Documentación de Django][]
- [Base de datos SQL de Azure][]


<!--Link references-->
[Centro para desarrolladores de Python]: /es-es/develop/python/
[Servicios en la nube de Azure]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Portal de administración de Azure]: https://manage.windowsazure.com
[Python Tools 2,1 para Visual Studio (en inglés)]: http://go.microsoft.com/fwlink/?LinkId=517189
[VSIX de ejemplo de Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK y herramientas para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK y herramientas para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Documentación de Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation
[Depuración remota en Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Proyectos web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Proyectos de servicio en la nube]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Documentación de Django]: https://www.djangoproject.com/
[Base de datos SQL de Azure]: /es-es/documentation/services/sql-database/
