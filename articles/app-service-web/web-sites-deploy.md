<properties
	pageTitle="Implementar una aplicación web en el servicio de aplicaciones de Azure"
	description="Obtenga información sobre los métodos disponibles para la implementación de contenido en aplicaciones web."
	services="app-service\web"
	documentationCenter=""
	authors="tdykstra"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/21/2015"
	ms.author="tdykstra"/>

#Implementar una aplicación web en el servicio de aplicaciones de Azure

## Información general

Existen varias opciones para implementar su propio contenido en [Aplicaciones web del servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714). En este tema se proporciona información general abreviada de cada opción y vínculos para obtener más información.


###<a name="cloud"></a>Implementación desde un sistema de control de código fuente hospedado en la nube

La mejor manera de implementar una aplicación web es configurar un [flujo de trabajo de entrega continua](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) integrado con el [sistema de control de código fuente](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control). La automatización no solo permite que el proceso de implementación sea más eficaz, sino que también hace que los procesos de copia de seguridad y restauración sean más controlables y confiables.

Si aún no tiene configurado el control de código fuente, la mejor manera de comenzar es usar un sistema de control de código fuente hospedado en la nube.

* [Visual Studio Online](#vso)
* [Sitios web de repositorio con Git](#git)
* [Sitios web de repositorio con Mercurial](#mercurial)
* [Dropbox](#dropbox)

###<a name="ide"></a>Implementación desde un IDE

[Visual Studio](http://www.visualstudio.com/) y [WebMatrix](http://www.microsoft.com/web/webmatrix/) son entornos de desarrollo integrado (IDE) de Microsoft que puede utilizar para el desarrollo web. Ambos proporcionan características integradas que facilitan la implementación de una aplicación web. Ambos pueden utilizar [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) para automatizar tareas adicionales relacionadas con la implementación, como implementación de base de datos y cambios en las cadenas de conexión. Ambos también pueden implementarse con el uso de [FTP o FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol)).

WebMatrix es rápido de instalar y fácil de entender, pero Visual Studio ofrece muchas más características para trabajar con aplicaciones web. Desde el IDE de Visual Studio puede crear, detener, iniciar y eliminar aplicaciones web, puede ver registros en tiempo real a medida que se crean, puede realizar una depuración remota, etc. Visual Studio también se integra con sistemas de control de código fuente como [Visual Studio Online](#vso), [Team Foundation Server](#tfs) y [repositorios Git](#git).

* [Visual Studio](#vs)
* [WebMatrix](#webmatrix)

###<a name="ftp"></a>Implementación mediante una utilidad FTP

Independientemente del IDE que utilice, también puede implementar contenido en su aplicación si usa [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para copiar archivos. Crear credenciales de FTP para una aplicación web es una tarea sencilla y puede usarlas en cualquier aplicación que funcione con FTP, incluidos exploradores, como Internet Explorer, y utilidades gratuitas completas, como [FileZilla](https://filezilla-project.org/). Las aplicaciones web también admiten el protocolo FTPS más seguro.

A pesar de que es fácil copiar los archivos de la aplicación web a Azure con utilidades de FTP, no se ocupan automáticamente de tareas de implementación relacionadas, ni tampoco las coordinan, como la implementación de una base de datos o el cambio de las cadenas de conexión. Además, muchas herramientas de FTP no comparan archivos de origen y archivos de destino para omitir copiar archivos que no se han modificado. En el caso de aplicaciones de gran tamaño, si siempre se copian todos los archivos, la implementación lleva demasiado tiempo incluso para actualizaciones pequeñas, debido a que siempre se copian todos los archivos.

###<a name="onpremises"></a>Implementación desde un sistema de control de código fuente local

Si utiliza TFS, Git o Mercurial en un repositorio local (no hospedado en la nube), puede implementar directamente desde el repositorio a una aplicación web.

* [Team Foundation Server (TFS)](#tfs)
* [Repositorios locales Git o Mercurial locales](#onpremises)

###<a name="commandline"></a>Implementación con herramientas de línea de comandos y la API de administración REST de Azure

Siempre es mejor automatizar el flujo de trabajo del desarrollo, pero si no puede hacerlo directamente en el sistema de control de código fuente, puede configurarlo manualmente a través de herramientas de línea de comandos. Esto generalmente implica usar más de una herramienta o marco de trabajo, dado que la implementación a menudo requiere ejecutar funciones de administración de sitios, además de copiar contenido.

Azure simplifica las tareas de administración de sitios que probablemente tenga que realizar para la implementación al proporcionar una API REST de administración y varios marcos de trabajo que facilitan el trabajo con la API.

* [FTP](#ftp)
* [MSBuild](#msbuild)
* [Scripts de FTP](#ftp2)
* [Windows PowerShell](#powershell)
* [API de administración de .NET](#api)
* [Interfaz de la línea de comandos de Azure (CLI de Azure)](#cli)
* [Línea de comandos de Web Deploy](#webdeploy)
 
###<a name="octopus"></a>Octopus Deploy (Implementación de Octopus)

[Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy) (Implementación de Octopus) se puede usar con Aplicaciones web del Servicio de aplicaciones. Para obtener más información, consulte [Deploy ASP.NET applications to Azure Web Sites](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites) (Implementación de aplicaciones ASP.NET en Sitios web de Azure).


##<a name="vso"></a>Visual Studio Online

[Visual Studio Online](http://www.visualstudio.com/) (anteriormente, Team Foundation Service) es una solución de Microsoft basada en la nube para el control de código fuente y la colaboración en equipo. El servicio es gratuito para un equipo de hasta cinco desarrolladores. Puede hacer la entrega continua a una aplicación web en servicios de aplicaciones y el repositorio puede usar [Git o TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs).

Para obtener más información, consulte los siguientes recursos:

* [Entrega continua a Azure con Visual Studio Online y Git](../cloud-services-continuous-delivery-use-vso.md). Tutorial paso a paso que muestra cómo configurar la entrega continua desde Visual Studio Online a una aplicación web, con TFVC. TFVC es la opción de control de código fuente centralizado, al contrario que Git, que es la opción de control de código fuente distribuido.
* [Entrega continua a Azure con Visual Studio Online y Git](../cloud-services-continuous-delivery-use-vso-git.md). Similar al tutorial anterior, pero usa Git en lugar de TFVC.

##<a name="git"></a>Sitios web de repositorio con Git

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) es un conocido sistema de control de código fuente distribuido. Azure tiene características integradas que facilitan la automatización de una implementación en una aplicación web desde sitios de repositorio populares basados en web que almacenan repositorios Git, incluidos [GitHub](http://www.github.com), [CodePlex](http://www.codeplex.com/) y [BitBucket](https://bitbucket.org/). Una ventaja de usar Git para la implementación es que resulta relativamente fácil volver a una implementación anterior si alguna vez se requiere hacerlo.

Para obtener más información, consulte los siguientes recursos:

* [Publicación desde el control de código fuente en aplicaciones web con Git](web-sites-publish-source-control.md). Cómo usar Git para publicar directamente desde su equipo local en aplicaciones web (en Azure, este método de publicación se denomina Git local). También muestra cómo habilitar la implementación continua de repositorios Git desde GitHub, CodePlex o BitBucket.
* [Implementación en aplicaciones web con GitHub con Kudu](http://azure.microsoft.com/documentation/videos/deploying-to-azure-from-github/). Vídeo de Scott Hanselman y David Ebbo que muestra cómo implementar una aplicación web directamente desde GitHub en aplicaciones web.
* [Implementación en Azure Button para aplicaciones web](http://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/). Blog sobre un método para desencadenar la implementación desde un repositorio Git.
* [Foro de Azure para Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

##<a name="mercurial"></a>Sitios web de repositorio con Mercurial

Si usa [Mercurial](http://mercurial.selenic.com/) como sistema de control de código fuente y almacena el repositorio en [CodePlex](http://www.codeplex.com/) o en [BitBucket](https://bitbucket.org/), puede usar las características integradas en el servicios de aplicaciones de Azure para implementar automáticamente el contenido.

Para obtener información acerca de la implementación con Mercurial, consulte los siguientes recursos:

* [Publicación desde el control de código fuente en aplicaciones web con Git](web-sites-publish-source-control.md). A pesar de que este tutorial muestra cómo publicar un repositorio Git, el proceso para los repositorios de Mercurial hospedados en CodePlex o BitBucket es similar.
* [Foro de Azure para Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

##<a name="dropbox"></a>Dropbox

[Dropbox](https://www.dropbox.com/) no es un sistema de control de código fuente, pero si almacena el código fuente en Dropbox podrá automatizar la implementación desde su cuenta de Dropbox.

* [Implementación en aplicaciones web desde Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Uso del [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715) para configurar una implementación de Dropbox.
* [Implementación de Dropbox en aplicaciones web](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). Este vídeo le guiará en el proceso de conectar una carpeta de Dropbox a una aplicación web y muestra la rapidez con que puede configurar y ejecutar una aplicación web o mantenerla con una sencilla implementación de arrastrar y soltar.
* [Foro de Azure para Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

##<a name="vs"></a>Visual Studio

Para obtener información sobre la implementación en aplicaciones web desde Visual Studio, consulte los siguientes recursos:

* [Introducción a Azure y ASP.NET](web-sites-dotnet-get-started.md). Creación e implementación de un proyecto web ASP.NET MVC simple mediante Visual Studio y Web Deploy.
* [Implementación de WebJobs de Azure con Visual Studio](websites-dotnet-deploy-webjobs.md). Configuración de proyectos de aplicación de consola para que se implementen como trabajos web.  
* [Implementación de una aplicación ASP.NET MVC 5 segura con suscripción, OAuth y base de datos SQL en aplicaciones web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Creación e implementación de un proyecto web ASP.NET MVC con una base de datos SQL mediante Visual Studio, Web Deploy y migraciones de Entity Framework Code First .
* [Información general sobre la implementación web para Visual Studio y ASP.NET](http://msdn.microsoft.com/library/dd394698.aspx). Una introducción básica a la implementación web con Visual Studio. Artículo antiguo, pero que incluye información que sigue siendo pertinente, incluida información general de las opciones para implementar una base de datos junto con la aplicación web, además de una lista de tareas adicionales de implementación que podría tener que hacer o configurar manualmente Visual Studio para que las haga. Este tema trata sobre implementación en general, no solo sobre implementación en aplicaciones web.
* [Implementación web de ASP.NET con Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Una serie de 12 tutoriales que abarca un rango más completo de tareas de implementación que otros de esta lista. Desde que se escribió el tutorial se han agregado algunas características de implementación de Azure, pero las notas agregadas posteriormente explican lo que falta.
* [Implementación de un sitio web ASP.NET en Azure en Visual Studio 2012 desde un repositorio Git directamente](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Explica cómo implementar un proyecto web de ASP.NET en Visual Studio, con el complemento Git para confirmar el código en Git y conectar Azure al repositorio Git. A partir de Visual Studio 2013, la compatibilidad de Git está integrada y no requiere la instalación de un complemento.

##<a name="webmatrix"></a>WebMatrix

Para obtener información sobre la implementación en aplicaciones web desde Webmatrix, consulte los siguientes recursos:

* [Desarrollo e implementación de una aplicación web con Microsoft WebMatrix](web-sites-dotnet-using-webmatrix.md). Cómo crear un sitio web ASP.NET simple con una plantilla WebMatrix e implementarlo en aplicaciones web con WebMatrix y Web Deploy.
* [Creación e implementación de un sitio web Node.js en Azure con WebMatrix](web-sites-nodejs-use-webmatrix.md).
* [Creación e implementación de una aplicación web PHP-MySQL con WebMatrix](web-sites-php-mysql-use-webmatrix.md).
* [WebMatrix 3: Git integrado e implementación en Azure.](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Uso de WebMatrix para implementar desde un repositorio de control de código fuente de Git.

Para obtener más información, consulte los siguientes recursos:

* [Creación de un sitio web PHP-MySQL e implementación con FTP.](web-sites-php-mysql-deploy-use-ftp.md).
* [Administración de Web Apps](web-sites-manage.md#ftp-credentials). Incluye información adicional que no se encuentra en el tutorial de PHP sobre cómo definir credenciales de FTP.

##<a name="tfs"></a>Team Foundation Server (TFS)

Team Foundation Server es la solución local de Microsoft para el control de código fuente y la colaboración en equipo. Puede configurar TFS para realizar entrega continua a una aplicación web.

Para obtener más información, consulte el siguiente recurso:

* [Entrega continua para servicios en la nube en Azure](../cloud-services-dotnet-continuous-delivery.md). Este documento es para un servicio en la nube de Azure, pero parte del contenido también es relevante para aplicaciones web.

##<a name="gitmercurial"></a>Repositorios locales Git o Mercurial

En Azure puede escribir la dirección URL de cualquier repositorio que utilice Git o Mercurial para poder implementar desde esa ubicación. También puede insertar directamente desde un repositorio local Git en una aplicación web.

Para obtener más información, consulte los siguientes recursos:

* [Publicación desde el control de código fuente en aplicaciones web con Git](web-sites-publish-source-control.md). Cómo usar Git para publicar directamente desde su equipo local en una aplicación web (en Azure, este método de publicación se denomina Git local). También muestra cómo habilitar la implementación continua de repositorios Git desde GitHub, CodePlex o BitBucket.
* [Publicación en aplicaciones web desde cualquier repositorio git/hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog que explica la característica "Repositorio externo" en aplicaciones web.
* [Foro de Azure para Git, Mercurial y Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).
* [Implementación de dos sitios web en Azure desde un repositorio Git](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). Publicación en el blog de Scott Hanselman.


##<a name="msbuild"></a>MSBuild

Si usa el [IDE de Visual Studio](#vs) para desarrollo, puede utilizar [MSBuild](http://msbuildbook.com/) para automatizar todo lo que puede hacer en el IDE. Puede configurar MSBuild para usar [Web Deploy](#webdeploy) o [FTP/FTPS](#ftp) para copiar archivos. Web Deploy también puede automatizar muchas otras tareas relacionadas con la implementación, como implementar bases de datos.

Para obtener más información acerca de la implementación de la línea de comandos con MSBuild, consulte los siguientes recursos:

* [Implementación web de ASP.NET con Visual Studio: Implementación de línea de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). El décimo tutorial de una serie sobre la implementación en Azure con Visual Studio. Muestra cómo usar la línea de comandos para la implementación después de haber configurado perfiles de publicación en Visual Studio.
* [Dentro de Microsoft Build Engine: Uso de MSBuild y Team Foundation Build](http://msbuildbook.com/). Copia impresa de un libro que incluye capítulos sobre cómo usar MSBuild para la implementación.

##<a name="ftp2"></a>Scripts de FTP

Crear credenciales de [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para una aplicación web es sencillo y puede usarlas con scripts por lotes de FTP.

Para obtener más información, consulte el siguiente recurso:

* [Uso de scripts por lotes de FTP](http://support.microsoft.com/kb/96269).

##<a name="powershell"></a>Windows PowerShell

Desde [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx) puede ejecutar funciones de implementación MSBuild o FTP. Si lo hace, también puede usar una recopilación de cmdlets de Windows PowerShell que hacen que sea fácil llamar a la API REST de administración de Azure.

Para obtener más información, consulte el siguiente recurso:

* [Creación de aplicaciones reales en la nube con Azure: automatizar todo.](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Capítulo de un libro electrónico que explica cómo la aplicación de ejemplo que aparece en el libro electrónico usa scripts de Windows PowerShell para crear un entorno de prueba de Azure e implementar en él. Consulte la sección [Recursos](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) para ver vínculos a documentación adicional de Azure PowerShell.
* [Uso de scripts de Windows PowerShell para publicar en entornos de prueba y desarrollo](http://msdn.microsoft.com/library/dn642480.aspx). Cómo usar scripts de implementación de Windows PowerShell que genera Visual Studio.

##<a name="api"></a>API de administración de .NET

Puede escribir código de C# para realizar funciones de MSBuild o FTP para implementación. Si lo hace, puede tener acceso a la API REST de administración de Azure para realizar funciones de administración de sitios.

Para obtener más información, consulte el siguiente recurso:

* [Automatización de todo con las bibliotecas de administración de Azure y .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Introducción a la API de administración .NET y vínculos a más documentación.

##<a name="cli"></a>Interfaz de la línea de comandos de Azure (CLI de Azure)

Puede usar la línea de comandos en máquinas Windows, Mac o Linux para implementar mediante FTP. Si lo hace, también puede tener acceso a la API de administración REST de Azure con la CLI de Azure.

Para obtener más información, consulte el siguiente recurso:

* [Herramientas de línea de comandos de Azure](/downloads/#cmd-line-tools). Página de portal en Azure.com para obtener información sobre la herramienta de línea de comandos.

##<a name="webdeploy"></a>Línea de comandos de Web Deploy

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) es el software de Microsoft para la implementación en IIS que no solo ofrece características inteligentes de sincronización de archivos, sino que también puede realizar o coordinar muchas otras tareas relacionadas con la implementación que no se pueden automatizar cuando usa FTP. Por ejemplo, Web Deploy puede implementar una base de datos nueva o actualizaciones de base de datos junto con su aplicación web. Web Deploy también puede minimizar el tiempo que se requiere para actualizar un sitio existente, dado que puede copiar de manera inteligente solo los archivos modificados. Microsoft WebMatrix, Visual Studio, Visual Studio Online y Team Foundation Server cuentan con compatibilidad integrada para Web Deploy, pero solo puede utilizar Web Deploy directamente desde la línea de comandos para automatizar la implementación. Los comandos de Web Deploy son muy poderosos, pero la curva de aprendizaje puede ser pronunciada.

Para obtener más información, consulte el siguiente recurso:

* [Aplicaciones web simples: implementación](http://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog de David Ebbo sobre una herramienta que escribió para facilitar el uso de Web Deploy.
* [Herramienta de implementación web](http://technet.microsoft.com/library/dd568996). Documentación oficial sobre el sitio de Microsoft TechNet. Información antigua, pero sigue siendo un buen lugar para comenzar.
* [Uso de Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Documentación oficial sobre el sitio de Microsoft IIS.NET. También es información antigua, pero es un buen lugar para comenzar.
* [StackOverflow](http://www.stackoverflow.com). El mejor sitio para visitar y obtener información más actualizada sobre el uso de Web Deploy desde la línea de comandos.
* [Implementación web de ASP.NET con Visual Studio: Implementación de línea de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild es el motor de compilación que emplea Visual Studio y que también se puede usar desde la línea de comandos para implementar aplicaciones web en Web Apps. Este tutorial forma parte de una serie que se refiere principalmente a la implementación de Visual Studio.

##<a name="nextsteps"></a>Pasos siguientes

En algunos escenarios, es posible que desee poder cambiar fácilmente entre una versión de ensayo y versión una de producción de su aplicación web. Para obtener más información, consulte [Implementación de ensayo en Web Apps](web-sites-staged-publishing.md).

Tener un plan de copia de seguridad y restauración es un elemento importante de cualquier flujo de trabajo de implementación. Para obtener información sobre la característica de copia de seguridad y restauración de Web Apps, consulte [Copias de seguridad de Web Apps](web-sites-backup.md).

Para obtener información sobre cómo usar el Control de acceso basado en roles de Azure para administrar el acceso a la implementación de Web Apps, consulte [RBAC y la publicación de aplicaciones web](http://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing).

Para obtener información sobre otros temas de implementación, consulte la sección Implementación en la [Documentación de aplicaciones web](/documentation/services/web-sites/).

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=July15_HO3-->