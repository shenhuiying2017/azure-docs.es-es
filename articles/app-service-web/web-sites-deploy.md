---
title: "Implementación de su aplicación en Azure App Service | Microsoft Docs"
description: "Obtenga más información sobre cómo implementar la aplicación en el Servicio de aplicaciones de Azure."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: f1464f71-2624-400e-86a2-e687e385804f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: cephalin;dariac
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 7eff46740cdbc8467c711aada5e6022d82930e31
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017


---
# <a name="deploy-your-app-to-azure-app-service"></a>Documentación de implementación del Servicio de aplicaciones de Azure
Este artículo le ayudará a determinar la mejor opción para implementar los archivos de su aplicación web, back-end de aplicación móvil o aplicación de API en el [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714)y, a continuación, le guiará a los recursos adecuados con instrucciones específicas para la opción de su preferencia.

## <a name="overview"></a>Descripción general de la implementación de Servicio de aplicaciones de Azure
El Servicio de aplicaciones de Azure mantiene el marco de la aplicación para usted (ASP.NET, PHP, Node.js, etc.). Algunos marcos están habilitados de forma predeterminada, mientras que es posible que otros, como Java y Python, necesiten una configuración de marca de verificación sencilla para habilitarlos. Además, puede personalizar el marco de la aplicación, como la versión de PHP o el valor de bits del tiempo de ejecución. Para obtener más información, consulte [Configuración de aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-configure.md).

