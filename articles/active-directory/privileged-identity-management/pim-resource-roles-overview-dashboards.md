---
title: 'Introducción: Realización de una revisión de acceso en Privileged Identity Management para recursos de Azure | Microsoft Docs'
description: En este documento se describe cómo realizar una revisión de acceso en PIM para recursos de Azure.
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
ms.openlocfilehash: 61be9873cac462c096599680a6e071e104f3a54c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200627"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>Uso de un panel de control de recursos para realizar una revisión de acceso

Puede usar un panel de recursos para realizar una revisión de acceso en Privileged Identity Management (PIM) para Azure Resources El panel Vista de administrador consta de tres elementos principales:

- Una representación gráfica de las activaciones de rol de recursos.
- Dos gráficos que muestran la distribución de las asignaciones de roles por tipo de asignación.
- Un área de datos que pertenece a nuevas asignaciones de roles.

![Captura de pantalla del panel de la vista de administrador, que muestra gráficos y tablas](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Captura de pantalla del panel de la vista de administrador, que muestra lista de datos](media/azure-pim-resource-rbac/role-settings.png)

La representación gráfica de las activaciones de rol de recursos de los últimos siete días. Estos datos se limitan al recurso seleccionado y muestran las activaciones de los roles más comunes (propietario, colaborador, administrador de acceso de usuario) y de la combinación de todos los roles.

A la derecha del grafo de activaciones, dos gráficos muestran la distribución de las asignaciones de roles por tipo de asignación, tanto de usuario como de grupo. Para cambiar el valor a un porcentaje (o viceversa), seleccione un segmento del gráfico.

Por debajo de los gráficos, verá el número de usuarios y grupos con nuevas asignaciones de roles de los últimos 30 días y una lista de los roles ordenada por asignaciones totales (en orden descendente).


