<properties 
	pageTitle="Creación de un sitio web PHP en Sitios web Azure" 
	description="Obtenga información acerca de cómo crear un sitio web de PHP en Sitios web de Azure" 
	documentationCenter="php" 
	services="" 
	editor="mollybos" 
	manager="wpickett" 
	authors="tfitzmac"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="10/21/2014" 
	ms.author="tomfitz"/>

#Creación de un sitio web PHP en Sitios web Azure

Este artículo le mostrará cómo crear un sitio web PHP en [Sitios web Azure][waws] con la ayuda del [Portal de administración de Azure], las [Herramientas de línea de comandos de Azure para Linux y Mac][xplat-tools] o los [cmdlets de Azure PowerShell][powershell-cmdlets].

En general, la creación de un sitio web PHP no es diferente a la de *any* sitio web en Sitios web Azure. De manera predeterminada, PHP está habilitado para todos los sitios web. Para obtener información sobre la configuración de PHP (o proporcionar su propio tiempo de ejecución de PHP personalizado), consulte [Configuración de PHP en Sitios web Azure][configure-php]..

Cada opción que se describe a continuación muestra cómo crear un sitio web en un entorno de hospedaje compartido sin coste alguno, pero con algunas limitaciones en el uso de CPU y el uso de ancho de banda. Para obtener más información, consulte [Precios de Sitios web Azure][websites-pricing]. Para obtener información sobre cómo actualizar y escalar su sitio web, consulte [Escalación de sitios web][scale-websites].

> [AZURE.NOTE]
> Si desea obtener una introducción a Sitios web Azure antes de inscribirse para abrir una cuenta, vaya a <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, donde puede crear inmediatamente y de forma gratuita un sitio básico de ASP.NET de corta duración en Sitios web Azure. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

