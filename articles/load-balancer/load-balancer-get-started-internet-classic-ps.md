---
title: "Creación de un equilibrador de carga con conexión a Internet: Azure PowerShell clásico | Microsoft Docs"
description: "Obtenga información sobre cómo crear un equilibrador de carga orientado a Internet en el modo clásico con PowerShell."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-service-management
ms.assetid: 73e8bfa4-8086-4ef0-9e35-9e00b24be319
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.openlocfilehash: 56e29dc792ef832a7693b9c8769e9b4269766955
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Introducción a la creación de un equilibrador de carga orientado a Internet (clásico) en PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [CLI de Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Antes de trabajar con recursos de Azure, es importante comprender que Azure tiene actualmente dos modelos de implementación: Azure Resource Manager y el clásico. Asegúrese de que comprende los [modelos de implementación y las herramientas](../azure-classic-rm.md) antes de trabajar con recursos de Azure. Puede ver la documentación de las distintas herramientas haciendo clic en las fichas en la parte superior de este artículo. Este artículo trata sobre el modelo de implementación clásico. También puede [obtener información sobre cómo crear un equilibrador de carga orientado a Internet con el Administrador de recursos de Azure](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>Configurar el equilibrador de carga con PowerShell

Para configurar un equilibrador de carga con PowerShell, complete estos pasos:

1. Si es la primera vez que usa Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.
2. Después de crear una máquina virtual, puede usar cmdlets de PowerShell para agregar un equilibrador de carga a una máquina virtual dentro del mismo servicio en la nube.

En el ejemplo siguiente se agrega un conjunto de equilibrador de carga denominado "webfarm" al servicio en la nube "mytestcloud" (o myctestcloud.cloudapp.net), agregando los puntos de conexión para el equilibrador de carga a las máquinas virtuales denominadas "web1" y "web2". El equilibrador de carga recibe tráfico de red en el puerto 80 y equilibra la carga entre las máquinas virtuales definidas por el punto de conexión local (en este caso el puerto 80) mediante TCP.

### <a name="step-1"></a>Paso 1

Crear un punto de conexión con equilibrio de carga para la primera máquina virtual "web1"

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

### <a name="step-2"></a>Paso 2

Crear otro punto de conexión para la segunda máquina virtual "web2" con el mismo nombre del conjunto de equilibrador de carga

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Quitar una máquina virtual de un equilibrador de carga

Puede usar Remove-AzureEndpoint para quitar un punto de conexión de la máquina virtual del equilibrador de carga.

```powershell
Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin | Update-AzureVM
```

## <a name="next-steps"></a>Pasos siguientes

También puede [empezar a crear un equilibrador de carga interno](load-balancer-get-started-ilb-classic-ps.md) y configurar el tipo de [modo de distribución](load-balancer-distribution-mode.md) para un comportamiento del tráfico de red de un equilibrador de carga específico.

Si la aplicación necesita mantener conexiones activas para servidores detrás de un equilibrador de carga, puede obtener más información acerca de la [configuración de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md). Le ayuda a conocer el comportamiento de conexión del tiempo de inactividad cuando se usa Azure Load Balancer.
