<properties
	pageTitle="Cambios del Administrador de recursos de la versión de vista previa 1.0 de Azure PowerShell | Microsoft Azure"
	description="Describe los cambios en los cmdlets del Administrador de recursos realizados para la versión de vista previa 1.0 de Azure PowerShell."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="na"
	ms.date="10/09/2015"
	ms.author="gauravbh;tomfitz"/>

#Cambios en los cdmlets de PowerShell de administración del Administrador de recursos de Azure

Como parte de la versión de vista previa 1.0 de Azure PowerShell, hemos aplicado algunas mejoras a los cmdlets de administración. Estas mejoras son independientes de los cambios de nombre del cmdlet que forman parte de la versión de vista previa 1.0. Algunas de estas mejoras implican grandes cambios y pueden detener sus scripts existentes. Esperamos que estos cambios mejoren su experiencia. Nos gustaría conocer su opinión acerca de estos cambios para incorporarla en Azure PowerShell 1.0. Por lo tanto, pruebe los nuevos cmdlets y háganos saber su opinión.

##Implementación de plantilla desacoplada desde los grupos de recursos

En la versión 0.9.8, todos los parámetros de implementación de plantilla también estaban presentes en los cmdlets del grupo de recursos. Por lo tanto, en New-AzureResourceGroup, podría crear un nuevo grupo de recursos, así como proporcionar detalles de las plantillas que le gustaría implementar. La misma funcionalidad de implementación de plantilla también estaba presente en New-AzureResourceGroupDeployment. En la versión de vista previa 1.0, hemos desacoplado esta funcionalidad. Ahora, New-AzureRMResourceGroup proporcionará la funcionalidad de creación de nuevos grupos de recursos y New-AzureRmResourceGroupDeployment proporcionará la funcionalidad de la implementación de la plantilla. Puede usar canalizaciones para utilizarlas juntas. Esto hace más fácil entender y usar los cmdlets.

##Cmdlets de registro de auditoría consolidados

Para los registros de auditoría, hemos tenido numerosos cmdlets para obtener registros en diversos ámbitos; por ejemplo, Get-AzureResourceProviderLog, Get-AzureResourceGroupLog, Get-AzureSubscriptionIdLog y Get-AzureResourceLog. Para obtener registros, a menudo ha tenido que ejecutar una combinación de los cmdlets de registro. Esta experiencia no ha sido óptima. Hemos consolidado esta funcionalidad en un solo cmdlet al que se puede llamar en diferentes ámbitos mediante el uso de parámetros. Ahora, puede llamar a Get-AzureRmLog con el parámetro adecuado para especificar el ámbito.

##Cambios en Get cmdlet for resources and resource groups (Obtener cmdlet para recursos y grupos de recursos)

Hemos incorporado cambios en los cmdlets Get-AzureRmResource y Get-AzureRmResourceGroup de modo que estos ejecuten ahora una consulta directamente solo en el proveedor de recursos. Hemos desacoplado la funcionalidad para ejecutar una consulta en la memoria caché en nuevos cmdlets llamados Find-AzureRmResource *. Si desea encontrar un grupo de recursos con una etiqueta particular, puede usar el nuevo cmdlet Find-AzureRmResourceGroup. Con este cambio, los parámetros para ejecutar una consulta en etiquetas se han quitado de los cmdlets Get-AzureRmResource y Get-AzureRmResourceGroup.

##Test-AzureResource y Test-AzureResourceGroup quitados

Estos cmdlets no funcionaban de forma confiable con cada tipo de recurso y escenario. Trabajamos para conseguir una mejor solución para esta funcionalidad. Mientras tanto, no deseábamos que siguiera utilizando los cmdlets que no eran fiables. Hemos quitado estos cmdlets en esta versión y agregaremos una solución confiable en una versión futura.

##Mejoras de Get-AzureRmResourceProvider

Ahora, puede usar el cmdlet Get-AzureRmResourceProvider para obtener información de ubicación para los proveedores de recursos. Le indicará qué proveedores y tipos están disponibles en una región determinada. Además, puede usar este cmdlet para obtener la lista de ubicaciones disponibles para un determinado proveedor. Hemos quitado el cmdlet Get-AzureLocation, ya que puede obtener toda la información de ubicación mediante el cmdlet Get-AzureRmResourceProvider.

##Cmdlets de directiva

Hemos agregado soporte técnico de la directiva al Administrador de recursos de Azure. Se han agregado los cmdlets de PowerShell para administrar directivas en esta versión. Para obtener más detalles acerca de las directivas, consulte [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).

<!---HONumber=Oct15_HO3-->