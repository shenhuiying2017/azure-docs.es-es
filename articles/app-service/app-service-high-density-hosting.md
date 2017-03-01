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
ms.date: 01/11/2017
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 0c2677b388f7a88ff88715a05212633565393cc2
ms.openlocfilehash: 2d5d1d5123ca718b2e7dcdf426b77f91969dc9dc
ms.lasthandoff: 01/13/2017


---
# <a name="high-density-hosting-on-azure-app-service"></a>Hospedaje de alta densidad en Azure App Service
Cuando se utilice App Service, la aplicación se desacoplará de la capacidad que se le ha asignado por dos conceptos:

* **La aplicación:** representa la aplicación y la configuración de su sistema en tiempo de ejecución. Por ejemplo, incluye la versión de .NET que el sistema en tiempo de ejecución debe cargar, la configuración de la aplicación, etc.
* **El plan de App Service:** define las características de la capacidad, el conjunto de características disponibles y la localidad de la aplicación. Por ejemplo, las características podrían ser una máquina grande (cuatro núcleos), cuatro instancias y características Premium del este de EE. UU.

Una aplicación siempre está vinculada a un plan del Servicio de aplicaciones, pero un plan del Servicio de aplicaciones puede proporcionar capacidad para una o más aplicaciones.

Como resultado, la plataforma ofrece flexibilidad para aislar una sola aplicación o tiene varias aplicaciones que comparten recursos al compartir un plan de App Service.

Sin embargo, cuando varias aplicaciones comparten un plan del Servicio de aplicaciones, una instancia de esa aplicación se ejecuta en cada instancia de ese plan.

## <a name="per-app-scaling"></a>Escalado por aplicación
*Escalado por aplicación* es una característica que se puede habilitar en el nivel del plan del Servicio de aplicaciones y usar después por aplicación.

El escalado por aplicación escala una aplicación de forma independiente desde el plan del Servicio de aplicaciones que lo hospeda. De este modo, se puede configurar un plan del Servicio de aplicaciones para proporcionar 10 instancias, pero se puede establecer una aplicación para escalar a solo 5 de ellas.

   >[!NOTE]
   >El escalado por aplicación solo está disponible para los planes de App Service de SKU **premium**.
   >

### <a name="per-app-scaling-using-powershell"></a>Escalado por aplicación mediante PowerShell

Puede crear un nuevo plan configurado como *Escalado por aplicación* pasando el atributo ```-perSiteScaling $true``` al commandlet ```New-AzureRmAppServicePlan```.

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

Una vez que tenga un plan que se haya configurado, puede establecer el número máximo de instancias para cada una de las aplicaciones.

En el ejemplo siguiente, la aplicación se limita a dos instancias como máximo, independientemente de la cantidad de instancias a las que se escale horizontalmente el plan de App Service subyacente.

```
    # Get the app we want to configure to use "PerSiteScaling"
    $newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
    # Modify the NumberOfWorkers setting to the desired value.
    $newapp.SiteConfig.NumberOfWorkers = 2
    
    # Post updated app back to azure
    Set-AzureRmWebApp $newapp
```

### <a name="per-app-scaling-using-azure-resource-manager"></a>Escalado por aplicación mediante Azure Resource Manager

La siguiente *plantilla de Azure Resource Manager* crea:

- Un plan de App Service que se escala horizontalmente a 10 instancias.
- Una aplicación que está configurada para escalarse hasta un máximo de&5; instancias.

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
El escalado por aplicación es una característica que está habilitada en las regiones de Azure públicas y los entornos del Servicio de aplicaciones. Sin embargo, la estrategia recomendada es usar entornos del Servicio de aplicaciones para aprovechar sus características avanzadas y los grupos de mayor capacidad.  

Siga estos pasos para configurar el hospedaje de alta densidad para las aplicaciones:

1. Configure el entorno del App Service y elija un grupo de trabajo dedicado al escenario de hospedaje de alta densidad.
2. Cree un único plan del Servicio de aplicaciones y escálelo para que utilice toda la capacidad disponible en el grupo de trabajo.
3. Establezca la marca PerSiteScaling en true en el plan de App Service.
4. Las nuevas aplicaciones se crean y se asignan al plan de App Service con la propiedad **numberOfWorkers** establecida en **1**. El uso de esta configuración produciría la máxima densidad posible en este grupo de trabajo.
5. El número de trabajadores se puede configurar por separado por cada aplicación para conceder recursos adicionales según sea necesario. Por ejemplo, en una aplicación de gran uso se podría establecer **numberOfWorkers** en **3** para tener más capacidad de procesamiento para esa aplicación, mientras que en las aplicaciones de poco uso se podría establecer **numberOfWorkers** en **1**.


