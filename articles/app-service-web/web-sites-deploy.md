<properties
	pageTitle="Documentación de implementación del Servicio de aplicaciones de Azure"
	description="Obtenga más información sobre cómo implementar la aplicación en el Servicio de aplicaciones de Azure."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/21/2016"
	ms.author="cephalin;dariac"/>
    
# Documentación de implementación del Servicio de aplicaciones de Azure

Este artículo le ayudará a determinar la mejor opción para implementar los archivos de su aplicación web, back-end de aplicación móvil o aplicación de API en el [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) y, a continuación, le guiará a los recursos adecuados con instrucciones específicas para la opción de su preferencia.

## <a name="overview"></a>Descripción general de la implementación de Servicio de aplicaciones de Azure

El Servicio de aplicaciones de Azure mantiene el marco de la aplicación para usted (ASP.NET, PHP, Node.js, etc.). Algunos marcos están habilitados de forma predeterminada, mientras que es posible que otros, como Java y Python, necesiten una configuración de marca de verificación sencilla para habilitarlos. Además, puede personalizar el marco de la aplicación, como la versión de PHP o el valor de bits del tiempo de ejecución. Para obtener más información, consulte [Configuración de aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-configure.md).

Como no tiene que preocuparse por el marco de la aplicación o del servidor web, la implementación de la aplicación en el Servicio de aplicaciones consiste en implementar código, archivos binarios, archivos de contenido y su estructura de directorios correspondiente en el directorio [**/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) en Azure (o el directorio **/site/wwwroot/App\_Data/Jobs/** para WebJobs). El Servicio de aplicaciones admite las siguientes opciones de implementación:

- [FTP o FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): use sus FTP o FTPS favoritos habilitados para mover los archivos a Azure, desde [FileZilla](https://filezilla-project.org) hasta IDE completos como [NetBeans](https://netbeans.org). Se trata estrictamente de un proceso de carga de archivos. El Servicio de aplicaciones no proporciona servicios adicionales, como control de versiones, administración de estructura de archivos, etc.

- [Kudu (Git/Mercurial o OneDrive/Dropbox)](https://github.com/projectkudu/kudu/wiki/Deployment): use el [motor de implementación](https://github.com/projectkudu/kudu/wiki) en el Servicio de aplicaciones. Inserte el código en Kudu directamente desde cualquier repositorio. Kudu también proporciona servicios agregados siempre que se inserte código en él, como por ejemplo, control de versiones, restauración de paquetes, MSBuild y [enlaces web](https://github.com/projectkudu/kudu/wiki/Web-hooks), para la implementación continua y otras tareas de automatización. El motor de implementación de Kudu es compatible con 3 tipos distintos de orígenes de implementación:
    * Sincronización de contenido de OneDrive y Dropbox.
    * Implementación continua basada en repositorio con sincronización automática desde GitHub, Bitbucket y Visual Studio Team Services.
    * Implementación basada en repositorio con sincronización manual desde Git local.

- [Web Deploy](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): implemente código en Servicio de aplicaciones directamente desde las herramientas de Microsoft de su preferencia, como Visual Studio, con las mismas herramientas que automatizan la implementación en servidores de IIS. Esta herramienta es compatible con la implementación de solo diferencias, la creación de bases de datos, las transformaciones de cadenas de conexión, etc. Web Deploy difiere de Kudu en que los archivos binarios de aplicación se compilan antes de implementarlos en Azure. De forma similar a FTP, el Servicio de aplicaciones no proporciona servicios adicionales.

Las herramientas de desarrollo web más populares admiten uno o varios de estos procesos de implementación. Aunque la herramienta que elija determina los procesos de implementación que puede aprovechar, la funcionalidad de DevOps real a su disposición depende de la combinación del proceso de implementación y las herramientas específicas que elija. Por ejemplo, si ejecuta Web Deploy desde [Visual Studio con Azure SDK](#vspros), aunque no obtenga la automatización de Kudu, tendrá la restauración de paquetes y la automatización de MSBuild en Visual Studio.

>[AZURE.NOTE] Estos procesos de implementación no [aprovisionan realmente los recursos de Azure](../azure-portal/resource-group-portal.md) que puede necesitar la aplicación. Sin embargo, la mayoría de los artículos de procedimientos vinculados muestra cómo aprovisionar la aplicación e implementar el código en ella de un extremo a otro. También puede encontrar opciones adicionales para el aprovisionamiento de recursos de Azure en la sección [Automatización de la implementación mediante herramientas de línea de comandos](#automate).
     
## <a name="ftp"></a>Implementación a través de FTP mediante la copia manual de archivos en Azure
Si está familiarizado con la copia manual de contenido web en un servidor web, puede usar una utilidad [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para copiar archivos, como el Explorador de Windows o [FileZilla](https://filezilla-project.org/).

Las ventajas de la copia manual de archivos son las siguientes:

- La familiaridad y la complejidad mínima de las herramientas de FTP.
- Se sabe exactamente dónde van los archivos.
- Mayor seguridad con FTPS.

Los inconvenientes de la copia manual de archivos son los siguientes:

- La necesidad de saber cómo implementar archivos en los directorios correctos en Servicio de aplicaciones.
- No hay control de versiones para la reversión cuando se producen errores.
- No existe un historial integrado de las implementaciones para solucionar problemas con la implementación.
- Existe la posibilidad de que se generen tiempos de implementación prolongados, debido a que muchas de las herramientas de FTP no ofrecen la funcionalidad para copiar solo las diferencias y simplemente copian todos los archivos.

### <a name="howtoftp"></a>Implementación mediante la copia manual de archivos en Azure
La copia de archivos en Azure implica unos pocos pasos sencillos:

1. Si ya estableció las credenciales de implementación, puede obtener la información sobre la conexión de FTP si va a **Configuración** > **Propiedades** y, luego, copia los valores de **FTP/usuario de implementación**, **Nombre de host FTP** y **Nombre de host FTPS**. Copie el valor de usuario **FTP/usuario de implementación** tal como aparece en el Portal de Azure, incluido el nombre de la aplicación a fin de proporcionar el contexto adecuado para el servidor FTP.
2. Desde el cliente de FTP, use la información de conexión recopilada para conectarse a la aplicación.
3. Copie los archivos y la estructura de directorio correspondiente al directorio [**/site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) en Azure (o el directorio **/site/wwwroot/App\_Data/Jobs/** para WebJobs).
4. Vaya a la dirección URL de la aplicación para comprobar que la aplicación se está ejecutando correctamente.

Para obtener más información, consulte el siguiente recurso:

* [Creación de un sitio web PHP-MySQL e implementación con FTP.](web-sites-php-mysql-deploy-use-ftp.md).

## <a name="dropbox"></a>Implementación mediante sincronización con una carpeta en la nube
Una buena alternativa a [copiar archivos manualmente](#ftp) es sincronizar archivos y carpetas con el Servicio de aplicaciones desde un servicio de almacenamiento en la nube, como OneDrive y Dropbox. La sincronización con una carpeta en la nube utiliza el proceso Kudu para la implementación (consulte [Información general sobre los procesos de implementación](#overview)).

Las ventajas de sincronizar con una carpeta en la nube son las siguientes:

- Sencillez de la implementación Servicios como OneDrive y Dropbox proporcionan clientes de sincronización de escritorio, por lo que el directorio de trabajo local también es el directorio de implementación.
- Implementación con un solo clic.
- Toda la funcionalidad del motor de implementación de Kudu está disponible (por ejemplo, la restauración de paquetes, la automatización, etc.).

Los inconvenientes de sincronizar con una carpeta en la nube son los siguientes:

- No hay control de versiones para la reversión cuando se producen errores.
- No hay implementación automatizada, se requiere la sincronización manual.

### <a name="howtodropbox"></a>Implementación mediante sincronización con una carpeta en la nube
En el [Portal de Azure](https://portal.azure.com), puede designar una carpeta para la sincronización de contenido en el almacenamiento en la nube de OneDrive o Dropbox, trabajar con el código de la aplicación y el contenido de dicha carpeta y sincronizar con el Servicio de aplicaciones con solo hacer clic en un botón.

* [Sincronización de contenido de una carpeta en la nube con Servicio de aplicaciones de Azure](app-service-deploy-content-sync.md).

## <a name="continuousdeployment"></a>Implementación continua desde un servicio de control de código fuente basado en la nube
Si el equipo de desarrollo usa un servicio de administración de código fuente (SCM) basado en la nube, como [Visual Studio Team Services](http://www.visualstudio.com/), [GitHub](https://www.github.com) o [BitBucket](https://bitbucket.org/), se puede configurar Servicio de aplicaciones para integrarlo con el repositorio e implementarlo de manera continua.

Las ventajas de la implementación desde un servicio de control de código fuente basado en la nube son las siguientes:

- Control de versiones para habilitar la reversión.
- Capacidad para configurar la implementación continua de repositorios Git (y Mercurial, cuando corresponda).
- Implementación específica de ramas, se pueden implementar ramas diferentes en [ranuras](web-sites-staged-publishing.md) distintas.
- Toda la funcionalidad del motor de implementación de Kudu está disponible (por ejemplo, control de versiones de implementación, reversión, restauración de paquetes, automatización).

La desventaja de la implementación desde un servicio de control de código fuente basado en la nube es la siguiente:

- Se requiere cierto conocimiento del servicio de SCM respectivo.

###<a name="vsts"></a>Implementación continua desde un servicio de control de código fuente basado en la nube
En el [Portal de Azure](https://portal.azure.com), puede configurar la implementación continua desde GitHub, BitBucket y Visual Studio Team Services.

* [Implementación continua en el Servicio de aplicaciones de Azure](app-service-continous-deployment.md).

## <a name="localgitdeployment"></a>Implementación desde Git local
Si el equipo de desarrollo usa un servicio de administración de código fuente (SCM) local basado en Git, puede configurarlo como un origen de implementación en Servicio de aplicaciones.

Las ventajas de implementar desde un repositorio local Git son las siguientes:

- Control de versiones para habilitar la reversión.
- Implementación específica de ramas, se pueden implementar ramas diferentes en [ranuras](web-sites-staged-publishing.md) distintas.
- Toda la funcionalidad del motor de implementación de Kudu está disponible (por ejemplo, control de versiones de implementación, reversión, restauración de paquetes, automatización).

La desventaja de implementar desde un repositorio local de Git es la siguiente:

- Se requiere cierto conocimiento del sistema de SCM respectivo.
- No hay ninguna solución llave en mano para la implementación continua.

###<a name="vsts"></a>Implementación desde Git local
En el [Portal de Azure](https://portal.azure.com), puede configurar la implementación de Git local.

* [Implementación de Git local en el Servicio de aplicaciones de Azure](app-service-deploy-local-git.md).
* [Publicación en aplicaciones web desde cualquier repositorio git/hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html).

## Implementación mediante un IDE
Si ya usa [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) con un [Azure SDK](https://azure.microsoft.com/downloads/), o bien otros conjuntos de aplicaciones de IDE, como [Xcode](https://developer.apple.com/xcode/), [Eclipse](https://www.eclipse.org) e [IntelliJ IDEA](https://www.jetbrains.com/idea/), puede efectuar implementaciones en Azure directamente desde el IDE. Esta opción es perfecta para un desarrollador individual.

Visual Studio admite los tres procesos de implementación (FTP, Git y Web Deploy), en función de sus preferencias, mientras que otros IDE pueden efectuar implementaciones en el Servicio de aplicaciones si tienen integración con FTP o Git (consulte [Descripción general de la implementación de Servicio de aplicaciones de Azure](#overview)).

Las ventajas de la implementación mediante un IDE son las siguientes:

- Reduce al mínimo, potencialmente, las herramientas para el ciclo de vida de la aplicación de un extremo a otro. Desarrolle, depure, realice un seguimiento e implemente la aplicación en Azure, todo ello sin salir de su IDE.

Los inconvenientes de la implementación mediante un IDE son los siguientes:

- Mayor complejidad de las herramientas.
- Aún requiere un sistema de control de código fuente para un proyecto de equipo.

<a name="vspros"></a> Las ventajas adicionales de la implementación mediante Visual Studio con Azure SDK son las siguientes:

- Azure SDK hace de los recursos de Azure ciudadanos de primera clase en Visual Studio. Cree, elimine, edite, inicie y detenga aplicaciones, consulte la Base de datos SQL del back-end, depure en directo la aplicación de Azure y mucho más.
- Edición en directo de archivos de código en Azure.
- Depuración en directo de aplicaciones en Azure.
- Explorador integrado de Azure.
- Implementación de solo diferencias.

###<a name="vs"></a>Implementación directa desde Visual Studio

* [Introducción a Azure y ASP.NET](web-sites-dotnet-get-started.md). Creación e implementación de un proyecto web ASP.NET MVC simple mediante Visual Studio y Web Deploy.
* [Implementación de WebJobs de Azure con Visual Studio](websites-dotnet-deploy-webjobs.md). Configuración de proyectos de aplicación de consola para que se implementen como trabajos web.
* [Implementación de una aplicación ASP.NET MVC 5 segura con suscripción, OAuth y base de datos SQL en aplicaciones web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Creación e implementación de un proyecto web ASP.NET MVC con una base de datos SQL mediante Visual Studio, Web Deploy y migraciones de Entity Framework Code First .
* [Implementación web de ASP.NET con Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Una serie de 12 tutoriales que abarca un rango más completo de tareas de implementación que otros de esta lista. Desde que se escribió el tutorial se han agregado algunas características de implementación de Azure, pero las notas agregadas posteriormente explican lo que falta.
* [Implementación de un sitio web ASP.NET en Azure en Visual Studio 2012 desde un repositorio Git directamente](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Explica cómo implementar un proyecto web de ASP.NET en Visual Studio, con el complemento Git para confirmar el código en Git y conectar Azure al repositorio Git. A partir de Visual Studio 2013, la compatibilidad con Git está integrada y no requiere la instalación de un complemento.

###<a name="aztk"></a>Implementación con los kits de herramientas de Azure para Eclipse e IntelliJ IDEA

Microsoft le permite implementar aplicaciones web en Azure directamente desde Eclipse e IntelliJ a través del [kit de herramientas de Azure para Eclipse](../azure-toolkit-for-eclipse.md) y [kit de herramientas de Azure para IntelliJ](../azure-toolkit-for-intellij.md). Los siguientes tutoriales ilustran los pasos que intervienen en la implementación de una sencilla aplicación web que muestra el mensaje "Hello world!" (¡Hola, mundo!) en Azure mediante cada uno de los dos IDE:

*  [Create a Hello World Web App for Azure in Eclipse](./app-service-web-eclipse-create-hello-world-web-app.md) (Creación de una aplicación web que muestra el mensaje "Hello world!" para Azure en Eclipse). En este tutorial, se muestra cómo utilizar el kit de herramientas de Azure para Eclipse con el fin de crear e implementar una aplicación web que muestra el mensaje "Hello world!" (¡Hola, mundo!) para Azure.
*  [Create a Hello World Web App for Azure in IntelliJ](./app-service-web-intellij-create-hello-world-web-app.md) (Creación de una aplicación web que muestra el mensaje "Hello world!" para Azure en IntelliJ). En este tutorial, se muestra cómo utilizar el kit de herramientas de Azure para IntelliJ con el fin de crear e implementar una aplicación web que muestra el mensaje "Hello world!" (¡Hola, mundo!) para Azure.


## <a name="automate"></a>Automatización de la implementación mediante herramientas de línea de comandos

* [Automatización de la implementación con MSBuild](#msbuild)
* [Copia de archivos con scripts y herramientas FTP](#ftp)
* [Automatización de la implementación con Windows PowerShell](#powershell)
* [Automatización de la implementación con la API de administración de .NET](#api)
* [Implementación desde la interfaz de la línea de comandos de Azure (CLI de Azure)](#cli)
* [Implementación desde la línea de comandos de Web Deploy](#webdeploy)
* [Uso de scripts por lotes de FTP](http://support.microsoft.com/kb/96269).
 
Otra opción de implementación es usar un servicio basado en la nube como [Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy). Para obtener más información, consulte [Deploy ASP.NET applications to Azure Web Sites](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites) (Implementación de aplicaciones ASP.NET en Sitios web de Azure).

###<a name="msbuild"></a>Automatización de la implementación con MSBuild

Si usa el [IDE de Visual Studio](#vs) para desarrollo, puede utilizar [MSBuild](http://msbuildbook.com/) para automatizar todo lo que puede hacer en el IDE. Puede configurar MSBuild para usar [Web Deploy](#webdeploy) o [FTP/FTPS](#ftp) para copiar archivos. Web Deploy también puede automatizar muchas otras tareas relacionadas con la implementación, como implementar bases de datos.

Para obtener más información acerca de la implementación de la línea de comandos con MSBuild, consulte los siguientes recursos:

* [Implementación web de ASP.NET con Visual Studio: Implementación de línea de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). El décimo tutorial de una serie sobre la implementación en Azure con Visual Studio. Muestra cómo usar la línea de comandos para la implementación después de haber configurado perfiles de publicación en Visual Studio.
* [Dentro de Microsoft Build Engine: Uso de MSBuild y Team Foundation Build](http://msbuildbook.com/). Copia impresa de un libro que incluye capítulos sobre cómo usar MSBuild para la implementación.

###<a name="powershell"></a>Automatización de la implementación con Windows PowerShell

Desde [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx) puede ejecutar funciones de implementación MSBuild o FTP. Si lo hace, también puede usar una recopilación de cmdlets de Windows PowerShell que hacen que sea fácil llamar a la API REST de administración de Azure.

Para obtener más información, consulte los siguientes recursos:

* [Implementación de una aplicación web vinculada a un repositorio de GitHub](app-service-web-arm-from-github-provision.md)
* [Aprovisionamiento de una aplicación web con una base de datos SQL](app-service-web-arm-with-sql-database-provision.md)
* [Aprovisionamiento e implementación predecibles de microservicios en Azure](app-service-deploy-complex-application-predictably.md)
* [Creación de aplicaciones reales en la nube con Azure: automatizar todo.](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Capítulo de un libro electrónico que explica cómo la aplicación de ejemplo que aparece en el libro electrónico usa scripts de Windows PowerShell para crear un entorno de prueba de Azure e implementar en él. Consulte la sección [Recursos](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) para ver vínculos a documentación adicional de Azure PowerShell.
* [Uso de scripts de Windows PowerShell para publicar en entornos de prueba y desarrollo](http://msdn.microsoft.com/library/dn642480.aspx). Cómo usar scripts de implementación de Windows PowerShell que genera Visual Studio.

###<a name="api"></a>Automatización de la implementación con la API de administración de .NET

Puede escribir código de C# para realizar funciones de MSBuild o FTP para implementación. Si lo hace, puede tener acceso a la API REST de administración de Azure para realizar funciones de administración de sitios.

Para obtener más información, consulte el siguiente recurso:

* [Automatización de todo con las bibliotecas de administración de Azure y .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Introducción a la API de administración .NET y vínculos a más documentación.

###<a name="cli"></a>Implementación desde la interfaz de la línea de comandos de Azure (CLI de Azure)

Puede usar la línea de comandos en máquinas Windows, Mac o Linux para implementar mediante FTP. Si lo hace, también puede tener acceso a la API de administración REST de Azure con la CLI de Azure.

Para obtener más información, consulte el siguiente recurso:

* [Herramientas de línea de comandos de Azure](/downloads/#cmd-line-tools). Página de portal en Azure.com para obtener información sobre la herramienta de línea de comandos.

###<a name="webdeploy"></a>Implementación desde la línea de comandos de Web Deploy

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) es el software de Microsoft para la implementación en IIS que no solo ofrece características inteligentes de sincronización de archivos, sino que también puede realizar o coordinar muchas otras tareas relacionadas con la implementación que no se pueden automatizar cuando usa FTP. Por ejemplo, Web Deploy puede implementar una base de datos nueva o actualizaciones de base de datos junto con su aplicación web. Web Deploy también puede minimizar el tiempo que se requiere para actualizar un sitio existente, dado que puede copiar de manera inteligente solo los archivos modificados. Microsoft Visual Studio y Team Foundation Server cuentan con compatibilidad integrada para Web Deploy, pero solo puede usar Web Deploy directamente desde la línea de comandos para automatizar la implementación. Los comandos de Web Deploy son muy poderosos, pero la curva de aprendizaje puede ser pronunciada.

Para obtener más información, consulte el siguiente recurso:

* [Aplicaciones web simples: implementación](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog de David Ebbo sobre una herramienta que escribió para facilitar el uso de Web Deploy.
* [Herramienta de implementación web](http://technet.microsoft.com/library/dd568996). Documentación oficial sobre el sitio de Microsoft TechNet. Información antigua, pero sigue siendo un buen lugar para comenzar.
* [Uso de Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Documentación oficial sobre el sitio de Microsoft IIS.NET. También es información antigua, pero es un buen lugar para comenzar.
* [Implementación web de ASP.NET con Visual Studio: Implementación de línea de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild es el motor de compilación que emplea Visual Studio y que también se puede usar desde la línea de comandos para implementar aplicaciones web en Web Apps. Este tutorial forma parte de una serie que se refiere principalmente a la implementación de Visual Studio.

##<a name="nextsteps"></a>Pasos siguientes

En algunos escenarios, es posible que desee poder cambiar fácilmente entre una versión de ensayo y versión una de producción de su aplicación. Para obtener más información, consulte [Implementación de ensayo en Web Apps](web-sites-staged-publishing.md).

Tener un plan de copia de seguridad y restauración es un elemento importante de cualquier flujo de trabajo de implementación. Para obtener información sobre la característica de copia de seguridad y restauración del Servicio de aplicaciones, consulte [Hacer copia de seguridad de una aplicación web en el servicio de aplicaciones de Azure](web-sites-backup.md).

Para obtener información sobre cómo usar el control de acceso basado en roles de Azure para administrar el acceso a la implementación del Servicio de aplicaciones, consulte [RBAC and Web App Publishing](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/) (RBAC y la publicación de aplicaciones web).


 

<!---HONumber=AcomDC_0713_2016-->