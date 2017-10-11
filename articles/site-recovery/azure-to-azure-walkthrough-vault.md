---
title: "Configuración de un almacén para la replicación de VM de Azure entre regiones con Azure Site Recovery | Microsoft Docs"
description: "Se resumen los pasos que hay que seguir para configurar un almacén para la replicación de Azure entre regiones de Azure con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 40472189-3d80-4963-b175-8bddcbc2f61f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: e03d17992ee0b12049636e40188950bcc4a6f31e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="step-4-set-up-a-vault-for-azure-to-azure-replication"></a>Paso 4: Configuración de un almacén de Azure para realizar la replicación de Azure

Después de [planear redes](azure-to-azure-walkthrough-network.md), use este artículo para configurar un almacén para máquinas virtuales (VM) de Azure que realizan la replicación en otra región de Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

- Cuando termine el artículo, debe tener un almacén de Recovery Services configurado.
- Publique cualquier comentario en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



>[!NOTE]
>
> La replicación de VM de Azure se encuentra en una versión preliminar en este momento.




## <a name="create-a-vault"></a>Creación de un almacén

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> Le recomendamos que cree el almacén de Recovery Services en la ubicación donde quiere que se repliquen las máquinas virtuales. Por ejemplo, si la ubicación de destino está en el centro de EE. UU., crear el almacén en **Centro de EE. UU**.


## <a name="next-steps"></a>Pasos siguientes

Vaya a [Paso 5: Habilitación de la replicación](azure-to-azure-walkthrough-enable-replication.md)
