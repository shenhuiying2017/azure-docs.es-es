---
title: Hospedaje de alta densidad en Azure App Service | Microsoft Docs
description: Hospedaje de alta densidad en Azure App Service
author: btardif
manager: erikre
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 22/01/2018
ms.author: byvinyal
ms.openlocfilehash: 2ffffd3cc9f5c59f74f71d6d7d31c5ea615d11f4
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="high-density-hosting-on-azure-app-service"></a>Hospedaje de alta densidad en Azure App Service
Cuando se utilice App Service, la aplicación se desacoplará de la capacidad que se le ha asignado por dos conceptos:

* **La aplicación:** representa la aplicación y la configuración de su sistema en tiempo de ejecución. Por ejemplo, incluye la versión de .NET que el sistema en tiempo de ejecución debe cargar, la configuración de la aplicación, etc.
* **El plan de App Service:** define las características de la capacidad, el conjunto de características disponibles y la localidad de la aplicación. Por ejemplo, las características podrían ser una máquina grande (cuatro núcleos), cuatro instancias y características Premium del este de EE. UU.

Una aplicación siempre está vinculada a un plan de App Service, pero un plan de App Service puede proporcionar capacidad para una o más aplicaciones.

Como resultado, la plataforma ofrece flexibilidad para aislar una sola aplicación o tiene varias aplicaciones que comparten recursos al compartir un plan de App Service.

Sin embargo, cuando varias aplicaciones comparten un plan de App Service, una instancia de esa aplicación se ejecuta en cada instancia de ese plan.

## <a name="per-app-scaling"></a>Escalado por aplicación
*Escalado por aplicación* es una característica que se puede habilitar en el nivel del plan de App Service y usar después por aplicación.

El escalado por aplicación escala una aplicación de forma independiente desde el plan de App Service que lo hospeda. De esta manera, se puede escalar un plan de App Service a 10 instancias, pero se puede configurar una aplicación para usar solo cinco.

   >[!NOTE]
   >El escalado por aplicación solo está disponible para los planes de App Service de SKU **Estándar**, **Premium**, **Premium V2** y **Aislado**.
   >

### <a name="per-app-scaling-using-powershell"></a>Escalado por aplicación mediante PowerShell

Cree un plan configurado como un plan de *escalado por aplicación* pasando el atributo ```-perSiteScaling $true``` al commandlet ```New-AzureRmAppServicePlan```.

```
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Si desea actualizar un plan de App Service existente para usar esta característica: 

- obtenga el plan de destino ```Get-AzureRmAppServicePlan```
- modifique la propiedad localmente ```$newASP.PerSiteScaling = $true```
- publique los cambios en Azure ```Set-AzureRmAppServicePlan``` 

```
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

En el nivel de aplicación, configure el número de instancias que puede usar la aplicación en el plan de App Service.

En el ejemplo siguiente, la aplicación está limitada a dos instancias, independientemente de la cantidad de estas a las que se escale horizontalmente el plan de App Service subyacente.

```
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> $newapp.SiteConfig.NumberOfWorkers es diferente de $newapp.MaxNumberOfWorkers. En el escalado por aplicación se usa $newapp.SiteConfig.NumberOfWorkers para determinar las características de escalado de la aplicación.

### <a name="per-app-scaling-using-azure-resource-manager"></a>Escalado por aplicación mediante Azure Resource Manager

La siguiente *plantilla de Azure Resource Manager* crea:

- Un plan de App Service que se escala horizontalmente a 10 instancias.
- Una aplicación que está configurada para escalarse hasta un máximo de 5 instancias.

El plan de App Service es establecer la propiedad **PerSiteScaling** en true ```"perSiteScaling": true```. La aplicación configura el **número de trabajadores** que se va a usar en 5 ```"properties": { "numberOfWorkers": "5" }```.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Configuración recomendada para el hospedaje de alta densidad
El escalado por aplicación es una característica que está habilitada en las regiones de Azure globales y los entornos de App Service. Sin embargo, la estrategia recomendada es usar entornos de App Service para aprovechar sus características avanzadas y los grupos de mayor capacidad.  

Siga estos pasos para configurar el hospedaje de alta densidad para las aplicaciones:

1. Configure el entorno del App Service y elija un grupo de trabajo dedicado al escenario de hospedaje de alta densidad.
1. Cree un único plan de App Service y escálelo para que utilice toda la capacidad disponible en el grupo de trabajo.
1. Establezca la marca PerSiteScaling en true en el plan de App Service.
1. Las nuevas aplicaciones se crean y se asignan al plan de App Service con la propiedad **numberOfWorkers** establecida en **1**. El uso de esta configuración produciría la máxima densidad posible en este grupo de trabajo.
1. El número de trabajadores se puede configurar por separado por cada aplicación para conceder recursos adicionales según sea necesario. Por ejemplo: 
    - En una aplicación de uso elevado, puede establecer **numberOfWorkers** en **3** para que haya una mayor capacidad de procesamiento para esa aplicación. 
    - En las aplicaciones de poco uso se establecerá **numberOfWorkers** en **1**.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción detallada sobre los planes de Azure App Service](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [Introducción al entorno de App Service](app-service-app-service-environment-intro.md)
