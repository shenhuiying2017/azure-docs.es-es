---
title: "Configuración de puertos de alta disponibilidad para Azure Load Balancer | Microsoft Docs"
description: "Aprenda a usar puertos de alta disponibilidad para el tráfico interno en todos los puertos de equilibrio de carga"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: 7256548b988812c64ca9a9f8a84fec377646635d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-high-availability-ports-for-internal-load-balancer"></a>Configuración de puertos de alta disponibilidad para el equilibrador de carga interno

En este artículo se proporciona un ejemplo de implementación de puertos de alta disponibilidad en un equilibrador de carga interno. Para configuraciones específicas de dispositivos virtuales de red, consulte los sitios web del proveedor correspondiente.

>[!NOTE]
> La característica Puertos de alta disponibilidad actualmente está en versión preliminar. Durante la versión preliminar, la característica podría no tener el mismo nivel de disponibilidad y confiabilidad que las características que se encuentran en las versiones de disponibilidad general. Para obtener más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

En la figura 1 se muestra la configuración del ejemplo de implementación siguiente que se describe en este artículo:
- Los dispositivos virtuales de red se implementan en el grupo back-end de un equilibrador de carga interno detrás de la configuración de los puertos de alta disponibilidad. 
- El enrutamiento definido por el usuario aplicado en las rutas de la subred DMZ enruta todo el tráfico a <?> al realizar el salto siguiente como IP virtual del equilibrador de carga interno. 
- El equilibrador de carga interno distribuye el tráfico a uno de los dispositivos virtuales de red activos según el algoritmo de equilibrado de carga.
- El dispositivo virtual de red procesa el tráfico y lo reenvía al destino original en la subred de back-end.
- La ruta de devolución también puede ser la misma, si se configura el enrutamiento definido por el usuario correspondiente en la subred de back-end. 

![implementación de puertos de alta disponibilidad de ejemplo](./media/load-balancer-configure-ha-ports/haports.png)

Figura 1: Dispositivos virtuales de red implementados detrás de un equilibrador de carga interno con puertos de alta disponibilidad 

## <a name="preview-sign-up"></a>Registro en versión preliminar

Para participar en la versión preliminar de la característica de puertos de alta disponibilidad en la SKU estándar de Load Balancer, registre su suscripción para obtener acceso mediante PowerShell o la CLI de Azure 2.0.

- Registro con PowerShell

   ```powershell
   Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```

- Registro con la CLI de Azure 2.0

    ```cli
  az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network  
    ```

## <a name="configuring-ha-ports"></a>Configuración de puertos de alta disponibilidad

La configuración de los puertos de alta disponibilidad implica configurar un equilibrador de carga interno, con los dispositivos virtuales de red en el grupo de back-end, la configuración de sondeo correspondiente de mantenimiento del equilibrador de carga correspondiente para detectar el estado de los dispositivos virtuales de red y la regla del equilibrador de carga con los puertos de alta disponibilidad. La configuración general del equilibrador de carga se aborda en la [introducción](load-balancer-get-started-ilb-arm-portal.md). En este artículo se resalta la configuración de los puertos de alta disponibilidad.

Básicamente, la configuración implica establecer el valor del puerto de back-end y de front-end en **0**, y el valor del protocolo en **Todos**. En este artículo se describe cómo configurar los puertos de alta disponibilidad mediante Azure Portal, PowerShell y la CLI de Azure 2.0.

### <a name="configure-ha-ports-load-balancer-rule-with-the-azure-portal"></a>Configuración de la regla del equilibrador de carga para los puertos de alta disponibilidad mediante Azure Portal

En Azure Portal se incluye la opción **Puertos HA** con una casilla para esta configuración. Al seleccionarla, la configuración del protocolo y el puerto relacionados se rellena automáticamente. 

![configuración de los puertos de alta disponibilidad mediante Azure Portal](./media/load-balancer-configure-ha-ports/haports-portal.png)

Figura 2: Configuración de los puertos mediante el portal

### <a name="configure-ha-ports-load-balancer-rule-with-powershell"></a>Configuración de la regla del equilibrador de carga para los puertos de alta disponibilidad mediante PowerShell

Use el comando siguiente para crear la regla del equilibrador de carga para los puertos de alta disponibilidad al crear el equilibrador de carga interno con PowerShell:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-ha-ports-load-balancer-rule-with-azure-cli-20"></a>Configuración de la regla del equilibrador de carga para los puertos de alta disponibilidad mediante la CLI de Azure 2.0

En el paso 4 del artículo de [creación de un conjunto de equilibrador de carga interno](load-balancer-get-started-ilb-arm-cli.md), use el siguiente comando para crear regla del equilibrador de carga para los puertos de alta disponibilidad.

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre los [puertos de alta disponibilidad](load-balancer-ha-ports-overview.md)
