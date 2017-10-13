---
title: "Creación de una dirección IP pública con zonas con Azure Portal | Microsoft Docs"
description: "Cree una dirección IP pública en una zona de disponibilidad con Azure Portal."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 2fcbed2f83d66a0b4336cd1c464bb02eff3ef229
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-portal"></a>Creación de una dirección IP pública en una zona de disponibilidad con Azure Portal

Puede implementar una dirección IP pública en una zona de disponibilidad de Azure (versión preliminar). Una zona de disponibilidad es una zona separada físicamente en una región de Azure. Obtenga información sobre cómo:

> * Creación de una dirección IP pública en una zona de disponibilidad
> * Identificación de recursos relacionados creados en la zona de disponibilidad

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

> [!NOTE]
> Las zonas de disponibilidad están en versión preliminar y están listas para escenarios de desarrollo y pruebas. El soporte técnico está disponible para recursos y regiones de Azure, y familias de tamaños de máquina virtual seleccionados. Para más información sobre cómo empezar a trabajar y qué recursos, regiones y familias de tamaños de máquina virtual de Azure puede probar con las zonas de disponibilidad, consulte la [introducción a las zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obtener soporte técnico, puede consultar [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) o [abrir un vale de soporte de Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure Portal en https://portal.azure.com. 

## <a name="create-a-zonal-public-ip-address"></a>Crear una dirección IP pública zonal

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y luego **Dirección IP pública**.
3. Escriba o seleccione valores para las siguientes configuraciones, seleccione su suscripción, acepte los valores predeterminados para el resto de configuraciones y haga clic **Crear**:

    |Configuración|Valor|
    |---|---|
    |SKU| **Básica**: Se asigna con el método de asignación estática o el de asignación dinámica. Se puede asignar a cualquier recurso de Azure al que se pueda asignar a una dirección IP pública, como interfaces de red, VPN Gateway, Application Gateway y equilibradores de carga accesibles desde Internet. Puede asignar la dirección IP pública a una zona específica en la opción **Zona disponibilidad**. No tienen redundancia de zona. Para más información sobre las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview). **Estándar**: Solo se asigna con el método de asignación estática. Se puede asignar a interfaces de red o equilibradores de carga accesibles desde Internet estándar. Si asigna la dirección IP pública a un equilibrador de carga accesible desde Internet estándar, debe seleccionar Estándar. Para más información sobre las SKU de equilibrador de carga de Azure, consulte [Azure load balancer standard SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) (SKU estándar de equilibrador de carga de Azure). De forma predeterminada, tienen redundancia de zona. Puede crearse de forma zonal y garantizada en una zona de disponibilidad específica. La SKU estándar se encuentra en versión preliminar. Antes de crear una dirección IP pública de SKU estándar, primero debe registrarse para la versión preliminar. Para registrarse para la versión preliminar, consulte [Register for the standard SKU preview](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#preview-sign-up) (Registrarse para la versión preliminar de la SKU estándar). La SKU estándar solo se puede crear en una ubicación admitida.  Para una lista de ubicaciones (regiones) admitidas, consulte [Disponibilidad de regiones](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#region-availability) y supervise la página [Actualizaciones de Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network) para conocer la compatibilidad con regiones adicionales.|   
    |Nombre|El nombre debe ser único dentro del grupo de recursos que seleccione.|
    |Grupos de recursos|Haga clic en Crear nuevo y escriba myResourceGroup.|
    |Ubicación|Europa occidental|
    |Zona de disponibilidad|Si seleccionó la SKU **Estándar**, puede seleccionar *Redundancia de zona* si desea que la dirección IP sea resistente a las zonas. Si selecciona la SKU **Básica**, la dirección IP no es resistente a las zonas. Independientemente de la SKU que elija, puede asignar la dirección a una zona específica, si así lo desea. |

    La configuración aparece en el portal, tal y como se muestra en la siguiente imagen:

    ![Creación de una dirección IP pública zonal](./media/create-public-ip-availability-zone-portal/public-ip-address.png) 

> [!NOTE]
> Cuando asigne una dirección IP pública de SKU estándar a la interfaz de red de una máquina virtual, deberá permitir explícitamente el tráfico previsto con un [grupo de seguridad de red](security-overview.md#network-security-groups). Para evitar que se produzca un error en la comunicación con el recurso, debe crear un grupo de seguridad de red, asociarlo y permitir explícitamente el tráfico deseado.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview)
- Más información sobre [direcciones IP públicas](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)