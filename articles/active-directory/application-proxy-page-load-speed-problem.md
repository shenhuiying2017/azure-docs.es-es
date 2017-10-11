---
title: "Tiempo excesivo de carga de una aplicación con el proxy de aplicación | Microsoft Docs"
description: "Solución de problemas de rendimiento de carga de páginas con el Proxy de aplicación de Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: ce462c90746e6af0dc201686557121665b82b93d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Tiempo excesivo de carga de una aplicación con el proxy de aplicación

Este artículo le ayudará a entender por qué una aplicación con Proxy de aplicación de Azure AD puede tardar mucho tiempo en cargar y lo que puede hacer para resolver este problema.

## <a name="overview"></a>Información general
Si sus aplicaciones funcionan, pero ve una latencia elevada, puede haber algunos ajustes menores en la topología de red que puede realizar para mejorar la velocidad. Para una evaluación de diferentes topologías, consulte el [documento de consideraciones de red](https://docs.microsoft.com/azure/active-directory/application-proxy-network-topology-considerations).

Si estas consideraciones no le sirven de ayuda, desgraciadamente ahora no disponemos de otras recomendaciones de optimización del rendimiento. Como el servicio de Proxy de aplicación se expande a otros centros de datos que le resulten más cercanos, podría empezar a ver directamente una mejora en la latencia. Para ver la lista completa de los centros de datos de Azure, consulte la [página de prueba de latencia](http://www.azurespeed.com/Azure/Latency). 

Con la [herramienta de prueba de los puertos del conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) encontrará los centros de datos con el servicio de Proxy de aplicación. 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Comentarios sobre las ubicaciones de los centros de datos de Proxy de aplicación 
Puede haber centros de datos de Azure que todavía no incluyan el Proxy de aplicación, pero que le supongan una mejora importante de la latencia. Envíe la ubicación del centro de datos a <aadapfeedback@microsoft.com> para que podamos implementar sus comentarios en los planes de expansión.

Estamos trabajando en algunas funcionalidades adicionales que ayuden a mejorar la latencia de los inquilinos que actualmente experimentan largas latencias; compartiremos la documentación en cuanto esté disponible.

## <a name="next-steps"></a>Pasos siguientes
[Trabajo con servidores proxy locales existentes](application-proxy-working-with-proxy-servers.md)
