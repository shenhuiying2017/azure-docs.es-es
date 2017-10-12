---
title: "Preparar los recursos de Azure para replicar máquinas virtuales de Hyper-V (con System Center VMM) en Azure con Azure Site Recovery | Microsoft Docs"
description: "Describe lo que necesita tener listo en Azure antes de iniciar la replicación de máquinas virtuales de Hyper-V (con VMM) en Azure con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1568bdc3-e767-477b-b040-f13699ab5644
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: 63b005f37ab5e15e8a1b4645446d65f1529f1bbd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-5-prepare-azure-resources-for-hyper-v-replication-with-vmm-to-azure"></a>Paso 5: Preparación de los recursos de Azure para la replicación de Hyper-V (con VMM) en Azure

Tras comprobar los [requisitos de red](vmm-to-azure-walkthrough-network.md), siga las instrucciones de este artículo para preparar los recursos de Azure para replicar máquinas virtuales de Hyper-V local en nubes de System Center Virtual Machine Manager (VMM) en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md).

Cuando haya terminado de leer este artículo, publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-an-azure-account"></a>Configuración de una cuenta de Azure

- Obtenga una [cuenta de Microsoft Azure](http://azure.microsoft.com/).
- Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Para comprobar las regiones admitidas para Site Recovery, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Obtenga información de los [precios de Site Recovery](site-recovery-faq.md#pricing) y los [detalles de precios](https://azure.microsoft.com/pricing/details/site-recovery/).
- Asegúrese de que su cuenta de Azure tiene los [permisos](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) correctos para crear máquinas virtuales de Azure. [Obtenga más información](../active-directory/role-based-access-built-in-roles.md) sobre control de acceso basado en rol de Azure.


## <a name="set-up-an-azure-network"></a>Configurar una red de Azure

- Configure una [red de Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md). Las VM de Azure se colocarán en esta red cuando se creen después de la conmutación por error.
- La red debe estar en la misma región que el almacén de Recovery Services.
- Site Recovery en Azure Portal puede usar redes configuradas en [Resource Manager](../resource-manager-deployment-model.md) o en modo clásico.
- Es recomendable configurar una red antes de empezar. Si no lo hace, deberá hacerlo durante la implementación de Site Recovery.
- Obtenga información de [precios de red virtual](https://azure.microsoft.com/pricing/details/virtual-network/).


## <a name="set-up-an-azure-storage-account"></a>Configurar una cuenta de almacenamiento de Azure

- Site Recovery replica máquinas virtuales locales en Azure Storage. Las máquinas virtuales de Azure se crean en el almacenamiento después de producirse la conmutación por error.
- Configurar una [cuenta de Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account) estándar o premium para almacenar los datos replicados en Azure.
- [Premium Storage](../storage/common/storage-premium-storage.md) se usa normalmente para las máquinas virtuales que necesitan un alto rendimiento constante de E/S y latencia baja para hospedar cargas de trabajo intensivas de E/S.
- Si desea utilizar una cuenta premium para almacenar los datos replicados, también necesita una cuenta de almacenamiento estándar para almacenar los registros de replicación que capturan los cambios continuos de los datos locales.
- Según el modelo de recursos que desee usar para las máquinas virtuales de Azure conmutadas por error, configure una cuenta en [modo Resource Manager](../storage/common/storage-create-storage-account.md) o en [modo clásico](../storage/common/storage-create-storage-account.md).
- Es recomendable configurar una cuenta de almacenamiento antes de empezar. Si no lo hace, deberá hacerlo durante la implementación de Site Recovery. Las cuentas deben estar en la misma región que el almacén de Recovery Services.
- No se pueden mover cuentas de almacenamiento que usa Site Recovery en grupos de recursos dentro de la misma suscripción, o bien en distintas suscripciones.


## <a name="next-steps"></a>Pasos siguientes

Ir al [Paso 6: Preparación de VMM](vmm-to-azure-walkthrough-vmm-hyper-v.md)
