<properties 
	pageTitle="Uso de etiquetas para organizar los recursos de Azure" 
	description="" 
	services="" 
	documentationCenter="" 
	authors="flanakin" 
	writer="" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2014" 
	ms.author="micflan"/>


# Uso de etiquetas para organizar los recursos de Azure

El portal de vista previa de Azure y el Administrador de recursos subyacente van a organizar sus recursos y personalizar su experiencia a su medida. 

En el portal completo de Azure, las suscripciones son la única manera de categorizar y agrupar los recursos. Con el portal de vista previa, [presentamos los grupos de recursos](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups), que le permiten agrupar entidades relacionadas. Esto adquirió más valor aún cuando [presentamos el acceso basado en roles](http://azure.microsoft.com/documentation/articles/role-based-access-control-configure). Ahora, siguiendo ese mismo espíritu, puede etiquetar sus recursos con pares clave/valor para categorizar aún más y ver recursos entre grupos de recursos y, dentro del portal, entre suscripciones.

Agrupe recursos por equipo, proyecto o, incluso, entorno para centrarse exactamente en lo que desea ver, cuando necesita verlo. 


## Etiquetas en el Portal de vista previa de Azure

Etiquetar recursos y grupos de recursos es fácil en el portal de vista previa. Use el centro de exploración para navegar al recurso o grupo de recursos que le gustaría etiquetar y haga clic en la parte Etiquetas en la sección Información general que se encuentra en la parte superior de la hoja. 

![Tags part on resource and resource group blades](./media/azure-preview-portal-using-tags/rgblade.png)

Se abrirá una hoja con la lista de etiquetas que ya se han aplicado. Si se trata de la primera etiqueta, la lista estará vacía. Para agregar una etiqueta, especifique un nombre y valor y presione Entrar. Después de agregar algunas etiquetas, verá opciones de Autocompletar según los valores y nombres de etiquetas preexistentes para asegurar mejor una taxonomía coherente entre los recursos y evitar errores comunes, como por ejemplo los ortográficos.

![Tag resources with name/value pairs](./media/azure-preview-portal-using-tags/tag-resources.png)

Desde aquí, puede hacer clic en cada etiqueta individual para ver una lista de todos los recursos con la misma etiqueta. Por supuesto, si se trata de la primera etiqueta, esa lista no será muy interesante. Por ahora, vamos a PowerShell para etiquetar todos nuestros recursos rápidamente.


## Etiquetado con PowerShell

En primer lugar, aproveche el [módulo Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) más reciente. Si es la primera vez que usa el módulo Azure PowerShell, [lea la documentación](http://azure.microsoft.com/documentation/articles/install-configure-powershell) para ponerse al día. Para la finalidad de este artículo, supondremos que ya se ha agregado una cuenta y ha seleccionado una suscripción con los recursos que desea etiquetar.

El etiquetado solamente está disponible para recursos y grupos de recursos disponibles en el [Administrador de recursos](http://msdn.microsoft.com/library/azure/dn790568.aspx), por lo que lo siguiente que debemos hacer es cambiar para usar dicho administrador. Para obtener más información, consulte [Uso de Windows PowerShell con el Administrador de recursos](http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/).

  Switch-AzureMode AzureResourceManager

Existen etiquetas directamente en los recursos y grupos de recursos, por lo que para ver qué etiquetas ya se aplican, podemos simplemente obtener un recurso o grupo de recursos con `Get-AzureResource` o `Get-AzureResourceGroup`, respectivamente. Comencemos con un grupo de recursos.

![Getting tags with Get-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

Este cmdlet devuelve varios bits de metadatos en el grupo de recursos, incluso qué etiquetas se han aplicado, si las hay. Para etiquetar  un grupo de recursos, simplemente usaremos `Set-AzureResourceGroup` y especificaremos un nombre y valor de etiqueta.

![Setting tags with Set-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

Recuerde que las etiquetas se actualizan como un todo, por lo que si va a agregar una etiqueta a un recurso que ya se ha etiquetado, deberá guardar una matriz con todas las etiquetas que desee conservar. Para quitar una, simplemente guarde la matriz sin la que desea quitar. 

El proceso es el mismo para los recursos, excepto en que va a usar los cmdlets `Get-AzureResource` y `Set-AzureResource`. Para obtener recursos o grupos de recursos con una etiqueta específica, use el cmdlet `Get-AzureResource` o `Get-AzureResourceGroup` con el parámetro `-Tag`.

![Getting tagged resources and resource groups with Get-AzureResource and Get-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## Etiquetado con el Administrador de recursos

El portal de vista previa y PowerShell usan la [API REST del Administrador de recursos](http://msdn.microsoft.com/library/azure/dn790568.aspx) en segundo plano. Si necesita integrar el etiquetado en otro entorno, puede obtener etiquetas con un comando GET en el identificador de recurso y actualizar el conjunto de etiquetas con una llamada PATCH.


## Administración de la taxonomía

Anteriormente, hablamos sobre cómo Autocompletar ayuda a garantizar la coherencia y evitar errores. Autocompletar se rellena según la taxonomía de la configuración de etiquetas disponibles para la suscripción. Cada etiqueta que agrega a un recurso o grupo de recursos se agrega automáticamente a la taxonomía de toda la suscripción, pero también puede rellenar por adelantado esa taxonomía con valores y nombres de etiqueta que le gustaría usar cuando los recursos se etiqueten en el futuro.

Para obtener una lista de todas las etiquetas dentro de una suscripción usando PowerShell, use el cmdlet `Get-AzureTag`.

![Get-AzureTag in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureTag-in-PowerShell.png)


Puede ver las etiquetas que comienzan con hidden-" y "link:". Se trata de etiquetas internas, que se deben omitir y evitar cambiar. 

Use el cmdlet `New-AzureTag` para agregar nuevas etiquetas a la taxonomía. Estas etiquetas se incluirán en la característica Autocompletar, aunque todavía no se hayan aplicado a los recursos o grupos de recursos. Para quitar un nombre o valor de etiqueta, quite primero la etiqueta de los recursos con los que se pueda utilizar y, a continuación, use el cmdlet `Remove-AzureTag` para quitarla de taxonomía.

Para ver la taxonomía de etiquetas en el portal, use el centro de exploración para ver todo y, a continuación, seleccione Etiquetas.

![Find tags via the Browse hub](./media/azure-preview-portal-using-tags/browse-tags.png)

Ancle las etiquetas más importantes en el Panel de inicio para un acceso rápido y estar preparado para trabajar. ¡Diviértase!

![Pin tags to the Startboard](./media/azure-preview-portal-using-tags/pin-tags.png)


<!--HONumber=46--> 

<!--HONumber=46--> 
