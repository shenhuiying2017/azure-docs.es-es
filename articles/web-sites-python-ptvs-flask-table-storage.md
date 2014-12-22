<properties linkid="web-sites-python-ptvs-flask-table-storage" title="Flask and Azure Table Storage on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Flask y Almacenamiento de Tabla de Azure en Azure con Python Tools 2.1 para Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Flask application that stores data in Azure Table Storage and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Flask y Almacenamiento de Tabla de Azure en Azure con Python Tools 2.1 para Visual Studio 

En este tutorial, crearemos una sencilla aplicación de sondeos mediante una de las plantillas de ejemplo de PTVS.

La aplicación de sondeos define una abstracción para su repositorio, por lo que puede alternar fácilmente entre los diferentes tipos de repositorios (en memoria, almacenamiento de Tabla de Azure, MongoDB).

Vamos a aprender a crear una cuenta de Almacenamiento de Azure, configurar la aplicación para utilizar el Almacenamiento de Tabla de Azure y publicar la aplicación en un sitio web de Azure.

Consulte el [Centro para desarrolladores de Python][] para ver más artículos que tratan el desarrollo de sitios web de Azure con PTVS utilizando marcos web de Bottle, Flask y Django con servicios de MongoDB, Almacenamiento de Tabla de Azure, MySQL y Base de datos SQL.  Aunque este artículo se centra en Sitios web Azure, los pasos son similares al desarrollo de [Servicios en la nube de Azure][].

