---
title: "Guía para desarrolladores de Azure Government | Microsoft Docs"
description: "En este artículo se comparan características y se ofrecen orientaciones sobre el desarrollo de aplicaciones para Azure Government."
services: azure-government
cloud: gov
documentationcenter: 
author: sachamicrosoft
manager: zakramer
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/29/2015
ms.author: jharve
translationtype: Human Translation
ms.sourcegitcommit: e0e313f2f3a077f45ba5935363c251bba89bc524
ms.openlocfilehash: c20736554425204f47fed96f8618f66002623259


---
# <a name="azure-government-developer-guide"></a>Guía para desarrolladores de Azure Government
El entorno de Azure Government es una instancia física independiente del resto de la red de Microsoft. Esta guía aborda las diferencias que los desarrolladores de aplicaciones y administradores deben conocer para interactuar y trabajar con regiones independientes de Azure.

## <a name="overview"></a>Información general
Azure Government es una instancia independiente del servicio Microsoft Azure. Aborda las necesidades de seguridad y cumplimiento de las agencias federales de Estados Unidos, los gobiernos locales y estatales y sus proveedores de soluciones. Azure Government ofrece un aislamiento físico y de red de las implementaciones externas al gobierno de los Estados Unidos y protege al personal de los Estados Unidos.

Microsoft proporciona varias herramientas para ayudar a los desarrolladores a crear e implementar aplicaciones en la nube para el servicio global de Microsoft Azure (servicio "global") y los servicios de Microsoft Azure Government.

Cuando los desarrolladores crean e implementan aplicaciones en los servicios de Azure Government, en lugar de en el servicio global, estos necesitan conocer las principales diferencias existentes entre los dos servicios. Los ámbitos específicos que deben comprenderse tienen que ver con instalar y configurar su entorno de programación, configurar puntos de conexión, escribir aplicaciones e implementarlas como servicios en Azure Government.

La información de este documento resume las diferencias entre los dos servicios. Complementa la información que está disponible el sitio [Azure Government](http://www.azure.com/gov "Azure Government") y la [biblioteca técnica de Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") en MSDN. La información oficial también puede estar disponible en otras ubicaciones, como el [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/ "Centro de confianza de Microsoft Azure"), el [Centro de documentación de Azure](https://azure.microsoft.com/documentation/) y los [blogs de Azure](https://azure.microsoft.com/blog/ "Blogs de Azure").

Este contenido está destinado a socios y desarrolladores que implementan Microsoft Azure Government.

## <a name="guidance-for-developers"></a>Instrucciones para desarrolladores
La mayoría del contenido técnico actualmente disponible da por supuesto que las aplicaciones se desarrollan para el servicio global, y no para Azure Government. Por esta razón, es importante tener en cuenta dos diferencias clave en las aplicaciones que desarrolle para hospedar en Azure Government.

* Ciertos servicios y características que se encuentran en regiones específicas del servicio global pueden no estar disponibles en Azure Government.
* Las configuraciones de características en Azure Government pueden diferir de las del servicio global. Por lo tanto, es importante revisar el código de ejemplo, las configuraciones y los pasos para asegurarse de que genera y ejecuta dentro del entorno de Cloud Services de Azure Government.

## <a name="available-features-and-services-in-azure-government"></a>Características y servicios disponibles en Azure Government
Están disponibles las siguientes características y servicios de Azure Government en las regiones Iowa Gob. EE. UU. y Virginia Gob. EE. UU.:

* Máquinas virtuales
* Conjuntos de escalado de máquina virtual
* Container Service
* Cuentas de Batch
* Colecciones de RemoteApp
* Conjuntos de disponibilidad
* Virtual Network
* Load Balancer
* Application Gateway
* Puerta de enlace de red virtual
* Puertas de enlace de red local
* Tablas de ruta
* Perfiles de Traffic Manager
* Circuitos ExpressRoute
* Grupos de seguridad de red
* Interfaces de red
* Direcciones IP públicas
* Conexiones
* Cuentas de almacenamiento
* StorSimple Manager
* App Service
* Servicios multimedia
* Base de datos SQL
* SQL Data Warehouse
* SQL Server Stretch Database
* Redis Cache
* Grupos elásticos de bases de datos SQL
* SQL Server
* Log Analytics
* Event Hubs
* Espacios de nombres de Service Bus
* Azure Active Directory
* Multi-Factor Authentication
* Rights Management
* Cuentas de automatización
* Marketplace

Hay otros servicios disponibles y se agregarán más servicios de forma continua. Para obtener la lista más reciente de servicios, consulte [Productos disponibles por región](https://azure.microsoft.com/regions/#services), una página que muestra los servicios disponibles en cada región.

Actualmente, Iowa Gob. EE. UU. y Virginia Gob. EE. UU. son los centros de datos compatibles con Azure Government. Para conocer los centros de datos actuales y los servicios disponibles, vea [Productos disponibles por región](https://azure.microsoft.com/regions/#services).

## <a name="endpoint-mapping"></a>Asignación de puntos de conexión
Para obtener más información sobre la asignación de puntos de conexión públicos de Azure y SQL Database a puntos de conexión específicos de Azure Government, vea la tabla siguiente:

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

Para la autenticación de Azure Resource Manager mediante Azure Active Directory, consulte [Authenticating Azure Resource Manager Requests](https://msdn.microsoft.com/library/azure/dn790557.aspx) (Autenticación de solicitudes de Azure Resource Manager).

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de Azure Active Government, consulte los siguientes recursos:

* [Suscribirse a una prueba](https://azuregov.microsoft.com/trial/azuregovtrial)
* [Adquirir y tener acceso a Azure Government](http://azure.com/gov)
* [Información general de Azure Government](/azure-government-overview)
* [Blog de Azure Government](http://blogs.msdn.com/b/azuregov/)
* [Cumplimiento de Azure](https://azure.microsoft.com/support/trust-center/compliance/)



<!--HONumber=Jan17_HO1-->