Como no tiene que preocuparse por el marco de la aplicación o del servidor web, la implementación de la aplicación en App Service consiste en implementar código, archivos binarios, archivos de contenido y su estructura de directorios correspondiente en el directorio [**/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) en Azure (o el directorio **/site/wwwroot/App_Data/Jobs/** para WebJobs). App Service admite tres procesos de implementación diferentes. Todos los métodos de implementación de este artículo usan uno de los siguientes procesos: 

* [FTP o FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol) use sus FTP o FTPS favoritos habilitados para mover los archivos a Azure, desde [FileZilla](https://filezilla-project.org) hasta IDE completos como [NetBeans](https://netbeans.org). Se trata estrictamente de un proceso de carga de archivos. El Servicio de aplicaciones no proporciona servicios adicionales, como control de versiones, administración de estructura de archivos, etc. 
* [Kudu (Git/Mercurial o OneDrive/Dropbox)](https://github.com/projectkudu/kudu/wiki/Deployment): Kudu es el [motor de implementación](https://github.com/projectkudu/kudu/wiki) en App Service. Inserte el código en Kudu directamente desde cualquier repositorio. Kudu también proporciona servicios agregados siempre que se inserte código en él, como por ejemplo, control de versiones, restauración de paquetes, MSBuild y [enlaces web](https://github.com/projectkudu/kudu/wiki/Web-hooks) , para la implementación continua y otras tareas de automatización. El motor de implementación de Kudu es compatible con 3 tipos distintos de orígenes de implementación:   
  
  * Sincronización de contenido de OneDrive y Dropbox.   
  * Implementación continua basada en repositorio con sincronización automática desde GitHub, Bitbucket y Visual Studio Team Services.  
  * Implementación basada en repositorio con sincronización manual desde Git local.  
* [Web Deploy](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): implemente código en App Service directamente desde las herramientas de Microsoft de su preferencia, como Visual Studio, con las mismas herramientas que automatizan la implementación en servidores de IIS. Esta herramienta es compatible con la implementación de solo diferencias, la creación de bases de datos, las transformaciones de cadenas de conexión, etc. Web Deploy difiere de Kudu en que los archivos binarios de aplicación se compilan antes de implementarlos en Azure. De forma similar a FTP, el Servicio de aplicaciones no proporciona servicios adicionales.

Las herramientas de desarrollo web más populares admiten uno o varios de estos procesos de implementación. Aunque la herramienta que elija determina los procesos de implementación que puede aprovechar, la funcionalidad de DevOps real a su disposición depende de la combinación del proceso de implementación y las herramientas específicas que elija. Por ejemplo, si ejecuta Web Deploy desde [Visual Studio con Azure SDK](#vspros), aunque no obtenga la automatización de Kudu, tendrá la restauración de paquetes y la automatización de MSBuild en Visual Studio. 

> [!NOTE]
> Estos procesos de implementación no [aprovisionan realmente los recursos de Azure](../azure-resource-manager/resource-group-template-deploy-portal.md) que puede necesitar la aplicación. Sin embargo, la mayoría de los artículos de procedimientos vinculados muestra cómo aprovisionar la aplicación e implementar el código en ella de un extremo a otro. También puede encontrar opciones adicionales para el aprovisionamiento de recursos de Azure en la sección [Automatización de la implementación mediante herramientas de línea de comandos](#automate) .
> 
> 

## <a name="ftp"></a>Implementación manual mediante la carga de archivos con FTP
Si está familiarizado con la copia manual de contenido web en un servidor web, puede usar una utilidad [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para copiar archivos, como el Explorador de Windows o [FileZilla](https://filezilla-project.org/).

Las ventajas de la copia manual de archivos son las siguientes:

* La familiaridad y la complejidad mínima de las herramientas de FTP. 
* Se sabe exactamente dónde van los archivos.
* Mayor seguridad con FTPS.

Los inconvenientes de la copia manual de archivos son los siguientes:

* La necesidad de saber cómo implementar archivos en los directorios correctos en Servicio de aplicaciones. 
* No hay control de versiones para la reversión cuando se producen errores.
* No existe un historial integrado de las implementaciones para solucionar problemas con la implementación.
* Existe la posibilidad de que se generen tiempos de implementación prolongados, debido a que muchas de las herramientas de FTP no ofrecen la funcionalidad para copiar solo las diferencias y simplemente copian todos los archivos.  

### <a name="howtoftp"></a>Carga de archivos con FTP
[Azure Portal](https://portal.azure.com) proporciona toda la información que necesita para conectarse a los directorios de la aplicación mediante FTP o FTPS.

* [Implementación de la aplicación en Azure App Service mediante FTP](app-service-deploy-ftp.md)

## <a name="dropbox"></a>Implementación mediante sincronización con una carpeta en la nube
Una buena alternativa a [copiar archivos manualmente](#ftp) es sincronizar archivos y carpetas con el Servicio de aplicaciones desde un servicio de almacenamiento en la nube, como OneDrive y Dropbox. La sincronización con una carpeta en la nube utiliza el proceso Kudu para la implementación (consulte [Información general sobre los procesos de implementación](#overview)).

Las ventajas de sincronizar con una carpeta en la nube son las siguientes:

* Sencillez de la implementación Servicios como OneDrive y Dropbox proporcionan clientes de sincronización de escritorio, por lo que el directorio de trabajo local también es el directorio de implementación.
* Implementación con un solo clic.
* Toda la funcionalidad del motor de implementación de Kudu está disponible (por ejemplo, la restauración de paquetes, la automatización, etc.).

Los inconvenientes de sincronizar con una carpeta en la nube son los siguientes:

* No hay control de versiones para la reversión cuando se producen errores.
* No hay implementación automatizada, se requiere la sincronización manual.

### <a name="howtodropbox"></a>Implementación mediante sincronización con una carpeta en la nube
En el [Portal de Azure](https://portal.azure.com), puede designar una carpeta para la sincronización de contenido en el almacenamiento en la nube de OneDrive o Dropbox, trabajar con el código de la aplicación y el contenido de dicha carpeta y sincronizar con el Servicio de aplicaciones con solo hacer clic en un botón.

* [Sincronización de contenido de una carpeta en la nube con Servicio de aplicaciones de Azure](app-service-deploy-content-sync.md). 

## <a name="continuousdeployment"></a>Implementación continua desde un servicio de control de código fuente basado en la nube
Si el equipo de desarrollo usa un servicio de administración de código fuente (SCM) basado en la nube, como [Visual Studio Team Services](http://www.visualstudio.com/), [GitHub](https://www.github.com) o [BitBucket](https://bitbucket.org/), se puede configurar App Service para integrarlo con el repositorio e implementarlo de manera continua. 

Las ventajas de la implementación desde un servicio de control de código fuente basado en la nube son las siguientes:

* Control de versiones para habilitar la reversión.
* Capacidad para configurar la implementación continua de repositorios Git (y Mercurial, cuando corresponda). 
* Implementación específica de ramas, se pueden implementar ramas diferentes en [ranuras](web-sites-staged-publishing.md)distintas.
* Toda la funcionalidad del motor de implementación de Kudu está disponible (por ejemplo, control de versiones de implementación, reversión, restauración de paquetes, automatización).

La desventaja de la implementación desde un servicio de control de código fuente basado en la nube es la siguiente:

* Se requiere cierto conocimiento del servicio de SCM respectivo.

### <a name="vsts"></a>Implementación continua desde un servicio de control de código fuente basado en la nube
En el [Portal de Azure](https://portal.azure.com), puede configurar la implementación continua desde GitHub, BitBucket y Visual Studio Team Services.

* [Implementación continua en el Servicio de aplicaciones de Azure](app-service-continuous-deployment.md) 

Para más información sobre cómo configurar una implementación continua manualmente desde un repositorio en la nube que no aparece en Azure Portal (como [GitLab](https://gitlab.com/)), consulte [Setting up continuous deployment using manual steps](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps) (Configuración de la implementación continua de forma manual).

## <a name="localgitdeployment"></a>Implementación desde Git local
Si el equipo de desarrollo usa un servicio de administración de código fuente (SCM) local basado en Git, puede configurarlo como un origen de implementación en Servicio de aplicaciones. 

Las ventajas de implementar desde un repositorio local Git son las siguientes:

* Control de versiones para habilitar la reversión.
* Implementación específica de ramas, se pueden implementar ramas diferentes en [ranuras](web-sites-staged-publishing.md)distintas.
* Toda la funcionalidad del motor de implementación de Kudu está disponible (por ejemplo, control de versiones de implementación, reversión, restauración de paquetes, automatización).

La desventaja de implementar desde un repositorio local de Git es la siguiente:

* Se requiere cierto conocimiento del sistema de SCM respectivo.
* No hay ninguna solución llave en mano para la implementación continua. 

### <a name="vsts"></a>Implementación desde Git local
En el [Portal de Azure](https://portal.azure.com), puede configurar la implementación de Git local.

* [Implementación de Git local en el Servicio de aplicaciones de Azure](app-service-deploy-local-git.md). 
* [Publicación en aplicaciones web desde cualquier repositorio git/hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html).  

## <a name="deploy-using-an-ide"></a>Implementación mediante un IDE
Si ya usa [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) con un [Azure SDK](https://azure.microsoft.com/downloads/), o bien otros conjuntos de aplicaciones de IDE, como [Xcode](https://developer.apple.com/xcode/), [Eclipse](https://www.eclipse.org) e [IntelliJ IDEA](https://www.jetbrains.com/idea/), puede efectuar implementaciones en Azure directamente desde el IDE. Esta opción es perfecta para un desarrollador individual.

Visual Studio admite los tres procesos de implementación (FTP, Git y Web Deploy), en función de sus preferencias, mientras que otros IDE pueden efectuar implementaciones en el Servicio de aplicaciones si tienen integración con FTP o Git (consulte [Descripción general de la implementación de Servicio de aplicaciones de Azure](#overview)).

Las ventajas de la implementación mediante un IDE son las siguientes:

* Reduce al mínimo, potencialmente, las herramientas para el ciclo de vida de la aplicación de un extremo a otro. Desarrolle, depure, realice un seguimiento e implemente la aplicación en Azure, todo ello sin salir de su IDE. 

Los inconvenientes de la implementación mediante un IDE son los siguientes:

* Mayor complejidad de las herramientas.
* Aún requiere un sistema de control de código fuente para un proyecto de equipo.

<a name="vspros"></a> Las ventajas adicionales de la implementación mediante Visual Studio con Azure SDK son las siguientes:

* Azure SDK hace de los recursos de Azure ciudadanos de primera clase en Visual Studio. Cree, elimine, edite, inicie y detenga aplicaciones, consulte la Base de datos SQL del back-end, depure en directo la aplicación de Azure y mucho más. 
* Edición en directo de archivos de código en Azure.
* Depuración en directo de aplicaciones en Azure.
* Explorador integrado de Azure.
* Implementación de solo diferencias. 

### <a name="vs"></a>Implementación directa desde Visual Studio
* [Introducción a Azure y ASP.NET](app-service-web-get-started-dotnet.md). Creación e implementación de un proyecto web ASP.NET MVC simple mediante Visual Studio y Web Deploy.
* [Implementación de WebJobs de Azure con Visual Studio](websites-dotnet-deploy-webjobs.md). Configuración de proyectos de aplicación de consola para que se implementen como trabajos web.  
* [Implementación web de ASP.NET con Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Una serie de 12 tutoriales que abarca un rango más completo de tareas de implementación que otros de esta lista. Desde que se escribió el tutorial se han agregado algunas características de implementación de Azure, pero las notas agregadas posteriormente explican lo que falta.
* [Implementación de un sitio web ASP.NET en Azure en Visual Studio 2012 desde un repositorio Git directamente](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Explica cómo implementar un proyecto web de ASP.NET en Visual Studio, con el complemento Git para confirmar el código en Git y conectar Azure al repositorio Git. A partir de Visual Studio 2013, la compatibilidad con Git está integrada y no requiere la instalación de un complemento.

### <a name="aztk"></a>Implementación con los kits de herramientas de Azure para Eclipse e IntelliJ IDEA
Microsoft le permite implementar Web Apps en Azure directamente desde Eclipse e IntelliJ a través del [kit de herramientas de Azure para Eclipse](../azure-toolkit-for-eclipse.md) y el [kit de herramientas de Azure para IntelliJ](../azure-toolkit-for-intellij.md). Los siguientes tutoriales ilustran los pasos que intervienen en la implementación de una sencilla aplicación web que muestra el mensaje "Hello world!" (¡Hola, mundo!) en Azure mediante cada uno de los dos IDE:

* [Creación de una aplicación web Hello World para Azure en Eclipse](app-service-web-eclipse-create-hello-world-web-app.md). En este tutorial, se muestra cómo utilizar el kit de herramientas de Azure para Eclipse con el fin de crear e implementar una aplicación web que muestra el mensaje "Hello world!" (¡Hola, mundo!) para Azure.
* [Creación de una aplicación web Hello World para Azure en IntelliJ](app-service-web-intellij-create-hello-world-web-app.md). En este tutorial, se muestra cómo utilizar el kit de herramientas de Azure para IntelliJ con el fin de crear e implementar una aplicación web que muestra el mensaje "Hello world!" (¡Hola, mundo!) para Azure.

## <a name="automate"></a>Automatización de la implementación mediante herramientas de línea de comandos
Si prefiere el terminal de línea de comandos como entorno de desarrollo, puede crear tareas de implementación de scripts para la aplicación de App Service con herramientas de la línea de comandos. 

Las ventajas de la implementación con las herramientas de la línea de comandos son las siguientes:

* Habilita escenarios de implementación con script.
* Integra el aprovisionamiento de recursos de Azure y la implementación de código.
* Integra la implementación de Azure en los scripts de integración continua existentes.

Los inconvenientes de la implementación con las herramientas de la línea de comandos son las siguientes:

* No está indicado para los desarrolladores que prefieren GUI.

### <a name="automatehow"></a>Automatización de la implementación con herramientas de la línea de comandos

Vea [Automate deployment of your Azure app with command-line tools](app-service-deploy-command-line.md) (Automatización de su aplicación de Azure con herramientas de la línea de comandos) para obtener una lista de herramientas de línea de comandos y vínculos a los tutoriales. 

## <a name="nextsteps"></a>Pasos siguientes
En algunos escenarios, es posible que desee poder cambiar fácilmente entre una versión de ensayo y versión una de producción de su aplicación. Para obtener más información, consulte [Implementación de ensayo en Web Apps](web-sites-staged-publishing.md).

Tener un plan de copia de seguridad y restauración es un elemento importante de cualquier flujo de trabajo de implementación. Para obtener información sobre la característica de copia de seguridad y restauración del Servicio de aplicaciones, consulte [Hacer copia de seguridad de una aplicación web en el servicio de aplicaciones de Azure](web-sites-backup.md).  

Para obtener información sobre cómo usar el control de acceso basado en roles de Azure para administrar el acceso a la implementación del Servicio de aplicaciones, consulte [RBAC and Web App Publishing](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/)(RBAC y la publicación de aplicaciones web).


