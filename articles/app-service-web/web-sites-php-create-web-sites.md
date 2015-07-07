<properties
	pageTitle="Creación de una aplicación web de PHP en el Servicio de aplicaciones de Azure"
	description="Aprenda a crear una aplicación web de PHP en el Servicio de aplicaciones de Azure"
	documentationCenter="php"
	services="app-service\web"
	editor="mollybos"
	manager="wpickett"
	authors="tfitzmac"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="hero-article"
	ms.date="04/29/2015"
	ms.author="tomfitz"/>

# Creación de una aplicación web de PHP en el Servicio de aplicaciones de Azure

## Información general
En este artículo aprenderá a crear una aplicación web de PHP en el [Servicio de aplicaciones de Azure] mediante el [Portal de vista previa de Azure](https://portal.azure.com), la [CLI de Azure][Azure CLI] o los [cmdlets de PowerShell de Azure][powershell-cmdlets].

En general, crear una aplicación web de PHP es igual que crear *cualquier* aplicación web en el Servicio de aplicaciones de Azure. De manera predeterminada, PHP está habilitado para todas las aplicaciones web. Para obtener información acerca de la configuración de PHP (o sobre proporcionar su propio tiempo de ejecución de PHP personalizado), consulte [Configuración de PHP en Aplicaciones web del Servicio de aplicaciones de Azure].

Cada opción que se describe a continuación muestra cómo crear una aplicación web en un entorno de hospedaje compartido sin coste alguno, pero con algunas limitaciones en el uso de CPU y el uso de ancho de banda. Para obtener más información, vea [Precios del Servicio de aplicaciones]. Para obtener información sobre cómo actualizar y escalar la aplicación web en el Servicio de aplicaciones, consulte [Escalación de una aplicación web en el Servicio de aplicaciones de Azure].

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Creación de una aplicación web de PHP con el Portal de vista previa de Azure

Al crear una aplicación web de PHP en el Portal de vista previa de Azure, tiene tres opciones:

- **Creación rápida**: consulte [Creación de una aplicación web con el Portal de vista previa de Azure](../web-sites-create-deploy.md#createawebsiteportal).
- **Creación con base de datos**: consulte [Creación de una aplicación web PHP-MySQL en el Servicio de aplicaciones de Azure e implementación mediante Git]
- **En Marketplace**: consulte [Creación de una aplicación web de WordPress en el Servicio de aplicaciones de Azure]

## Creación de una aplicación web de PHP con la CLI de Azure

Para crear una aplicación web de PHP con la CLI de Azure, haga lo siguiente:

1. Instale la CLI de Azure siguiendo las instrucciones que encontrará en: [Instalación de la CLI de Azure](../xplat-cli.md#install).

1. Descargue e importe su archivo de configuración de publicación siguiendo las instrucciones que encontrará en: [Descarga e importación de la configuración de publicación](../xplat-cli.md#configure).

1. Ejecute el siguiente comando desde un símbolo del sistema:

		azure site create MyWebAppName

La dirección URL de la aplicación web recién creada será `http://MyWebAppName.azurewebsites.net`.

Tenga en cuenta que puede ejecutar el comando `azure site create` con cualquiera de las siguientes opciones:

* `--location [location name]`. Esta opción le permite especificar la ubicación del centro de datos en el que se crea la aplicación web (por ejemplo, "Oeste de EE. UU"). Si omite esta opción, se le solicitará que elija una ubicación.
* `--hostname [custom host name]`. Esta opción le permite especificar un nombre de host personalizado para la aplicación web.
* `--git`. Esta opción le permite usar git para publicar en la aplicación web al crear repositorios de git tanto en su directorio de aplicaciones local como en el centro de datos de la aplicación web. Tenga en cuenta que si su carpeta local ya está en un repositorio de git, el comando agregará un remoto nuevo al repositorio actual, el cual apuntará al repositorio del centro de datos de la aplicación web.

## Creación de una aplicación web PHP con los cmdlets de Azure PowerShell

Para crear una aplicación web PHP con los cmdlets de Azure PowerShell, haga lo siguiente:

1. Instale los cmdlets de Azure PowerShell siguiendo las instrucciones que encontrará en: [Introducción a Azure PowerShell](/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Descargue e importe su archivo de configuración de publicación siguiendo las instrucciones que encontrará en: [Cómo: Importar configuración de publicación](/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Abra el símbolo del sistema de PowerShell y ejecute el siguiente comando:

		New-AzureWebsite MyWebAppName

La dirección URL de la aplicación web recién creada será `http://MyWebAppName.azurewebsites.net`.

Tenga en cuenta que puede ejecutar el comando `New-AzureWebsite` con cualquiera de las siguientes opciones:

* `-Location [location name]`. Esta opción le permite especificar la ubicación del centro de datos en el que se crea la aplicación web (por ejemplo, "Oeste de EE. UU"). Si omite esta opción, se le solicitará que elija una ubicación.
* `-Hostname [custom host name]`. Esta opción le permite especificar un nombre de host personalizado para la aplicación web.
* `-Git`. Esta opción le permite usar git para publicar en la aplicación web al crear repositorios de git tanto en su directorio de aplicaciones local como en el centro de datos de la aplicación web. Tenga en cuenta que si su carpeta local ya está en un repositorio de git, el comando agregará un remoto nuevo al repositorio actual, el cual apuntará al repositorio del centro de datos de la aplicación web.

## Pasos siguientes

Ahora que creó una aplicación web PHP en el Servicio de aplicaciones de Azure, puede administrar, configurar, supervisar, implementar y escalar la aplicación. Para obtener más información, consulte los vínculos siguientes:

* [Configuración de Aplicaciones web en Servicio de aplicaciones de Azure](web-sites-configure.md)
* [Configuración de PHP en Aplicaciones web del Servicio de aplicaciones de Azure]
* [Administración de aplicaciones web mediante el Portal de Azure](web-sites-manage.md)
* [Supervisión de Aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-monitor.md)
* [Escalación de una aplicación web en el Servicio de aplicaciones de Azure]
* [Implementación continua mediante GIT en el Servicio de aplicaciones de Azure](web-sites-publish-source-control.md)

Para conocer tutoriales integrales, visite la página [Tutoriales del Centro para desarrolladores de PHP](/develop/php/tutorials/).

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714).
* Para obtener una guía del cambio del portal al de vista previa, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715).

[Servicio de aplicaciones de Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Portal]: http://go.microsoft.com/fwlink/?LinkId=529715
[Azure CLI]: ../xplat-cli.md
[powershell-cmdlets]: ../powershell-install-configure.md
[Configuración de PHP en Aplicaciones web del Servicio de aplicaciones de Azure]: web-sites-php-configure.md
[Creación de una aplicación web PHP-MySQL en el Servicio de aplicaciones de Azure e implementación mediante Git]: web-sites-php-mysql-deploy-use-git.md
[Creación de una aplicación web de WordPress en el Servicio de aplicaciones de Azure]: web-sites-php-web-site-gallery.md
[Precios del Servicio de aplicaciones]: /pricing/details/app-service/
[Escalación de una aplicación web en el Servicio de aplicaciones de Azure]: web-sites-scale.md
 

<!---HONumber=62-->