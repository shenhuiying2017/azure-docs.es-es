---
title: Creación de un estándar de equilibrador de carga público con redundancia de zona en el front-end para la dirección IP pública mediante Azure Portal | Microsoft Docs
description: Aprenda a crear un estándar de equilibrador de carga público con redundancia de zona en el front-end para la dirección IP pública mediante Azure Portal
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 9a51638ea6d85178e6631ac278c116e4c7e05d61
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200265"
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Creación de un estándar de equilibrador de carga público con redundancia de zona en el front-end para la dirección IP pública mediante Azure Portal

Este artículo le ayudará a crear un servicio [Load Balancer Estándar](https://aka.ms/azureloadbalancerstandard) público con front-end con redundancia de zona mediante una dirección IP pública estándar. De manera predeterminada, una dirección IP de front-end única en un equilibrador de carga estándar tiene redundancia de zona.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

> [!NOTE]
 El soporte técnico para las zonas de disponibilidad está disponible para recursos y regiones de Azure, y familias de tamaños de máquina virtual seleccionados. Para más información sobre cómo empezar a trabajar y qué recursos, regiones y familias de tamaños de máquina virtual de Azure puede probar con las zonas de disponibilidad, consulte la [introducción a las zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview). Para soporte técnico, eche un vistazo a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) o [Creación de una solicitud de soporte técnico de Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure 

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Creación de un equilibrador de carga con redundancia de zona

1. Desde un explorador, navegue a Azure Portal: [http://portal.azure.com](http://portal.azure.com) e inicie sesión con su cuenta de Azure.
2. En la parte superior izquierda de la pantalla, seleccione **Crear un recurso** > **Redes** > **Equilibrador de carga**.
3. En la página **Crear equilibrador de carga**, en **Nombre**, escriba **myLoadBalancer**.
4. En **Tipo**, seleccione **Público**.
5. En la SKU, seleccione **Estándar**.
6. Haga clic en **Dirección IP pública**, haga clic en **Crear nuevo** y en la página **Crear dirección IP pública**, debajo del nombre, escriba **myPublicIPStandard**.
    >[!NOTE] 
    > La dirección IP pública creada en este paso es de SKU estándar y, de forma predeterminada, tiene redundancia de zona. 
8. En **Ubicación**, seleccione **Este de EE. UU. 2** y haga clic en **Aceptar**. El equilibrador de carga empieza entonces a implementarse y este proceso tarda unos minutos en completarse correctamente.

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre [Load Balancer Estándar y zonas de disponibilidad](load-balancer-standard-availability-zones.md).



