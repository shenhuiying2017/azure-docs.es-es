<properties linkid="web-sites-python-ptvs-django-mysql" title="Django and MySQL on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Django y MySQL en Azure con Python Tools 2.1 para Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a MySQL database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Django y MySQL en Azure con Python Tools 2.1 para Visual Studio 

En este tutorial, crearemos una sencilla aplicación de sondeos mediante una de las plantillas de ejemplo de PTVS.

Vamos a aprender a usar un servicio MySQL hospedado en Azure, configurar la aplicación para usar MySQL y publicar la aplicación en un sitio web de Azure.

Consulte el [Centro para desarrolladores de Python][] para ver más artículos que tratan el desarrollo de sitios web de Azure con PTVS utilizando marcos web de Bottle, Flask y Django con servicios de MongoDB, Almacenamiento de Tabla de Azure, MySQL y Base de datos SQL.  Aunque este artículo se centra en Sitios web Azure, los pasos son similares al desarrollo de [Servicios en la nube de Azure][].

+ [Requisitos previos](#prerequisites)
+ [Creación del proyecto](#create-the-project)
+ [Creación de una base de datos MySQL](#create-a-mysql-database)
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

  	![New Project Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  Se le pedirá que instale paquetes externos.  Seleccione **Install into a virtual environment**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  Seleccione **Python 2.7** como intérprete de base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  Haga clic con el botón derecho en el nodo del proyecto y seleccione **Python**, **Django Sync DB**.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  Se abrirá una consola de administración de Django.  Siga las indicaciones para crear un usuario.

    Esto creará una base de datos sqlite en la carpeta del proyecto.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Confirme que la aplicación funciona presionando <kbd>F5</kbd>.

1.  Haga clic en **Log in** en la barra de navegación en la parte superior.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Escriba las credenciales del usuario que creó al sincronizar la base de datos.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Haga clic en **Create Sample Polls**.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  Haga clic en un sondeo y vote.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-mysql-database"></a>Creación de una base de datos MySQL

Para la base de datos, vamos a crear una base de datos hospedada MySQL de ClearDB en Azure.

Como alternativa, puede crear su propia máquina virtual que se ejecuta en Azure y, a continuación, instalar y administrar MySQL usted mismo.

Puede crear una base de datos con un plan gratuito siguiendo estos pasos.

1.  Inicie sesión en el [Portal de administración de Azure][].

1.  En la parte inferior del panel de navegación, haga clic en **NUEVO**.

  	![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  Haga clic en **TIENDA** y luego en **ClearDB MySQL Database**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  En Nombre, escriba un nombre que se va a utilizar para el servicio de base de datos.

1.  Seleccione un valor en Región/grupo de afinidad en el que se ubicará el servicio de base de datos. Si va a usar la base de datos desde la aplicación de Azure, seleccione la misma región en la que implementará la aplicación.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  Haga clic en **COMPRAR**.

##<a name="configure-the-project"></a>Configuración del proyecto

En esta sección, configuraremos nuestra aplicación para que utilice la base de datos de MySQL que acabamos de crear.  Veremos cómo obtener la configuración de conexión desde el portal de Azure.  Instalaremos también los paquetes de Python adicionales necesarios para utilizar las bases de datos de MySQL con Django.  Entonces podremos ejecutar la aplicación localmente.

1.  En el [Portal de administración de Azure][], haga clic en **COMPLEMENTOS** y, a continuación, haga clic en el servicio de base de datos MySQL de ClearDB que creó anteriormente.

1.  Haga clic en **INFORMACIÓN DE CONEXIÓN**.  Puede utilizar el botón Copiar para colocar el valor de **CONNECTIONSTRING** en el portapapeles.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  En Visual Studio, abra **settings.py** desde la carpeta *ProjectName*.  Pegue la cadena de conexión temporalmente en el editor.  La cadena de conexión está en este formato:

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Cambie el valor de **ENGINE** predeterminado para utilizar MySQL, y configure los valores de **NAME**, **USER**, **PASSWORD** y **HOST** desde **CONNECTIONSTRING**.

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1.  En el Explorador de soluciones, en **Python Environments**, haga doble clic en el entorno virtual y seleccione **Install Python Package**.

1. Instale el paquete `mysql-python` mediante **easy_install**.

  	![Install Package Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  Haga clic con el botón derecho en el nodo del proyecto y seleccione **Python**, **Django Sync DB**.  

Esto creará las tablas de la base de datos de MySQL que creamos en la sección anterior.  Siga las instrucciones para crear un usuario, que no tiene que coincidir con el usuario de la base de datos de sqlite que se creó en la primera sección.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Ejecute la aplicación con <kbd>F5</kbd>.  Los sondeos que se crean con **Create Sample Polls** y los datos enviados con la votación se serializarán en la base de datos de MySQL.

##<a name="publish-to-an-azure-website"></a>Publicación en un sitio web de Azure

PTVS proporciona una manera fácil de implementar la aplicación web en un sitio web de Azure.

1.  En el **Explorador de soluciones**, haga clic con el botón derecho en el nodo del proyecto y seleccione **Publicar**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  Haga clic en **Sitios web Azure de Microsoft**.

1.  Haga clic en **Nuevo** para crear un sitio nuevo.

1.  Seleccione un **Nombre de sitio** y una **Región** y haga clic en **Crear**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  Acepte todos los demás valores predeterminados y haga clic en **Publicar**.

1.  El explorador web se abrirá automáticamente en el sitio publicado.  Debería ver la aplicación en funcionamiento del modo esperado, utilizando la base de datos **MySQL** hospedada en Azure.

    ¡Enhorabuena!

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Pasos siguientes

Siga estos vínculos para obtener más información acerca de Python Tools para Visual Studio, Django y MySQL.

- [Documentación de Python Tools para Visual Studio][]
  - [Proyectos web][]
  - [Proyectos de servicio en la nube][]
  - [Depuración remota en Microsoft Azure][]
- [Documentación de Django][]
- [MySQL][]


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
[MySQL]: http://www.mysql.com/
