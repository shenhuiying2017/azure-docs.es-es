---
title: "Supervisión en Microsoft Azure | Microsoft Docs"
description: Opciones disponibles si desea supervisar algo en Microsoft Azure. Azure Monitor y Log Analytics de Application Insights
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
ms.date: 05/12/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: d4a94a92585420cf92018084437422fd0c66fa2d
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Información general sobre la supervisión en Microsoft Azure
En este artículo se proporciona información general sobre las herramientas de supervisión disponibles en Microsoft Azure. Se aplica a: 
- supervisión de aplicaciones que se ejecutan en Microsoft Azure 
- herramientas y servicios que se ejecutan fuera de Azure que pueden supervisar objetos en Azure. 

Se tratan de los distintos productos y servicios disponibles y cómo funcionan conjuntamente. Puede ayudar a determinar cuáles son las herramientas más adecuadas para el usuario y en qué casos.  

## <a name="why-use-monitoring-and-diagnostics"></a>¿Por qué usar supervisión y diagnóstico?

Los problemas de rendimiento de la aplicación en la nube pueden afectar a su negocio. Con varios componentes interconectados y versiones frecuentes, las degradaciones pueden ocurrir en cualquier momento. Y, si va a desarrollar una aplicación, los usuarios normalmente encuentran problemas que no se han detectado durante las pruebas. Debe tener conocimiento de estos problemas de inmediato y disponer de las herramientas de diagnóstico y solución de problemas. Microsoft Azure tiene una gama de herramientas para identificar estos problemas.

## <a name="how-do-i-monitor-my-azure-cloud-apps"></a>¿Cómo puedo supervisar las aplicaciones de nube de Azure?

Hay una gama de herramientas para la supervisión de servicios y aplicaciones de Azure. Algunas de sus características se superponen. Se debe en parte a motivos históricos y en parte al desenfoque entre el desarrollo y el funcionamiento de una aplicación. 

Estas son las herramientas principales:

-   **Azure Monitor** es una herramienta básica para la supervisión de servicios que se ejecutan en Azure. Proporciona datos a nivel de infraestructura sobre el rendimiento de un servicio y el entorno circundante. Si va a administrar todas las aplicaciones en Azure, decida si desea escalar o reducir verticalmente los recursos y luego Azure Monitor proporciona lo que debe usar para empezar.

-   **Application Insights** puede utilizarse para el desarrollo y como una solución de supervisión de producción. Funciona mediante la instalación de un paquete en la aplicación y, por tanto, ofrece una vista más interna de lo que está sucediendo. Los datos incluyen tiempos de respuesta de dependencias, seguimientos de excepciones, instantáneas de depuración y perfiles de ejecución. Proporciona herramientas inteligentes y sólidas para analizar toda esta telemetría, a fin de ayudar a depurar una aplicación y a saber qué hacen los usuarios con ella. Puede indicar si un pico en los tiempos de respuesta se debe a algo de una aplicación o a algún problema de recursos externo. Si utiliza Visual Studio y la aplicación presenta errores, puede recurrir directamente a las líneas de código del problema para poder corregirlo.  

-   **Log Analytics** está destinado a quienes necesitan ajustar el rendimiento y planificar el mantenimiento de aplicaciones que se ejecutan en producción. Se basa en Azure. Recopila y agrega los datos de muchos orígenes, aunque con un retraso de 10 a 15 minutos. Proporciona una solución integral de administración de TI para Azure, entornos locales e infraestructuras de terceros basadas en la nube (por ejemplo, Amazon Web Services). Proporciona herramientas más enriquecidas para analizar datos en más orígenes, permite realizar consultas complejas en todos los registros y puede alertar de forma proactiva sobre condiciones específicas.  Incluso puede recopilar datos personalizados en su repositorio central para que pueda consultarlos y visualizarlos. 

-   **System Center Operations Manager (SCOM)** está diseñado para administrar y supervisar grandes instalaciones de la nube. Es posible que ya esté familiarizado con esta solución como una herramienta de administración para nubes locales basadas en Windows Sever e Hyper-V, pero también se puede integrar con aplicaciones de Azure y administrarlas. Entre otras cosas, puede instalar Application Insights en las aplicaciones dinámicas existentes.  Si una aplicación deja de funcionar, se lo notifica en cuestión de segundos. Tenga en cuenta que Log Analytics no sustituye a SCOM. Funcionan bien de forma conjunta.  


## <a name="accessing-monitoring-in-the-azure-portal"></a>Acceso a la supervisión en Azure Portal
Todos los servicios de supervisión de Azure ahora están disponibles en un único panel de interfaz de usuario. Para obtener información sobre cómo acceder a este espacio, vea [Introducción a Azure Monitor](monitoring-get-started.md). 

También puede acceder a funciones de supervisión para recursos específicos; para ello, resalte dichos recursos y explore en profundidad sus opciones de supervisión. 

