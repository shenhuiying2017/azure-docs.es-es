---
title: "Supervisión en Microsoft Azure | Microsoft Docs"
description: Opciones disponibles si desea supervisar algo en Microsoft Azure. Azure Monitor, Application Insights y Log Analytics
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: robb
ms.openlocfilehash: c34211e0c55c10defaa32f1e0a2195514ff3ae5f
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="overview-of-monitoring-in-microsoft-azure"></a>Información general sobre la supervisión en Microsoft Azure
En este artículo se proporciona información general de las herramientas y los servicios implicados en la supervisión holísticamente de Microsoft Azure. Se aplica a:
- Uso de servicios de Azure para supervisar las aplicaciones y la infraestructura de Azure
- Uso de servicios de Azure para supervisar las aplicaciones y la infraestructura que no son de Azure e híbridas
- Uso de servicios de Azure para supervisar las aplicaciones y la infraestructura de Azure

En este artículo se tratan los distintos productos y servicios disponibles y cómo funcionan conjuntamente. Puede ayudar a determinar cuáles son las herramientas más adecuadas para el usuario y en qué casos.  

## <a name="why-use-azures-monitoring-services"></a>¿Por qué usar los servicios de supervisión de Azure?

Los problemas de rendimiento de la aplicación en la nube pueden afectar a su negocio. Con varios componentes interconectados y versiones frecuentes, las degradaciones pueden ocurrir en cualquier momento. Y, si va a desarrollar una aplicación, los usuarios normalmente encuentran problemas que no se han detectado durante las pruebas. Debe tener conocimiento de estos problemas de inmediato y disponer de las herramientas de diagnóstico y solución de problemas. Además, los problemas en la aplicación a menudo son el resultado de la infraestructura subyacente en la que se ejecutan esas aplicaciones, por lo que tener una vista holística de la aplicación e infraestructura es clave para supervisar el entorno de Azure. Microsoft Azure tiene una gama de herramientas para identificar y resolver tales problemas.

## <a name="how-do-i-monitor-my-azure-environment"></a>¿Cómo puedo supervisar mi entorno de Azure?

Hay una variedad de herramientas para supervisar el entorno de Azure, desde el código de aplicación que se ejecuta en Azure a los servicios y la infraestructura que hospeda la aplicación. Estas herramientas funcionan conjuntamente para ofrecer la supervisión en la nube integral e incluyen:

-   **Azure Monitor**: servicio de Azure que funciona como una canalización consolidada para todos los datos de supervisión de servicios de Azure. Le permite acceder a las métricas de rendimiento y a los eventos que describen el funcionamiento de la infraestructura de Azure y todos los servicios de Azure que está utilizando. Azure Monitor es una canalización de datos de supervisión para el entorno de Azure que ofrece datos directamente en Log Analytics, así como herramientas de terceros donde puede obtener información detallada sobre esos datos y combinarlos con datos locales u otros recursos en la nube.

-   **Application Insights**: servicio de Azure que ofrece análisis de usuario y supervisión de rendimiento de aplicaciones. Supervisa el código que ha escrito y las aplicaciones que implementó en Azure, localmente o en otras nubes. Mediante la instrumentación de la aplicación con el SDK de Application Insights puede obtener acceso a una variedad de datos, incluidos los tiempos de respuesta de las dependencias, seguimientos de excepciones, instantáneas de depuración y perfiles de ejecución. Proporciona herramientas eficaces para analizar esta telemetría de aplicación mientras desarrolla y utiliza la aplicación. Se integra profundamente con Visual Studio para que pueda ir directamente a las líneas de código problemáticas para que pueda corregirlas y también ofrece análisis de uso para analizar el uso que el cliente hace de las aplicaciones para los directores de producto.

-   **Log Analytics**: conocido anteriormente como Log Analytics de OMS, es un servicio de Azure que ingiere datos de registro y de métrica de servicios de Azure (mediante Azure Monitor), máquinas virtuales de Azure y en infraestructura local o en otra infraestructura en la nube, y ofrece búsqueda de registros flexible y análisis listo para usar en estos datos. Proporciona herramientas enriquecidas para analizar datos en orígenes, permite realizar consultas complejas en todos los registros y puede alertar de forma proactiva sobre condiciones específicas.  Incluso puede recopilar datos personalizados en su repositorio central para que pueda consultarlos y visualizarlos. También puede aprovechar las soluciones integradas de Log Analytics para obtener información detallada inmediatamente sobre la seguridad y funcionalidad de la infraestructura.

## <a name="accessing-monitoring-in-the-azure-portal"></a>Acceso a la supervisión en Azure Portal
Todos los servicios de supervisión de Azure ahora están disponibles en un único panel de interfaz de usuario. Para obtener información sobre cómo acceder a este espacio, vea [Introducción a Azure Monitor](monitoring-get-started.md). 

