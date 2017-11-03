---
title: Mover una instancia de Azure Web App a otro grupo de recursos | Microsoft Docs
description: Se describen los escenarios donde puede trasladar aplicaciones web y servicios de aplicaciones de un grupo de recursos a otro.
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: zarizvi
ms.openlocfilehash: 4d5d6540dd0b51a4f172649a1fd9a2343efeceee
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/20/2017
---
# <a name="move-an-azure-web-app-to-another-resource-group"></a>Mover una instancia de Azure Web App a otro grupo de recursos
Puede mover una instancia de Web App y sus recursos relacionados a otro grupo de recursos de la misma suscripción, o a un grupo de recursos de otra suscripción diferente. Esto se realiza como parte de la administración de recursos estándar de Azure. Para más información, consulte [Traslado de recursos de Azure a una nueva suscripción o grupo de recursos](../azure-resource-manager/resource-group-move-resources.md).

## <a name="limitations-when-moving-within-the-same-subscription"></a>Limitaciones a la hora de realizar un traslado dentro de la misma suscripción

Al mover una instancia de Web App _dentro de la misma suscripción_, no se pueden mover los certificados SSL cargados. Sin embargo, puede mover una instancia de Web App al nuevo grupo de recursos sin mover su certificado SSL cargado y la funcionalidad SSL de la aplicación seguirá funcionando. 

Si desea mover el certificado SSL junto con la instancia de Web App, siga estos pasos:

1.  Elimine el certificado cargado desde la instancia de Web App.
2.  Traslade la instancia.
3.  Cargue el certificado en la instancia de Web App trasladada.

## <a name="limitations-when-moving-across-subscriptions"></a>Limitaciones a la hora de realizar un traslado entre suscripciones

Al mover una instancia de Web App _entre suscripciones_, se aplican las limitaciones siguientes:

- El grupo de recursos de destino no debe tener ningún recurso de App Service. Entre los recursos de App Service se incluyen:
    - Web Apps
    - Planes del Servicio de aplicaciones
    - Certificados SSL cargados o importados
    - Entornos del Servicio de aplicaciones
- Todos los recursos de App Service del grupo de recursos se deben mover conjuntamente.
- Los recursos de App Service solo se pueden mover del grupo de recursos en el que se crearon originalmente. Si un recurso de App Service ya no se encuentra en su grupo de recursos original, deberá devolverse a este en primer lugar y, a continuación, se podrá mover entre suscripciones. 
