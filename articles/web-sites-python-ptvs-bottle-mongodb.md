<properties linkid="web-sites-python-ptvs-bottle-mongodb" title="Bottle and MongoDB on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Bottle y MongoDB en Azure con Python Tools 2.1 para Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Bottle application that stores data in a MongoDB database instance and can be deployed to a web site." metaKeywords="" services="web-sites" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Bottle y MongoDB en Azure con Python Tools 2.1 para Visual Studio

En este tutorial, crearemos una sencilla aplicación de sondeos mediante una de las plantillas de ejemplo de PTVS.

La aplicación de sondeos define una abstracción para su repositorio, por lo que puede alternar fácilmente entre los diferentes tipos de repositorios (en memoria, almacenamiento de Tabla de Azure, MongoDB).

Vamos a aprender a usar uno de los servicios de MongoDB hospedados en Azure, configurar la aplicación para usar MongoDB y publicar la aplicación en un sitio web de Azure.

Consulte el [Centro para desarrolladores de Python][] para ver más artículos que tratan el desarrollo de sitios web de Azure con PTVS utilizando marcos web de Bottle, Flask y Django con servicios de MongoDB, Almacenamiento de Tabla de Azure, MySQL y Base de datos SQL.  Aunque este artículo se centra en Sitios web Azure, los pasos son similares al desarrollo de [Servicios en la nube de Azure][].

+ [Requisitos previos](#prerequisites)
+ [Creación del proyecto](#create-the-project)
+ [Creación de una base de datos de MongoDB](#create-a-mongodb-database)
+ [Configuración del proyecto](#configure-the-project)
+ [Exploración de la base de datos de MongoDB](#explore-the-mongodb-database)
+ [Publicación en un sitio web de Azure](#publish-to-an-azure-website)
+ [Configuración del sitio web de Azure](#configure-the-azure-website)
+ [Pasos siguientes](#next-steps)

##<a name="prerequisites"></a>Requisitos previos

 - Visual Studio 2012 o 2013
 - [Python Tools 2,1 para Visual Studio (en inglés)][]
 - [VSIX de ejemplo de Python Tools 2.1 para Visual Studio][]
 - [Herramientas del SDK de Azure para VS 2013][] o [Herramientas del SDK de Azure para VS 2012][]
 - [Python 2.7 de 32 bits][] o [Python 3.4 de 32 bits][]
 - [RoboMongo][] (opcional)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Creación del proyecto

En esta sección, crearemos un proyecto de Visual Studio mediante una plantilla de ejemplo.  Crearemos un entorno virtual e instalaremos los paquetes necesarios.  A continuación, ejecutaremos la aplicación localmente mediante el repositorio en memoria predeterminado.

1.  En Visual Studio, seleccione **Archivo**, **Nuevo proyecto**. 

1.  Las plantillas de proyecto de los VSIX de ejemplo de PTVS están disponibles en **Python**, **Samples**.  Seleccione **Polls Bottle Web Project** y haga clic en OK para crear el proyecto.

  	![New Project Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleNewProject.png)

1.  Se le pedirá que instale paquetes externos.  Seleccione **Install into a virtual environment**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleExternalPackages.png)

1.  Seleccione **Python 2.7** o **Python 3.4** como intérprete de base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAddVirtualEnv.png)

1.  Confirme que la aplicación funciona presionando <kbd>F5</kbd>.  De forma predeterminada, la aplicación utiliza un repositorio en memoria que no requiere ninguna configuración.  Todos los datos se pierden cuando se detiene el servidor web.

1.  Haga clic en **Create Sample Polls** y, a continuación, haga clic en un sondeo y vote.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleInMemoryBrowser.png)

##<a name="create-a-mongodb-database"></a>Creación de una base de datos de MongoDB

Para la base de datos, vamos a crear una base de datos hospedada de MongoLab en Azure.

Como alternativa, puede crear su propia máquina virtual que se ejecuta en Azure y, a continuación, instalar y administrar MongoDB usted mismo.

Puede crear una prueba gratuita con MongoLab siguiendo estos pasos.

1.  Inicie sesión en el [Portal de administración de Azure][].

1.  En la parte inferior del panel de navegación, haga clic en **NUEVO**.

  	![New Button](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAzurePlusNew.png)

1.  Haga clic en **TIENDA** y, a continuación, en **MongoLab**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon1.png)

1.  En Nombre, escriba un nombre que se va a utilizar para el servicio de base de datos.

1.  Seleccione un valor en Región/grupo de afinidad en el que se ubicará el servicio de base de datos. Si va a usar la base de datos desde la aplicación de Azure, seleccione la misma región en la que implementará la aplicación.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon2.png)

1.  Haga clic en **COMPRAR**.

##<a name="configure-the-project"></a>Configuración del proyecto

En esta sección, configuraremos nuestra aplicación para que utilice la base de datos de MongoDB que acabamos de crear.  Veremos cómo obtener la configuración de conexión desde el portal de Azure.  Entonces podremos ejecutar la aplicación localmente.

1.  En el [Portal de administración de Azure][], haga clic en **COMPLEMENTOS** y, a continuación, haga clic en el servicio de MongoLab que creó anteriormente.

1.  Haga clic en **INFORMACIÓN DE CONEXIÓN**.  Puede utilizar el botón Copiar para colocar el valor de **MONGOLAB\_URI** en el portapapeles.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  En Visual Studio, haga clic con el botón derecho en el nombre del proyecto en el Explorador de soluciones y seleccione **Propiedades**.  Haga clic en la pestaña **Depurar**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBProjectDebugSettings.png)

1.  Establezca los valores de las variables de entorno requeridas por la aplicación en **Comando depurar servidor**, **Entorno**.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<valor de MONGOLAB_URI>
        MONGODB_DATABASE=<nombre de base de datos>

Esto le permitirá establecer las variables de entorno al **Iniciar la depuración**.  Si desea que las variables se configuren al **Iniciar sin depurar**, configure los mismos valores también en el **Comando ejecutar servidor**.

Como alternativa, puede definir variables de entorno mediante el Panel de control de Windows.  Esta opción es mejor si desea evitar el almacenamiento de credenciales en el archivo de código fuente o de proyecto.  Tenga en cuenta que necesitará reiniciar Visual Studio para que los nuevos valores de entorno estén disponibles para la aplicación.

1.  El código que implementa el repositorio de MongoDB está en **models/mongodb.py**.

1.  Ejecute la aplicación con <kbd>F5</kbd>.  Los sondeos que se crean con **Create Sample Polls** y los datos enviados con la votación se serializarán en MongoDB.

1.  Vaya a la página **About** para comprobar que la aplicación utiliza el repositorio de **MongoDB**.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBAbout.png)

##<a name="explore-the-mongodb-database"></a>Exploración de la base de datos de MongoDB

Puede utilizar una aplicación como [RoboMongo][] para consultar y realizar modificaciones en una base de datos MongoDB.  En esta sección usaremos RoboMongo para ver el contenido de la base de datos de la aplicación de sondeos.

1.  Cree una nueva conexión.  Necesitará el valor de **MONGOLAB\_URI** recuperado en la sección anterior.

Observe el formato del URI: `mongodb://<name>:<password>@<address>:<port>/<name>`

El nombre coincide con el nombre que especificó cuando creó el servicio con Azure.  Se utiliza para el nombre de la base de datos y el nombre de usuario.

1.  En la página de conexión, establezca el **nombre** en cualquier nombre que desee para la conexión.  Configure también los campos **Dirección** y **Puerto** según los campos *address* y *port* de **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  En la página de autenticación, configure la **Base de datos** y el **Nombre de usuario** según el campo *name* de **MONGOLAB\_URI**.  Configure también la **Contraseña** según el campo *password* de **MONGOLAB\_URI**.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Guarde y conéctese a la base de datos.  Ahora puede consultar la colección de sondeos.

  	![RoboMongo Query Results](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoQuery.png)

##<a name="publish-to-an-azure-website"></a>Publicación en un sitio web de Azure

PTVS proporciona una manera fácil de implementar la aplicación web en un sitio web de Azure.

1.  En el **Explorador de soluciones**, haga clic con el botón derecho en el nodo del proyecto y seleccione **Publicar**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  Haga clic en **Sitios web Azure de Microsoft**.

1.  Haga clic en **Nuevo** para crear un sitio nuevo.

1.  Seleccione un **Nombre de sitio** y una **Región** y haga clic en **Crear**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonCreateWebSite.png)

