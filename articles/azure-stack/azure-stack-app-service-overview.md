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
ms.date: 10/10/2017
ms.author: anwestg
ms.openlocfilehash: fd2d355b2556faddb06acf2998b54ffcc9aa7919
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="app-service-on-azure-stack-overview"></a>Introducción a App Service en Azure Stack

Azure App Service en Azure Stack es una oferta de plataforma como servicio (PaaS) de Microsoft Azure disponible en Azure Stack. El servicio permite a los clientes, internos o externos, crear aplicaciones de Azure Functions, API y web para cualquier plataforma o dispositivo. Pueden integrar sus aplicaciones con aplicaciones locales y automatizar sus procesos empresariales. Los operadores en la nube de Azure Stack pueden ejecutar las aplicaciones en máquinas virtuales totalmente administradas, con la elección de los recursos de máquinas virtuales compartidas o máquinas virtuales dedicadas.

Azure App Service incluye nuevas funcionalidades para automatizar procesos empresariales y hospedar las API en la nube. Como un servicio integrado único, Azure App Service le permite crear distintos componentes, como sitios web, API de RESTful y procesos empresariales, en una única solución.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>¿Por qué ofrecer Azure App Service en Azure Stack?

Estas son algunas de las características y funcionalidades principales de App Service:
- **Varios lenguajes y plataformas**: App Service es compatible con ASP.NET, Node.js, Java, PHP y Python. También puede ejecutar Windows PowerShell y otros scripts o ejecutables en máquinas virtuales de App Service.
- **Optimización de DevOps**: configure la integración y la implementación continuas con GitHub, Git local o BitBucket. Promueva actualizaciones a través de entornos de ensayo y de prueba. Administre las aplicaciones de App Service mediante Azure PowerShell o la interfaz de la línea de comandos (CLI) multiplataforma.
- **Integración con visual Studio**: existen herramientas dedicadas en Visual Studio que permiten optimizar las tareas de creación e implementación de aplicaciones.

## <a name="app-types-in-app-service"></a>Tipos de aplicaciones en App Service

App Service ofrece varios tipos de aplicación, cada uno pensado para hospedar una carga de trabajo específica:

- [Web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) para hospedar sitios y aplicaciones web.
- [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) para hospedar API de RESTful.
- Azure Functions para hospedar cargas de trabajo sin servidor basadas en eventos.

La palabra app (aplicación) aquí hace referencia a los recursos de hospedaje dedicados a ejecutar una carga de trabajo. Si se toma "aplicación web" como ejemplo, probablemente piense en una aplicación web como aquellos recursos de proceso y código de aplicación que juntos ofrecen funcionalidad a un explorador. Pero en App Service, una aplicación web son los recursos de procesos que Azure Stack proporciona para hospedar su código de aplicación.

La aplicación puede estar compuesta de varias aplicaciones de App Service de diferentes tipos. Por ejemplo, si la aplicación se compone de un front-end web y un back-end de la API de RESTful, se pueden realizar las operaciones siguientes:
- Implementar los dos (front-end y API) en una sola aplicación web.
- Implementar el código de front-end en una aplicación web y el código de back-end en una aplicación de API.

   ![](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>¿Qué es un plan de App Service?

El proveedor de recursos de App Service usa el mismo código que Azure App Service. Como resultado, merece la pena describir algunos conceptos comunes. En App Service, el contenedor de precios para las aplicaciones se denomina plan de App Service. Representa el conjunto de máquinas virtuales dedicadas usadas para hospedar las aplicaciones. Dentro de una suscripción determinada, pueden haber varios planes de App Service.

En Azure, hay trabajados compartidos y dedicados. Un trabajo compartido admite el hospedaje de aplicaciones para varios inquilinos de alta densidad, y solo existe un conjunto de trabajos compartidos. Solo un inquilino usa los servidores dedicados que pueden ser de tres tamaños: pequeño, mediano y grande. Las necesidades de los clientes locales no siempre se pueden describir con estos términos. En App Service en Azure Stack, los administradores de proveedores de recursos pueden definir los niveles de trabajo que quieren que estén disponibles. Según las necesidades concretas de hospedaje, puede definir varios conjuntos de trabajos compartidos o diferentes conjuntos de trabajos dedicados. Mediante el uso de esas definiciones de nivel de trabajo, pueden definir sus propias SKU de precios.

## <a name="portal-features"></a>Características del portal

App Service en Azure Stack usa la misma interfaz de usuario que Azure App Service, como ocurre con el back-end. Algunas características están deshabilitadas y no funcionan en Azure Stack. Las expectativas o los servicios específicos de Azure que requieren esas características no están disponibles aún en Azure Stack.

## <a name="next-steps"></a>Pasos siguientes


- [Antes de empezar a trabajar con App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Instale el proveedor de recursos de App Service](azure-stack-app-service-deploy.md)

También puede probar otros [servicios de plataforma como servicio (PaaS)](azure-stack-tools-paas-services.md), como el [proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md) y el [proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md).
