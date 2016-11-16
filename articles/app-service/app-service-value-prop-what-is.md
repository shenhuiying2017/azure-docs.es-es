---
title: "Azure App Service para aplicaciones web, móviles y de API| Microsoft Docs"
description: "Descubra cómo el Servicio de aplicaciones de Azure le ayuda a desarrollar, implementar y administrar aplicaciones web y móviles."
keywords: "app service, azure app service, costo de app service, escala, escalable, implementación de aplicaciones, implementación de aplicaciones de azure, paas, plataforma como servicio, sitio web, web, azure mobile"
services: app-service
documentationcenter: 
author: omarkmsft
manager: erikre
editor: cephalin
ms.assetid: 979cafa8-eeb6-4d3b-87cf-764a821c3e4f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/26/2016
ms.author: omark
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 042da7ac46fd20bd0e9af33f449f8ea4a6a42089


---
# <a name="what-is-azure-app-service"></a>¿Qué es Servicios de aplicaciones de Azure?
*Servicio de aplicaciones* es una oferta de [plataforma como servicio](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) de Microsoft Azure. Cree aplicaciones web y móviles para cualquier plataforma o dispositivo. Integre sus aplicaciones con soluciones SaaS, conecte con aplicaciones locales y automatice los procesos empresariales. Azure ejecuta las aplicaciones en máquinas virtuales totalmente administradas, con la elección de los recursos compartidos de máquinas virtuales o máquinas virtuales dedicadas.

El Servicio de aplicaciones incluye las funcionalidades web y móviles que anteriormente se ofrecían por separado como Sitios web de Azure y Servicios móviles de Azure. También incluye nuevas funcionalidades para automatizar procesos empresariales y hospedar las API en la nube. Como un servicio integrado único, Servicio de aplicaciones le permite crear distintos componentes, como sitios web, back-end de aplicación móvil, API de RESTful y procesos empresariales, en una única solución.

El vídeo de 4 minutos siguiente proporciona una breve explicación de la relación entre el Servicio de aplicaciones y las ofertas anteriores de Azure, así como sus novedades.

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/app-service-history-lesson/player]
> 
> 

## <a name="why-use-app-service"></a>¿Por qué usar el Servicio de aplicaciones?
Estas son algunas de las características y funcionalidades principales del Servicio de aplicaciones:

