<properties pageTitle="Implementación de un sitio web de Azure" metaKeywords="implementar o publicar un sitio web de Azure" description="Obtenga información acerca de los métodos que están disponibles para implementar contenido en un sitio web de Azure." metaCanonical="" services="web-sites" documentationCenter="" title="How to Deploy an Azure Website" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/02/2014" ms.author="tdykstra" />

#Implementación de un sitio web de Azure

Existen varias opciones para implementar su propio contenido en un sitio web de Azure. En este tema se proporciona información general abreviada de cada opción y vínculos para obtener más información.

* [Implementación desde un sistema de control de código fuente hospedado en la nube](#cloud)
	* Visual Studio Online (VSO)
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

###<a name="vso"></a>Visual Studio Online (VSO)

[Visual Studio Online](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) (anteriormente, Team Foundation Service) es una solución de Microsoft basada en la nube para el control de código fuente y la colaboración en equipo. El servicio es gratuito para un equipo de hasta cinco desarrolladores. También puede configurar VSO para realizar la entrega continua a un sitio de web Azure; además, el repositorio puede utilizar [Git o TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs).

Para obtener más información, consulte los siguientes recursos:

* [Entrega continua a Azure con VSO y TFVC](http://www.visualstudio.com/es-es/learn/continuous-delivery-in-vs). Breve tutorial paso a paso que muestra cómo configurar la entrega continua desde VSO a un sitio web de Azure, a través de TFVC. TFVC es la opción de control de código fuente centralizado de VSO, por oposición a Git, que es la opción de control de código fuente distribuido.
* [Entrega continua a Azure mediante VSO y TFVC](/es-es/documentation/articles/cloud-services-continuous-delivery-use-vso/). Similar al tutorial anterior, pero este describe los pasos para registrarse en una cuenta VSO y proteger un proyecto en el control de recursos.
* [Entrega continua a Azure con Visual Studio Online y Git](http://azure.microsoft.com/es-es/documentation/articles/cloud-services-continuous-delivery-use-vso-git/). Similar al tutorial anterior, pero usa Git en lugar de TFVC.

###<a name="git"></a>Sitios web de repositorio con Git

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) es un conocido sistema de control de código fuente distribuido. Azure cuenta con características integradas que facilitan la automatización de la implementación en un sitio web de Azure desde conocidos sitios de repositorio basados en web que almacenan repositorios Git, entre los que se incluyen [GitHub](http://www.github.com), [CodePlex](http://www.codeplex.com/) y [BitBucket](https://bitbucket.org/). Una ventaja de usar Git para la implementación es que resulta relativamente fácil volver a una implementación anterior si alguna vez se requiere hacerlo. 

Para obtener más información, consulte los siguientes recursos:

* [Publicación en Sitios web Azure desde el control de código fuente](/es-es/documentation/articles/web-sites-publish-source-control/). Uso de Git para publicar directamente desde su equipo local en un sitio web de Azure (en Azure, este método de publicación se denomina Git local). También muestra cómo habilitar la implementación continua de repositorios Git desde GitHub, CodePlex o BitBucket.
* [Implementación en Sitios web con GitHub mediante Kudu](/es-es/documentation/videos/deploying-to-azure-from-github/). Vídeo de Scott Hanselman y David Ebbo que muestra cómo implementar un sitio web directamente desde GitHub a un sitio web de Azure.
* [Foro de Azure sobre Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="mercurial"></a>Sitios web de repositorio con Mercurial

Si usa [Mercurial](http://mercurial.selenic.com/) como sistema de control de código fuente y almacena el repositorio en [CodePlex](http://www.codeplex.com/) o [BitBucket](https://bitbucket.org/), puede utilizar características integradas en Sitios web Azure para implementar automáticamente el contenido.

Para obtener información acerca de la implementación con Mercurial, consulte los siguientes recursos:

* [Publicación en Sitios web Azure desde el control de código fuente](/es-es/documentation/articles/web-sites-publish-source-control/). A pesar de que este tutorial muestra cómo publicar un repositorio Git, el proceso para los repositorios de Mercurial hospedados en CodePlex o BitBucket es similar.
* [Foro de Azure sobre Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="dropbox"></a>Dropbox

[Dropbox](https://www.dropbox.com/) no es un sistema de control de código fuente, pero si almacena el código fuente en Dropbox podrá automatizar la implementación desde su cuenta de Dropbox.

* [Implementación en Azure mediante Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Cómo usar el Portal de administración de Azure para configurar una implementación de Dropbox.
* [Dropbox y Sitios web de Azure](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). Este vídeo le guiará en el proceso de conectar una carpeta de Dropbox a un sitio web de Azure y muestra la rapidez con que puede configurar y ejecutar un sitio web o mantenerlo gracias a una implementación simple a través de arrastrar y soltar.
* [Foro de Azure sobre Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).







##<a name="ide"></a>Implementación desde un IDE

[Visual Studio](http://www.visualstudio.com/es-es/downloads/download-visual-studio-vs.aspx) y [WebMatrix](http://www.microsoft.com/web/webmatrix/) son entornos de desarrollo integrado (IDE) de Microsoft que puede utilizar para el desarrollo web. Ambos proporcionan características integradas que facilitan la implementan en Sitios web Azure.  Ambos pueden utilizar [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) para automatizar tareas adicionales relacionadas con la implementación, como implementación de base de datos y cambios en las cadenas de conexión. Ambos también pueden implementarse mediante el uso de [FTP o FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol)). 

WebMatrix es rápido de instalar y fácil de entender, pero Visual Studio ofrece muchas más características para trabajar con Sitios web Azure. Desde el IDE de Visual Studio puede crear, detener, iniciar y eliminar sitios web de Azure, puede ver registros en tiempo real a medida que se crean, puede realizar una depuración remota, etc. Visual Studio también se integra con sistemas de control de código fuente como [Visual Studio Online](#vso), [Team Foundation Server](#tfs) y [repositorios Git](#git).

###<a name="vs"></a>Visual Studio

Para obtener información sobre la implementación en Sitios web Azure desde Visual Studio, consulte los siguientes recursos:

* [Introducción a Azure y ASP.NET](/es-es/develop/net/tutorials/get-started/). Creación e implementación de un proyecto web ASP.NET MVC simple mediante Visual Studio y Web Deploy.
* [Implementación de WebJobs de Azure a Sitios web de Azure](/es-es/documentation/articles/websites-dotnet-deploy-webjobs/). Configuración de proyectos de aplicación de consola para que se implementen como trabajos web.  
* [Implementación de una aplicación ASP.NET MVC 5 segura con suscripción, OAuth y Base de datos SQL en un sitio web de Azure](/es-es/develop/net/tutorials/web-site-with-sql-database/). Creación e implementación de un proyecto web ASP.NET MVC con una base de datos SQL mediante Visual Studio, Web Deploy y migraciones de Entity Framework Code First.
* [Información general sobre la implementación de proyectos de aplicación web para Visual Studio y ASP.NET](http://msdn.microsoft.com/es-es/library/dd394698.aspx). Una introducción básica a la implementación web con Visual Studio. Artículo antiguo, pero que incluye información que sigue siendo pertinente, incluida información general de las opciones para implementar una base de datos junto con la aplicación web, además de una lista de tareas adicionales de implementación que podría tener que hacer o configurar manualmente Visual Studio para que las haga. Este tema trata sobre implementación en general, no solo sobre implementación en Sitios web Azure.
* [Implementación web de ASP.NET con Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Una serie de 12 tutoriales que abarca un rango más completo de tareas de implementación que otros de esta lista. 
* [Deploying an ASP.NET Website to Azure in Visual Studio 2012 from a Git Repository directly](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Explica cómo implementar un proyecto web de ASP.NET en Visual Studio, con el complemento Git para confirmar el código en Git y conectar Azure al repositorio Git.

###<a name="webmatrix"></a>WebMatrix

Para obtener información sobre la implementación en Sitios web Azure desde WebMatrix, consulte los siguientes recursos:

* [Desarrollo e implementación de un sitio web con Microsoft WebMatrix](http://www.windowsazure.com/es-es/documentation/articles/web-sites-dotnet-using-webmatrix/). Creación de un sitio web simple de ASP.NET con una plantilla de WebMatrix y su implementación en un sitio web de Azure mediante WebMatrix y Web Deploy.
* [Compilación e implementación de un sitio web Node.js en Azure con WebMatrix](http://www.windowsazure.com/es-es/documentation/articles/web-sites-nodejs-use-webmatrix/).
* [Creación de un sitio web Azure de PHP-MySQL e implementación mediante WebMatrix](http://www.windowsazure.com/es-es/documentation/articles/web-sites-php-mysql-use-webmatrix/).
* [WebMatrix 3: Integrated Git and Deployment to Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Uso de WebMatrix para implementar desde un repositorio de control de código fuente de Git.

##<a name="ftp"></a>Implementación mediante el uso de una utilidad de FTP

Independientemente del IDE que utilice, también puede implementar contenido en su sitio si usa [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para copiar archivos. Crear credenciales de FTP para un sitio web de Azure es una tarea sencilla y puede usarlas en cualquier aplicación que funcione con FTP, incluidos exploradores, como Internet Explorer, y utilidades gratuitas completas, como [FileZilla](https://filezilla-project.org/).  Los sitios web de Azure también son compatibles con el protocolo FTPS, que es más seguro. 

A pesar de que es fácil copiar los archivos del sitio web en Azure con utilidades de FTP, no se ocupan automáticamente de tareas de implementación relacionadas, ni tampoco las coordinan, como la implementación de una base de datos o el cambio de las cadenas de conexión. Además, muchas herramientas de FTP no comparan archivos de origen y archivos de destino para omitir copiar archivos que no se han modificado. En el caso de sitios de gran tamaño, si siempre se copian todos los archivos, la implementación lleva demasiado tiempo incluso para actualizaciones pequeñas, debido a que siempre se copian todos los archivos.

Para obtener más información, consulte los siguientes recursos:

* [Creación de un sitio web PHP-MySQL de Azure e implementación mediante FTP](/es-es/documentation/articles/web-sites-php-mysql-deploy-use-ftp/). 
* [Administración de sitios web](http://www.windowsazure.com/es-es/documentation/articles/web-sites-manage#ftp-credentials). Incluye información adicional que no se encuentra en el tutorial de PHP sobre cómo definir credenciales de FTP. 


##<a name="onpremises"></a>Implementación desde un sistema de control de código fuente local

Si utiliza TFS, Git o Mercurial en un repositorio local (no hospedado en la nube), puede implementar directamente desde el repositorio en Sitios web Azure.

###<a name="tfs"></a>Team Foundation Server (TFS)

Team Foundation Server es la solución local de Microsoft para el control de código fuente y la colaboración en equipo. Puede configurar TFS para que realice entrega continua a un sitio web de Azure.

Para obtener más información, consulte el siguiente recurso:

* [Entrega continua para Servicios en la nube de Azure](/es-es/develop/net/common-tasks/continuous-delivery/). Este documento es para un servicio en la nube de Azure, pero parte del contenido también es relevante para los sitios web.

###<a name="gitmercurial"></a>Repositorios locales de Git o Mercurial

En Azure puede escribir la dirección URL de cualquier repositorio que utilice Git o Mercurial para poder implementar desde esa ubicación. También puede insertar directamente desde un repositorio local de Git en un sitio web de Azure. 

Para obtener más información, consulte los siguientes recursos:

* [Publicación en Sitios web Azure desde el control de código fuente](/es-es/documentation/articles/web-sites-publish-source-control/). Uso de Git para publicar directamente desde su equipo local en un sitio web de Azure (en Azure, este método de publicación se denomina Git local). También muestra cómo habilitar la implementación continua de repositorios Git desde GitHub, CodePlex o BitBucket.
* [Publishing to Azure Web Sites from any git/hg repo](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog de David Ebbo en que se explica la característica de "repositorio externo" en Sitios web Azure.
* [Foro de Azure sobre Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).
* [Deploying TWO websites to Azure from one Git Repository](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). Publicación en el blog de Scott Hanselman.











##<a name="commandline"></a> Uso de herramientas de línea de comandos y la API REST de administración de Azure

Siempre es mejor automatizar el flujo de trabajo del desarrollo, pero si no puede hacerlo directamente en el sistema de control de código fuente, puede configurarlo manualmente a través de herramientas de línea de comandos. Esto generalmente implica usar más de una herramienta o marco de trabajo, dado que la implementación a menudo requiere ejecutar funciones de administración de sitios, además de copiar contenido.

Azure simplifica las tareas de administración de sitios que probablemente tenga que realizar para la implementación al proporcionar una API REST de administración y varios marcos de trabajo que facilitan el trabajo con la API.

###<a name=msbuild></a>MSBuild

If you use the [Visual Studio IDE](#vs) for development, you can use [MSBuild](http://msbuildbook.com/) to automate anything you can do in your IDE. You can configure MSBuild to use either [Web Deploy](#webdeploy) or [FTP/FTPS](#ftp) to copy files. Web Deploy can also automate many other deployment-related tasks, such as deploying databases.

For more information about command-line deployment using MSBuild, see the following resources:

* [ASP.NET Web Deployment using Visual Studio: Command Line Deployment](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Tenth in a series of tutorials about deployment to Azure using Visual Studio. Shows how to use the command line to deploy after setting up publish profiles in Visual Studio.
* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build](http://msbuildbook.com/). Hard-copy book that includes chapters on how to use MSBuild for deployment.

###<a name="ftp2"></a>Scripts de FTP

Crear credenciales de [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para un sitio web de Azure es sencillo y puede usarlas con scripts por lotes de FTP.

Para obtener más información, consulte el siguiente recurso:

* [Utilizar secuencias de comandos por lotes de FTP](http://support.microsoft.com/kb/96269).

###<a name="powershell"></a>Windows PowerShell

Puede realizar funciones de implementación MSBuild o FTP desde [Windows PowerShell](http://msdn.microsoft.com/es-es/library/dd835506.aspx). Si lo hace, también puede usar una recopilación de cmdlets de Windows PowerShell que hacen que sea fácil llamar a la API REST de administración de Azure.

Para obtener más información, consulte el siguiente recurso:

* [Building Real-World Cloud Apps with Azure - Automate Everything](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Capítulo de un libro electrónico que explica cómo la aplicación de ejemplo que aparece en el libro electrónico usa scripts de Windows PowerShell para crear un entorno de prueba de Azure e implementar en él. Consulte la sección [Resources](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) para ver vínculos a documentación adicional sobre Azure PowerShell.

###<a name="api"></a>API de administración de .NET

Puede escribir código de C# para realizar funciones de MSBuild o FTP para implementación. Si lo hace, puede tener acceso a la API REST de administración de Azure para realizar funciones de administración de sitios.

Para obtener más información, consulte el siguiente recurso:

* [Automating everything with the Azure Management Libraries and .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Introducción a la API de administración .NET y vínculos a más documentación.

###<a name="cli"></a>Línea de comandos entre plataformas (xpat-cli)

Puede usar la línea de comandos en máquinas Mac o Linux para implementar mediante FTP. Si lo hace, también puede tener acceso a la API REST de administración de Azure con la interfaz de línea de comandos entre plataformas de Azure (xpat-cli). La xpat-cli también se puede usar en máquinas Windows.

Para obtener más información, consulte el siguiente recurso:

* [Herramientas de línea de comandos](/es-es/downloads/#cmd-line-tools). Portal page in WindowsAzure.com for command line tool information.

###<a name="webdeploy"></a>Web Deploy command line

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) is Microsoft software for deployment to IIS that not only provides intelligent file sync features but also can perform or coordinate many other deployment-related tasks that can't be automated when you use FTP. For example, Web Deploy can deploy a new database or database updates along with your website. Web Deploy can also minimize the time required to update an existing site since it can intelligently copy only changed files. Microsoft WebMatrix, Visual Studio, Visual Studio Online, and Team Foundation Server have support for Web Deploy built-in, but you can also use Web Deploy directly from the command line to automate deployment. Web Deploy commands are very powerful but the learning curve can be steep.

For more information, see the following resource:

* [Web Deployment Tool](http://technet.microsoft.com/es-es/library/dd568996). Official documentation on the Microsoft TechNet site. Dated but still a good place to start.
* [Using Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Official documentation on the Microsoft IIS.NET site. Also dated but a good place to start.
* [StackOverflow](http://www.stackoverflow.com). The best place to go for more current information about how to use Web Deploy from the command line.
* [ASP.NET Web Deployment using Visual Studio: Command Line Deployment](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild is the build engine used by Visual Studio, and it can also be used from the command line to deploy web applications to Azure Websites. This tutorial is part of a series that is mainly about Visual Studio deployment.

##<a name="nextsteps"></a>Next Steps

In some scenarios you might want to be able to easily switch back and forth between a staging and a production version of your website. You can do this with the Azure Websites staged deployment feature. For more information, see [Staged Deployment on Microsoft Azure Web Sites](/es-es/documentation/articles/web-sites-staged-publishing/).

Tener un plan de copia de seguridad y restauración es un elemento importante de cualquier flujo de trabajo de implementación. Para obtener información sobre la característica de copia de seguridad y restauración de Sitios web de Azure, consulte [Copias de seguridad de Sitios web Azure](/es-es/documentation/articles/web-sites-backup/).  

Para obtener más información sobre otros temas de implementación, consulte la sección sobre implementación en [Documentación sobre Sitios web Azure](/es-es/documentation/services/web-sites/).
