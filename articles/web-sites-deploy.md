<properties  linkid="manage-services-how-to-deploy-websites" urlDisplayName="How to deploy an Azure Web Site" pageTitle="How to deploy an Azure Web Site" metaKeywords="Azure deploy publish web site" description="Learn what methods are available for deploying content to an Azure Web Site." metaCanonical="" services="web-sites" documentationCenter="" title="How to Deploy an Azure Web Site" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

# Implementación de un sitio web de Azure

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

## <a name="cloud"></a>Implementación desde un sistema de control de código fuente hospedado en la nube

La mejor manera de implementar un sitio web es configurar un [flujo de trabajo de entrega continua][1] integrado con el [sistema de control de código fuente][2]. La automatización no solo permite que el proceso de implementación sea más eficaz, sino que también hace que los procesos de copia de seguridad y restauración sean más controlables y confiables.

Si aún no tiene configurado el control de código fuente, la mejor manera de comenzar es usar un sistema de control de código fuente hospedado en la nube.

### <a name="vso"></a>Visual Studio Online (VSO)

[Visual Studio Online][3] (anteriormente, Team Foundation Service) es
una solución de Microsoft basada en la nube para el control de código fuente y la colaboración en equipo. El servicio es gratuito para un equipo de hasta cinco desarrolladores. También puede configurar VSO para realizar la entrega continua a un sitio de web Azure; además, el repositorio puede utilizar [Git o TFVC][3].

Para obtener más información, consulte los siguientes recursos:

* [Implementación continua][4]. Tutorial paso a paso que muestra cómo
  configurar la entrega continua desde VSO a un sitio web de Azure, a
  través de TFVC. TFVC es la opción de control de código fuente
  centralizado de VSO, por oposición a Git, que es la opción de control
  de código fuente distribuido.
* [Entrega continua a Azure con Visual Studio
  Online](/en-us/documentation/articles/cloud-services-continuous-delivery-use-vso/).
  Registro en VSO, registro de un proyecto para el control de código
  fuente y su configuración para la entrega continua (implementación
  automática) a Azure. Está escrita para servicios en la nube, pero gran
  parte del proceso de configuración de la implementación en Visual
  Studio Online es similar para Sitios web.

### <a name="git"></a>Sitios web de repositorio con Git

[Git][3] es un conocido sistema de control de código fuente distribuido. Azure cuenta con características integradas que facilitan la automatización de la implementación en un sitio web de Azure desde conocidos sitios de repositorio basados en web que almacenan repositorios Git, entre los que se incluyen [GitHub][5], [CodePlex][6] y [BitBucket][7]. Una ventaja de usar Git para la implementación es que resulta relativamente fácil volver a una implementación anterior si alguna vez se requiere hacerlo.

Para obtener más información, consulte los siguientes recursos:

* [Publicación en Sitios web Azure desde el control de código
  fuente](/en-us/documentation/articles/web-sites-publish-source-control/).
  Uso de Git para publicar directamente desde su equipo local en un
  sitio web de Azure (en Azure, este método de publicación se denomina
  Git local). También muestra cómo habilitar la implementación continua
  de repositorios Git desde GitHub, CodePlex o BitBucket.
* [Foro de Azure sobre Git, Mercurial y DropBox][8].

### <a name="mercurial"></a>Sitios web de repositorio con Mercurial

Si utiliza [Mercurial][9] como sistema de control de código fuente y almacena el repositorio en [CodePlex][6] o en [BitBucket][7], puede utilizar características integradas en Sitios web Azure para implementar automáticamente el contenido.

Para obtener información acerca de la implementación con Mercurial, consulte los siguientes recursos:

* [Publicación en Sitios web Azure desde el control de código
  fuente](/en-us/documentation/articles/web-sites-publish-source-control/).
  A pesar de que este tutorial muestra cómo publicar un repositorio Git,
  el proceso para los repositorios de Mercurial hospedados en CodePlex o
  BitBucket es similar.
* [Foro de Azure sobre Git, Mercurial y DropBox][8].

### <a name="dropbox"></a>DropBox

[DropBox][10] no es un sistema de control de código fuente, pero si
almacena el código fuente en DropBox podrá automatizar la implementación desde su cuenta de DropBox.

* [Implementación en Azure mediante Dropbox][11]. Cómo usar el Portal de
  administración de Azure para configurar una implementación de DropBox.
* [DropBox and Azure Web Sites][12]. Este vídeo le guiará en el proceso
  de conectar una carpeta de DropBox a un sitio web de Azure y muestra
  la rapidez con que puede configurar y ejecutar un sitio web o
  mantenerlo gracias a una implementación simple a través de arrastrar y
  soltar.
* [Foro de Azure sobre Git, Mercurial y DropBox][8].

## <a name="ide"></a>Implementación desde un IDE

