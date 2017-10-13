---
title: "Creación de un estándar de equilibrador de carga público con redundancia de zona en el front-end para la dirección IP pública mediante Azure Portal | Microsoft Docs"
description: "Aprenda a crear un estándar de equilibrador de carga público con redundancia de zona en el front-end para la dirección IP pública mediante Azure Portal"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: e28983f6015f8dcaa546c20fd9bc8d986a15ab5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Creación de un estándar de equilibrador de carga público con redundancia de zona en el front-end para la dirección IP pública mediante Azure Portal

Este artículo le ayudará a crear un servicio [Load Balancer Estándar](https://aka.ms/azureloadbalancerstandard) público con front-end con redundancia de zona mediante una dirección IP pública estándar.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Registro para la versión preliminar de las zonas de disponibilidad, Load Balancer Estándar y dirección IP pública Estándar
 
>[!NOTE]
La [SKU estándar de Load Balancer](https://aka.ms/azureloadbalancerstandard) se encuentra actualmente en versión preliminar. Durante la versión preliminar, la característica podría no tener el mismo nivel de disponibilidad y confiabilidad que las características que se encuentran en las versiones de disponibilidad general. Para obtener más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Use la [SKU básica de Load Balancer](load-balancer-overview.md) disponible con carácter general con sus servicios de producción. 

> [!NOTE]
> Las zonas de disponibilidad están en versión preliminar y están listas para escenarios de desarrollo y pruebas. El soporte técnico está disponible para recursos y regiones de Azure, y familias de tamaños de máquina virtual seleccionados. Para más información sobre cómo empezar a trabajar y qué recursos, regiones y familias de tamaños de máquina virtual de Azure puede probar con las zonas de disponibilidad, consulte la [introducción a las zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obtener soporte técnico, puede consultar [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) o [abrir un vale de soporte de Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Antes de seleccionar una zona o una opción con redundancia de zona para la dirección IP pública de front-end de Load Balancer, primero debe completar los pasos descritos en el [registro para la versión preliminar de zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure. 

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Creación de un equilibrador de carga con redundancia de zona

1. Desde un explorador, vaya a Azure Portal: [http://portal.azure.com](http://portal.azure.com) e inicie sesión con su cuenta de Azure.
2. En la parte superior izquierda de la pantalla, seleccione **Nuevo** > **Redes** > **Equilibrador de carga.**
3. En **Crear equilibrador de carga, en **Nombre**, escriba **myPublicLB**.
4. En **Tipo**, seleccione **Público**.
5. En la SKU, seleccione **Estándar (versión preliminar)**.
6. Haga clic en **Dirección IP pública**, **Crear nuevo**; en la página de **creación de una dirección IP pública**, en el nombre, escriba **myPublicIPStandard** y en **Zona de disponibilidad (versión preliminar)**, seleccione **Redundancia de zona**.
7. En **Ubicación**, seleccione **Este de EE. UU. 2** y haga clic en **Aceptar**. El equilibrador de carga empieza entonces a implementarse y este proceso tarda unos minutos en completarse correctamente.

    ![creación de un estándar de equilibrador de carga con redundancia de zona con Azure Portal](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>Pasos siguientes
- Aprenda a [crear una dirección IP pública en una zona de disponibilidad](../virtual-network/create-public-ip-availability-zone-portal.md).



