---
title: "Guía para desarrolladores de Azure Government | Microsoft Docs"
description: "En este artículo se comparan características y se ofrecen orientaciones sobre el desarrollo de aplicaciones para Azure Government."
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: zakramer
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: 0bab116e977a8490bf0c114c8b829aa1ce07e923
ms.openlocfilehash: 41af18426ce3f8124458a61c3af280c3dd17cfcd


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

Actualmente, Iowa Gob. EE. UU. y Virginia Gob. EE. UU. son los centros de datos compatibles con Azure Government. Para conocer los centros de datos actuales y los servicios disponibles, vea [Productos disponibles por región](https://azure.microsoft.com/regions/services).


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



## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de Azure Active Government, consulte los siguientes recursos:

* [Suscribirse a una prueba](https://azuregov.microsoft.com/trial/azuregovtrial)
* [Adquirir y tener acceso a Azure Government](http://azure.com/gov)
* [Información general de Azure Government](/azure-government-overview)
* [Blog de Azure Government](http://blogs.msdn.microsoft.com/azuregov/)
* [Cumplimiento de Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)



<!--HONumber=Feb17_HO3-->


