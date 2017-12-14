---
title: "Agrupación de máquinas virtuales con el etiquetado de VMware vCenter | Microsoft Docs"
description: "En este artículo se describe cómo crear un grupo antes de ejecutar una evaluación con el servicio Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 967c2a1fc0e5144c6c7d5c8c4a81cec3d77ffdb5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="group-vms-with-vcenter-tagging"></a>Agrupación de máquinas virtuales con el etiquetado de vCenter

En este artículo se describe cómo crear un grupo de máquinas para la evaluación de [Azure Migrate ](migrate-overview.md) usando el etiquetado de VMware vCenter. Especifique la categoría de etiqueta que desea utilizar al crear un grupo. 

## <a name="set-up-tagging"></a>Configuración del etiquetado

Durante la implementación de Azure Migrate, una máquina virtual de Azure Migrate local detecta máquinas que se ejecutan en hosts de ESXi administrados por un servidor vCenter. Debe configurar el etiquetado de vCenter antes del proceso de detección.

1. En el cliente web de VMware vSphere, desplácese hasta el servidor vCenter.
2. Haga clic en **Etiquetas** para revisar las etiquetas actuales.
3. Para etiquetar una máquina virtual, seleccione **Objetos relacionados** > **Máquinas virtuales** y, luego, seleccione la máquina virtual que desea etiquetar.
4. En **Resumen** > **Etiquetas**, haga clic en **Asignar**. 
5. Haga clic en **Nueva etiqueta** y especifique un nombre y una descripción para la etiqueta.
6. Con el fin de crear una categoría para la etiqueta, seleccione **Nueva categoría** en la lista desplegable.
7. Especifique un nombre y una descripción para la categoría, y la cardinalidad. y, a continuación, haga clic en **Aceptar**.

    ![Etiquetas de máquina virtual](./media/how-to-tag-v-center/vm-tag.png)

## <a name="use-tagging-to-create-groups"></a>Uso de etiquetas para crear grupos

1. Configure la detección de máquinas locales, como se describe en el [tutorial de evaluación de VMware](tutorial-assessment-vmware.md#run-the-collector-to-discover-vms).
2. En **Tag category for grouping** (Categoría de etiqueta para la agrupación), seleccione la categoría de etiqueta de vCenter en la que debe basarse el grupo de evaluación. Azure Migrate crea automáticamente un grupo para la categoría seleccionada.

    

## <a name="next-steps"></a>Pasos siguientes

[Configurar la evaluación de máquinas virtuales VMware](tutorial-assessment-vmware.md).
