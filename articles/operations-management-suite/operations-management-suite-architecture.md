---
title: Arquitectura de Operations Management Suite (OMS) | Microsoft Docs
description: "Microsoft Operations Management Suite (OMS) es la solución de administración de TI basada en la nube de Microsoft que le ayuda a administrar y proteger su infraestructura local y en la nube.  Este artículo identifica los distintos servicios que se incluyen en OMS y proporciona vínculos a contenido detallado."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 40e41686-7e35-4d85-bbe8-edbcb295a534
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 76f69946724b5297b1f9a1f715819c69c4a4a51d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="oms-architecture"></a>Arquitectura de OMS
[Operations Management Suite (OMS)](https://azure.microsoft.com/documentation/services/operations-management-suite/) es una colección de servicios basados en la nube para la administración de sus entornos locales y en la nube.  En este artículo se describen los distintos componentes locales y en la nube de OMS y su arquitectura informática en la nube de alto nivel.  Puede consultar la documentación para cada servicio para obtener más detalles.

## <a name="log-analytics"></a>Log Analytics
Todos los datos que recopila [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) se almacenan en el repositorio de OMS que se hospeda en Azure.  Los orígenes conectados generan datos recopilados en el repositorio de OMS.  Actualmente hay tres tipos de orígenes conectados compatibles.

* Un agente instalado en un equipo [Windows](../log-analytics/log-analytics-windows-agents.md) o [Linux](../log-analytics/log-analytics-linux-agents.md) conectado directamente a OMS.
* Un grupo de administración de System Center Operations Manager (SCOM) [conectado a Log Analytics](../log-analytics/log-analytics-om-agents.md) .  Los agentes de SCOM continúan comunicándose con los servidores de administración, que reenvían eventos y datos de rendimiento a Log Analytics.
* Una [cuenta de almacenamiento de Azure](../log-analytics/log-analytics-azure-storage.md) que recopila datos de [Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) de un rol de trabajo, rol web o máquina virtual de Azure.

Los orígenes de datos definen los datos que Log Analytics recopila de orígenes conectados, incluidos los registros de eventos y los contadores de rendimiento.  Las soluciones agregan funcionalidad a OMS y pueden agregarse fácilmente al espacio de trabajo desde la [Galería de soluciones de OMS](../log-analytics/log-analytics-add-solutions.md).  Algunas soluciones pueden requerir una conexión directa a Log Analytics de agentes SCOM mientras otros pueden requerir que se instale un agente adicional.

Log Analytics tiene un portal basado en web que puede utilizar para administrar recursos de OMS, agregar y configurar soluciones de OMS y ver y analizar datos en el repositorio de OMS.

![Arquitectura de alto nivel de Log Analytics](media/operations-management-suite-architecture/log-analytics.png)

## <a name="azure-automation"></a>Automatización de Azure
[runbooks de Automatización de Azure](http://azure.microsoft.com/documentation/services/automation) se ejecutan en la nube de Azure y pueden obtener acceso a los recursos que están en Azure, en otros servicios en la nube, o a los que se puede obtener acceso desde Internet público.  También puede designar máquinas locales en su centro de datos local con [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md) para que los Runbooks puedan acceder a recursos locales.

[configuraciones de DSC](../automation/automation-dsc-overview.md) almacenadas en Automatización de Azure pueden aplicarse directamente a máquinas virtuales de Azure.  Otras máquinas físicas y virtuales pueden solicitar las configuraciones del servidor de extracción de DSC de Automatización de Azure.

Automatización de Azure tiene una solución de OMS que muestra las estadísticas y vínculos para iniciar el Portal de Azure para todas las operaciones.

![Arquitectura de alto nivel de Automatización de Azure](media/operations-management-suite-architecture/automation.png)

## <a name="azure-backup"></a>Copia de seguridad de Azure
Los datos protegidos en [Copia de seguridad de Azure](http://azure.microsoft.com/documentation/services/backup) se almacenan en un almacén de copia de seguridad ubicado en una región geográfica determinada.  Los datos se replican en la misma región y, según el tipo de almacén, también pueden replicarse en otra región para obtener una redundancia adicional.

Copia de seguridad de Azure tiene tres escenarios fundamentales.

* Máquina de Windows con agente de copia de seguridad de Azure.  Esto le permite realizar una copia de seguridad de archivos y carpetas desde cualquier servidor o cliente de Windows directamente a su almacén de copia de seguridad de Azure.  
* System Center Data Protection Manager (DPM) o servidor de Copia de seguridad de Microsoft Azure. Esto le permite aprovechar DPM o el servidor de Copia de seguridad de Microsoft Azure para realizar la copia de seguridad de archivos y carpetas, además de las cargas de trabajo de aplicaciones como SQL Server y SharePoint al almacenamiento local y, a continuación, efectuar la replicación en el almacén de copia de seguridad de Azure.
* Extensiones de la máquina virtual de Azure.  Esto le permite realizar una copia de seguridad de máquinas virtuales de Azure en su almacén de Copia de seguridad de Azure.

Copia de seguridad de Azure tiene una solución de OMS que muestra las estadísticas y vínculos para iniciar el Portal de Azure para todas las operaciones.

![Arquitectura de alto nivel de Copia de seguridad de Azure](media/operations-management-suite-architecture/backup.png)

## <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) organiza la replicación, la conmutación por error y la conmutación por recuperación de máquinas virtuales y servidores físicos. Los datos de replicación se intercambian entre hosts de Hyper-V, hipervisores de VMware y servidores físicos en los centros de datos principal y secundario, o entre el centro de datos y el almacenamiento de Azure.  Site Recovery almacena metadatos en almacenes ubicados en una región geográfica de Azure determinada. No se almacenan datos replicados por el servicio de Site Recovery.

Azure Site Recovery tiene tres escenarios fundamentales de replicación.

**Replicación de máquinas virtuales de Hyper-V**

* Si las máquinas virtuales de Hyper-V se administran en nubes VMM, puede realizar la replicación en un centro de datos secundario o en Almacenamiento de Azure.  La replicación en Azure se realiza a través de una conexión a Internet segura.  La replicación en un centro de datos secundario se realiza a través de LAN.
* Si las máquinas virtuales de Hyper-V no están administradas por VMM, puede realizar la replicación solo en Almacenamiento de Azure.  La replicación en Azure se realiza a través de una conexión a Internet segura.

**Replicación de máquinas virtuales de VMWare**

* Puede realizar la replicación de máquinas virtuales de VMware en un centro de datos secundario con VMware o en Almacenamiento de Azure.  La replicación en Azure puede realizarse en una VPN de sitio o sitio o a sitio o Azure ExpressRoute, o a través de una conexión a Internet segura. La replicación a un centro de datos secundario se realiza a través del canal de datos InMage Scout.

**Replicación de servidores físicos de Windows o Linux** 

* Puede realizar la replicación de servidores físicos en un centro de datos secundario o en Almacenamiento de Azure. La replicación en Azure puede realizarse en una VPN de sitio o sitio o a sitio o Azure ExpressRoute, o a través de una conexión a Internet segura. La replicación a un centro de datos secundario se realiza a través del canal de datos InMage Scout.  Azure Site Recovery tiene una solución de OMS que muestra algunas estadísticas, pero debe usar el Portal de Azure para todas las operaciones.

![Arquitectura de alto nivel de Azure Site Recovery](media/operations-management-suite-architecture/site-recovery.png)

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Obtenga información sobre [Azure Automation](https://azure.microsoft.com/documentation/services/automation).
* Obtenga información sobre [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* Obtenga información sobre [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).