[Visual Studio][13] y [WebMatrix][14] son entornos de desarrollo
integrado (IDE) de Microsoft que puede utilizar para el desarrollo web. Ambos proporcionan características integradas que facilitan la implementan en Sitios web Azure. Ambos pueden utilizar [Web Deploy][15] para automatizar tareas adicionales relacionadas con la implementación, como implementación de base de datos y cambios en las cadenas de conexión. Ambos también pueden implementarse mediante el uso de [FTP o FTPS][16]).

WebMatrix es rápido de instalar y fácil de entender, pero Visual Studio ofrece muchas más características para trabajar con Sitios web Azure. Desde el IDE de Visual Studio puede crear, detener, iniciar y eliminar sitios web de Azure, puede ver registros en tiempo real a medida que se crean, puede realizar una depuración remota, etc. Visual Studio también se integra con sistemas de control de código fuente como [Visual Studio Online](#vso), [Team Foundation Server](#tfs) y [repositorios Git](#git).

### <a name="vs"></a>Visual Studio

Para obtener información sobre la implementación en Sitios web Azure desde Visual Studio, consulte los siguientes recursos:

* [Introducción a Azure y
  ASP.NET](/en-us/develop/net/tutorials/get-started/). Creación e
  implementación de un proyecto web ASP.NET MVC simple mediante Visual
  Studio y Web Deploy.
* [Implementación de una aplicación ASP.NET MVC 5 segura con
  suscripción, OAuth y Base de datos SQL en un sitio web de
  Azure](/en-us/develop/net/tutorials/web-site-with-sql-database/).
  Creación e implementación de un proyecto web ASP.NET MVC con una base
  de datos SQL mediante Visual Studio, Web Deploy y migraciones de
  Entity Framework Code First .
* [Información general sobre la implementación de proyectos de
  aplicación web para Visual Studio y ASP.NET][17]. Una introducción
  básica a la implementación web con Visual Studio. Artículo antiguo,
  pero que incluye información que sigue siendo pertinente, incluida
  información general de las opciones para implementar una base de datos
  junto con la aplicación web, además de una lista de tareas adicionales
  de implementación que podría tener que hacer o configurar manualmente
  Visual Studio para que las haga. Este tema trata sobre implementación
  en general, no solo sobre implementación en Sitios web Azure.
* [Implementación web de ASP.NET con Visual Studio][18]. Una serie de 12
  tutoriales que abarca un rango más completo de tareas de
  implementación que otros de esta lista.
* [Deploying an ASP.NET Website to Azure in Visual Studio 2012 from a
  Git Repository directly][19]. Explica cómo implementar un proyecto web
  de ASP.NET en Visual Studio, con el complemento Git para confirmar el
  código en Git y conectar Azure al repositorio Git.

### <a name="webmatrix"></a>WebMatrix

Para obtener información sobre la implementación en Sitios web Azure desde WebMatrix, consulte los siguientes recursos:

* [Desarrollo e implementación de un sitio web con Microsoft
  WebMatrix][20]. Creación de un sitio web simple de ASP.NET con una
  plantilla de WebMatrix y su implementación en un sitio web de Azure
  mediante WebMatrix y Web Deploy.
* [Compilación e implementación de un sitio web Node.js en Azure con
  WebMatrix][21].
* [Creación de un sitio web Azure de PHP-MySQL e implementación mediante
  WebMatrix][22].
* [WebMatrix 3: Integrated Git and Deployment to Azure][23]. Uso de
  WebMatrix para implementar desde un repositorio de control de código
  fuente de Git.

## <a name="ftp"></a>Implementación mediante el uso de una utilidad de FTP

Independientemente del IDE que utilice, también puede implementar contenido en su sitio si usa [FTP][16] para copiar archivos. Crear credenciales de FTP para un sitio web de Azure es una tarea sencilla y puede usarlas en cualquier aplicación que funcione con FTP, incluidos exploradores, como Internet Explorer, y utilidades gratuitas completas, como [FileZilla][24]. Los sitios web de Azure también son compatibles con el protocolo FTPS, que es más seguro.

A pesar de que es fácil copiar los archivos del sitio web en Azure con utilidades de FTP, no se ocupan automáticamente de tareas de implementación relacionadas, ni tampoco las coordinan, como la implementación de una base de datos o el cambio de las cadenas de conexión. Además, muchas herramientas de FTP no comparan archivos de origen y archivos de destino para omitir copiar archivos que no se han modificado. En el caso de sitios de gran tamaño, si siempre se copian todos los archivos, la implementación lleva demasiado tiempo incluso para actualizaciones pequeñas, debido a que siempre se copian todos los archivos.

Para obtener más información, consulte los siguientes recursos:

* [Creación de un sitio web PHP-MySQL de Azure e implementación mediante
  FTP](/en-us/documentation/articles/web-sites-php-mysql-deploy-use-ftp/).
* [Administración de sitios web][25]. Incluye información adicional que
  no se encuentra en el tutorial de PHP sobre cómo definir credenciales
  de FTP. Consulte los comentarios que aparecen al final de la página
  para obtener información que no se encuentra en el documento mismo
  sobre cómo obtener las credenciales.

## <a name="onpremises"></a>Implementación desde un sistema de control de código fuente local

Si utiliza TFS, Git o Mercurial en un repositorio local (no hospedado en la nube), puede implementar directamente desde el repositorio en Sitios web Azure.

### <a name="tfs"></a>Team Foundation Server (TFS)

Team Foundation Server es la solución local de Microsoft para el control de código fuente y la colaboración en equipo. Puede configurar TFS para que realice entrega continua a un sitio web de Azure.

Para obtener más información, consulte el siguiente recurso:

* [Entrega continua para Servicios en la nube de
  Azure](/en-us/develop/net/common-tasks/continuous-delivery/). Este
  documento es para un servicio en la nube de Azure, pero parte del
  contenido también es relevante para los sitios web.

### <a name="gitmercurial"></a>Repositorios locales de Git o Mercurial

En Azure puede escribir la dirección URL de cualquier repositorio que utilice Git o Mercurial para poder implementar desde esa ubicación. También puede insertar directamente desde un repositorio local de Git en un sitio web de Azure.

Para obtener más información, consulte los siguientes recursos:

* [Publicación en Sitios web Azure desde el control de código
  fuente](/en-us/documentation/articles/web-sites-publish-source-control/).
  Uso de Git para publicar directamente desde su equipo local en un
  sitio web de Azure (en Azure, este método de publicación se denomina
  Git local). También muestra cómo habilitar la implementación continua
  de repositorios Git desde GitHub, CodePlex o BitBucket.
* [Publishing to Azure Web Sites from any git/hg repo][26]. Blog de
  David Ebbo en que se explica la característica de "repositorio
  externo" en Sitios web Azure.
* [Foro de Azure sobre Git, Mercurial y DropBox][8].
* [Deploying TWO websites to Azure from one Git Repository][27].
  Publicación en el blog de Scott Hanselman.

## <a name="commandline"></a> Uso de herramientas de línea de comandos y la API REST de administración de Azure

Siempre es mejor automatizar el flujo de trabajo del desarrollo, pero si no puede hacerlo directamente en el sistema de control de código fuente, puede configurarlo manualmente a través de herramientas de línea de comandos. Esto generalmente implica usar más de una herramienta o marco de trabajo, dado que la implementación a menudo requiere ejecutar funciones de administración de sitios, además de copiar contenido.

Azure simplifica las tareas de administración de sitios que probablemente tenga que realizar para la implementación al proporcionar una API REST de administración y varios marcos de trabajo que facilitan el trabajo con la API.

### <a name=msbuild></a>MSBuild

Si usa el [IDE de Visual Studio](#vs) para el desarrollo, puede utilizar [MSBuild][28] para automatizar todo lo que pueda hacer en el IDE. Puede configurar MSBuild para utilizar [Web Deploy](#webdeploy) o [FTP/FTPS](#ftp) para copiar archivos. Web Deploy también puede automatizar muchas otras tareas relacionadas con la implementación, como implementar bases de datos.

Para obtener más información acerca de la implementación de la línea de comandos con MSBuild, consulte los siguientes recursos:

* [Implementación web de ASP.NET con Visual Studio: Command Line
  Deployment][29]. El décimo tutorial de una serie sobre la
  implementación en Azure con Visual Studio. Muestra cómo usar la línea
  de comandos para la implementación después de haber configurado
  perfiles de publicación en Visual Studio.
* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation
  Build][28]. Copia impresa de un libro que incluye capítulos sobre cómo
  usar MSBuild para la implementación.

### <a name="ftp2"></a>Scripts de FTP

Crear credenciales de [FTP/FTPS][16] para un sitio web de Azure es sencillo y puede usarlas con scripts por lotes de FTP.

Para obtener más información, consulte el siguiente recurso:

* [Utilizar secuencias de comandos por lotes de FTP][30].

### <a name="powershell"></a>Windows PowerShell

Puede realizar funciones de MSBuild o FTP desde [Windows PowerShell][31]. Si lo hace, también puede usar una recopilación de cmdlets de Windows PowerShell que hacen que sea fácil llamar a la API REST de administración de Azure.

Para obtener más información, consulte el siguiente recurso:

* [Building Real-World Cloud Apps with Azure - Automate Everything][32].
  Capítulo de un libro electrónico que explica cómo la aplicación de
  ejemplo que aparece en el libro electrónico usa scripts de Windows
  PowerShell para crear un entorno de prueba de Azure e implementar en
  él. Consulte la sección [Resources][33] para ver vínculos a
  documentación adicional sobre Azure PowerShell.

### <a name="api"></a>API de administración de .NET

Puede escribir código de C# para realizar funciones de MSBuild o FTP para implementación. Si lo hace, puede tener acceso a la API REST de administración de Azure para realizar funciones de administración de sitios.

Para obtener más información, consulte el siguiente recurso:

* [Automating everything with the Azure Management Libraries and
  .NET][34]. Introducción a la API de administración .NET y vínculos a
  más documentación.

### <a name="cli"></a>Línea de comandos entre plataformas (xpat-cli)

Puede usar la línea de comandos en máquinas Mac o Linux para implementar mediante FTP. Si lo hace, también puede tener acceso a la API REST de administración de Azure con la interfaz de línea de comandos entre plataformas de Azure (xpat-cli). La xpat-cli también se puede usar en máquinas Windows.

Para obtener más información, consulte el siguiente recurso:

* [Herramientas de línea de comandos](/en-us/downloads/#cmd-line-tools).
  Página de portal en WindowsAzure.com para obtener información sobre la herramienta de línea de comandos.

### <a name="webdeploy"></a>Línea de comandos de Web Deploy

[Web Deploy][15] es el software de Microsoft para la implementación en IIS que no solo ofrece características inteligentes de sincronización de archivos, sino que también puede realizar o coordinar muchas otras tareas relacionadas con la implementación que no se pueden automatizar cuando usa FTP. Por ejemplo, Web Deploy puede implementar una base de datos nueva o actualizaciones de base de datos junto con su sitio web. Web Deploy también puede minimizar el tiempo que se requiere para actualizar un sitio existente, dado que puede copiar de manera inteligente solo los archivos modificados. Microsoft WebMatrix, Visual Studio, Visual Studio Online y Team Foundation Server cuentan con compatibilidad integrada para Web Deploy, pero solo puede utilizar Web Deploy directamente desde la línea de comandos para automatizar la implementación. Los comandos de Web Deploy son muy poderosos, pero la curva de aprendizaje puede ser pronunciada.

Para obtener más información, consulte el siguiente recurso:

* [Web Deployment Tool][35]. Documentación oficial sobre el sitio de
  Microsoft TechNet. Información antigua, pero sigue siendo un buen
  lugar para comenzar.
* [Using Web Deploy][36]. Documentación oficial sobre el sitio de
  Microsoft IIS.NET. También es información antigua, pero es un buen
  lugar para comenzar.
* [StackOverflow][37]. El mejor sitio para visitar y obtener información
  más actualizada sobre el uso de Web Deploy desde la línea de comandos.
* [Implementación web de ASP.NET con Visual Studio: Command Line
  Deployment][29]. MSBuild es el motor de compilación que emplea Visual
  Studio y que también se puede usar desde la línea de comandos para
  implementar aplicaciones web en sitios web de Azure. Este tutorial
  forma parte de una serie que se refiere principalmente a la
  implementación de Visual Studio.

## <a name="nextsteps"></a>Pasos siguientes

Para obtener más información, consulte la sección sobre implementación en [Documentación sobre Sitios web Azure](/en-us/documentation/services/web-sites/).



[1]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
[2]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
[3]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs
[4]: http://www.visualstudio.com/en-us/learn/continuous-delivery-in-vs
[5]: http://www.github.com
[6]: http://www.codeplex.com/
[7]: https://bitbucket.org/
[8]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit
[9]: http://mercurial.selenic.com/
[10]: https://www.dropbox.com/
[11]: http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx
[12]: http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites
[13]: http://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx
[14]: http://www.microsoft.com/web/webmatrix/
[15]: http://www.iis.net/downloads/microsoft/web-deploy
[16]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
[17]: http://msdn.microsoft.com/en-us/library/dd394698.aspx
[18]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction
[19]: http://www.dotnetcurry.com/ShowArticle.aspx?ID=881
[20]: http://www.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-using-webmatrix/
[21]: http://www.windowsazure.com/en-us/documentation/articles/web-sites-nodejs-use-webmatrix/
[22]: http://www.windowsazure.com/en-us/documentation/articles/web-sites-php-mysql-use-webmatrix/
[23]: http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD
[24]: https://filezilla-project.org/
[25]: http://www.windowsazure.com/en-us/documentation/articles/web-sites-manage/
[26]: http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html
[27]: http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx
[28]: http://msbuildbook.com/
[29]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment
[30]: http://support.microsoft.com/kb/96269
[31]: http://msdn.microsoft.com/en-us/library/dd835506.aspx
[32]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
[33]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources
[34]: http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx
[35]: http://technet.microsoft.com/en-us/library/dd568996
[36]: http://www.iis.net/learn/publish/using-web-deploy
[37]: http://www.stackoverflow.com