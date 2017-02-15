---
title: "Guía para desarrolladores de Azure Government"
description: "Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure"
services: 
cloud: gov
documentationcenter: 
author: Joharve2
manager: Chrisnie
editor: 
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/29/2015
ms.author: jharve
translationtype: Human Translation
ms.sourcegitcommit: 08de3cb0d62e800db88238764e4f9f221d2add06
ms.openlocfilehash: 42c1b022a22cefae15d32690856bdb80b0eaffa9


---
# <a name="microsoft-azure-government-developer-guide"></a>Guía para desarrolladores de Microsoft Azure Government
<p> El entorno de Azure Government es una instancia física independiente del resto de la red de Microsoft.  Esta guía para desarrolladores ofrece detalles sobre las diferencias con las que los desarrolladores de aplicaciones y administradores se encontrarán al interactuar y trabajar con estas regiones independientes de Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## <a name="in-this-topic"></a>En este tema
* [Información general](#Overview)
* [Instrucciones para desarrolladores](#Guidance)
* [Características disponibles actualmente en Microsoft Azure Government](#Features)
* [Asignación de extremos](#Endpoint)
* [Pasos siguientes](#next)

## <a name="a-nameoverviewaoverview"></a><a name="Overview"></a>Información general
Microsoft Azure Government es una instancia independiente del servicio de Microsoft Azure que aborda las necesidades de seguridad y cumplimiento de las agencias federales, los gobiernos locales y estatales y sus proveedores de soluciones de los Estados Unidos. Azure Government ofrece un aislamiento físico y de red de las implementaciones externas al gobierno de los Estados Unidos y protege al personal de los Estados Unidos.

Microsoft proporciona varias herramientas para crear e implementar aplicaciones en la nube en el servicio de Microsoft Azure global de Microsoft (servicio "Global") y los servicios de Microsoft Azure Government.

Al crear e implementar aplicaciones en los servicios de Azure Government, en lugar del servicio Global, los desarrolladores necesitan conocer las principales diferencias existentes entre los dos servicios.  Específicamente en torno a instalar y configurar su entorno de programación, configurar extremos, la escritura de aplicaciones y su implementación como servicios en Azure Government.

La información de este documento resume esas diferencias y complementa la información disponible en el sitio de [Azure Government](http://www.azure.com/gov "Azure Government") y la [Biblioteca de documentación técnica de Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") en MSDN. La información oficial también puede estar disponible en muchas otras ubicaciones, como el [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/ "Microsoft Azure Trust Center"/), el [Centro de documentación de Azure](https://azure.microsoft.com/documentation/) y los [blogs de Azure](https://azure.microsoft.com/blog/ "Azure Blogs"/).

Este contenido está destinado a socios y desarrolladores que implementan Microsoft Azure Government.

## <a name="a-nameguidanceaguidance-for-developers"></a><a name="Guidance"></a>Instrucciones para desarrolladores
Dado que la mayoría del contenido técnico que está disponible actualmente da por supuesto que las aplicaciones se están desarrollando para el servicio Global en lugar de Microsoft Azure Government, es importante asegurarse de que los desarrolladores sean conscientes de las diferencias principales para las aplicaciones desarrolladas para ser hospedadas en Azure Government.

* En primer lugar, hay servicios y características diferentes. Esto significa que ciertas características que se encuentran en regiones específicas del servicio Global pueden no estar disponibles en Azure Government.
* En segundo lugar, para las características que se ofrecen en Azure Government, hay diferencias de configuración desde el servicio Global.  Por lo tanto, debe revisar el código de ejemplo, las configuraciones y los pasos asegurarse de que genera y ejecuta dentro del entorno de servicios en la nube de Azure Government.

## <a name="a-namefeaturesa-features-currently-available-in-microsoft-azure-government"></a><a name="Features"></a> Características disponibles actualmente en Microsoft Azure Government
Azure Government actualmente tiene los siguientes servicios disponibles en las regiones US GOV IOWA y US GOV VIRGINIA:

* Virtual Machines
* Conjuntos de escalado de máquina virtual
* Servicios de contenedor
* Cuentas de Batch
* Colecciones de RemoteApp
* Conjuntos de disponibilidad
* Redes virtuales
* Equilibradores de carga
* Puertas de enlace de aplicaciones
* Puertas de enlace de red virtual
* Puertas de enlace de red local
* Tablas de ruta
* Perfiles de Traffic Manager
* Circuitos ExpressRoute
* Grupo de seguridad de red
* Interfaces de red
* Direcciones IP públicas
* Conexiones
* Cuentas de almacenamiento
* Administradores de StorSimple
* Servicios de aplicaciones
* Media Services
* BASES DE DATOS SQL
* Almacenes de SQL Data Warehouse
* Bases de datos de SQL Server Stretch Database
* Cachés de Redis Cache
* Grupos elásticos SQL
* Servidores SQL Server
* Log Analytics
* Centros de eventos
* Espacios de nombres de Service Bus
* Azure Active Directory
* Multi-Factor Authentication
* Rights management
* Cuentas de automatización
* Marketplace

Hay otros servicios disponibles y se agregarán más servicios de forma continua.  Para obtener la lista más reciente de servicios, consulte la [página de regiones](https://azure.microsoft.com/regions/#services) , en la que se resaltan todas las regiones disponibles y sus servicios.

Actualmente, US GOV Iowa y US GOV Virginia son los centros de datos compatibles con Azure Government.  Consulte la página de regiones mencionada más arriba para ver los centros de datos y los servicios disponibles actualmente.

## <a name="a-nameendpointaendpoint-mapping"></a><a name="Endpoint"></a>Asignación de extremos
Utilice la siguiente tabla como guía al asignar extremos públicos de Microsoft Azure y Base de datos SQL a extremos específicos de Azure Government.

| Nombre | Punto de conexión de Azure Government |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId  | https://management.core.usgovcloudapi.net/ |
| GalleryUrl | https://gallery.usgovcloudapi.net/ |
| ManagementPortalUrl | https://manage.windowsazure.us |
| ServiceManagementUrl | https://management.core.usgovcloudapi.net/ |
| PublishSettingsFileUrl | https://manage.windowsazure.us/publishsettings/index |
| ResourceManagerUrl | https://management.usgovcloudapi.net/ |
| SqlDatabaseDnsSuffix | .database.usgovcloudapi.net |
| StorageEndpointSuffix | core.usgovcloudapi.net |
| ActiveDirectoryAuthority | https://login-us.microsoftonline.com/ |
| GraphUrl | https://graph.windows.net/ |
| GraphEndpointResourceId | https://graph.windows.net/ |
| TrafficManagerDnsSuffix | usgovtrafficmanager.net |
| AzureKeyVaultDnsSuffix | vault.usgovcloudapi.net |
| AzureKeyVaultServiceEndpointResourceId | https://vault.usgovcloudapi.net |

* Para la autenticación de Azure Resource Manager mediante Azure AD, consulte [Solicitudes de autenticación de Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790557.aspx)

## <a name="a-namenextanext-steps"></a><a name="next"></a>Pasos siguientes
Si está interesado en obtener más información sobre Azure Government, consulte algunos de los vínculos siguientes.

* **[Suscribirse a una prueba](https://azuregov.microsoft.com/trial/azuregovtrial)**
* **[Adquirir y tener acceso a Azure Government](http://azure.com/gov)**
* **[Información general de Azure Government](/azure-government-overview)**
* **[Blog de Azure Government](http://blogs.msdn.com/b/azuregov/)**
* **[Cumplimiento de Azure](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md



<!--HONumber=Dec16_HO2-->


