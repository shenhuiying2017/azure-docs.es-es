<properties 
	pageTitle="Hospedaje de alta densidad en el Servicio de aplicaciones de Azure" 
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
	ms.date="05/17/2016" 
	ms.author="byvinyal"/>

#Hospedaje de alta densidad en el Servicio de aplicaciones de Azure#

##Descripción de escalado de la aplicación##

Cuando se utilice el Servicio de aplicaciones, la aplicación se desacoplará de la capacidad que se le ha asignado por dos conceptos:
 
>**La aplicación:** representa la aplicación y su configuración en tiempo de ejecución. Por ejemplo, ¿qué versión de .NET debe cargar el tiempo de ejecución? ¿cuáles son las configuraciones de la aplicación? etc.

>**El Plan del servicio de aplicaciones:** define las características de la capacidad, el conjunto de características disponibles y la localidad de la aplicación. Por ejemplo, Máquina grande (cuatro núcleos), cuatro instancias, características premium en Este de EE. UU.

Una aplicación siempre está vinculada a un **Plan del servicio de aplicaciones** pero un **Plan del servicio de aplicaciones** puede proporcionar capacidad a una o más aplicaciones.

Esto significa que la plataforma ofrece flexibilidad para aislar una sola aplicación o tiene varias aplicaciones que comparten recursos al compartir un **Plan del servicio de aplicaciones**.

Sin embargo, cuando varias aplicaciones comparten un **Plan del servicio de aplicaciones**, una instancia de esa aplicación se ejecutará en cada instancia de ese **Plan del servicio de aplicaciones**.

##Per App scaling (Escalado por aplicación)##
**Per App scaling** (Escalado por aplicación) es una característica que se puede habilitar en el nivel **Plan del servicio de aplicaciones** y usar después por aplicación.

**Per App Scaling** (Escalado por aplicación) permite escalar una aplicación independientemente del **Plan del servicio de aplicaciones** que se use para hospedarlo. De este modo, un **Plan del servicio de aplicaciones** puede configurarse para proporcionar 10 instancias, pero se puede establecer una aplicación para escalar a 5 solo de ellos.

La plantilla ARM siguiente creará un **Plan del servicio de aplicaciones** escalado a 10 instancias y una aplicación configurada para usar **Per App scaling** (Escalado por aplicación) y escalada solo a 5 instancias.

Para ello, el Plan del servicio de aplicaciones establece la propiedad de escalado por sitio en true (`"perSiteScaling": true`) y la aplicación establece el número de trabajadores que se utilice en 1 `"properties": { "numberOfWorkers": "1" }`

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


##Configuración recomendada para el hospedaje de alta densidad##

**Per App scaling** (Escalado por aplicación) es una característica que está habilitada en ambas regiones públicas de Azure, así como en Entornos del Servicio de aplicaciones; sin embargo, la estrategia recomendada es utilizar Entornos del Servicio de aplicaciones para aprovechar sus características avanzadas y los grupos de mayor capacidad.

Siga los pasos indicados a continuación como guía para configurar el **Hospedaje de alta densidad** para sus aplicaciones.

1. Configure el **Entorno de servicio de aplicaciones** y elija un **grupo de trabajo** dedicado al escenario de *hospedaje de alta densidad*.

1. Cree un único **Plan del servicio de aplicaciones** y escálelo para que utilice toda la capacidad disponible en el **grupo de trabajo**.

1. Establezca la marca de escalado por sitio en true en el **Plan del servicio de aplicaciones**.

1. Se crean sitios nuevos y se asignan al **Plan del servicio de aplicaciones** con la propiedad *numberOfWorkers* establecida en *1*. Esto produciría la máxima densidad posible en este **grupo de trabajo**

1. El número de trabajadores se puede configurar por separado por cada sitio para conceder recursos adicionales si es necesario. Por ejemplo, en un sitio de gran uso se puede establecer *numberOfWorkers* en *3* para tener más capacidad de procesamiento para esa aplicación, mientras que en los sitios de poco uso se puede establecer *numberOfWorkers* en *1*.

<!---HONumber=AcomDC_0518_2016-->