##Tabla de contenido
* [Creación de un sitio web con el Portal de administración de Azure](#portal)
* [Creación de un sitio web con las Herramientas de línea de comandos de Azure para Mac y Linux](#XplatTools)
* [Creación de un sitio web con los cmdlets de Azure PowerShell](#PowerShell)

<h2><a name="portal"></a>Creación de un sitio web PHP con el Portal de administración de Azure</h2>

Al crear un sitio web en el Portal de administración de Azure, tiene tres opciones: **Quick Create**, **Create with Database** y **From Gallery**. Las siguientes instrucciones abarcan la opción **Quick Create**. Para obtener información sobre las otras dos opciones, consulte [Creación de un sitio web de Azure de PHP-MySQL e implementación mediante Git][website-mysql-git] y [Creación de un sitio web de WordPress desde la galería de Azure][wordpress-gallery].

Para crear un sitio web PHP con el Portal de administración de Azure, haga lo siguiente:

1. Inicie sesión en el [Portal de administración de Azure].
1. Haga clic en **New** en la parte inferior de la página y, a continuación, haga clic en **Proceso**, **Sitio web** y **Quick Create**. Proporcione una **URL** para su sitio web y seleccione la **Region** para su sitio web. Por último, haga clic en **Create Website**.

	![Select Quick Create web site](./media/web-sites-php-create-web-sites/select-quickcreate-website.png)

<h2><a name="XplatTools"></a>Creación de un sitio web PHP con las Herramientas de línea de comandos de Azure para Mac y Linux</h2>

Para crear un sitio web PHP con las Herramientas de línea de comandos de Azure para Mac y Linux, haga lo siguiente:

1. Instale las Herramientas de línea de comandos de Azure siguiendo las siguientes instrucciones: [Instalación de las herramientas de línea de comandos de Azure para Mac y Linux](/es-es/develop/php/how-to-guides/command-line-tools/#Download).

1. Descargue e importe su archivo de configuración de publicación siguiendo las siguientes instrucciones: [Descarga e importación de la configuración de publicación ](/es-es/develop/php/how-to-guides/command-line-tools/#Account).

1. Ejecute el siguiente comando desde un símbolo del sistema:

		azure site create MySiteName

La URL del sitio web recién creado será `http://MySiteName.azurewebsites.net`.  
 
Observe que puede ejecutar el comando  `azure site create` con cualquiera de las siguientes opciones:

* `--location [location name]`. Esta opción le permite especificar la ubicación del centro de datos en el que se crea su sitio web (por ejemplo, "Oeste de EE. UU"). Si omite esta opción, se le solicitará que elija una ubicación.
* `--hostname [custom host name]`. Esta opción le permite especificar un nombre de host personalizado para su sitio web.
* `--git`. Esta opción le permite usar git para publicar en su sitio web al crear repositorios de git tanto en su directorio de aplicaciones local como en el centro de datos de su sitio web. Tenga en cuenta que si su carpeta local ya está en un repositorio de git, el comando agregará un remoto nuevo al repositorio actual, el cual apuntará al repositorio del centro de datos de su sitio web.

Para obtener información sobre opciones adicionales, consulte [Creación y administración de un sitio web de Azure](/es-es/develop/php/how-to-guides/command-line-tools/#WebSites)..

<h2><a name="PowerShell"></a>Creación de un sitio web PHP con los cmdlets de Azure PowerShell</h2>

Para crear un sitio web PHP con los cmdlets de Azure PowerShell, haga lo siguiente:

1. Instale los cmdlets de Azure PowerShell siguiendo las siguientes instrucciones: [Introducción a Azure PowerShell](/es-es/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).:

1. Descargue e importe su archivo de configuración de publicación siguiendo las siguientes instrucciones: [Cómo: Importación de la configuración de publicación](/es-es/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Abra el símbolo del sistema de PowerShell y ejecute el siguiente comando:

		New-AzureWebSite MySiteName

La URL del sitio web recién creado será  `http://MySiteName.azurewebsites.net`.  
 
Observe que puede ejecutar el comando  `New-AzureWebSite` con cualquiera de las siguientes opciones:

* `-Location [location name]`. Esta opción le permite especificar la ubicación del centro de datos en el que se crea su sitio web (por ejemplo, "Oeste de EE. UU"). Si omite esta opción, se le solicitará que elija una ubicación.
* `-Hostname [custom host name]`. Esta opción le permite especificar un nombre de host personalizado para su sitio web.
* `-Git`. Esta opción le permite usar git para publicar en su sitio web al crear repositorios de git tanto en su directorio de aplicaciones local como en el centro de datos de su sitio web. Tenga en cuenta que si su carpeta local ya está en un repositorio de git, el comando agregará un remoto nuevo al repositorio actual, el cual apuntará al repositorio del centro de datos de su sitio web.

Para obtener información sobre opciones adicionales, consulte [Creación y administración de Creación y administración de un sitio web de Azure](/es-es/develop/php/how-to-guides/powershell-cmdlets/#WebSite).

<h2><a name="NextStepsPasos siguientes"></a></h2>

Ahora que creó un sitio web PHP en Sitios web Azure, puede administrar, configurar, supervisar, implementar y escalar su sitio. Para obtener más información, consulte los vínculos siguientes:

* [Configuración de sitios web](/es-es/manage/services/web-sites/how-to-configure-websites/)
* [Configuración de PHP en Sitios web Azure][configure-php]
* [Administración de sitios web](/es-es/manage/services/web-sites/how-to-manage-websites/)
* [Supervisión de sitios web](/es-es/manage/services/web-sites/how-to-monitor-websites/)
* [Escalación de sitios web](/es-es/manage/services/web-sites/how-to-scale-websites/)
* [Publicación con Git](/es-es/develop/php/common-tasks/publishing-with-git/)

Para conocer tutoriales integrales, visite la página [Tutoriales del Centro para desarrolladores de PHP](/es-es/develop/php/tutorials/).

[waws]: /es-es/manage/services/web-sites/
[Portal de administración de Azure]: http://manage.windowsazure.com/
[xplat-tools]: /es-es/develop/php/how-to-guides/command-line-tools/
[powershell-cmdlets]: /es-es/develop/php/how-to-guides/powershell-cmdlets/
[configure-php]: /es-es/develop/php/common-tasks/configure-php-web-site/
[website-mysql-git]: /es-es/develop/php/tutorials/website-w-mysql-and-git/
[wordpress-gallery]: /es-es/develop/php/tutorials/website-from-gallery/
[websites-pricing]: http://azure.microsoft.com/pricing/details/#header-1
[scale-websites]: /es-es/manage/services/web-sites/how-to-scale-websites/




<!--HONumber=42-->
