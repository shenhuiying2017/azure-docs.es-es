<properties title="How to create a PHP website in Azure Websites" pageTitle="How to create a PHP website in Azure Websites" metaKeywords="PHP Azure Web Sites" description="Learn how to create a PHP website in Azure Websites" documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="wpickett" authors="cephalin" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Creación de un sitio web PHP en Sitios web Azure

Este artículo le mostrará cómo crear un sitio web PHP en [Sitios web Azure][Sitios web Azure] con la ayuda del [Portal de administración de Azure][Portal de administración de Azure], las [Herramientas de línea de comandos de Azure para Linux y Mac][Herramientas de línea de comandos de Azure para Linux y Mac] o los [cmdlets de Azure PowerShell][cmdlets de Azure PowerShell].

En general, la creación de un sitio web PHP no es diferente a la de crear *cualquier* sitio web en Sitios web Azure. De manera predeterminada, PHP está habilitado para todos los sitios web. Para obtener información sobre la configuración de PHP (o proporcionar su propio tiempo de ejecución de PHP personalizado), consulte [Configuración de PHP en Sitios web Azure][Configuración de PHP en Sitios web Azure].

Cada opción que se describe a continuación muestra cómo crear un sitio web en un entorno de hospedaje compartido sin coste alguno, pero con algunas limitaciones en el uso de CPU y el uso de ancho de banda. Para obtener más información, consulte [Precios de Sitios web Azure][Precios de Sitios web Azure]. Para obtener información sobre como actualizar y escalar su sitio web, consulte [Escalación de sitios web][Escalación de sitios web].

## Tabla de contenido

-   [Creación de un sitio web con el Portal de administración de Azure][Creación de un sitio web con el Portal de administración de Azure]
-   [Creación de un sitio web con las Herramientas de línea de comandos de Azure para Mac y Linux][Creación de un sitio web con las Herramientas de línea de comandos de Azure para Mac y Linux]
-   [Creación de un sitio web con los cmdlets de Azure PowerShell][Creación de un sitio web con los cmdlets de Azure PowerShell]

## <a name="portal"></a>Creación de un sitio web PHP con el Portal de administración de Azure

Al crear un sitio web en el Portal de administración de Azure, tiene tres opciones: **Quick Create**, **Create with Database** y **From Gallery**. Las siguientes instrucciones abarcan la opción **Quick Create**. Para obtener información sobre las otras dos opciones, consulte [Creación de un sitio web de Azure de PHP-MySQL e implementación mediante Git][Creación de un sitio web de Azure de PHP-MySQL e implementación mediante Git] y [Creación de un sitio web de WordPress desde la galería de Azure][Creación de un sitio web de WordPress desde la galería de Azure].

Para crear un sitio web PHP con el Portal de administración de Azure, haga lo siguiente:

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].
2.  Haga clic en **New** en la parte inferior de la página y, a continuación, haga clic en **Proceso**, **Sitio web** y **Quick Create**. Proporcione una **URL** para su sitio web y seleccione la **Region** para su sitio web. Por último, haga clic en **Create Web Site**.

    ![Selección del sitio web con Quick Create][Selección del sitio web con Quick Create]

## <a name="XplatTools"></a>Creación de un sitio web PHP con las Herramientas de línea de comandos de Azure para Mac y Linux

Para crear un sitio web PHP con las Herramientas de línea de comandos de Azure para Mac y Linux, haga lo siguiente:

1.  Instale las Herramientas de línea de comandos de Azure siguiendo las siguientes instrucciones: [Instalación de las Herramientas de línea de comandos de Azure para Mac y Linux][Instalación de las Herramientas de línea de comandos de Azure para Mac y Linux].

2.  Descargue e importe su archivo de configuración de publicación siguiendo las siguientes instrucciones: [Descarga e importación de la configuración de publicación][Descarga e importación de la configuración de publicación].

3.  Ejecute el siguiente comando desde un símbolo del sistema:

        azure site create MySiteName

La URL del sitio web recién creado será `http://MySiteName.azurewebsites.net`.

Observe que puede ejecutar el comando `azure site create` con cualquiera de las siguientes opciones:

-   `--location [location name]`. Esta opción le permite especificar la ubicación del centro de datos en el que se crea su sitio web (por ejemplo, "Oeste de EE. UU"). Si omite esta opción, se le solicitará que elija una ubicación.
-   `--hostname [custom host name]`. Esta opción le permite especificar un nombre de host personalizado para su sitio web.
-   `--git`. Esta opción le permite usar git para publicar en su sitio web al crear repositorios de git tanto en su directorio de aplicaciones local como en el centro de datos de su sitio web. Tenga en cuenta que si su carpeta local ya está en un repositorio de git, el comando agregará un remoto nuevo al repositorio actual, el cual apuntará al repositorio del centro de datos de su sitio web.

Para obtener información sobre opciones adicionales, consulte [Creación y administración de un sitio web de Azure][Creación y administración de un sitio web de Azure].

