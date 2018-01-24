---
title: Latencias de informes de Azure Active Directory | Microsoft Docs
description: "Obtenga información acerca de la cantidad de tiempo necesaria para que los eventos de informes aparezcan en Azure Portal"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: markvi;dhanyahk
ms.reviewer: dhanyahk
ms.openlocfilehash: 5ec41817fede495b8262e28d2d614a480d98ff3b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="azure-active-directory-reporting-latencies"></a>Latencias de informes de Azure Active Directory

Con los [informes](active-directory-preview-explainer.md) de Azure Active Directory, obtendrá toda la información que necesita para determinar cómo funciona el entorno. La cantidad de tiempo necesaria para que los datos de informes aparezcan en Azure Portal se denomina latencia. 

En este tema se muestra la información de latencia de todas las categorías de informes en Azure Portal. 


## <a name="activity-reports"></a>Informes de actividad

Hay dos áreas de informes de actividad:

- **Actividades de inicio de sesión** : información sobre el uso de las aplicaciones administradas y las actividades de inicio de sesión de usuario
- **Registros de auditoría** : información de la actividad del sistema acerca de los usuarios y administración de grupos, sus aplicaciones administradas y actividades de directorio

La tabla siguiente enumera la información de latencia para los informes de actividad.

| Informe | Mínima | Media | Comentarios |
| :-- | --- | --- | :-- |
| Registros de auditoría | 30 minutos  | 1 hora  |En algunos casos, los datos de actividad de auditoría pueden tardar hasta 2 horas en aparecer.|
| Inicios de sesión | 15 minutos  | 2 horas |En algunos casos, los datos de actividad de inicio de sesión pueden tardar hasta 24 horas en aparecer. Aquí se incluyen los datos de actividad de inicio de sesión procedentes de aplicaciones heredadas de Office. |







## <a name="security-reports"></a>Informes de seguridad

Hay dos áreas de informes de seguridad:

- **Inicios de sesión peligrosos**: un inicio de sesión peligroso es un indicador de un intento de inicio de sesión que puede haber realizado alguien que no es el propietario legítimo de una cuenta de usuario. 
- **Usuarios marcados en riesgo**: un usuario en peligro es un indicador de una cuenta de usuario que puede haber estado en peligro. 

La tabla siguiente enumera la información de latencia para los informes de seguridad.

| Informe | Mínima | Media | Máxima |
| :-- | --- | --- | --- |
| Usuarios en riesgo          | 5 minutos   | 15 minutos  | 2 horas  |
| Inicios de sesión no seguros         | 5 minutos   | 15 minutos  | 2 horas  |

## <a name="risk-events"></a>Eventos de riesgo

Azure Active Directory utiliza algoritmos y heurística de aprendizaje automático adaptable para detectar acciones sospechosas que están relacionadas con las cuentas de usuario. Cada acción sospechosa detectada se almacena en un registro llamado evento de riesgo.

La tabla siguiente enumera la información de latencia para eventos de riesgo.

| Informe | Mínima | Media | Máxima |
| :-- | --- | --- | --- |
| Inicios de sesión desde direcciones IP anónimas |5 minutos |15 minutos |2 horas |
| Inicios de sesión desde ubicaciones desconocidas |5 minutos |15 minutos |2 horas |
| Usuarios con credenciales perdidas |2 horas |4 horas |8 horas |
| Viaje imposible a ubicaciones inusuales |5 minutos |1 hora |8 horas  |
| Inicios de sesión desde dispositivos infectados |2 horas |4 horas |8 horas  |
| Inicios de sesión desde direcciones IP con actividad sospechosa |2 horas |4 horas |8 horas  |



## <a name="next-steps"></a>pasos siguientes

Si desea obtener más información acerca de los informes de actividad en Azure Portal, consulte:

- [Informes de actividad de inicio de sesión en el portal de Azure Active Directory](active-directory-reporting-activity-sign-ins.md)
- [Informes de actividad de auditoría en el portal de Azure Active Directory](active-directory-reporting-activity-audit-logs.md)

Si desea obtener más información acerca de los informes de seguridad en Azure Portal, consulte:

- [Informe de seguridad de usuarios en riesgo en el portal de Azure Active Directory](active-directory-reporting-security-user-at-risk.md)
- [Informe de inicios de sesión poco seguros en el portal de Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md)

Si desea obtener más información acerca de los eventos de riesgo, consulte [Eventos de riesgo de Azure Active Directory](active-directory-reporting-risk-events.md).