+ [Requisitos previos](#prerequisites)
+ [Creación del proyecto](#create-the-project)
+ [Creación de una cuenta de almacenamiento de Azure](#create-an-azure-storage-account)
+ [Configuración del proyecto](#configure-the-project)
+ [Exploración del Almacenamiento de Tabla de Azure](#explore-the-azure-table-storage)
+ [Publicación en un sitio web de Azure](#publish-to-an-azure-website)
+ [Configuración del sitio web de Azure](#configure-the-azure-website)
+ [Pasos siguientes](#next-steps)

##<a name="prerequisites"></a>Requisitos previos

 - Visual Studio 2012 o 2013
 - [Python Tools 2,1 para Visual Studio (en inglés)][]
 - [VSIX de ejemplo de Python Tools 2.1 para Visual Studio][]
 - [Herramientas del SDK de Azure para VS 2013][] o [Herramientas del SDK de Azure para VS 2012][]
 - [Python 2.7 de 32 bits][] o [Python 3.4 de 32 bits][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Creación del proyecto

En esta sección, crearemos un proyecto de Visual Studio mediante una plantilla de ejemplo.  Crearemos un entorno virtual e instalaremos los paquetes necesarios.  A continuación, ejecutaremos la aplicación localmente mediante el repositorio en memoria predeterminado.

1.  En Visual Studio, seleccione **Archivo**, **Nuevo proyecto**.

1.  Las plantillas de proyecto de los VSIX de ejemplo de PTVS están disponibles en **Python**, **Samples**.  Seleccione **Polls Flask Web Project** y haga clic en OK para crear el proyecto.

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)

1.  Se le pedirá que instale paquetes externos.  Seleccione **Install into a virtual environment**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)

1.  Seleccione **Python 2.7** o **Python 3.4** como intérprete de base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)

1.  Confirme que la aplicación funciona presionando <kbd>F5</kbd>.  De forma predeterminada, la aplicación utiliza un repositorio en memoria que no requiere ninguna configuración.  Todos los datos se pierden cuando se detiene el servidor web.

1.  Haga clic en **Create Sample Polls** y, a continuación, haga clic en un sondeo y vote.

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

##<a name="create-an-azure-storage-account"></a>Creación de una cuenta de almacenamiento de Azure

Necesita una cuenta de almacenamiento de Azure para usar operaciones de almacenamiento. Siga estos pasos para crear una cuenta de almacenamiento.

1.  Inicie sesión en el [Portal de administración de Azure][].

1.  En la parte inferior del panel de navegación, haga clic en **NUEVO**.

  	![New Button](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzurePlusNew.png)

1.  Haga clic en **SERVICIOS DE DATOS**, en **ALMACENAMIENTO** y, a continuación, en **CREACIÓN RÁPIDA**.

  	![Quick Create](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)

1.  En URL, escriba el nombre de subdominio que vaya usar en el URI para la cuenta de almacenamiento.  Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este valor se convierte en el nombre del host dentro del URI que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción.

1.  Seleccione un valor de Región/grupo de afinidad en el que ubicar el almacenamiento. Si va a usar el almacenamiento desde la aplicación de Azure, seleccione la misma región en la que implementará la aplicación.

1.  También puede habilitar la replicación geográfica.  Con la replicación geográfica, Almacenamiento de Azure ahora mantiene los datos en dos ubicaciones. Almacenamiento de Azure mantiene constantemente réplicas en buen estado de los datos en ambas ubicaciones.

1.  Haga clic en **CREAR CUENTA DE ALMACENAMIENTO**.

##<a name="configure-the-project"></a>Configuración del proyecto

En esta sección, configuraremos nuestra aplicación para que utilice la cuenta de almacenamiento que acabamos de crear.  Veremos cómo obtener la configuración de conexión desde el portal de Azure.  Entonces podremos ejecutar la aplicación localmente.

1.  En el [Portal de administración de Azure][], haga clic en la cuenta de almacenamiento creada en la sección anterior.

1.  Haga clic en **ADMINISTRAR CLAVES DE ACCESO**.

  	![Manage Access Keys Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  En Visual Studio, haga clic con el botón derecho en el nombre del proyecto en el Explorador de soluciones y seleccione **Propiedades**.  Haga clic en la pestaña **Depurar**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)

1.  Establezca los valores de las variables de entorno requeridas por la aplicación en **Comando depurar servidor**, **Entorno**.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<nombre de la cuenta de almacenamiento>
        STORAGE_KEY=<clave de acceso principal>

Esto le permitirá establecer las variables de entorno al **Iniciar la depuración**.  Si desea que las variables se configuren al **Iniciar sin depurar**, configure los mismos valores también en el **Comando ejecutar servidor**.

Como alternativa, puede definir variables de entorno mediante el Panel de control de Windows.  Esta opción es mejor si desea evitar el almacenamiento de credenciales en el archivo de código fuente o de proyecto.  Tenga en cuenta que necesitará reiniciar Visual Studio para que los nuevos valores de entorno estén disponibles para la aplicación.

1.  El código que implementa el repositorio de Almacenamiento de Tabla de Azure está en **models/azuretablestorage.py**.  Consulte la [documentación] para obtener más información sobre cómo utilizar el servicio de Tabla en Python.

1.  Ejecute la aplicación con <kbd>F5</kbd>.  Los sondeos que se crean con **Create Sample Polls** y los datos enviados con la votación se serializarán en Almacenamiento de Tabla de Azure.

1.  Vaya a la página **About** para comprobar que la aplicación utiliza el repositorio de **Almacenamiento de Tabla de Azure**.

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

##<a name="explore-the-azure-table-storage"></a>Exploración del Almacenamiento de Tabla de Azure

Es fácil ver y editar tablas de almacenamiento mediante el Explorador de servidores en Visual Studio.  En esta sección usaremos el Explorador de servidores para ver el contenido de las tablas de la aplicación de sondeos.

> [WACOM.NOTE]Para ello se requiere que esté instalado Microsoft Azure Tools, que está disponible como parte de [Azure SDK para .NET][].

1.  Abra el **Explorador de servidores**.  Expanda **Azure**, **Almacenamiento**, su cuenta de almacenamiento y, a continuación, **Tablas**.

  	![Server Explorer](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorer.png)

1.  Haga doble clic en la tabla de **sondeos** u **opciones** para ver el contenido de la tabla en una ventana de documento, así como para agregar, quitar y modificar entidades.

  	![Table Query Results](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorerTable.png)

##<a name="publish-to-an-azure-website"></a>Publicación en un sitio web de Azure

PTVS proporciona una manera fácil de implementar la aplicación web en un sitio web de Azure.

1.  En el **Explorador de soluciones**, haga clic con el botón derecho en el nodo del proyecto y seleccione **Publicar**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  Haga clic en **Sitios web Azure de Microsoft**.

1.  Haga clic en **Nuevo** para crear un sitio nuevo.

1.  Seleccione un **Nombre de sitio** y una **Región** y haga clic en **Crear**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonCreateWebSite.png)

1.  Acepte todos los demás valores predeterminados y haga clic en **Publicar**.

1.  El explorador web se abrirá automáticamente en el sitio publicado.  Si se desplaza a la página Acerca de, verá que utiliza el repositorio **en memoria**, no en el repositorio de **Almacenamiento de Tabla de Azure**.

Esto se debe a que las variables de entorno no se establecen en el sitio web de Azure, por lo que usa los valores predeterminados especificados en **settings.py**.

##<a name="configure-the-azure-website"></a>Configuración del sitio web de Azure

En esta sección, configuraremos las variables de entorno para el sitio.

1.  En el [Portal de administración de Azure][], haga clic en el sitio creado en la sección anterior.

1.  En el menú superior, haga clic en **CONFIGURAR**.

  	![Top Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteTopMenu.png)

1.  Desplácese hacia abajo hasta la sección **configuración de aplicaciones** y establezca los valores para **REPOSITORY\_NAME**, **STORAGE\_NAME** y **STORAGE\_KEY** tal como se describe en la sección anterior.

  	![App Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. En el menú inferior, haga clic en **GUARDAR**, a continuación **REINICIAR** y finalmente **EXAMINAR**.

  	![Bottom Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Debería ver la aplicación en funcionamiento del modo esperado, utilizando el repositorio **Almacenamiento de Tabla de Azure**.

    ¡Enhorabuena!

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

##<a name="next-steps"></a>Pasos siguientes

Siga estos vínculos para obtener más información acerca de Python Tools para Visual Studio, Flask y Almacenamiento de Tabla de Azure.

- [Documentación de Python Tools para Visual Studio][]
  - [Proyectos web][]
  - [Proyectos de servicio en la nube][]
  - [Depuración remota en Microsoft Azure][]
- [Documentación de Flask][]
- [Almacenamiento de Azure][]
- [Azure SDK para Python][]
- [Uso del servicio Almacenamiento de Tabla desde Python][]


<!--Link references-->
[Centro para desarrolladores de Python]: /es-es/develop/python/
[Servicios en la nube de Azure]: ../cloud-services-python-ptvs/
[documentación]: ../storage-python-how-to-use-table-storage/
[Uso del servicio Almacenamiento de Tabla desde Python]: ../storage-python-how-to-use-table-storage/

<!--External Link references-->
[Portal de administración de Azure]: https://manage.windowsazure.com
[Azure SDK para .NET]: http://azure.microsoft.com/es-es/downloads/
[Python Tools 2,1 para Visual Studio (en inglés)]: http://go.microsoft.com/fwlink/?LinkId=517189
[VSIX de ejemplo de Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK y herramientas para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK y herramientas para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentación de Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation
[Documentación de Flask]: http://flask.pocoo.org/
[Depuración remota en Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Proyectos web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Proyectos de servicio en la nube]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Almacenamiento de Azure]: http://azure.microsoft.com/es-es/documentation/services/storage/
[Azure SDK para Python]: https://github.com/Azure/azure-sdk-for-python
