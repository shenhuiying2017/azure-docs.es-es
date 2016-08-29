<properties
	pageTitle="Hospedaje de alta densidad en el Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Hospedaje de alta densidad en el Servicio de aplicaciones de Azure"
	authors="btardif"
	manager="wpickett"
	editor=""
	services="app-service\web"
	documentationCenter=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/07/2016"
	ms.author="byvinyal"/>

# Hospedaje de alta densidad en el Servicio de aplicaciones de Azure#

Cuando se utiliza el Servicio de aplicaciones de Azure, la aplicación se desliga de su capacidad asignada por dos conceptos:

- **La aplicación:** representa la aplicación y la configuración de su sistema en tiempo de ejecución. Por ejemplo, incluye la versión de .NET que el sistema en tiempo de ejecución debe cargar, la configuración de la aplicación, etc.

- **El plan del Servicio de aplicaciones:** define las características de la capacidad, el conjunto de características disponibles y la localidad de la aplicación. Por ejemplo, las características podrían ser una máquina grande (cuatro núcleos), cuatro instancias y características Premium del este de EE. UU.

Una aplicación siempre está vinculada a un plan del Servicio de aplicaciones, pero un plan del Servicio de aplicaciones puede proporcionar capacidad para una o más aplicaciones.

Esto significa que la plataforma ofrece flexibilidad para aislar una sola aplicación o tiene varias aplicaciones que comparten recursos al compartir un plan del Servicio de aplicaciones.

Sin embargo, cuando varias aplicaciones comparten un plan del Servicio de aplicaciones, una instancia de esa aplicación se ejecuta en cada instancia de ese plan.

## Escalado por aplicación##
*Escalado por aplicación* es una característica que se puede habilitar en el nivel del plan del Servicio de aplicaciones y usar después por aplicación.

El escalado por aplicación escala una aplicación de forma independiente desde el plan del Servicio de aplicaciones que lo hospeda. De este modo, se puede configurar un plan del Servicio de aplicaciones para proporcionar 10 instancias, pero se puede establecer una aplicación para escalar a solo 5 de ellas.

La siguiente plantilla de Azure Resource Manager creará un plan del Servicio de aplicaciones que se escala horizontalmente hasta 10 instancias y una aplicación que está configurada para usar el escalado por aplicación y escalar a solo 5 instancias.

Para ello, el plan del Servicio de aplicaciones establece la propiedad de **escalado por sitio** en true (`"perSiteScaling": true`) y la aplicación establece el **número de trabajadores** que se usará en 1 (`"properties": { "numberOfWorkers": "1" }`).

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
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
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
                    "properties": { "numberOfWorkers": "1" }
             } ]
         }]
    }


## Configuración recomendada para el hospedaje de alta densidad

El escalado por aplicación es una característica que está habilitada en las regiones de Azure públicas y los entornos del Servicio de aplicaciones. Sin embargo, la estrategia recomendada es usar entornos del Servicio de aplicaciones para aprovechar sus características avanzadas y los grupos de mayor capacidad.

Siga estos pasos para configurar el hospedaje de alta densidad para las aplicaciones:

1. Configure el entorno del Servicio de aplicaciones y elija un grupo de trabajo dedicado al escenario de hospedaje de alta densidad.

1. Cree un único plan del Servicio de aplicaciones y escálelo para que utilice toda la capacidad disponible en el grupo de trabajo.

1. Establezca la marca de escalado por sitio en true en el plan del Servicio de aplicaciones.

1. Los nuevos sitios se crean y se asignan al plan del Servicio de aplicaciones con la propiedad **numberOfWorkers** establecida en **1**. Esto produciría la máxima densidad posible en este grupo de trabajo.

1. El número de trabajadores se puede configurar por separado por cada sitio para conceder recursos adicionales si es necesario. Por ejemplo, en un sitio de gran uso se podría establecer **numberOfWorkers** en **3** para tener más capacidad de procesamiento para esa aplicación, mientras que en los sitios de poco uso se podría establecer **numberOfWorkers** en **1**.

<!---HONumber=AcomDC_0817_2016-->