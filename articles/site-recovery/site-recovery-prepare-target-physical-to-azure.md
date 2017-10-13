---
title: "Preparación del destino (físico a Azure) | Microsoft Docs"
description: "En este artículo se describe cómo preparar el entorno de Azure para comenzar a replicar servidores físicos que ejecutan Windows o Linux en Azure."
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
ms.date: 5/31/2017
ms.author: bsiva
ms.openlocfilehash: aa7a32ace8354f615a8b8cc137f6bdf48fbadf48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-target-vmware-to-azure"></a>Preparación del destino (VMware a Azure)
> [!div class="op_single_selector"]
> * [VMware a Azure](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Físico a Azure](./site-recovery-prepare-target-physical-to-azure.md)

En este artículo se describe cómo preparar el entorno de Azure para comenzar a replicar servidores físicos (x64) que ejecutan Windows o Linux en Azure.

## <a name="prerequisites"></a>Requisitos previos

En el artículo se da por hecho lo siguiente:
- Ha creado un almacén de Recovery Services para proteger sus servidores físicos. Puede crear un almacén de Recovery Services desde [Azure Portal](http://portal.azure.com "Azure Portal").
- Ha [configurado el entorno local](./site-recovery-set-up-physical-to-azure.md) para replicar servidores físicos en Azure.

## <a name="prepare-target"></a>Preparación del destino

Después de completar el **Paso 1: Selección del objetivo de protección** y el **Paso 2: Preparación del origen**, irá al **Paso 3: Destino**.

![Preparación del destino](./media/site-recovery-prepare-target-physical-to-azure/prepare-target-physical-to-azure.png)

1. **Suscripción:** En el menú desplegable, seleccione la suscripción en la que desea replicar los servidores físicos.
2. **Modelo de implementación:** Seleccione el modelo de implementación (clásica o Resource Manager).

Según el modelo de implementación elegido, se ejecuta una validación para asegurarse de que tiene al menos una cuenta de almacenamiento compatible y una red virtual en la suscripción de destino en la que replicar y conmutar por error los servidores físicos.

Una vez completadas las validaciones correctamente, haga clic en Aceptar para ir al paso siguiente.

Si no tiene una cuenta de almacenamiento de Resource Manager compatible o una red virtual, o le gustaría agregar más, puede hacerlo haciendo clic en los botones **+ Cuenta de almacenamiento** o **+ Red** en la parte superior de la hoja.

## <a name="next-steps"></a>Pasos siguientes
[Configuración de las opciones de replicación](./site-recovery-setup-replication-settings-vmware.md).
