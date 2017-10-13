---
title: P+F sobre problemas de disponibilidad de aplicaciones y servicios en Microsoft Azure Cloud Services FAQ| Microsoft Docs
description: "En este artículo se enumeran las preguntas frecuentes sobre la disponibilidad de aplicaciones y servicios en Microsoft Azure Cloud Services."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.openlocfilehash: a893a6d009dd018ad440964419e0a5a1963084b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de disponibilidad de aplicaciones y servicios en Azure Cloud Services: preguntas frecuentes (P+F)| Microsoft Docs

En este artículo se incluyen preguntas frecuentes sobre la disponibilidad de aplicaciones y servicios en [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). También puede consultar la [página Tamaños de los servicios en la nube](cloud-services-sizes-specs.md) para obtener información de tamaño.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Mi rol se recicla. ¿Se ha implementado alguna actualización para el servicio en la nube?
Aproximadamente una vez al mes, Microsoft publica una nueva versión de SO invitado para máquinas virtuales PaaS de Azure en Windows. El SO invitado es solo una de estas actualizaciones en la canalización. Otros muchos factores pueden influir en una publicación. Además, Azure se ejecuta en cientos de miles de máquinas. Por lo tanto, es imposible predecir la fecha y hora exactas de cuándo se reiniciarán los roles. Se actualizará la fuente RSS de actualización del SO invitado con la información más reciente disponible, pero se debe considerar el tiempo notificado como un valor aproximado. Somos conscientes de que esto es problemático para los clientes y estamos trabajando en un plan limitar o regular los reinicios de manera precisa.

Para obtener información detallada sobre las actualizaciones recientes del SO invitado, consulte [Matriz de compatibilidad del SDK y versiones del SO invitado de Azure](cloud-services-guestos-update-matrix.md).

Puede encontrar valiosa información sobre los reinicios e indicaciones a datos técnicos de actualizaciones de SO invitado y de SO host en la entrada de blog de MSDN titulada [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (Reinicios de instancias de rol debidos a actualizaciones del SO).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>¿Por qué tarda más de lo habitual la primera solicitud a mi servicio en la nube después de que el servicio ha estado inactivo durante algún tiempo?
Cuando el servidor web recibe la primera solicitud, primero se vuelve a compilar el código y, a continuación, se procesa la solicitud. Por eso la primera solicitud tarda más tiempo que las demás. De forma predeterminada, el grupo de aplicaciones se cierra en casos de inactividad del usuario. También se recicla de forma predeterminada cada 1740 minutos (29 horas).

Los grupos de aplicaciones de Internet Information Services (IIS) se pueden reciclar periódicamente con el fin de evitar estados inestables que pueden llevar a que la aplicación se bloquee, se cuelgue o experimente pérdidas de memoria.

Los documentos siguientes le ayudarán a comprender y mitigar este problema:
* [Fixing slow initial load for IIS](http://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis) (Corrección de carga inicial lenta en IIS)
* [IIS 7.5 web application first request after app-pool recycle very slow](http://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow) (Primera solicitud de aplicación web de IIS.7.5 muy lenta después del reciclaje del grupo de aplicaciones)

Si quiere cambiar el comportamiento predeterminado de IIS, debe usar las tareas de inicio, porque si aplica manualmente cambios a las instancias de rol web, al final se perderán los cambios.

Para más información, consulte [Configuración y ejecución de tareas de inicio para un servicio en la nube](cloud-services-startup-tasks.md).
