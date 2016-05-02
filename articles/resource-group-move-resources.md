<properties 
	pageTitle="Traslado de recursos al nuevo grupo de recursos" 
	description="Use Azure PowerShell o la API de REST para trasladar los recursos a un nuevo grupo de recursos para el Administrador de recursos de Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016" 
	ms.author="tomfitz"/>

# Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción

En este tema se muestra cómo trasladar recursos de un grupo de recursos a otro. También es posible mover los recursos a una nueva suscripción (sin embargo la suscripción debe existir en el mismo [inquilino](./active-directory/active-directory-howto-tenant.md)). Es posible que necesite trasladar recursos cuando decida que:

1. Para fines de facturación, un recurso debe residir en una suscripción diferente.
2. Un recurso ya no comparte el mismo ciclo de vida que los recursos con el que estaba agrupado anteriormente. Desea trasladarlo a un nuevo grupo de recursos para administrar ese recurso independientemente de los otros recursos.
3. Ahora, el recurso comparte el mismo ciclo de vida que los otros recursos de un grupo de recursos distinto. Desea trasladarlo al grupo de recursos con los otros recursos para administrarlos de forma conjunta.

Al mover los recursos, el grupo de origen y el grupo de destino se bloquean durante la operación. Las operaciones de escritura y eliminación están bloqueadas en los grupos hasta que se completa el movimiento.

No puede cambiar la ubicación del recurso. Si se mueve un recurso, solo se mueve a un nuevo grupo de recursos. El nuevo grupo de recursos puede tener una ubicación diferente, pero no cambia la ubicación del recurso.

## Lista de comprobación antes de mover recursos

Hay algunos pasos importantes que deben realizarse antes de mover un recurso. Puede evitar errores mediante la comprobación de estas condiciones.

1. El servicio debe admitir la capacidad de traslado de recursos. Consulte la siguiente lista para obtener más información sobre [los servicios que admiten el traslado de recursos](#services-that-support-move).
2. La suscripción de destino correspondiente al proveedor de recursos del recurso que se traslada debe estar registrada. Si no es así, recibirá un error en el que se indicará que la **suscripción no está registrada para un tipo de recurso**. Podría encontrar este problema al mover un recurso a una nueva suscripción que nunca se ha utilizado el suscripción con ese tipo de recurso. Para obtener más información sobre cómo comprobar el estado de registro y registrar proveedores de recursos, consulte [Tipos y proveedores de recursos](../resource-manager-supported-services/#resource-providers-and-types).
3. Si usa Azure PowerShell o la CLI de Azure, utilice la versión más reciente. Para actualizar su versión, ejecute el Instalador de plataforma web de Microsoft y compruebe si hay disponible una nueva versión. Para más información, vea [Instalación y configuración de Azure PowerShell](powershell-install-configure.md) e [Instalación de la CLI de Azure](xplat-cli-install.md).
4. Si traslada la aplicación del Servicio de aplicaciones, tiene que revisar las [limitaciones del Servicio de aplicaciones](#app-service-limitations).

## Servicios compatibles con el traslado

Por ahora, los servicios que admiten el traslado a un nuevo grupo de recursos y a una nueva suscripción son:

- Administración de API
- Aplicaciones del Servicio de aplicaciones (consulte [App Service limitations](#app-service-limitations) [Limitaciones del Servicio de aplicaciones] más abajo)
- Automatización
- Lote
- Servicio CDN
- Factoría de datos
- DocumentDB
- Clústeres de HDInsight
- Almacén de claves
- Mobile Engagement
- Centros de notificaciones
- Visión operativa
- Caché en Redis
- Search
- Servidor de Base de datos SQL (consulte [SQL Database limitations](#sql-database-limitations) [Limitaciones de Base de datos SQL] más abajo)

## Servicios parcialmente compatibles con el traslado

Los servicios que admiten el traslado a un nuevo grupo de recursos, pero no una nueva suscripción son:

- Máquinas virtuales (clásicas)
- Almacenamiento (clásico)
- Redes virtuales
- Servicios en la nube

## Servicios no compatibles con el traslado

Los servicios que actualmente no permiten trasladar un recurso son:

- Máquinas virtuales
- Almacenamiento
- ExpressRoute

## Limitaciones del Servicio de aplicaciones

Si se trabaja con aplicaciones del Servicio de aplicaciones, no se puede mover solo un plan del Servicio de aplicaciones. Para mover las aplicaciones del Servicio de aplicaciones, las opciones son:

- Mover todos los recursos de un grupo de recursos a otro grupo de recursos, si el grupo de recursos de destino no tiene ya recursos Microsoft.Web.
- Mover las aplicaciones web a un grupo de recursos distinto, pero mantener el plan del Servicio de aplicaciones del grupo de recursos original.

## Limitaciones de Base de datos SQL

No puede mover una base de datos SQL por separado del servidor. La base de datos y el servidor deben residir en el mismo grupo de recursos. Cuando se mueve un servidor SQL Server, se mueven también todas sus bases de datos.

## Uso de PowerShell para trasladar recursos

Para mover recursos existentes a otro grupo de recursos o a otra suscripción, use el comando **Move-AzureRmResource**.

El primer ejemplo muestra cómo trasladar un recurso a un nuevo grupo de recursos.

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

El segundo ejemplo muestra cómo trasladar varios recursos a un nuevo grupo de recursos.

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Para moverlos a una nueva suscripción, especifique un valor para el parámetro **DestinationSubscriptionId**.

## Uso de CLI de Azure para mover recursos

Para mover recursos existentes a otro grupo de recursos o a otra suscripción, use el comando **azure resource move**. En el siguiente ejemplo se muestra cómo trasladar una Caché de Redis a un nuevo grupo de recursos. En el parámetro **-i**, ofrezca una lista separada por comas del id. de recurso que se va a mover.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## Uso de la API de REST para trasladar recursos

Para trasladar recursos existentes a otro grupo de recursos o a una suscripción, ejecute:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

En el cuerpo de la solicitud, especifique el grupo de recursos de destino y los recursos a mover. Para obtener más información acerca de la operación REST de movimiento, consulte [Mover recursos](https://msdn.microsoft.com/library/azure/mt218710.aspx).

## Uso del portal para mover recursos

Algunos recursos se pueden mover a través del portal; sin embargo, no todos los proveedores de recursos que admiten dicha proporcionan esa funcionalidad a través del portal.

Para mover un recurso, selecciónelo y, después, haga clic en el botón **Mover**.

![mover recurso](./media/resource-group-move-resources/move-resources.png)

Especifique el lugar al que desea mover el recurso. Si deben moverse otros recursos junto con el seleccionado, se enumerarán.

![seleccionar destino](./media/resource-group-move-resources/select-destination.png)

## Pasos siguientes
- Para obtener información sobre los cmdlets de PowerShell para administrar su suscripción, vea [Uso de Azure PowerShell con Azure Resource Manager](powershell-azure-resource-manager.md).
- Para obtener información sobre los comandos de CLI de Azure para administrar su suscripción, vea [Uso de la CLI de Azure para Mac, Linux y Windows con Azure Resource Manager](xplat-cli-azure-resource-manager.md).
- Para obtener información sobre características para administrar su suscripción, vea [Uso del Portal de Azure para implementar y administrar los recursos de Azure](./azure-portal/resource-group-portal.md).
- Para aprender a aplicar una organización lógica a los recursos, vea [Uso de etiquetas para organizar los recursos de Azure](resource-group-using-tags.md).

<!---HONumber=AcomDC_0420_2016-->