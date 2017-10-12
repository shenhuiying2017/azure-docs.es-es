---
title: "Integración del SDK de Windows Universal para Azure Mobile Engagement | Microsoft Docs"
description: "Integración del SDK de Windows Universal para Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 9ded187d-5c07-4377-a41c-ce205dd38b50
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: d616ad58156a19e89b3e106639a38df67cbd0abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Integración del SDK de Windows Universal para Azure Mobile Engagement
Este documento describe todas las opciones de integración y configuración disponibles para el SDK de Windows Universal para Azure Mobile Engagement.

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar este tutorial, primero debe completar la [Introducción a Azure Mobile Engagement para aplicaciones Android](mobile-engagement-windows-store-dotnet-get-started.md), un tutorial de 15 minutos.

## <a name="advanced-features"></a>Características avanzadas
### <a name="reporting-features"></a>Características de informes
Puede agregar estas características:

1. [Opciones de informes avanzados](mobile-engagement-windows-store-advanced-reporting.md)
2. [Opciones de configuración avanzada](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Notificaciones
[Integración de Reach (notificaciones) en su aplicación universal de Windows](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Implementación del plan de etiquetas:
[Uso de la API de etiquetado avanzado de Mobile Engagement en su aplicación universal de Windows](mobile-engagement-windows-store-use-engagement-api.md)

## <a name="release-notes"></a>Notas de la versión
### <a name="341-11032016"></a>3.4.1 (11/03/2016)

* Mejoras de estabilidad.

Para versiones anteriores, consulte las [notas de la versión completas](mobile-engagement-windows-store-release-notes.md)

## <a name="upgrade-procedures"></a>Procedimientos de actualización
Si ya integró una versión anterior de Engagement en la aplicación, debería tener en cuenta los siguientes puntos a la hora de actualizar el SDK.

Es posible que tenga que seguir varios procedimientos si se perdió varias versiones del SDK. Consulte los [procedimientos de actualización](mobile-engagement-windows-store-upgrade-procedure.md)completos. Por ejemplo, si migra desde 0.10.1 a 0.11.0, primero debe seguir el procedimiento "de 0.9.0 a 0.10.1" y luego el procedimiento "de 0.10.1 a 0.11.0".

### <a name="from-330-to-340"></a>De 3.3.0 a 3.4.0
#### <a name="test-logs"></a>Registros de prueba
Los registros de consola generados por el SDK ahora se pueden habilitar, deshabilitar o filtrar. Para personalizarlo, actualice la propiedad `EngagementAgent.Instance.TestLogEnabled` a uno de los valores disponibles en la enumeración `EngagementTestLogLevel`, por ejemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

#### <a name="resources"></a>Recursos
Se ha mejorado la superposición de Reach. Forma parte de los recursos del paquete NuGet del SDK.

Al actualizar a la nueva versión del SDK puede elegir si desea conservar los archivos existentes de la carpeta de superposición de los recursos:

* Si la superposición anterior funciona o va a integrar los elementos `WebView` manualmente, puede decidir mantener los archivos de salida. El funcionamiento no se verá afectado.
* Para actualizar a la nueva superposición, reemplace toda la carpeta `overlay` de sus recursos por la nueva desde el paquete del SDK (aplicaciones para UWP: tras la actualización, puede obtener la nueva carpeta de superposición desde %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> El uso de la nueva superposición sobrescribirá todas las personalizaciones realizadas en la versión anterior.
> 
> 

### <a name="upgrade-from-older-versions"></a>Actualizar de versiones anteriores
Consulte [Procedimientos de actualización](mobile-engagement-windows-store-upgrade-procedure.md)

