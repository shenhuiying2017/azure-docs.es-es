---
title: "Antes de iniciar la replicación de máquinas virtuales de Azure a otra región | Microsoft Docs"
description: "Se resumen los pasos que hay que seguir antes de replicar máquinas virtuales de Azure entre regiones de Azure mediante el servicio Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: d38fc766d5226be7161433555da9622e006c80e9
ms.contentlocale: es-es
ms.lasthandoff: 08/31/2017

---

# <a name="step-2-before-you-start"></a>Paso 2: antes de comenzar

Una vez que ha revisada la [arquitectura](azure-to-azure-walkthrough-architecture.md) para la replicación de máquinas virtuales (VM) de Azure entre regiones de Azure con [Azure Site Recovery](site-recovery-overview.md), use este artículo para comprobar los requisitos previos.

- Cuando termine el artículo, debe tener una idea clara de lo que se necesita para crear el trabajo de implementación, y haber completado los pasos de los requisitos previos.
- Publique cualquier comentario en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
>
> La replicación de máquinas virtuales de Azure se encuentra en una versión preliminar en este momento.



## <a name="support-recommendations"></a>Recomendaciones de compatibilidad

Consulte la siguiente tabla. Obtenga una lista completa de los requisitos de compatibilidad en la [matriz de compatibilidad](site-recovery-support-matrix-azure-to-azure.md).

**Componente** | **Requisito**
--- | ---
**Almacén de Recovery Services** | Se recomienda crear un almacén de Recovery Services en la región de Azure de destino que quiera usar para la recuperación ante desastres. Por ejemplo, si desea replicar máquinas virtuales de origen del este de EE. UU. al centro de EE. UU., cree el almacén en el centro de EE. UU.
**Suscripción de Azure** | Su suscripción de Azure debe estar habilitada para crear máquinas virtuales en la ubicación de destino que quiera usar como región de recuperación ante desastres. Para habilitar la cuota necesaria, póngase en contacto con el soporte técnico.
**Capacidad de la región de destino** | En la región de Azure de destino, la suscripción debe tener suficiente capacidad para las máquinas virtuales, las cuentas de almacenamiento y los componentes de red.
**Storage** | A fin de evitar problemas de rendimiento, use las [instrucciones de almacenamiento](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) para las máquinas virtuales de Azure de origen.<br/><br/> Las cuentas de almacenamiento deben estar en la misma región que el almacén.<br/><br/> No se puede replicar en cuentas premium en el centro y sur de la India.<br/><br/> Si implementa la replicación con la configuración predeterminada, Site Recovery crea las cuentas de almacenamiento necesarias en función de la configuración de origen. Si personaliza la configuración, siga los [objetivos de escalabilidad para discos de máquina virtual](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
**Redes** | Debe permitir la conectividad saliente desde máquinas virtuales de Azure, en determinados intervalos de direcciones URL e IP.<br/><br/> Las cuentas de red deben estar en la misma región que el almacén.
**MV de Azure** | Asegúrese de que todos los certificados raíz más recientes se encuentran en la máquina virtual de Azure de Windows o Linux. Si no es así, no podrá registrar la máquina virtual en Site Recovery, debido a restricciones de seguridad.
**Cuenta de usuario de Azure** | Su cuenta de usuario de Azure debe tener ciertos [permisos](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar la replicación de una máquina virtual en Azure.


## <a name="set-permissions-on-the-account"></a>Obtención de permisos en la cuenta

1. Obtenga información sobre los [permisos](site-recovery-role-based-linked-access-control.md) necesarios para la replicación.
2. Siga estas [instrucciones](../active-directory/role-based-access-control-configure.md#add-access) para agregar los permisos.


## <a name="verify-target-resources"></a>Comprobación de los recursos de destino

1. Habilite la suscripción de Azure para que se puedan crear máquinas virtuales en la región de destino que desee usar para la recuperación ante desastres. Para habilitar la cuota necesaria, póngase en contacto con el soporte técnico.
2. Asegúrese de que su suscripción tiene suficientes recursos para habilitar las máquinas virtuales con tamaños que se correspondan con las máquinas virtuales de origen. De forma predeterminada, al configurar la replicación, Site Recovery toma el mismo tamaño para la máquina virtual de destino o el más cercano posible. Obtenga información sobre [cómo solucionar problemas](site-recovery-azure-to-azure-troubleshoot-errors.md#azure-resource-quota-issues-error-code-150097) en los recursos de destino.

## <a name="verify-azure-vm-certificates"></a>Comprobación de los certificados de la máquina virtual de Azure

1. Asegúrese de que todos los certificados raíz más recientes estén presentes en la máquina virtual de Windows o Linux que desee replicar. Si los certificados raíz más recientes no están presentes, la máquina virtual no se puede registrar en Site Recovery debido a restricciones de seguridad.
2. Para máquinas virtuales de Windows, haga lo siguiente:

    - Instale las actualizaciones de Windows más recientes en la máquina virtual para que todos los certificados raíz de confianza están presentes en ella.
    - En un entorno desconectado, siga el proceso de actualización de certificados o el proceso de Windows Update estándar en su organización para obtener los certificados raíz más recientes y las CRL actualizadas en las máquinas virtuales.
3. En las máquinas virtuales de Linux, para obtener los certificados raíz de confianza y la lista de revocación de certificados más recientes en la máquina virtual, siga las instrucciones proporcionadas por su distribuidor de Linux. Obtenga información sobre [cómo solucionar problemas](site-recovery-azure-to-azure-troubleshoot-errors.md#trusted-root-certificates-error-code-151066) de los certificados raíz de confianza.


## <a name="next-steps"></a>Pasos siguientes

Vaya a [Paso 3: planeamiento de las redes](azure-to-azure-walkthrough-network.md) para configurar la conectividad saliente.

