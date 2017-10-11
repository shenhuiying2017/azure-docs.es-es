---
title: "Trasladar recursos de la aplicación web a otro grupo de recursos"
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
ms.date: 12/21/2016
ms.author: zarizvi
ms.openlocfilehash: 1b5059dc052005b6079f70ecf6771a3771df8d87
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="supported-move-configurations"></a>Configuraciones admitidas para traslados
Puede trasladar recursos de aplicaciones web de Azure con la [API Trasladar recursos de Resource Manager](../azure-resource-manager/resource-group-move-resources.md).

Las aplicaciones web de Azure admiten actualmente los siguientes escenarios de traslado:

* Mover todo el contenido de un grupo de recursos (aplicaciones web, planes de App Service y certificados) a otro grupo de recursos. 
   > [!Note]
   > Nota: El grupo de recursos de destino no puede contener ningún recurso Microsoft.Web en este escenario.

* Trasladar aplicaciones web individuales a un grupo de recursos diferente, manteniéndolas hospedadas en el plan de App Service en que se encuentran actualmente (el plan de App Service se encuentra en el grupo de recursos anterior).


