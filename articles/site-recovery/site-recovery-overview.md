---
title: "¿Qué es Site Recovery? | Microsoft Docs"
description: "Proporciona información general sobre el servicio de Azure Site Recovery y resume escenarios de implementación."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 64d085bff08d9a824204851d32504fac3e79024c


---
# <a name="what-is-site-recovery"></a>¿Qué es Site Recovery?
¡Bienvenido al servicio Azure Site Recovery! Este artículo proporciona una introducción rápida de Site Recovery.

Su organización necesita una estrategia de recuperación ante desastres y continuidad del negocio (BCDR) que proteja y mantenga disponibles las aplicaciones y los datos durante los tiempos de inactividad planeados e imprevistos, además de restablecer las condiciones de trabajo normales lo antes posible.

Site Recovery contribuye a su estrategia BCDR mediante la coordinación de la replicación de máquinas virtuales locales y servidores físicos. Puede replicar servidores y máquinas virtuales de un centro de datos principal en la nube (Azure) o en un centro de datos secundario.

Cuando se producen interrupciones en el sitio principal, se realiza la conmutación por error al sitio secundario para mantener disponibles y accesibles las cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal.

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery en el Portal de Azure
Azure tiene dos [modelos de implementación](../resource-manager-deployment-model.md) diferentes para crear recursos y utilizarlos. El modelo de Azure Resource Manager y el modelo de administración de servicios clásico. Azure también tiene dos portales. El [Portal de Azure clásico](https://manage.windowsazure.com/) y [Azure Portal](https://portal.azure.com).

* Site Recovery está disponible tanto en el portal clásico como en Azure Portal.
* En el Portal de Azure clásico, Site Recovery se puede usar con el modelo de administración de servicios clásico.
* En Azure Portal, se puede usar tanto el modelo de implementación clásico como el modelo de Resource Manager.

La información de este artículo se aplica tanto a la implementación clásica como a la del Portal de Azure. Indicaremos las diferencias cuando proceda.

## <a name="why-deploy-site-recovery"></a>¿Por qué implementar Site Recovery?
Esto es lo que Site Recovery puede hacer por su empresa:

* **Simplificar BCDR**: puede replicar, conmutar por error y recuperar muchas cargas de trabajo desde una única ubicación en Azure Portal. Site Recovery coordina la replicación y la conmutación por error, sin interceptar los datos de aplicaciones.
* **Replicación flexible**: puede replicar cualquier carga de trabajo que se ejecuta en máquinas virtuales de Hyper-V y VMware, y en servidores físicos de Windows o Linux compatibles.
* **Eliminación de un centro de datos secundario**: puede replicar las cargas de trabajo en Azure, en lugar de en un sitio secundario. Esto elimina el costo y la complejidad de mantener un centro de datos secundario. Los datos replicados se almacenan en Azure Storage con toda la resistencia que proporciona. Cuando se produce la conmutación por error, las máquinas virtuales de Azure se crean con los datos replicados.
* **Pruebas de replicación sencillas**: puede ejecutar fácilmente conmutaciones por error de prueba para permitir maniobras de recuperación ante desastres sin que los entornos de producción se vean afectados.
* **Conmutación por error y recuperación**: puede ejecutar conmutaciones por error planeadas para interrupciones previstas sin pérdida de datos o conmutaciones por error imprevistas con una pérdida de datos mínima (según la frecuencia de replicación) ante desastres inesperados. Luego, puede conmutar por recuperación a su sitio principal cuando vuelva a estar disponible.
* **Conmutación por error de varias máquinas virtuales**: puede configurar planes de recuperación que incluyen scripts y runbooks de Azure Automation. Los planes de recuperación le permiten modelar y personalizar la conmutación por error y la recuperación de aplicaciones de varios niveles que se distribuyen en varias máquinas virtuales.
* **Integración con tecnologías de BCDR existentes**: Site Recovery se integra con otras tecnologías de BCDR. Por ejemplo, puede utilizar Site Recovery para proteger el back-end de SQL Server de cargas de trabajo corporativas, incluida la compatibilidad nativa para SQL Server AlwaysOn, a fin de administrar la conmutación por error de grupos de disponibilidad.

## <a name="what-can-i-replicate"></a>¿Qué puedo replicar?
Este es un resumen de lo que puede replicar con Site Recovery.

![De local a local](./media/site-recovery-overview/asr-overview-graphic.png)

| **REPLICATE** | **REPLICACIÓN EN** |
| --- | --- |
| Máquinas virtuales de VMware locales |[Las tablas de Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Sitio secundario](site-recovery-vmware-to-vmware.md) |
| Máquinas virtuales de Hyper-V locales administradas en nubes de VMM |[Las tablas de Azure](site-recovery-vmm-to-azure.md)<br/><br/> [Sitio secundario](site-recovery-vmm-to-vmm.md) |
| Máquinas virtuales de Hyper-V locales administradas en nubes VMM, con almacenamiento SAN |[Sitio secundario](site-recovery-vmm-san.md) |
| Máquinas virtuales de Hyper-V locales, sin VMM |[Las tablas de Azure](site-recovery-hyper-v-site-to-azure.md) |
| Servidores físicos de Windows o Linux locales |[Las tablas de Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Sitio secundario](site-recovery-vmware-to-vmware.md) |

## <a name="how-does-site-recovery-protect-workloads"></a>¿Cómo se protegen las cargas de trabajo de Site Recovery?
Site Recovery proporciona replicación compatible con la aplicación, de modo que las cargas de trabajo y las aplicaciones se siguen ejecutando de forma coherente cuando se producen interrupciones.

* **Instantáneas coherentes con la aplicación**: las máquinas se replican mediante instantáneas coherentes con la aplicación para aplicaciones únicas o de varios niveles. Además de capturar los datos del disco, las instantáneas coherentes con la aplicación capturan todos los datos en memoria y todas las transacciones en curso.
* **Replicación casi sincrónica**: Site Recovery proporciona una frecuencia de replicación de tan solo 30 segundos para Hyper-V y replicación continua para VMware.
* **Planes de recuperación flexibles**: puede crear y personalizar planes de recuperación con scripts externos y acciones manuales. La integración con runbooks de Azure Automation permite recuperar toda una pila de aplicaciones con un solo clic.
* **Integración con SQL Server AlwaysOn**: puede administrar la conmutación por error de grupos de disponibilidad mediante planes de recuperación.
* **Biblioteca de automatización**: una rica biblioteca de Automatización de Azure que proporciona scripts específicos de la aplicación y preparados para la producción que pueden descargarse e integrarse con Site Recovery.
* **Administración de red simple**: la administración de red avanzada en Site Recovery y Azure simplifica los requisitos de red para una aplicación, incluida la reserva de direcciones IP, la configuración de equilibradores de carga o la integración de Azure Traffic Manager para conmutaciones de red eficientes.

## <a name="next-steps"></a>Pasos siguientes
* Más información en [¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery?](site-recovery-workload.md)
* Más información acerca de la arquitectura de Site Recovery en [¿Cómo funciona Azure Site Recovery?](site-recovery-components.md)




<!--HONumber=Nov16_HO2-->


