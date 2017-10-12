---
title: "Administración de Azure Cloud Services mediante Azure Automation | Microsoft Docs"
description: "Obtenga información acerca de cómo puede usarse el servicio Automatización de Azure para administrar servicios en la nube de Azure a escala."
services: cloud-services, automation
documentationcenter: 
author: jodoglevy
manager: timlt
editor: 
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 6b5acac1b8647c324988c316cd5602b3dba98a1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Administración de Servicios en la nube de Azure mediante Automatización de Azure
Esta guía le ofrece el servicio Automatización de Azure y cómo se puede usar para simplificar la administración de los servicios en la nube de Azure.

## <a name="what-is-azure-automation"></a>¿Qué es Automatización de Azure?
[Automatización de Azure](https://azure.microsoft.com/services/automation/) es un servicio de Azure para simplificar la administración en la nube mediante la automatización de procesos. Mediante Automatización de Azure, se pueden automatizar las tareas de ejecución prolongada, manuales, propensas a errores y que se repiten con frecuencia para aumentar la confiabilidad, la eficiencia y el valioso tiempo para su organización.

Automatización de Azure proporciona un motor de ejecución de flujo de trabajo altamente confiable y de alta disponibilidad que realiza la escalación para satisfacer sus necesidades a medida que crece la organización. En Automatización de Azure, los sistemas de terceros pueden interrumpir los procesos manualmente o en intervalos programados para que las tareas se realicen justo cuando sea necesario.

Reduzca la sobrecarga operativa y libere al personal de TI/DevOps para concentrarse en el trabajo que proporciona valor al negocio trasladando las tareas de administración en la nube para que se ejecuten automáticamente mediante Automatización de Azure.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>¿Cómo puede ayudar el servicio Automatización de Azure a administrar los servicios en la nube de Azure?
Los servicios en la nube de Azure se pueden administrar en Automatización de Azure mediante el uso de cmdlets de PowerShell que están disponibles en las [herramientas de Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automatización de Azure tiene estos cmdlets de PowerShell de servicios en la nube disponibles directamente para que pueda realizar todas las tareas de administración de servicios en la nube dentro del servicio. También puede emparejar estos cmdlets en Automatización de Azure con los cmdlets para otros servicios de Azure, para automatizar tareas complejas a través de los servicios de Azure y sistemas de terceros.

Algunos usos de ejemplo de automatización de Azure para administrar servicios en la nube de Azure son los siguientes:

* [Implementación continua de un servicio en la nube siempre que cscfg o cspkg estén actualizados en el almacenamiento de blobs de Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Reinicio de instancias de servicio en la nube en paralelo (un dominio de actualización a la vez)](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido los aspectos básicos de Automatización de Azure y cómo se puede usar para administrar servicios en la nube de Azure, siga estos vínculos para obtener más información acerca de Automatización de Azure.

* [Información general sobre Automatización de Azure](../automation/automation-intro.md)
* [Mi primer runbook](../automation/automation-first-runbook-graphical.md)
* [Mapa de aprendizaje de Automatización de Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
