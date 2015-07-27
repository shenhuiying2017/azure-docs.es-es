<properties 
	pageTitle="Traslado de recursos al nuevo grupo de recursos" 
	description="Use Azure PowerShell o la API de REST para trasladar los recursos a un nuevo grupo de recursos para el Administrador de recursos de Azure." 
	services="" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="tomfitz"/>

# Traslado de los recursos a un nuevo grupo de recursos o a una suscripción

En este tema se muestra cómo trasladar recursos de un grupo de recursos a otro. También puede trasladar recursos a una nueva suscripción. Es posible que necesite trasladar recursos cuando decida que:

1. Para fines de facturación, un recurso debe residir en una suscripción diferente.
2. Un recurso ya no comparte el mismo ciclo de vida que los recursos con el que estaba agrupado anteriormente. Desea trasladarlo a un nuevo grupo de recursos para administrar ese recurso independientemente de los otros recursos.
3. Ahora, el recurso comparte el mismo ciclo de vida que los otros recursos de un grupo de recursos distinto. Desea trasladarlo al grupo de recursos con los otros recursos para administrarlos de forma conjunta.

Hay algunas consideraciones importantes cuando se mueve un recurso:

1. No puede cambiar la ubicación del recurso. Si se mueve un recurso, solo se mueve a un nuevo grupo de recursos. El nuevo grupo de recursos puede tener una ubicación diferente, pero no cambia la ubicación del recurso.
2. El grupo de recursos de destino debe contener únicamente los recursos que comparten el mismo ciclo de vida de aplicación que los recursos que se van a mover.
3. Si usa Azure PowerShell, asegúrese de que está utilizando la versión más reciente. El comando **Move-AzureResource** se actualiza con frecuencia. Para actualizar su versión, ejecute el Instalador de plataforma web de Microsoft y compruebe si hay disponible una nueva versión. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).
4. La operación de traslado puede tardar en completarse y durante ese tiempo el símbolo del sistema de PowerShell esperará hasta que se haya completado la operación.

## Servicios admitidos

No todos los servicios admiten actualmente la capacidad de traslado de recursos.

Por ahora, los servicios que admiten el traslado a un nuevo grupo de recursos y a una suscripción son:

- Administración de API
- Búsqueda de Azure
- Factoría de datos
- Almacén de claves
- Mobile Engagement
- Visión operativa
- Caché en Redis

Los servicios que admiten el traslado a un nuevo grupo de recursos, pero no una nueva suscripción son:

- Proceso (clásico)
- Almacenamiento (clásico)

Los servicios que actualmente permiten trasladar un recurso son:

- Redes virtuales

## Uso de PowerShell para trasladar recursos

Para trasladar recursos existentes a otro grupo de recursos o a una suscripción, use el comando el**Move-AzureResource**.

El primer ejemplo muestra cómo trasladar un recurso a un nuevo grupo de recursos.

    PS C:> Move-AzureResource -DestinationResourceGroupName TestRG -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OtherExample/providers/Microsoft.ClassicStorage/storageAccounts/examplestorage

El segundo ejemplo muestra cómo trasladar varios recursos a un nuevo grupo de recursos.

    PS C:> $webapp = Get-AzureResource -ResourceGroupName OldRG -ResourceName ExampleSite -ResourceType Microsoft.Web/sites
    PS C:> $plan = Get-AzureResource -ResourceGroupName OldRG -ResourceName ExamplePlan -ResourceType Microsoft.Web/serverFarms
    PS C:> Move-AzureResource -DestinationResourceGroupName NewRG -ResourceId ($webapp.ResourceId, $plan.ResourceId)

Para trasladar a una nueva suscripción, especifique un valor para el parámetro **DestinationSubscriptionId**.

## Uso de la API de REST para trasladar recursos

Para trasladar recursos existentes a otro grupo de recursos o a una suscripción, ejecute:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Reemplace **{source-subscription-id}** y **{source-resource-group-name}** por la suscripción y el grupo de recursos que contienen actualmente los recursos que desea trasladar. Utilice **2015-01-01** para {api-version}.

En la solicitud, incluyen un objeto JSON que define el grupo de recursos de destino y los recursos que desea mover.

    {
        "targetResourceGroup": "/subscriptions/{target-subscription-id}/resourceGroups/{target-resource-group-name}", "resources": [
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}"
        ]
    }

## Pasos siguientes
- [Uso de Azure PowerShell con el Administrador de recursos](./powershell-azure-resource-manager.md)
- [Uso de la CLI de Azure para Mac, Linux y Windows con administración de recursos de Azure](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Uso del Portal de Azure para administrar los recursos de Azure](azure-portal/resource-group-portal.md)
- [Uso de etiquetas para organizar los recursos de Azure](./resource-group-using-tags.md)

<!---HONumber=July15_HO3-->