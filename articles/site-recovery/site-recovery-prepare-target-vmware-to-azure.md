---
title: "Preparación del destino (VMware a Azure) | Microsoft Docs"
description: "En este artículo se describe cómo configurar su entorno de Azure para comenzar a replicar máquinas virtuales de VMware en Azure."
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 11/23/2017
ms.author: bsiva
ms.openlocfilehash: 98e0a7cd77e8e6e9ce124845aad49bd03a2bf1d8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="prepare-target-vmware-to-azure"></a>Preparación del destino (VMware a Azure)
> [!div class="op_single_selector"]
> * [VMware a Azure](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Físico a Azure](./site-recovery-prepare-target-physical-to-azure.md)

En este artículo se describe cómo configurar su entorno de Azure para comenzar a replicar máquinas virtuales de VMware en Azure.

## <a name="prerequisites"></a>Requisitos previos

En este artículo se da por supuesto lo siguiente:
- Ha creado un almacén de Recovery Services para proteger sus máquinas virtuales de VMware. Puede crear un almacén de Recovery Services desde [Azure Portal](http://portal.azure.com "Azure Portal").
- Ha [configurado el entorno local](./site-recovery-set-up-vmware-to-azure.md) para replicar máquinas virtuales de VMware en Azure.

## <a name="prepare-target"></a>Preparación del destino

Después de completar el **Paso 1: Selección del objetivo de protección** y el **Paso 2: Preparación del origen**, irá al **Paso 3: Destino**.

![Preparación del destino](./media/site-recovery-prepare-target-vmware-to-azure/prepare-target-vmware-to-azure.png)

1. **Suscripción:** En el menú desplegable, seleccione la suscripción en la que desea replicar las máquinas virtuales.
2. **Modelo de implementación:** Seleccione el modelo de implementación (clásica o Resource Manager).

Según el modelo de implementación elegido, se ejecuta una validación para asegurarse de que tiene al menos una cuenta de almacenamiento compatible y una red virtual en la conmutación de destino en la que replicar y conmutar por error las máquinas virtuales.

Una vez completadas las validaciones correctamente, haga clic en Aceptar para ir al paso siguiente.

Si no tiene una cuenta de almacenamiento de Resource Manager compatible o una red virtual, puede crearla haciendo clic en los botones **+ Cuenta de almacenamiento** o **+ Red** en la parte superior de la página.

## <a name="next-steps"></a>Pasos siguientes
[Configuración de las opciones de replicación](./site-recovery-setup-replication-settings-vmware.md).