## <a name="examples-of-when-to-use-which-tool"></a>Ejemplos de cuándo se debe utilizar qué herramienta 

En las secciones siguientes se muestran algunos escenarios básicos y qué herramientas se deben usar de forma conjunta. 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>Escenario 1: corregir errores en una aplicación de Azure en desarrollo   

**La mejor opción es usar conjuntamente Application Insights, Azure Monitor y Visual Studio**

Ahora, Azure proporciona toda la funcionalidad del depurador de Visual Studio en la nube. Configure Azure Monitor para enviar telemetría a Application Insights. Habilite Visual Studio para incluir el SDK de Application Insights en la aplicación. En Application Insights, puede usar la asignación de aplicaciones para detectar visualmente qué partes de la aplicación en ejecución tienen un estado incorrecto o no. Para aquellas partes que no tienen un estado correcto, los errores y las excepciones se encuentran disponibles a efectos de exploración. Puede usar los distintos análisis de Application Insights para indagar. Si no está seguro del error, puede usar el depurador de Visual Studio para realizar un seguimiento del código y solucionar un problema más adelante. 

Para más información, vea [Supervisión de aplicaciones web](../application-insights/app-insights-azure-web-apps.md) y consulte el índice de la izquierda para obtener instrucciones sobre los distintos tipos de aplicaciones y lenguajes.  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>Escenario 2: depurar una aplicación web de Azure .NET para los errores que solo se muestran en producción 

> [!NOTE]
> Estas características se encuentran en versión preliminar. 

**La mejor opción es usar Application Insights y, si es posible, Visual Studio para una experiencia integral de depuración.**

Use el depurador de instantáneas de Application Insights para depurar la aplicación. Cuando se produce un umbral de error determinado con componentes de producción, el sistema captura telemetría automáticamente en ventanas de tiempo denominadas "instantáneas". La cantidad capturada es segura para una nube de producción porque es lo suficientemente pequeña como para no afectar al rendimiento, pero lo suficientemente grande como para permitir el seguimiento.  El sistema puede capturar varias instantáneas. Puede buscar un momento específico en Azure Portal o usar Visual Studio para una experiencia completa. Con Visual Studio, los desarrolladores pueden recorrer esa instantánea como si estuvieran depurando en tiempo real. Las variables locales, los parámetros, la memoria y los marcos se encuentran disponibles. Es necesario conceder acceso a los desarrolladores a estos datos de producción a través de un rol de RBAC.  

Para más información, vea [Depuración de instantáneas](../application-insights/app-insights-snapshot-debugger.md). 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>Escenario 3: depurar una aplicación de Azure que usa contenedores o microservicios 

**Igual que el escenario 1. Use Application Insights, Azure Monitor y Visual Studio de forma conjunta** Application Insights también admite la recopilación de telemetría de procesos que se ejecutan dentro de contenedores y desde microservicios (Kubernetes, Docker y Azure Service Fabric). Para más información, [vea este vídeo sobre cómo depurar contenedores y microservicios](https://go.microsoft.com/fwlink/?linkid=848184). 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>Escenario 4: corregir problemas de rendimiento en aplicaciones de Azure

[Application Insights Profiler](../application-insights/app-insights-profiler.md) está diseñado para ayudar a solucionar estos tipos de problemas. Puede identificar y solucionar problemas de rendimiento de aplicaciones que se ejecutan en App Services (Web Apps, Logic Apps, Mobile Apps y API Apps) y de otros recursos de procesos como Virtual Machines, Conjuntos de escalado de máquinas virtuales (VMSS), Cloud Services y Service Fabric. 

> [!NOTE]
> La capacidad de generar perfiles de Virtual Machines, Conjuntos de escalado de máquinas virtuales (VMSS), Cloud Services y Service Fabric se encuentra en versión preliminar.   

Además, se le informa de forma proactiva por correo electrónico sobre determinados tipos de errores, como tiempos de carga de páginas muy lentos, mediante la herramienta de detección inteligente.  No es necesario realizar ninguna configuración en esta herramienta. Para más información, vea [Detección inteligente: anomalías de rendimiento](../application-insights/app-insights-proactive-performance-diagnostics.md) y [Detección inteligente: anomalías de rendimiento](https://azure.microsoft.com/blog/Enhancments-ApplicationInsights-SmartDetection/preview).



## <a name="next-steps"></a>Pasos siguientes
Más información acerca de

* [Azure Monitor en un vídeo de Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Introducción a Azure Monitor](monitoring-get-started.md)
* [Diagnósticos de Azure](../azure-diagnostics.md): si intenta diagnosticar problemas en su aplicación de Cloud Services, Virtual Machines, Conjuntos de escalado de máquinas virtuales o Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) : si está intentando diagnosticar problemas en su aplicación web de Servicio de aplicaciones.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) y la solución de supervisión en producción [Operations Management Suite](https://www.microsoft.com/oms/)
