<properties 
	pageTitle="Implementación de un sitio web de Azure" 
	description="Obtenga información acerca de los métodos que están disponibles para implementar contenido en un sitio web de Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/07/2015" 
	ms.author="tdykstra"/>

#Implementación de un sitio web de Azure

Existen varias opciones para implementar su propio contenido en un sitio web de Azure. En este tema se proporciona información general abreviada de cada opción y vínculos para obtener más información.

* [Implementación desde un sistema de control de código fuente hospedado en la nube](#cloud)
	* Visual Studio Online
	* Sitios web de repositorio con Git
	* Sitios web de repositorio con Mercurial
	* Dropbox
* [Implementación desde un IDE](#ide)
	* Visual Studio
	* WebMatrix
* [Implementación mediante el uso de una utilidad de FTP](#ftp)
* [Implementación desde un sistema de control de código fuente local](#onpremises)
	* Team Foundation Server (TFS)
	* Repositorios locales de Git o Mercurial
* [Uso de herramientas de línea de comandos y la API REST de administración de Azure](#commandline)
	* MSBuild
	* Scripts de FTP
	* Windows PowerShell
	* API de administración de .NET
	* Línea de comandos entre plataformas (xpat-cli)
	* Línea de comandos de Web Deploy


##<a name="cloud"></a>Implementación desde un sistema de control de código fuente hospedado en la nube

La mejor manera de implementar un sitio web es configurar un [flujo de trabajo de entrega continua](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) integrado con el [sistema de control de código fuente](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control). La automatización no solo permite que el proceso de implementación sea más eficaz, sino que también hace que los procesos de copia de seguridad y restauración sean más controlables y confiables. 

Si aún no tiene configurado el control de código fuente, la mejor manera de comenzar es usar un sistema de control de código fuente hospedado en la nube.

###<a name="vso"></a>Visual Studio Online

[Visual Studio Online](http://www.visualstudio.com/) (anteriormente, Team Foundation Service) es una solución de Microsoft basada en la nube para el control de código fuente y la colaboración en equipo. El servicio es gratuito para un equipo de hasta cinco desarrolladores. Puede hacer entrega continua a un sitio web de Azure y puede usar el repositorio [Git o TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs).

Para obtener más información, consulte los siguientes recursos:

* [Entrega continua a Azure con Visual Studio Online y TFVC](../cloud-services-continuous-delivery-use-vso/). Tutorial paso a paso en el que se muestra cómo configurar la entrega continua desde Visual Studio Online a un Sitio web de Azure, mediante TFVC. TFVC es la opción de control de código fuente centralizado, en contraposición a Git, que es la opción de control de código fuente distribuido.
* [Entrega continua a Azure con Visual Studio Online y Git](../cloud-services-continuous-delivery-use-vso-git/). Similar al tutorial anterior, pero usa Git en lugar de TFVC.

###<a name="git"></a>Sitios web de repositorio con Git

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) es un conocido sistema de control de código fuente distribuido. Azure cuenta con características integradas que facilitan la automatización de la implementación en un sitio web de Azure desde conocidos sitios de repositorio basados en web que almacenan repositorios Git, entre los que se incluyen [GitHub](, http://www.github.com)[CodePlex](http://www.codeplex.com/) y [BitBucket](https://bitbucket.org/). Una ventaja de usar Git para la implementación es que resulta relativamente fácil volver a una implementación anterior si alguna vez se requiere hacerlo. 

Para obtener más información, consulte los siguientes recursos:

* [Publicación desde el Control de código fuente a Sitios web de Azure con Git](../web-sites-publish-source-control/). Uso de Git para publicar directamente desde su equipo local en un sitio web de Azure (en Azure, este método de publicación se denomina Git local). También muestra cómo habilitar la implementación continua de repositorios Git desde GitHub, CodePlex o BitBucket.
* [Implementación en Sitios web con GitHub mediante Kudu](/es-es/documentation/videos/deploying-to-azure-from-github/). Vídeo de Scott Hanselman y David Ebbo que muestra cómo implementar un sitio web directamente desde GitHub a un sitio web de Azure.
* [Botón Implementación en Azure para Sitios web Azure](http://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/). Blog sobre un método para desencadenar la implementación desde un repositorio Git.
* [Foro de Azure sobre Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="mercurial"></a>Sitios web de repositorio con Mercurial

Si usa [Mercurial](http://mercurial.selenic.com/) como sistema de control de código fuente y almacena el repositorio en [CodePlex](http://www.codeplex.com/) o [BitBucket](https://bitbucket.org/), puede utilizar características integradas en Sitios web Azure para implementar automáticamente el contenido.

Para obtener información acerca de la implementación con Mercurial, consulte los siguientes recursos:

* [Publicación desde el Control de código fuente a Sitios web de Azure con Git](../web-sites-publish-source-control/). A pesar de que este tutorial muestra cómo publicar un repositorio Git, el proceso para los repositorios de Mercurial hospedados en CodePlex o BitBucket es similar.
* [Foro de Azure sobre Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="dropbox"></a>Dropbox

[Dropbox](https://www.dropbox.com/) no es un sistema de control de código fuente, pero si almacena el código fuente en Dropbox podrá automatizar la implementación desde su cuenta de Dropbox.

* [Implementación en Sitios web Azure desde Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Cómo usar el Portal de administración de Azure para configurar una implementación de Dropbox.
* [Implementación de Dropbox en Sitios web Azure](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). Este vídeo le guiará en el proceso de conectar una carpeta de Dropbox a un sitio web de Azure y muestra la rapidez con que puede configurar y ejecutar un sitio web o mantenerlo gracias a una implementación simple a través de arrastrar y soltar.
* [Foro de Azure sobre Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).







##<a name="ide"></a>Implementación desde un IDE

[Visual Studio](http://www.visualstudio.com/) y [WebMatrix](http://www.microsoft.com/web/webmatrix/) son entornos de desarrollo integrado (IDE) de Microsoft que puede utilizar para el desarrollo web. Ambos proporcionan características integradas que facilitan la implementan en Sitios web Azure.  Ambos pueden utilizar [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) para automatizar tareas adicionales relacionadas con la implementación, como implementación de base de datos y cambios en las cadenas de conexión. Ambos también pueden implementarse mediante el uso de [FTP o FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol))). 

WebMatrix es rápido de instalar y fácil de entender, pero Visual Studio ofrece muchas más características para trabajar con Sitios web Azure. Desde el IDE de Visual Studio puede crear, detener, iniciar y eliminar sitios web de Azure, puede ver registros en tiempo real a medida que se crean, puede realizar una depuración remota, etc. Visual Studio también se integra con sistemas de control de código fuente como [Visual Studio Online](#vso), [Team Foundation Server](#tfs) y [repositorios Git](#git).

###<a name="vs"></a>Visual Studio

Para obtener información sobre la implementación en Sitios web Azure desde Visual Studio, consulte los siguientes recursos:

* [Introducción a Azure y ASP.NET](../web-sites-dotnet-get-started/). Creación e implementación de un proyecto web ASP.NET MVC simple mediante Visual Studio y Web Deploy.
* [Implementación de WebJobs de Azure a Sitios web de Azure](../websites-dotnet-deploy-webjobs/). Configuración de proyectos de aplicación de consola para que se implementen como trabajos web.  
* [Implementación de una aplicación ASP.NET MVC 5 segura con suscripción, OAuth y Base de datos SQL en un sitio web de Azure](../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/). Creación e implementación de un proyecto web ASP.NET MVC con una base de datos SQL mediante Visual Studio, Web Deploy y migraciones de Entity Framework Code First.
* [Información general sobre la implementación de proyectos de aplicación web para Visual Studio y ASP.NET](http://msdn.microsoft.com/library/dd394698.aspx). Una introducción básica a la implementación web con Visual Studio. Artículo antiguo, pero que incluye información que sigue siendo pertinente, incluida información general de las opciones para implementar una base de datos junto con la aplicación web, además de una lista de tareas adicionales de implementación que podría tener que hacer o configurar manualmente Visual Studio para que las haga. Este tema trata sobre implementación en general, no solo sobre implementación en Sitios web Azure.
* [Implementación web de ASP.NET con Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Una serie de 12 tutoriales que abarca un rango más completo de tareas de implementación que otros de esta lista. Desde que se escribió el tutorial, se han agregado algunas características de implementación de Azure, pero existen notas agregadas posteriormente que explican lo que falta. 
* [Deploying an ASP.NET Website to Azure in Visual Studio 2012 from a Git Repository directly](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Explica cómo implementar un proyecto web de ASP.NET en Visual Studio, con el complemento Git para confirmar el código en Git y conectar Azure al repositorio Git. A partir de Visual Studio 2013, la compatibilidad con Git está integrada y no requiere la instalación de un complemento.

###<a name="webmatrix"></a>WebMatrix

Para obtener información sobre la implementación en Sitios web Azure desde WebMatrix, consulte los siguientes recursos:

* [Desarrollo e implementación de un sitio web con Microsoft WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-using-webmatrix/). Creación de un sitio web simple de ASP.NET con una plantilla de WebMatrix y su implementación en un sitio web de Azure mediante WebMatrix y Web Deploy.
* [Compilación e implementación de un sitio web Node.js en Azure con WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-use-webmatrix/).
* [Creación de un sitio web Azure de PHP-MySQL e implementación mediante WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-php-mysql-use-webmatrix/).
* [WebMatrix 3: Integrated Git and Deployment to Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Uso de WebMatrix para implementar desde un repositorio de control de código fuente de Git.

##<a name="ftp"></a>Implementación mediante el uso de una utilidad de FTP

Independientemente del IDE que utilice, también puede implementar contenido en su sitio si usa [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para copiar archivos. Crear credenciales de FTP para un sitio web de Azure es una tarea sencilla y puede usarlas en cualquier aplicación que funcione con FTP, incluidos exploradores, como Internet Explorer, y utilidades gratuitas completas, como [FileZilla](https://filezilla-project.org/).  Los sitios web de Azure también son compatibles con el protocolo FTPS, que es más seguro. 

A pesar de que es fácil copiar los archivos del sitio web en Azure con utilidades de FTP, no se ocupan automáticamente de tareas de implementación relacionadas, ni tampoco las coordinan, como la implementación de una base de datos o el cambio de las cadenas de conexión. Además, muchas herramientas de FTP no comparan archivos de origen y archivos de destino para omitir copiar archivos que no se han modificado. En el caso de sitios de gran tamaño, si siempre se copian todos los archivos, la implementación lleva demasiado tiempo incluso para actualizaciones pequeñas, debido a que siempre se copian todos los archivos.

Para obtener más información, consulte los siguientes recursos:

* [Creación de un sitio web PHP-MySQL de Azure e implementación mediante FTP](../web-sites-php-mysql-deploy-use-ftp/).
* [Administración de sitios web](../web-sites-manage/#ftp-credentials). Incluye información adicional que no se encuentra en el tutorial de PHP sobre cómo definir credenciales de FTP. 


##<a name="onpremises"></a>Implementación desde un sistema de control de código fuente local

Si utiliza TFS, Git o Mercurial en un repositorio local (no hospedado en la nube), puede implementar directamente desde el repositorio en Sitios web Azure.

###<a name="tfs"></a>Team Foundation Server (TFS)

Team Foundation Server es la solución local de Microsoft para el control de código fuente y la colaboración en equipo. Puede configurar TFS para que realice entrega continua a un sitio web de Azure.

Para obtener más información, consulte el siguiente recurso:

* [Entrega continua para Servicios en la nube de Azure](../cloud-services-dotnet-continuous-delivery/). Este documento es para un servicio en la nube de Azure, pero parte del contenido también es relevante para los sitios web.

###<a name="gitmercurial"></a>Repositorios locales de Git o Mercurial

En Azure puede escribir la dirección URL de cualquier repositorio que utilice Git o Mercurial para poder implementar desde esa ubicación. También puede insertar directamente desde un repositorio local de Git en un sitio web de Azure. 

Para obtener más información, consulte los siguientes recursos:

* [Publicación desde el Control de código fuente en Sitios web de Azure con Git](../web-sites-publish-source-control/). Uso de Git para publicar directamente desde su equipo local en un sitio web de Azure (en Azure, este método de publicación se denomina Git local). También muestra cómo habilitar la implementación continua de repositorios Git desde GitHub, CodePlex o BitBucket.
* [Publishing to Azure Web Sites from any git/hg repo](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog que explica la característica de "Repositorio externo" en Sitios web de Azure.
* [Foro de Azure sobre Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).
* [Deploying TWO websites to Azure from one Git Repository](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). Publicación en el blog de Scott Hanselman.











##<a name="commandline"></a> Uso de herramientas de línea de comandos y la API de REST de administración de Azure

Siempre es mejor automatizar el flujo de trabajo del desarrollo, pero si no puede hacerlo directamente en el sistema de control de código fuente, puede configurarlo manualmente a través de herramientas de línea de comandos. Esto generalmente implica usar más de una herramienta o marco de trabajo, dado que la implementación a menudo requiere ejecutar funciones de administración de sitios, además de copiar contenido.

Azure simplifica las tareas de administración de sitios que probablemente tenga que realizar para la implementación al proporcionar una API REST de administración y varios marcos de trabajo que facilitan el trabajo con la API.

###<a name=msbuild></a>MSBuild

Si usa el [IDE de Visual Studio](#vs) para el desarrollo, puede utilizar  [MSBuild](http://msbuildbook.com/) para automatizar todo lo que pueda hacer en el IDE. Puede configurar MSBuild para utilizar [Web Deploy](#webdeploy) o [FTP/FTPS](#ftp) para copiar archivos. Web Deploy también puede automatizar muchas otras tareas relacionadas con la implementación, como implementar bases de datos.

Para obtener más información acerca de la implementación de la línea de comandos con MSBuild, consulte los siguientes recursos:

* [Implementación web de ASP.NET con Visual Studio. Implementación de línea de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). El décimo tutorial de una serie sobre la implementación en Azure con Visual Studio. Muestra cómo usar la línea de comandos para la implementación después de haber configurado perfiles de publicación en Visual Studio.
* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build](http://msbuildbook.com/). Copia impresa de un libro que incluye capítulos sobre cómo usar MSBuild para la implementación.

###<a name="ftp2"></a>Scripts de FTP

Crear credenciales de [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para un sitio web de Azure es sencillo y puede usarlas con scripts por lotes de FTP.

Para obtener más información, consulte el siguiente recurso:

* [Utilizar secuencias de comandos por lotes de FTP](http://support.microsoft.com/kb/96269).

###<a name="powershell"></a>Windows PowerShell

Puede realizar funciones de implementación MSBuild o FTP desde [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). Si lo hace, también puede usar una recopilación de cmdlets de Windows PowerShell que hacen que sea fácil llamar a la API REST de administración de Azure.

Para obtener más información, consulte el siguiente recurso:

* [Building Real-World Cloud Apps with Azure - Automate Everything](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Capítulo de un libro electrónico que explica cómo la aplicación de ejemplo que aparece en el libro electrónico usa scripts de Windows PowerShell para crear un entorno de prueba de Azure e implementar en él. Consulte la sección [Resources](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) para ver vínculos a documentación adicional sobre Azure PowerShell.
* [Utilizar scripts de Windows PowerShell para la publicación en entornos de desarrollo y pruebas](http://msdn.microsoft.com/library/dn642480.aspx). Cómo usar los scripts de implementación de Windows PowerShell que genera Visual Studio.

###<a name="api"></a>API de administración de .NET

Puede escribir código de C# para realizar funciones de MSBuild o FTP para implementación. Si lo hace, puede tener acceso a la API REST de administración de Azure para realizar funciones de administración de sitios.

Para obtener más información, consulte el siguiente recurso:

* [Automating everything with the Azure Management Libraries and .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Introducción a la API de administración .NET y vínculos a más documentación.

###<a name="cli"></a>Línea de comandos entre plataformas (xplat-cli)

Puede usar la línea de comandos en máquinas Mac o Linux para implementar mediante FTP. Si lo hace, también puede tener acceso a la API de REST de administración de Azure con la interfaz de línea de comandos entre plataformas de Azure (xplat-cli). xplat-cli también puede utilizarse en equipos con Windows.

Para obtener más información, consulte el siguiente recurso:

* [Herramientas de línea de comandos](/es-es/downloads/#cmd-line-tools). Página de portal en WindowsAzure.com para obtener información sobre la herramienta de línea de comandos.

###<a name="webdeploy"></a>Línea de comandos de Web Deploy

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) es el software de Microsoft para la implementación en IIS que no solo ofrece características inteligentes de sincronización de archivos, sino que también puede realizar o coordinar muchas otras tareas relacionadas con la implementación que no se pueden automatizar cuando usa FTP. Por ejemplo, Web Deploy puede implementar una base de datos nueva o actualizaciones de base de datos junto con su sitio web. Web Deploy también puede minimizar el tiempo que se requiere para actualizar un sitio existente, dado que puede copiar de manera inteligente solo los archivos modificados. Microsoft WebMatrix, Visual Studio, Visual Studio Online y Team Foundation Server cuentan con compatibilidad integrada para Web Deploy, pero solo puede utilizar Web Deploy directamente desde la línea de comandos para automatizar la implementación. Los comandos de Web Deploy son muy poderosos, pero la curva de aprendizaje puede ser pronunciada.

Para obtener más información, consulte el siguiente recurso:

* [Sitios web Azure simples: Implementación](http://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog de David Ebbo acerca de una herramienta que escribió para que Web Deploy resulte más fácil de usar.
* [Web Deployment Tool](http://technet.microsoft.com/library/dd568996). Documentación oficial sobre el sitio de Microsoft TechNet. Información antigua, pero sigue siendo un buen lugar para comenzar.
* [Using Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Documentación oficial sobre el sitio de Microsoft IIS.NET. También es información antigua, pero es un buen lugar para comenzar.
* [StackOverflow](http://www.stackoverflow.com). El mejor sitio para visitar y obtener información más actualizada sobre el uso de Web Deploy desde la línea de comandos.
* [Implementación web de ASP.NET con Visual Studio. Command Line Deployment](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild es el motor de compilación que emplea Visual Studio y que también se puede usar desde la línea de comandos para implementar aplicaciones web en sitios web de Azure. Este tutorial forma parte de una serie que se refiere principalmente a la implementación de Visual Studio.

##<a name="nextstepsPasos siguientes"></a>

En algunos escenarios, es posible que desee poder cambiar fácilmente entre una versión de ensayo y versión una de producción de su sitio web. Puede hacer esto con la característica de implementación de ensayo de Sitios web de Azure. Para obtener más información, vea [Implementación de ensayo en sitios web de Microsoft Azure](../web-sites-staged-publishing/).

Tener un plan de copia de seguridad y restauración es un elemento importante de cualquier flujo de trabajo de implementación. Para obtener información sobre la característica de copia de seguridad y restauración de Sitios web de Azure, consulte [Copias de seguridad de Sitios web Azure](../web-sites-backup/).  

Para obtener información acerca de cómo usar el Control de acceso basado en roles de Azure para administrar el acceso a la implementación del sitio web, consulte [Publicación de Sitios web de Azure y RBAC](http://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing).

Para obtener más información sobre otros temas de implementación, consulte la sección sobre implementación en [Documentación sobre Sitios web Azure](/es-es/documentation/services/web-sites/).




<!--HONumber=42-->
