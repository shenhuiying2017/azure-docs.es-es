---
title: "Preparar los recursos de Azure para replicar servidores físicos locales en Azure con Azure Site Recovery | Microsoft Docs"
description: "Describe lo que necesita tener listo en Azure antes de iniciar la replicación de servidores locales en Azure con el servicio Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 4e320d9b-8bb8-46bb-ba21-77c5d16748ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: raynew
ms.openlocfilehash: b7411fa6aba04ffd34f3f4bd03e706ca75afc9c8
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="step-5-prepare-azure-resources-for-physical-server-replication-to-azure"></a>Paso 5: Preparación de los recursos de Azure para la replicación de servidor físico en Azure


Siga las instrucciones de este artículo para preparar los recursos de Azure para replicar servidores locales en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md).

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Antes de comenzar

Asegúrese de que ha leído los [requisitos previos](physical-walkthrough-prerequisites.md).

## <a name="set-up-an-azure-account"></a>Configuración de una cuenta de Azure

- Obtenga una [cuenta de Microsoft Azure](http://azure.microsoft.com/).
- Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Compruebe las regiones admitidas para Site Recovery, consultando **Disponibilidad geográfica** en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Obtenga información de los [precios de Site Recovery](site-recovery-faq.md#pricing) y los [detalles de precios](https://azure.microsoft.com/pricing/details/site-recovery/).



## <a name="set-up-an-azure-network"></a>Configurar una red de Azure

- Configure una red de Azure. Las VM de Azure se colocarán en esta red cuando se creen después de la conmutación por error.
- Site Recovery en Azure Portal puede usar redes configuradas en [Resource Manager](../resource-manager-deployment-model.md) o en modo clásico.
- La red debe estar en la misma región que el almacén de Recovery Services.
- Obtenga información de [precios de red virtual](https://azure.microsoft.com/pricing/details/virtual-network/).
- Obtenga más información sobre la [conectividad de la máquina virtual de Azure](physical-walkthrough-network.md) después de la conmutación por error.


## <a name="set-up-an-azure-storage-account"></a>Configurar una cuenta de almacenamiento de Azure

- Site Recovery replica servidores locales en Azure Storage. Las máquinas virtuales de Azure se crean en el almacenamiento después de producirse la conmutación por error.
- Configure una [cuenta de Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account) para los datos replicados.
- Site Recovery en Azure Portal puede usar cuentas de almacenamiento configuradas en Resource Manager o en modo clásico.
- La cuenta de almacenamiento puede ser estándar o [premium](../storage/common/storage-premium-storage.md).
- Si configura una cuenta de premium, también necesitará una cuenta estándar adicional para los datos de registro.


## <a name="next-steps"></a>Pasos siguientes

Vaya a [Paso 6: Configuración de un almacén](physical-walkthrough-create-vault.md)
