---
title: "Migración de una red virtual de Azure (clásica) de un grupo de afinidad a una región | Microsoft Docs"
description: "Obtenga información sobre cómo migrar una red virtual (clásica) de un grupo de afinidad a una región."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: b9b3bd0f2184ac85261166d5fe2ab67e1bf319d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migración de una red virtual (clásica) de un grupo de afinidad a una región

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [el Administrador de recursos y el clásico](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artículo trata del modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de implementación de Resource Manager.

Los grupos de afinidad garantizan que los recursos creados en el mismo grupo de afinidad se hospedan físicamente en servidores que están cerca, lo que permite que estos recursos se comuniquen con mayor rapidez. En el pasado, los grupos de afinidad eran un requisito para crear redes virtuales (clásicas). En ese momento, el servicio de administrador de red que administraba las redes virtuales (clásicas) solo podía trabajar en un conjunto de servidores físicos o una unidad de escalado. Mejoras arquitectónicas han aumentado el ámbito de administración de red a una región.

Como resultado de estas mejoras de arquitectura, los grupos de afinidad ya no se recomiendan ni son necesarios para las redes virtuales (clásicas). El uso de grupos de afinidad para redes virtuales (clásicas) se reemplaza por regiones. Las redes virtuales (clásicas) que están asociadas a regiones se denominan redes virtuales regionales.

Por lo general, se recomienda no usar grupos de afinidad. Además del requisito de redes virtuales, también era importante usar los grupos de afinidad para asegurarse de que los recursos, por ejemplo, de proceso (clásico) y almacenamiento (clásico), se colocaran próximos entre sí. Sin embargo, con la arquitectura de red de Azure actual, estos requisitos de colocación ya no son necesarios.

> [!IMPORTANT]
> Aunque es técnicamente posible crear una red virtual que está asociada a un grupo de afinidad, no hay ninguna razón para hacerlo. Muchas características de red virtual, como los grupos de seguridad de red, solo están disponibles cuando se usa una red virtual regional y no están disponibles para las redes virtuales que están asociadas a grupos de afinidad.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Edición del archivo de configuración de red

1. Exporte un archivo de configuración de red. Para más información sobre cómo exportar un archivo de configuración de red con PowerShell o la interfaz de línea de comandos (CLI) 1.0 de Azure, consulte [Configuración de una red virtual mediante un archivo de configuración de red](virtual-networks-using-network-configuration-file.md#export).
2. Edite el archivo de configuración de red, y reemplace **AffinityGroup** por **Location**. Especifique una [región](https://azure.microsoft.com/regions) de Azure para **Location**.
   
   > [!NOTE]
   > **Location** es la región que especificó para el grupo de afinidad que está asociado a la red virtual (clásica). Por ejemplo, si la red virtual (clásica) está asociada a un grupo de afinidad que se encuentra en Oeste de EE. UU., al migrar, el valor de **Location** debe apuntar a Oeste de EE. UU. 
   > 
   > 
   
    Edite las líneas siguientes del archivo de configuración de red y reemplace los valores por los suyos propios: 
   
    **Valor antiguo:**\<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **Valor nuevo:**\<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. Guarde los cambios e [importe](virtual-networks-using-network-configuration-file.md#import) la configuración de red en Azure.

> [!NOTE]
> Esta migración NO causa ningún tiempo de inactividad en sus servicios.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Qué hacer si tiene una máquina virtual (clásica= en un grupo de afinidad
No es necesario quitar las máquinas virtuales (clásicas) que están en un grupo de afinidad de dicho grupo. Una vez implementada una máquina virtual, se implementa en una sola unidad de escalado. Los grupos de afinidad pueden restringir el conjunto de tamaños de máquina virtual disponibles para una nueva implementación de máquina virtual, pero cualquier máquina virtual existente que esté implementa ya está restringida al conjunto de tamaños de máquina virtual disponible en la unidad de escalado en el que se implementa la máquina virtual. Dado que la máquina virtual se ha implementado en una unidad de escalado, quitar una máquina virtual de un grupo de afinidad no tiene ningún efecto en la máquina virtual.
