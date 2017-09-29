---
title: "Introducción a App Service: Azure Stack | Microsoft Docs"
description: "Introducción a App Service en Azure Stack"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 13928744e7d2fc145662c2a0d5c26d512cf02150
ms.contentlocale: es-es
ms.lasthandoff: 09/15/2017

---

# <a name="app-service-on-azure-stack-overview"></a>Introducción a App Service en Azure Stack

Azure App Service en Azure Stack es la oferta de Azure para Azure Stack. El instalador de App Service en Azure Stack crea el siguiente conjunto de instancias de rol:

*  Controller
*  Administración (se crean dos instancias)
*  FrontEnd
*  Publicador
*  Trabajo (en modo compartido)

Además, el instalador de App Service en Azure Stack crea un servidor de archivos.
    
## <a name="whats-new-in-the-first-release-candidate-of-app-service-on-azure-stack"></a>Novedades de la primera versión candidata para lanzamiento de App Service en Azure Stack
![App Service en el portal de Azure Stack][1]

La primera versión candidata para lanzamiento de App Service en Azure Stack se basa en la tercera versión preliminar y aporta mejoras y nuevas funcionalidades:

* Azure Functions en entornos de Azure Stack basado en los Servicios de federación de Active Directory (AD FS) 
* Compatibilidad con el inicio de sesión único para el portal de Functions y las herramientas avanzadas de desarrollador (Kudu)
* Compatibilidad con Java para aplicaciones web, móviles y de API
* Administración de niveles de trabajo por conjuntos de escalado de máquinas virtuales para mejorar las funcionalidades de escalabilidad horizontal de los administradores de servicios
* Localización de la experiencia de administración
* Mayor estabilidad del servicio
* Actualizaciones de la experiencia con el portal del inquilino y actualizaciones del proceso de instalación

## <a name="limitations-of-the-technical-preview"></a>Limitaciones de la versión preliminar técnica

No se ofrece soporte técnico para las versiones preliminares de App Service en Azure Stack, aunque se supervisará el foro de MSDN de Azure Stack. No se deben poner cargas de trabajo de producción en esta versión preliminar. Tampoco existen actualizaciones entre versiones preliminares de App Service en Azure Stack. Los principales objetivos de estas versiones preliminares son mostrar lo que ofrecemos y obtener comentarios. 

## <a name="what-is-an-app-service-plan"></a>¿Qué es un plan de App Service?

El proveedor de recursos de App Service usa el mismo código que Azure App Service. Como resultado, merece la pena describir algunos conceptos comunes. En App Service, el contenedor de precios para las aplicaciones se denomina plan de App Service. Representa el conjunto de máquinas virtuales dedicadas usadas para hospedar las aplicaciones. Dentro de una suscripción determinada, pueden haber varios planes de App Service. 

En Azure, hay trabajados compartidos y dedicados. Un trabajo compartido admite el hospedaje de aplicaciones para varios inquilinos de alta densidad, y solo existe un conjunto de trabajos compartidos. Solo un inquilino usa los servidores dedicados que pueden ser de tres tamaños: pequeño, mediano y grande. Las necesidades de los clientes locales no siempre se pueden describir con estos términos. En App Service en Azure Stack, los administradores de proveedores de recursos pueden definir los niveles de trabajo que quieren que estén disponibles. Los administradores pueden definir varios conjuntos de trabajos compartidos o diferentes conjuntos de trabajos dedicados en función de sus necesidades únicas de hospedaje. Mediante el uso de esas definiciones de nivel de trabajo, pueden definir sus propias SKU de precios.

## <a name="portal-features"></a>Características del portal

App Service en Azure Stack usa la misma interfaz de usuario que Azure App Service, como ocurre con el back-end. Algunas características están deshabilitadas y no funcionan en Azure Stack. Las expectativas o los servicios específicos de Azure que requieren esas características no están disponibles aún en Azure Stack. 

## <a name="next-steps"></a>Pasos siguientes

- [Antes de empezar a trabajar con App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Instale el proveedor de recursos de App Service](azure-stack-app-service-deploy.md)

También puede probar otros [servicios de plataforma como servicio (PaaS)](azure-stack-tools-paas-services.md), como el [proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md) y el [proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md).

<!--Image references-->
[1]: ./media/azure-stack-app-service-overview/AppService_Portal.png