* **Varios lenguajes y plataformas** : Servicio de aplicaciones es compatible con ASP.NET, Node.js, Java, PHP y Python. También puede ejecutar [Windows PowerShell y otros scripts o ejecutables](../app-service-web/web-sites-create-web-jobs.md) en máquinas virtuales del Servicio de aplicaciones.
* **Optimización de DevOps** : configure la [integración y la implementación continuas](../app-service-web/app-service-continuous-deployment.md) con Visual Studio Team Services, GitHub o BitBucket. Promueva actualizaciones a través de [entornos de ensayo y prueba](../app-service-web/web-sites-staged-publishing.md). Realice [las pruebas A/B](../app-service-web/app-service-web-test-in-production-get-start.md). Administre las aplicaciones de App Service mediante [Azure PowerShell](../powershell-install-configure.md) o la [interfaz de la línea de comandos (CLI) multiplataforma](../xplat-cli-install.md).
* **Escala global con alta disponibilidad**: escale [verticalmente](../app-service-web/web-sites-scale.md) u [horizontalmente](../monitoring-and-diagnostics/insights-how-to-scale.md) de forma manual o automática. Hospede las aplicaciones en cualquier parte de la infraestructura del centro de datos global de Microsoft y el [Acuerdo de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/app-service/) del Servicio de aplicaciones promete una alta disponibilidad.
* **Conexiones a plataformas SaaS y a datos locales** : elija entre más de 50 [conectores](../connectors/apis-list.md) para sistemas empresariales (como SAP, Siebel y Oracle), servicios de SaaS conocidos (como Salesforce y Office 365) y servicios de Internet (como Facebook y Twitter). Acceda a los datos locales mediante [Conexiones híbridas](../biztalk-services/integration-hybrid-connection-overview.md) y [Azure Virtual Networks](../app-service-web/web-sites-integrate-with-vnet.md).
* **Seguridad y cumplimiento** : Servicio de aplicaciones cumple con [ISO, SOC y PCI](https://www.microsoft.com/TrustCenter/).
* **Plantillas de aplicación** : elija entre una amplia lista de plantillas de aplicación en [Azure Marketplace](https://azure.microsoft.com/marketplace/) que le permiten usar un asistente para instalar el software de código abierto popular, como WordPress, Joomla y Drupal.
* **Integración con visual Studio** : existen herramientas dedicadas en Visual Studio que permiten optimizar las tareas de creación, implementación y depuración.

## <a name="app-types-in-app-service"></a>Tipos de aplicaciones en el Servicio de aplicaciones
Servicio de aplicaciones ofrece varios *tipos de aplicación*, cada uno pensado para hospedar un tipo específico de carga de trabajo:

* [**Aplicaciones web**](../app-service-web/app-service-web-overview.md): para hospedar sitios y aplicaciones web.
* [**Mobile Apps**](../app-service-mobile/app-service-mobile-value-prop.md): para hospedar back-ends de aplicaciones móviles.
* [**API Apps**](../app-service-api/app-service-api-apps-why-best-platform.md): para hospedar API de RESTful.
* [**Logic Apps**](../app-service-logic/app-service-logic-what-are-logic-apps.md): para automatizar procesos empresariales e integrar sistemas y datos a través de nubes sin escribir código.

La palabra *aplicación* aquí hace referencia a los recursos de hospedaje dedicados a ejecutar una carga de trabajo. Si se toma "aplicación web" como ejemplo, probablemente piense en una aplicación web como aquellos recursos de proceso y código de aplicación que juntos ofrecen funcionalidad a un explorador. Pero en el Servicio de aplicaciones, una *aplicación web* son recursos de procesos que Azure proporciona para hospedar su código de aplicación. Si la aplicación se compone de un front-end web y de un back-end de API de RESTful, podría implementar ambos en una aplicación web o podría implementar el código de front-end en una aplicación web y el código de back-end en una aplicación de API. La aplicación puede estar compuesta de varias aplicaciones de Servicio de aplicaciones de diferentes tipos.

## <a name="app-service-plans"></a>Planes del Servicio de aplicaciones
[Planes del Servicio de aplicaciones](azure-web-sites-web-hosting-plans-in-depth-overview.md) especifican el tipo de recurso de proceso con el que se ejecutan sus aplicaciones. Si se esperan cargas de tráfico ligero, puede usar máquinas virtuales compartidas (planes de tarifa **Gratis** y **Compartido**). Para cargas superiores, puede elegir entre varios tamaños de máquinas virtuales dedicadas (planes de tarifa **Básico**, **Estándar** y **Premium**). Varias aplicaciones de App Service pueden compartir el mismo plan, y se escalan y reducen verticalmente en función de los planes de tarifa en el plan.

Si necesita mayor escalabilidad y aislamiento de red, puede ejecutar las aplicaciones en un [entorno del Servicio de aplicaciones](../app-service-web/app-service-app-service-environment-intro.md).

## <a name="pricing"></a>Precios
Para más información sobre el costo de Servicio de aplicaciones, consulte [Precios de Servicio de aplicaciones](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="testdrive-app-service"></a>Prueba de App Service
[Cree una aplicación web, móvil o lógica de prueba](http://go.microsoft.com/fwlink/?LinkId=523751), utilícela durante una hora sin necesidad de proporcionar ninguna tarjeta de crédito, sin compromiso y sin complicaciones.

También puede abrir una [cuenta de Azure gratis](https://azure.microsoft.com/pricing/free-trial/)y seguir uno de nuestros tutoriales de introducción:

* [Implementación de su primera aplicación web en Azure en 5 minutos](../app-service-web/app-service-web-get-started.md)
* [Creación de una aplicación de Android](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Introducción a Aplicaciones de API, ASP.NET y Swagger en el Servicio de aplicaciones de Azure](../app-service-api/app-service-api-dotnet-get-started.md)
* [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO2-->


