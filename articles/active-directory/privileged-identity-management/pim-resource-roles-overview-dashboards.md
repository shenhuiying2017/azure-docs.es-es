---
title: Cómo realizar una revisión de acceso en Privileged Identity Management para Azure Resources | Microsoft Docs
description: En este documento se describe cómo realizar una revisión de acceso en PIM para Azure Resources.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: f1bcf114b997c3056016b84cafc28253ea1af28e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="resource-dashboards"></a>Paneles de recursos

El panel Vista de administrador consta de cuatro elementos principales. Una representación gráfica de las activaciones de rol de recursos de los últimos siete días. Estos datos se limitan al recurso seleccionado y muestran las activaciones de los roles más comunes (Propietario, Colaborador, Administrador de acceso de usuario) y de la combinación de todos los roles.

A la derecha del grafo de activaciones hay dos gráficos que muestran la distribución de las asignaciones de roles por tipo de asignación, tanto de usuario como de grupo. Al seleccionar un segmento del gráfico, el valor cambia a porcentaje (o viceversa).

![](media/azure-pim-resource-rbac/rbac-overview-top.png)

Por debajo de los gráficos, verá el número de usuarios y grupos con nuevas asignaciones de roles de los últimos 30 días (a la izquierda) y una lista de los roles ordenada por asignaciones totales (descendente).

![](media/azure-pim-resource-rbac/role-settings.png)
