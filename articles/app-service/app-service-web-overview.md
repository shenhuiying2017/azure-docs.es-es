---
title: "Introducción a Web Apps | Microsoft Docs"
description: "Vea cómo Azure App Service lo ayuda a desarrollar y hospedar aplicaciones web."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f8510bb6b412e9af8aad30ba32bc74206c22042f
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2017
---
# <a name="web-apps-overview"></a>Introducción a Web Apps

*Azure App Service Web Apps* (o solo Web Apps) es un servicio para hospedar aplicaciones web, API de REST y back-ends para dispositivos móviles. Puede desarrollarlo en su lenguaje preferido, ya sea. NET, .NET Core, Java, Ruby, Node.js, PHP o Python. Puede ejecutar y escalar aplicaciones con facilidad en máquinas virtuales Windows o Linux (consulte [App Service en Linux](containers/app-service-linux-intro.md)). 

Web Apps no solo agrega a la aplicación la funcionalidad de Microsoft Azure, como la seguridad, el equilibrio de carga, el escalado automático y la administración automatizada. También puede sacar partido de sus funcionalidades de DevOps, por ejemplo, la implementación continua desde VSTS, GitHub, Docker Hub y otros orígenes, la administración de paquetes, entornos de ensayo, dominio personalizado y certificados SSL. 

Con App Service, se paga por los recursos de proceso de Azure que se utilizan. Los recursos de proceso que usa se determinan a través del _plan de App Service_ en el que ejecuta las aplicaciones web. Para obtener más información, consulte [Planes de App Service en Azure Web Apps](azure-web-sites-web-hosting-plans-in-depth-overview.md).

El siguiente vídeo de 5 minutos presenta Azure App Service Web Apps.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

## <a name="why-use-web-apps"></a>¿Por qué usar Web Apps?
Estas son algunas características clave de App Service Web Apps:

* **Varios lenguajes y marcos**: Web Apps tiene compatibilidad de primera clase con ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP o Python. También puede ejecutar [PowerShell y otros scripts o ejecutables](web-sites-create-web-jobs.md) como servicios en segundo plano.
* **Optimización de DevOps**: configure la [integración y la implementación continuas](app-service-continuous-deployment.md) con Visual Studio Team Services, GitHub, BitBucket, Docker Hub o Azure Container Service. Promueva actualizaciones a través de [entornos de ensayo y prueba](web-sites-staged-publishing.md). Administre las aplicaciones de Web Apps mediante [Azure PowerShell](/powershell/azureps-cmdlets-docs) o la [interfaz de la línea de comandos multiplataforma (CLI)](/cli/azure/install-azure-cli).
* **Escala global con alta disponibilidad**: escale [verticalmente](web-sites-scale.md) u [horizontalmente](../monitoring-and-diagnostics/insights-how-to-scale.md) de forma manual o automática. Hospede las aplicaciones en cualquier parte de la infraestructura del centro de datos global de Microsoft y el [Acuerdo de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/app-service/) de App Service promete una alta disponibilidad.
* **Conexiones a plataformas SaaS y a datos locales**: elija entre más de 50 [conectores](../connectors/apis-list.md) para sistemas empresariales (como SAP), servicios SaaS (como Salesforce) y servicios de Internet (como Facebook). Acceda a los datos locales mediante [Conexiones híbridas](../biztalk-services/integration-hybrid-connection-overview.md) y [Azure Virtual Networks](web-sites-integrate-with-vnet.md).
* **Seguridad y cumplimiento**: App Service cumple con [ISO, SOC y PCI](https://www.microsoft.com/TrustCenter/). Autentique a los usuarios con [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) o con inicio de sesión social ([Google](app-service-mobile-how-to-configure-google-authentication.md), [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md), [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) y [ Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)). Cree [restricciones de direcciones IP](app-service-ip-restrictions.md) y [administre las identidades de servicio](app-service-managed-service-identity.md).
* **Plantillas de aplicación**: elija entre una amplia lista de plantillas de aplicación en [Azure Marketplace](https://azure.microsoft.com/marketplace/), como WordPress, Joomla y Drupal.
* **Integración con visual Studio** : existen herramientas dedicadas en Visual Studio que permiten optimizar las tareas de creación, implementación y depuración.
* **API y características para móviles**: Web Apps proporciona compatibilidad CORS llave en mano para escenarios de la API RESTful y simplifica los escenarios de aplicaciones móviles habilitando la autenticación, la sincronización de datos sin conexión, las notificaciones push, etc.
* **Código sin servidor**: ejecute un fragmento de código o script a petición sin tener que proporcionar explícitamente ni administrar la infraestructura, y pague solo por el tiempo de proceso que el código utiliza realmente (vea [Azure Functions](/azure/azure-functions/)).

Además de App Service Web Apps, Azure ofrece otros servicios que se pueden utilizar para hospedar aplicaciones web y sitios web. En la mayoría de los casos, Web Apps es la mejor opción.  Como arquitectura de microservicios, considere [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Si necesita más control sobre las máquinas virtuales en las que se ejecuta el código, considere [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Para más información sobre qué servicio de Azure elegir, consulte [Comparación de Azure App Service, Virtual Machines, Service Fabric y Cloud Services](choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Pasos siguientes

Cree su primera aplicación web.

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python](app-service-web-get-started-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)

