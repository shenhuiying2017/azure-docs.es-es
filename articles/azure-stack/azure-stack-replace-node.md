---
title: Reemplazar un nodo de la unidad de escalado en un sistema integrado de Azure Stack | Microsoft Docs
description: "Obtenga información acerca de cómo reemplazar un nodo físico de la unidad de escalado en un sistema integrado de Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: f3d6215b672be0d8de8ba0d94320b1a2413eadf1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Reemplazar un nodo de la unidad de escalado en un sistema integrado de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack*

En este artículo se describe el proceso general para reemplazar un equipo físico (también denominado un *nodo de la unidad de escalado*) en un sistema integrado de Azure Stack. Los pasos para reemplazar el nodo de la unidad de escalado actual varían en función del proveedor de hardware del fabricante de equipos originales (OEM). Consulte la documentación de Field Replaceable Unit (FRU) del proveedor para obtener pasos detallados específicos para su sistema.

En el siguiente diagrama de flujo se muestra el proceso general de FRU para reemplazar un nodo completo de la unidad de escalado.

![Diagrama de flujo del proceso de reemplazo del nodo](media/azure-stack-replace-node/ReplaceNodeFlow.PNG)

*Es posible que esta acción no sea necesaria en función de la condición física del hardware.

## <a name="review-alert-information"></a>Revisar información de las alertas

Si un nodo de la unidad de escalado no está funcionando, recibirá las alertas críticas siguientes:

- Node not connected to network controller (Nodo no conectado al controlador de red)
- Node inaccessible for virtual machine placement (Nodo inaccesible para la colocación de la máquina virtual)
- Scale unit node is offline (Nodo de la unidad de escalado desconectado)

![Lista de alertas para cuando la unidad de escalado no funciona](media/azure-stack-replace-node/NodeDownAlerts.PNG)

Si abre la alerta "Scale unit node is offline" (Nodo de la unidad de escalado desconectado), en la descripción de la misma podrá consultar el nodo de la unidad de escalado que no es accesible. También puede recibir alertas adicionales en la solución de supervisión específica del OEM que se ejecuta en el host de ciclo de vida del hardware.

![Detalles de la alerta de nodo sin conexión](media/azure-stack-replace-node/NodeOffline.PNG)

## <a name="scale-unit-node-replacement-process"></a>Proceso de reemplazo del nodo de la unidad de escalado

Los pasos siguientes se proporcionan como una descripción general del proceso de reemplazo del nodo de la unidad de escalado. Consulte la documentación de FRU del proveedor de hardware OEM para obtener pasos detallados y específicos para su sistema. No siga estos pasos sin consultar la documentación proporcionada por su OEM.

1. Use la acción [Purgar](azure-stack-node-actions.md#scale-unit-node-actions) para poner el nodo de la unidad de escalado en el modo de mantenimiento. Es posible que esta acción no sea necesaria en función de la condición física del hardware.
2. Si el nodo aún está encendido, use la acción [Apagar](azure-stack-node-actions.md#scale-unit-node-actions). Es posible que esta acción no sea necesaria en función de la condición física del hardware.
 
   > [!NOTE]
   > En el caso poco probable de que la acción de apagado no funcione, utilice en su lugar la interfaz web del controlador de administración de placa base (BMC).

1. Reemplace el equipo físico. Normalmente, esta tarea la realiza el proveedor de hardware de OEM.
2. Use la acción [Reparar](azure-stack-node-actions.md#scale-unit-node-actions) para agregar el nuevo equipo físico a la unidad de escalado.
3. Use el punto de conexión privilegiado para [comprobar el estado de la reparación del disco virtual](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Con las nuevas unidades de datos, un trabajo de reparación del almacenamiento completo puede tardar varias horas, según la carga del sistema y el espacio consumido.
4. Una vez finalizada la acción de reparación, confirme que todas las alertas activas se cerraron automáticamente.

## <a name="next-steps"></a>Pasos siguientes

- Para información sobre cómo reemplazar un disco físico intercambiable directamente, consulte [Reemplazar un disco físico en Azure Stack](azure-stack-replace-disk.md). 
- Para obtener información acerca de cómo reemplazar un componente de hardware no intercambiable en caliente, consulte [Replace a hardware component](azure-stack-replace-component.md) (Reemplazar un componente de hardware). 