1.  Acepte todos los demás valores predeterminados y haga clic en **Publicar**.

1.  El explorador web se abrirá automáticamente en el sitio publicado.  Si se desplaza a la página About, verá que utiliza el repositorio **en memoria**, no en el repositorio de **MongoDB**.

Esto se debe a que las variables de entorno no se establecen en el sitio web de Azure, por lo que usa los valores predeterminados especificados en **settings.py**.

##<a name="configure-the-azure-website"></a>Configuración del sitio web de Azure

En esta sección, configuraremos las variables de entorno para el sitio.

1.  En el [Portal de administración de Azure][], haga clic en el sitio creado en la sección anterior.

1.  En el menú superior, haga clic en **CONFIGURAR**.

  	![Top Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteTopMenu.png)

1.  Desplácese hacia abajo hasta la sección **configuración de aplicaciones** y establezca los valores para **REPOSITORY\_NAME**, **MONGODB\_HOST** y **MONGODB\_DATABASE** tal como se describe en la sección anterior.

  	![App Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  En el menú inferior, haga clic en **GUARDAR**, a continuación **REINICIAR** y finalmente **EXAMINAR**.

  	![Bottom Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Debería ver la aplicación en funcionamiento del modo esperado, utilizando el repositorio **MongoDB**.

    ¡Enhorabuena!

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleAzureBrowser.png)

##<a name="next-steps"></a>Pasos siguientes

Siga estos vínculos para obtener más información acerca de Python Tools para Visual Studio, Bottle y MongoDB.

- [Documentación de Python Tools para Visual Studio][]
  - [Proyectos web][]
  - [Proyectos de servicio en la nube][]
  - [Depuración remota en Microsoft Azure][]
- [Documentación de Bottle][]
- [MongoDB][]
- [Documentación de PyMongo][]
- [PyMongo][]


<!--Link references-->
[Centro para desarrolladores de Python]: /es-es/develop/python/
[Servicios en la nube de Azure]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Portal de administración de Azure]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Python Tools 2,1 para Visual Studio (en inglés)]: http://go.microsoft.com/fwlink/?LinkId=517189
[VSIX de ejemplo de Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK y herramientas para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK y herramientas para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentación de Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation
[Documentación de Bottle]: http://bottlepy.org/docs/dev/index.html
[MongoDB]: http://www.mongodb.org/
[Documentación de PyMongo]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Depuración remota en Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Proyectos web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Proyectos de servicio en la nube]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
