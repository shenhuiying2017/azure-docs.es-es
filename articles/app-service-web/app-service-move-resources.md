---
title: "Trasladar recursos de la aplicación web a otro grupo de recursos"
description: Se describen los escenarios donde puede trasladar aplicaciones web y servicios de aplicaciones de un grupo de recursos a otro.
services: app-service
documentationcenter: 
author: ZainRizvi
manager: wpickett
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2016
ms.author: zarizvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 08b8953b91a87a131f70309e41be3a9ce0123299


---
# <a name="supported-move-configurations"></a>Configuraciones admitidas para traslados
Puede trasladar recursos de aplicaciones web de Azure con la [API Mover recursos de ARM](../resource-group-move-resources.md).

Las aplicaciones web de Azure admiten actualmente los siguientes escenarios de traslado:

* Mover todo el contenido de un grupo de recursos (aplicaciones web, planes de servicio de aplicaciones y certificados) a otro grupo de recursos 
  * Nota: El grupo de recursos de destino no puede contener ningún recurso Microsoft.Web en este escenario.
* Trasladar aplicaciones web individuales a un grupo de recursos diferente, manteniéndolas hospedadas en el plan de servicio de aplicaciones en que se encuentran actualmente (el plan de servicio de aplicaciones se encuentra en el grupo de recursos anterior)




<!--HONumber=Nov16_HO3-->


