---
title: "Configurar un almacén para la replicación de Hyper-V (con System Center VMM) en Azure con Azure Site Recovery | Microsoft Docs"
description: "Resume los pasos necesarios para configurar un almacén para la replicación de Hyper-V (con VMM) en Azure con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: b3cd6f03-c33c-406d-91d4-5cba69f79abd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: af453ec27ba15ad8c59cf9f544584ad18dc0f74a
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2017
---
# <a name="step-7-set-up-a-vault-for-hyper-v-replication"></a>Paso 7: configuración de un almacén para la replicación de Hyper-V

Este artículo describe cómo configurar un almacén y especificar qué se desea replicar desde la ubicación local en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.


Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Servicios de recuperación

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]



## <a name="select-a-protection-goal"></a>Selección de un objetivo de protección

Seleccione aquello que desea replicar y la ubicación donde se va a realizar la replicación.

1. Haga clic en **Almacenes de Recovery Services** > almacén.
2. En el menú de recursos, haga clic en **Site Recovery** > **Preparar la infraestructura** > **Objetivo de protección**.
3. En **Objetivo de protección**, seleccione **En Azure** > **Sí, con Hyper-V**. Seleccione **Sí** para confirmar que usa VMM. 

     ![Elegir objetivos](./media/vmm-to-azure-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>Pasos siguientes

Vaya al [paso 8: configuración de los valores de origen y destino](vmm-to-azure-walkthrough-source-target.md)
