<properties 
	pageTitle="Flask y MongoDB en Azure con Python Tools 2.1 para Visual Studio" 
	description="Obtenga información acerca de cómo usar las herramientas de Python para Visual Studio para crear una aplicación web de Flask que almacene datos en una instancia de base de datos de MongoDB y se pueda implementar en Aplicaciones web del Servicio de aplicaciones de Azure." 
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
	ms.date="04/16/2015" 
	ms.author="huguesv"/>



# Flask y MongoDB en Azure con Python Tools 2.1 para Visual Studio

  	<!-- The MongoLab workflow is not yet supported in the Preview Portal -->

En este tutorial, usaremos las [Herramientas de Python para Visual Studio] a fin de crear una aplicación web de sondeos sencilla mediante una de las plantillas de ejemplo de PTVS. Este tutorial también se encuentra disponible como [vídeo](https://www.youtube.com/watch?v=eql-crFgrAE).

La aplicación web de sondeos define una abstracción para su repositorio, por lo que puede alternar fácilmente entre los diferentes tipos de repositorios (en memoria, almacenamiento de tablas de Azure y MongoDB).

Facilitaremos información acerca del uso de uno de los servicios de MongoDB hospedados en Azure, la configuración de la aplicación web para utilizar MongoDB y la publicación de la aplicación web en [Aplicaciones web del Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Consulte el [Centro para desarrolladores de Python] para tener acceso a más artículos que tratan sobre el desarrollo de Aplicaciones web del Servicio de aplicaciones de Azure con PTVS mediante el uso de marcos web Bottle, Flask y Django, con MongoDB, Almacenamiento de tablas de Azure y los servicios de Base de datos MySQL y SQL. Si bien estos artículos se centran en el Servicio de aplicaciones, los pasos son similares a los que se aplican para desarrollar [Servicios en la nube de Azure].

## Requisitos previos

 - Visual Studio 2012 o 2013
 - [Python Tools 2.1 para Visual Studio]
 - [Python Tools 2.1 para archivos VSIX de ejemplo de Visual Studio]
 - [Herramientas del SDK de Azure para 2013] o [Herramientas del SDK de Azure para VS 2012]
 - [Python 2.7 de 32 bits] o [Python 3.4 de 32 bits]
 - [RoboMongo](optional)

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Creación del proyecto

En esta sección, vamos a crear un proyecto de Visual Studio con la utilización de una plantilla de ejemplo. Vamos a crear un entorno virtual e instalar los paquetes necesarios. A continuación, vamos a ejecutar la aplicación localmente con el repositorio en memoria predeterminado.

1.  En Visual Studio, seleccione **Archivo** y **Nuevo proyecto**. 

1.  Las plantillas de proyecto de los archivos VSIX de ejemplo de PTVS se encuentran disponibles en **Python**, **Samples** (Ejemplos). Seleccione **Polls Flask Web Project** (Proyecto web de Flask para sondeos) y haga clic en OK (Aceptar) para crear el proyecto.

  	![Cuadro de diálogo Nuevo proyecto](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskNewProject.png)

1.  Se le pedirá que instale paquetes externos. Seleccione **Instalar en un entorno virtual**.

  	![Cuadro de diálogo Paquetes externos](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskExternalPackages.png)

1.  Seleccione **Python 2.7** o **Python 3.4** como intérprete de base.

  	![Cuadro de diálogo Agregar entorno virtual](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAddVirtualEnv.png)

1.  Presione `F5` para confirmar que la aplicación funciona. De forma predeterminada, la aplicación usa un repositorio en memoria que no requiere ninguna configuración. Todos los datos se pierden cuando el servidor web se detiene.

1.  Haga clic en **Create Sample Polls** (Crear sondeos de ejemplo) y, a continuación, haga clic en un sondeo y vote.

  	![Explorador web](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskInMemoryBrowser.png)

## Creación de una base de datos MongoDB

Para la base de datos, crearemos una base de datos MongoLab hospedada en Azure.

Como alternativa, puede crear su propia máquina virtual para que se ejecute en Azure y, a continuación, instalar y administrar MongoDB por su cuenta.

Siga estos pasos para crear una prueba gratuita con MongoLab.

1.  Inicie sesión en el [Portal de administración de Azure].

1.  En la parte inferior del panel de navegación, haga clic en **NUEVO**.

  	<!-- ![New Button](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAzurePlusNew.png) -->

1.  Haga clic en **MARKETPLACE**, luego en **MongoLab** y en **Siguiente**.

  	<!-- ![Choose Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon1.png) -->

1.  En Nombre, escriba un nombre que usará para el servicio de base de datos.

1.  Seleccione una Región en la que ubicar el servicio de base de datos. Si va a usar la base de datos desde la aplicación de Azure, seleccione la misma región en la que implementará la aplicación.

  	<!-- ![Personalize Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon2.png) -->

1.  Haga clic en **Siguiente** y, a continuación, en **COMPRAR**.

## Configuración del proyecto

En esta sección, vamos a configurar nuestra aplicación para usar la base de datos de MongoDB que acabamos de crear. Observaremos cómo obtener la configuración de la conexión en el portal de Azure. A continuación, ejecutaremos la aplicación localmente.

1.  En el [Portal de administración de Azure], haga clic en **MARKETPLACE** y, a continuación, en el servicio de MongoLab que ha creado anteriormente.

1.  Haga clic en **INFORMACIÓN DE CONEXIÓN**. Puede usar el botón Copiar para colocar el valor de **MONGOLAB\_URI** en el Portapapeles.

  	![Cuadro de diálogo Información de conexión](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  En Visual Studio, haga clic con el botón derecho en el Explorador de soluciones y seleccione **Propiedades**. Haga clic en la pestaña **Depurar**.

  	![Configuración de depuración del proyecto](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBProjectDebugSettings.png)

1.  Establezca los valores de las variables del entorno que necesita la aplicación en **Depurar comando del servidor**, **Entorno**.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    De esta forma se definirán las variables del entorno para **Iniciar depuración**. Si desea que las variables se definan al **Iniciar sin depurar**, establezca los mismos valores en **Ejecutar comando del servidor**.

    De forma alternativa, puede definir las variables del entorno con el Panel de control de Windows. Se trata de una opción más conveniente si desea evitar que las credenciales se almacenen en el archivo de proyecto / de código fuente. Tenga en cuenta que necesitará reiniciar Visual Studio para que los nuevos valores del entorno estén disponibles en la aplicación.

1.  El código que implementa el repositorio de MongoDB se encuentra en **models/mongodb.py**.

1.  Presione `F5` para ejecutar la aplicación. Los sondeos creados con **Create Sample Polls** (Crear sondeos sencillos) y los datos enviados al votar se serializarán en MongoDB.

1.  Vaya a la página **Acerca de** para comprobar que la aplicación usa el repositorio de **MongoDB**.

  	![Explorador web](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBAbout.png)

## Exploración de la base de datos MongoDB

Puede usar una aplicación como [RoboMongo] para consultar una base de datos MongoDB y realizar modificaciones en ella. En esta sección, vamos a utilizar RoboMongo para ver el contenido de la base de datos de la aplicación de sondeos.

1.  Cree una conexión nueva. Necesitará el valor de **MONGOLAB\_URI** que hemos recuperado en la sección anterior.

    Tenga en cuenta el formato del URI: `mongodb://<name>:<password>@<address>:<port>/<name>`

    El nombre coincide con el nombre indicado al crear el servicio con Azure. Se usa para el nombre de la base de datos y para el nombre de usuario.

1.  En la página de conexión, establezca el valor **Nombre** con el nombre que desee para la conexión. Defina también los campos **Dirección** y **Puerto** para la *dirección* y el *puerto* de **MONGOLAB\_URI**.

  	![Cuadro de diálogo Configuración de conexión](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  En la página de autenticación, defina los valores **Base de datos** y **Nombre de usuario** para el *nombre* de **MONGOLAB\_URI**. Defina también el valor **Contraseña** para la *contraseña* de **MONGOLAB\_URI**.

  	![Cuadro de diálogo Configuración de conexión](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Guarde la base de datos y conéctese a ella. Ahora puede consultar la colección de sondeos.

  	![Resultados de la consulta de RoboMongo](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoQuery.png)

## Publicación de aplicación web del Servicio de aplicaciones de Azure

PTVS ofrece una forma fácil de implementar la aplicación web en el Servicio de aplicaciones de Azure.

1.  En el **Explorador de soluciones**, haga clic con el botón derecho en el nodo del proyecto y seleccione **Publicar**.

1.  Haga clic en **Aplicaciones web de Microsoft Azure**.

3. Si no ha iniciado sesión en Azure, haga clic en **Iniciar sesión** y use la cuenta de Microsoft de su suscripción de Azure para iniciar sesión.

2.  Haga clic en **Nuevo** para crear una aplicación web nueva.

1.  Rellene los campos siguientes y haga clic en **Crear**.
	-	**Nombre de aplicación web**
	-	**Plan del Servicio de aplicaciones**
	-	**Grupos de recursos**
	-	**Región**
	-	Deje **Servidor de base de datos** establecido en **No hay base de datos**

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonCreateWebSite.png) -->

1.  Acepte todos los valores predeterminados y haga clic en **Publicar**.

1.  El explorador web se abrirá automáticamente en la aplicación web publicada. Si navega hasta la página Acerca de, observará que usa el repositorio **En memoria** y no el repositorio de **MongoDB**.

    Esto se debe a que las variables del entorno no están definidas en la instancia de Aplicaciones web del Servicio de aplicaciones de Azure, por lo que usa los valores predeterminados especificados en **settings.py**.

## Configuración de la instancia de Aplicaciones web

En esta sección, vamos a configurar las variables del entorno para la instancia de Aplicaciones web.

1.  En el [Portal de administración de Azure], haga clic en la aplicación web creada en la sección anterior.

1.  En el menú superior, haga clic en **CONFIGURAR**.

  	<!-- ![Top Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteTopMenu.png) -->

1.  Desplácese hacia abajo hasta la sección **Configuración de la aplicación** y establezca los valores para **REPOSITORY\_NAME**, **MONGODB\_HOST** y **MONGODB\_DATABASE**, como se describe en la sección anterior.

  	<!-- ![App Settings](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png) -->

1.  En el menú inferior, haga clic en **GUARDAR**, en **REINICIAR** y, por último, en **EXAMINAR**.

  	<!-- ![Bottom Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureBottomMenu.png) -->

1.  La aplicación ahora debe funcionar según lo previsto, con el uso del repositorio de **MongoDB**.

    ¡Enhorabuena!

  	![Explorador web](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskAzureBrowser.png)

## Pasos siguientes

Siga estos vínculos para obtener más información sobre las herramientas de Python para Visual Studio, Flask y MongoDB.

- [Documentación sobre Python Tools para Visual Studio]
  - [Proyectos web]
  - [Proyectos de servicio en la nube]
  - [Depuración remota en Microsoft Azure]
- [Documentación de Flask]
- [MongoDB]
- [Documentación de PyMongo]
- [PyMongo]

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 


<!--Link references-->
[Centro para desarrolladores de Python]: /develop/python/
[Servicios en la nube de Azure]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[Portal de administración de Azure]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Herramientas de Python para Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 para archivos VSIX de ejemplo de Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Herramientas del SDK de Azure para 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Herramientas del SDK de Azure para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
[Documentación sobre Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation
[Documentación de Flask]: http://flask.pocoo.org/
[MongoDB]: http://www.mongodb.org/
[Documentación de PyMongo]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Depuración remota en Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Proyectos web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Proyectos de servicio en la nube]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
 

<!---HONumber=August15_HO6-->