También puede acceder a funciones de supervisión para recursos específicos de Azure; para ello, resalte dichos recursos y explore en profundidad sus opciones de supervisión. 

## <a name="examples-of-when-to-use-which-tool"></a>Ejemplos de cuándo se debe utilizar qué herramienta 

En las secciones siguientes se muestran algunos escenarios básicos y qué herramientas se deben usar de forma conjunta. 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>Escenario 1: corregir errores en una aplicación de Azure en desarrollo   

**La mejor opción es usar conjuntamente Application Insights, Azure Monitor y Visual Studio**

Ahora, Azure proporciona toda la funcionalidad del depurador de Visual Studio en la nube. Configure Azure Monitor para enviar telemetría a Application Insights. Habilite Visual Studio para incluir el SDK de Application Insights en la aplicación. En Application Insights, puede usar la asignación de aplicaciones para detectar visualmente qué partes de la aplicación en ejecución tienen un estado correcto o no. Para aquellas partes que no tienen un estado correcto, los errores y las excepciones se encuentran disponibles a efectos de exploración. Puede usar los distintos análisis de Application Insights para indagar. Si no está seguro del error, puede usar el depurador de Visual Studio para realizar un seguimiento del código y solucionar un problema más adelante. 

Para más información, vea [Supervisión de Web Apps](../application-insights/app-insights-azure-web-apps.md) y consulte el índice de la izquierda para obtener instrucciones sobre los distintos tipos de aplicaciones y lenguajes.  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>Escenario 2: depurar una aplicación web de Azure .NET para los errores que solo se muestran en producción 

> [!NOTE]
> Estas características se encuentran en versión preliminar. 

**La mejor opción es usar Application Insights y, si es posible, Visual Studio para una experiencia integral de depuración.**

Use el depurador de instantáneas de Application Insights para depurar la aplicación. Cuando se produce un umbral de error determinado con componentes de producción, el sistema captura telemetría automáticamente en ventanas de tiempo denominadas "instantáneas". La cantidad capturada es segura para una nube de producción porque es lo suficientemente pequeña como para no afectar al rendimiento, pero lo suficientemente grande como para permitir el seguimiento.  El sistema puede capturar varias instantáneas. Puede buscar un momento específico en Azure Portal o usar Visual Studio para una experiencia completa. Con Visual Studio, los desarrolladores pueden recorrer esa instantánea como si estuvieran depurando en tiempo real. Las variables locales, los parámetros, la memoria y los marcos se encuentran disponibles. Es necesario conceder acceso a los desarrolladores a estos datos de producción a través de un [rol de RBAC](../active-directory/role-based-access-built-in-roles.md).  

Para más información, vea [Depuración de instantáneas](../application-insights/app-insights-snapshot-debugger.md). 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>Escenario 3: depurar una aplicación de Azure que usa contenedores o microservicios 

**Igual que el escenario 1. Usar conjuntamente Application Insights y Visual Studio**

Application Insights también admite la recopilación de telemetría de procesos que se ejecutan dentro de contenedores y desde microservicios (Kubernetes, Docker y Azure Service Fabric). Para más información, [vea este vídeo sobre cómo depurar contenedores y microservicios](https://go.microsoft.com/fwlink/?linkid=848184). 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>Escenario 4: corregir problemas de rendimiento en aplicaciones de Azure

[Application Insights Profiler](../application-insights/app-insights-profiler.md) está diseñado para ayudar a solucionar estos tipos de problemas. Puede identificar y solucionar problemas de rendimiento de aplicaciones que se ejecutan en App Services (Web Apps, Logic Apps, Mobile Apps, API Apps y Function Apps) y de otros recursos de procesos como Virtual Machines, Conjuntos de escalado de máquinas virtuales (VMSS), Cloud Services y Service Fabric. 

> [!NOTE]
> La capacidad de generar perfiles de Virtual Machines, Conjuntos de escalado de máquinas virtuales (VMSS), Cloud Services y Service Fabric se encuentra en versión preliminar.   

Además, se le informa de forma proactiva por correo electrónico sobre determinados tipos de errores, como tiempos de carga de páginas muy lentos, mediante la herramienta de detección inteligente.  No es necesario realizar ninguna configuración en esta herramienta. Para más información, consulte [Detección inteligente: anomalías de rendimiento](../application-insights/app-insights-proactive-performance-diagnostics.md).



## <a name="next-steps"></a>Pasos siguientes
Más información acerca de

* [Azure Monitor en un vídeo de Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Introducción a Azure Monitor](monitoring-get-started.md)
* [Diagnósticos de Azure](../azure-diagnostics.md): si intenta diagnosticar problemas en su aplicación de Cloud Services, Virtual Machines, Conjuntos de escalado de máquinas virtuales o Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) : si está intentando diagnosticar problemas en su aplicación web de App Service.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) y la solución de supervisión en producción [Operations Management Suite](https://www.microsoft.com/oms/)
