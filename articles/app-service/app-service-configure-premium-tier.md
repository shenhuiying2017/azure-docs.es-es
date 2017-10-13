---
title: "Configuración del nivel PremiumV2 para Azure App Service | Microsoft Docs"
description: "Obtenga información sobre cómo mejorar el rendimiento para su aplicación web, móvil y de API en Azure App Service mediante el escalado al nuevo plan de tarifa de PremiumV2."
keywords: servicio de aplicaciones, servicio de aplicaciones de azure, escala, escalable, plan del servicio de aplicaciones, costo del servicio de aplicaciones
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: cephalin
ms.openlocfilehash: 92cc8d8b0f67dde95ea2e3fc2f0f083bd8ac8aab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Configuración del nivel PremiumV2 para Azure App Service

El nuevo plan de tarifa **PremiumV2** incluye las [máquinas virtuales de la serie Dv2](../virtual-machines/windows/sizes-general.md#dv2-series), que cuentan con procesadores más rápidos, almacenamiento SSD y el doble de memoria en proporción de núcleo en comparación con el nivel **Estándar**. En este artículo, aprenderá a crear una aplicación en el nivel **PremiumV2** o escalar verticalmente una aplicación al nivel **PremiumV2**.

## <a name="prerequisites"></a>Requisitos previos

Para escalar verticalmente una aplicación web en **PremiumV2**, debe tener una aplicación web en Azure App Service que se ejecute en un plan de tarifa inferior a **PremiumV2**.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Disponibilidad del nivel PremiumV2

El nivel PremiumV2 actualmente solo está disponible para App Service en _Windows_. Los contenedores de Linux todavía no se admiten.

PremiumV2 ya está disponible en la mayoría de las regiones de Azure y no deja de crecer. Para ver si está disponible en su región, ejecute el siguiente comando de la CLI de Azure en [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

Si recibe un error durante la creación de la aplicación o la creación del plan de App Service, significa que lo más probable es que **PremiumV2** no esté disponible para la región elegida.

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Creación de una aplicación en el nivel PremiumV2

El plan de tarifa de una aplicación de App Service se define en el [plan de App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) en el que se ejecuta. Puede crear un plan de App Service por sí mismo o como parte de la creación de la aplicación web.

Cuando configure el plan de App Service en <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, seleccione **Plan de tarifa**. 

Elija una de las opciones **PremiumV2** y haga clic en **Seleccionar**.

![](media/app-service-configure-premium-tier/pick-premium-tier.png)

> [!IMPORTANT] 
> Si no ve **P1V2**, **P2V2** y **P3V2** como opciones, significa que **PremiumV2** no está disponible en la región elegida o que está configurando un plan de App Service de Linux que no admite **PremiumV2**.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Escalado vertical de una aplicación existente a nivel PremiumV2

Antes de escalar una aplicación existente al nivel **PremiumV2**, asegúrese de que **PremiumV2** está disponible en su región. Para información, consulte [Disponibilidad del nivel PremiumV2](#availability). Si no está disponible en su región, consulte [Escalado vertical desde una región no admitida](#unsupported).

Dependiendo del entorno de hospedaje, el escalado vertical puede requerir pasos adicionales. 

En <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, abra la página de la aplicación de App Service.

En el panel izquierdo de navegación de la página de la aplicación de App Service, seleccione **Escalar verticalmente (plan de App Service)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Seleccione uno de los tamaños de **PremiumV2** y haga clic en **Seleccionar**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Si la operación finaliza correctamente, la página de información general de la aplicación muestra que ahora está en un nivel **PremiumV2**.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Si recibe un error

Algunos planes de App Service no se pueden escalar verticalmente al nivel PremiumV2. Si la operación de escalado vertical produce un error, necesitará un nuevo plan de App Service para la aplicación.

Crear un plan de App Service _Windows_ en la misma región y grupo de recursos que la aplicación de App Service existente. Siga los pasos de [Creación de una aplicación en el nivel PremiumV2](#create) para establecerlo en el nivel **PremiumV2**. Si lo desea, utilice la misma configuración de escalado horizontal que el plan de App Service existente (número de instancias, escalado automático, etc).

Vuelva a abrir la página de la aplicación de App Service. En el panel izquierdo de navegación de App Service, seleccione **Cambiar el plan de App Service**.

![](media/app-service-configure-premium-tier/change-plan.png)

Seleccione el plan de App Service que acaba de crear.

![](media/app-service-configure-premium-tier/select-plan.png)

Una vez completada la operación de cambio, la aplicación se ejecuta en el nivel **PremiumV2**.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-region"></a>Escalado vertical desde una región no admitida

Si la aplicación se ejecuta en una región donde **PremiumV2** todavía no está disponible, puede mover la aplicación a otra región para aprovechar **PremiumV2**. Tiene dos opciones:

- Cree una aplicación en el nuevo plan **PremiumV2** y luego vuelva a implementar el código de aplicación. Siga los pasos de [Creación de una aplicación en el nivel PremiumV2](#create) para establecerlo en el nivel **PremiumV2**. Si lo desea, utilice la misma configuración de escalado horizontal que el plan de App Service existente (número de instancias, escalado automático, etc).
- Si la aplicación ya se ejecuta en un nivel **Premium** existente, puede clonar la aplicación con todas las configuraciones de aplicación, las cadenas de conexión y la configuración de implementación.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    En la página **Clonar aplicación**, puede crear un nuevo plan de App Service en la región que desee y especificar la configuración que desea clonar.

## <a name="automate-with-scripts"></a>Automatizar con scripts

Puede automatizar la creación de la aplicación en el nivel **PremiumV2** con scripts mediante la [CLI de Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>CLI de Azure

El siguiente comando crea un plan de App Service en _P1V2_. También puede ejecutarlo en Cloud Shell. Las opciones para `--sku` son P1V2, _P2V2_ y _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

El siguiente comando crea un plan de App Service en _P1V2_. Las opciones para `-WorkerSize` son _Pequeño_, _Medio_ y _Grande_.

```PowerShell
New-AzureRmAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Más recursos

[Escalado vertical de aplicaciones en Azure](web-sites-scale.md)  
[Escalado del recuento de instancias de forma manual o automática](../monitoring-and-diagnostics/insights-how-to-scale.md)