## <a name="PowerShell"></a>Creación de un sitio web PHP con los cmdlets de Azure PowerShell

Para crear un sitio web PHP con los cmdlets de Azure PowerShell, haga lo siguiente:

1.  Instale los cmdlets de Azure PowerShell siguiendo las siguientes instrucciones: [Introducción a Azure PowerShell][Introducción a Azure PowerShell]:

2.  Descargue e importe su archivo de configuración de publicación siguiendo las siguientes instrucciones: [Direccionamiento del configuración de publicación][Direccionamiento del configuración de publicación].

3.  Abra el símbolo del sistema de PowerShell y ejecute el siguiente comando:

        New-AzureWebSite MySiteName

La URL del sitio web recién creado será `http://MySiteName.azurewebsites.net`.

Observe que puede ejecutar el comando `New-AzureWebSite` con cualquiera de las siguientes opciones:

-   `-Location [location name]`. Esta opción le permite especificar la ubicación del centro de datos en el que se crea su sitio web (por ejemplo, "Oeste de EE. UU"). Si omite esta opción, se le solicitará que elija una ubicación.
-   `-Hostname [custom host name]`. Esta opción le permite especificar un nombre de host personalizado para su sitio web.
-   `-Git`. Esta opción le permite usar git para publicar en su sitio web al crear repositorios de git tanto en su directorio de aplicaciones local como en el centro de datos de su sitio web. Tenga en cuenta que si su carpeta local ya está en un repositorio de git, el comando agregará un remoto nuevo al repositorio actual, el cual apuntará al repositorio del centro de datos de su sitio web.

Para obtener información sobre opciones adicionales, consulte [Creación y administración de un sitio web de Azure][1].

## <a name="NextSteps"></a>Pasos siguientes

Ahora que creó un sitio web PHP en Sitios web Azure, puede administrar, configurar, supervisar, implementar y escalar su sitio. Para obtener más información, consulte los vínculos siguientes:

-   [Configuración de sitios web][Configuración de sitios web]
-   [Configuración de PHP en Sitios web Azure][Configuración de PHP en Sitios web Azure]
-   [Administración de sitios web][Administración de sitios web]
-   [Supervisión de sitios web][Supervisión de sitios web]
-   [Escalación de sitios web][Escalación de sitios web]
-   [Publicación con Git][Publicación con Git]

Para conocer tutoriales integrales, visite la página [Tutoriales del Centro para desarrolladores de PHP][Tutoriales del Centro para desarrolladores de PHP].

  [Sitios web Azure]: /es-es/manage/services/web-sites/
  [Portal de administración de Azure]: http://manage.windowsazure.com/
  [Herramientas de línea de comandos de Azure para Linux y Mac]: /es-es/develop/php/how-to-guides/command-line-tools/
  [cmdlets de Azure PowerShell]: /es-es/develop/php/how-to-guides/powershell-cmdlets/
  [Configuración de PHP en Sitios web Azure]: /es-es/develop/php/common-tasks/configure-php-web-site/
  [Precios de Sitios web Azure]: http://www.windowsazure.com/es-es/pricing/details/#header-1
  [Escalación de sitios web]: /es-es/manage/services/web-sites/how-to-scale-websites/
  [Creación de un sitio web con el Portal de administración de Azure]: #portal
  [Creación de un sitio web con las Herramientas de línea de comandos de Azure para Mac y Linux]: #XplatTools
  [Creación de un sitio web con los cmdlets de Azure PowerShell]: #PowerShell
  [Creación de un sitio web de Azure de PHP-MySQL e implementación mediante Git]: /es-es/develop/php/tutorials/website-w-mysql-and-git/
  [Creación de un sitio web de WordPress desde la galería de Azure]: /es-es/develop/php/tutorials/website-from-gallery/
  [Selección del sitio web con Quick Create]: ./media/web-sites-php-create-web-sites/select-quickcreate-website.png
  [Instalación de las Herramientas de línea de comandos de Azure para Mac y Linux]: /es-es/develop/php/how-to-guides/command-line-tools/#Download
  [Descarga e importación de la configuración de publicación]: /es-es/develop/php/how-to-guides/command-line-tools/#Account
  [Creación y administración de un sitio web de Azure]: /es-es/develop/php/how-to-guides/command-line-tools/#WebSites
  [Introducción a Azure PowerShell]: /es-es/develop/php/how-to-guides/powershell-cmdlets/#GetStarted
  [Direccionamiento del configuración de publicación]: /es-es/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings
  [1]: /es-es/develop/php/how-to-guides/powershell-cmdlets/#WebSite
  [Configuración de sitios web]: /es-es/manage/services/web-sites/how-to-configure-websites/
  [Administración de sitios web]: /es-es/manage/services/web-sites/how-to-manage-websites/
  [Supervisión de sitios web]: /es-es/manage/services/web-sites/how-to-monitor-websites/
  [Publicación con Git]: /es-es/develop/php/common-tasks/publishing-with-git/
  [Tutoriales del Centro para desarrolladores de PHP]: /es-es/develop/php/tutorials/
