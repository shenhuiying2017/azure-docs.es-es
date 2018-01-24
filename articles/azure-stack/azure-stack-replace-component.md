---
title: "Sustitución de un componente de hardware en un nodo de unidad de escalado de Azure Stack | Microsoft Docs"
description: "Obtenga información sobre cómo reemplazar un componente de hardware en un sistema integrado de Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: c6e036bf-8c80-48b5-b2d2-aa7390c1b7c9
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: 4937b7725c8f39314ccc41584a8646b7197f6bdf
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Sustitución de un componente de hardware en un nodo de unidad de escalado de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack*

En este artículo se describe el proceso general para reemplazar los componentes de hardware que se pueden intercambiar directamente. Los verdaderos pasos para reemplazar varían en función del fabricante del hardware OEM. Consulte la documentación de la unidad reemplazable de campo (FRU) del proveedor para obtener los pasos detallados específicos para el sistema integrado de Azure Stack.

Los componentes que no se pueden intercambiar directamente incluyen:

- CPU*
- Memoria*
- Controlador de administración de placa base (BMC)/tarjeta de vídeo
- Controlador de disco/adaptador de bus host (HBA)/backplane
- Adaptador de red (NIC)
- Disco del sistema operativo*
- Unidades de datos (unidades que no admiten intercambio directo, por ejemplo, tarjeta complementaria PCI-e)*

*Es posible que estos componentes admitan el intercambio directo, pero esto puede variar en función de la implementación del proveedor. Consulte la documentación de FRU del distribuidor de OEM para ver los pasos detallados.

En el diagrama de flujo siguiente se muestra el proceso general de FRU para reemplazar un componente de hardware que no admite el intercambio directo.

![Diagrama de flujo que muestra el flujo de reemplazo de componente](media/azure-stack-replace-component/replacecomponentflow.PNG)

*Es posible que esta acción no sea necesaria en función de la condición física del hardware.

**Si el fabricante del hardware OEM realiza el reemplazo de componente y actualiza el firmware podría variar en función del contrato de soporte técnico.

## <a name="review-alert-information"></a>Revisión de la información de las alertas

El sistema de mantenimiento y supervisión de Azure Stack rastrea el estado de los adaptadores de red y las unidades de datos que Espacios de almacenamiento directo controla. No rastrea otros componentes de hardware. Para todos los demás componentes de hardware, se generan alertas en la solución de supervisión de hardware específica del proveedor que se ejecuta en el host de ciclo de vida de hardware.

## <a name="component-replacement-process"></a>Proceso de reemplazo de los componentes

Los pasos siguientes proporcionan una información general de alto nivel del proceso de reemplazo de componentes. No siga estos pasos sin consultar la documentación de FRU proporcionada por su OEM.

1. Use la acción [Purgar](azure-stack-node-actions.md#scale-unit-node-actions) para poner el nodo de la unidad de escalado en el modo de mantenimiento. Es posible que esta acción no sea necesaria en función de la condición física del hardware.

   > [!NOTE]
   > En cualquier caso, solo se puede purgar y apagar un nodo al mismo tiempo sin que se interrumpan los Espacios de almacenamiento directo (S2D).

2. Una vez que el nodo de la unidad de escalado está en el modo de mantenimiento, use la acción de [apagado](azure-stack-node-actions.md#scale-unit-node-actions). Es posible que esta acción no sea necesaria en función de la condición física del hardware.
 
   > [!NOTE]
   > En el caso improbable de que la acción de apagado no funcione, use en su lugar la interfaz web del controlador de administración de placa base (BMC).

3. Reemplace el componente de hardware dañado. Si el fabricante del hardware OEM realiza el reemplazo de componente podría variar en función del contrato de soporte técnico.  
4. Actualice el firmware. Siga el proceso de actualización del firmware específico del proveedor con el host de ciclo de vida de hardware para asegurarse de que el componente de hardware de reemplazo tiene aplicado el nivel de firmware aprobado. Si el fabricante del hardware OEM realiza este paso podría variar en función del contrato de soporte técnico.  
5. Use la acción de [reparación](azure-stack-node-actions.md#scale-unit-node-actions) para devolver el nodo de la unidad de escalado a la unidad de escalado.
6. Use el punto de conexión con privilegios para [comprobar el estado de la reparación del disco virtual](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Con las nuevas unidades de datos, un trabajo de reparación del almacenamiento completo puede tardar varias horas, según la carga del sistema y el espacio consumido.
7. Una vez finalizada la acción de reparación, confirme que todas las alertas activas se cerraron automáticamente.

## <a name="next-steps"></a>pasos siguientes

- Para información sobre cómo reemplazar un disco físico intercambiable directamente, consulte [Reemplazar un disco físico en Azure Stack](azure-stack-replace-disk.md).
- Para información sobre cómo reemplazar un nodo físico, consulte el artículo sobre cómo [reemplazar un nodo de unidad de escalado](azure-stack-replace-node.md).
- 
