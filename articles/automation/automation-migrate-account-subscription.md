<properties
   pageTitle="Migrar una cuenta de Automatización y sus recursos | Microsoft Azure"
   description="En este artículo se describe cómo mover una cuenta de Automatización en Automatización de Azure y sus recursos relacionados correspondientes de una suscripción a otra."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/07/2016"
   ms.author="magoedte" />

# Migrar una cuenta de Automatización y sus recursos

Si quiere migrar las cuentas de Automatización y sus recursos asociados (activos, Runbooks, módulos, etc.) que ha creado en el Portal de Azure desde un grupo de recursos a otro o desde una suscripción a otra, puede hacerlo fácilmente con la característica [Mover recursos](../resource-group-move-resources.md), disponible en el Portal de Azure. Pero, antes de llevar esto a cabo, conviene revisar la siguiente [lista de comprobación antes de mover recursos](../resource-group-move-resources.md#Checklist-before-moving-resources) y, además, la siguiente lista específica de la Automatización.

1.  El grupo de recursos/suscripción de destino debe estar en la misma región que el origen. Es decir, no se pueden mover cuentas de Automatización de una región a otra.
2.  Al mover recursos (por ejemplo, Runbooks, trabajos, etc.), el grupo de origen y el grupo de destino se bloquean durante la operación. Las operaciones de escritura y eliminación están bloqueadas en los grupos hasta que se completa el movimiento.
3.  Los Runbooks o variables que hacen referencia a un identificador de suscripción o recurso de la suscripción existente deben actualizarse después de completar la migración.


>[AZURE.NOTE] Con esta característica no se pueden mover recursos de Automatización clásicos.

## Para mover la cuenta de Automatización a través del Portal

1. En la cuenta de Automatización, haga clic en **Mover** en la parte superior de la hoja.<br> ![Opción Mover](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. En la hoja **Mover recursos**, vea que se muestran recursos relacionados tanto con la cuenta de Automatización como con los grupos de recursos. Seleccione la **suscripción** y el **grupo de recursos** de las listas desplegables o seleccione la opción **Crear un nuevo grupo de recursos** y escriba el nombre del nuevo grupo de recursos en el campo proporcionado.
3. Revise y active la casilla *Entiendo que las herramientas y los scripts necesitarán actualizarse para usar los nuevos identificadores de recursos después de que los recursos se hayan movido*. Tras ello, haga clic en **Aceptar**.<br> ![Hoja Mover recursos](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>

Este paso puede tardar varios minutos en completarse. En **Notificaciones**, aparecerá el estado de cada acción que haya tenido lugar: validación, migración y, por último, cuando el proceso finalice.

## Para mover la cuenta de Automatización con PowerShell

Para mover los recursos de Automatización existentes a otra suscripción o grupo de recursos, use el cmdlet **Get-AzureRmResource** para obtener la cuenta de Automatización específica y, después, el cmdlet **Move-AzureRmResource** para llevar a cabo el movimiento.

El primer ejemplo muestra cómo mover una cuenta de Automatización a un nuevo grupo de recursos.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ``` 

Después de ejecutar el ejemplo de código anterior, se le pedirá que confirme que quiere realizar esta acción. Cuando haga clic en **Sí** y deje que el script continúe, no recibirá ninguna notificación mientras la migración se realiza.

Para moverlos a una nueva suscripción, especifique un valor para el parámetro *DestinationSubscriptionId*.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ``` 

Al igual que en el ejemplo anterior, se le pedirá que confirme el movimiento.

## Pasos siguientes

- Para más información sobre cómo mover recursos a un nuevo grupo de recursos o a una nueva suscripción, vea [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../resource-group-move-resources.md).
- Para más información sobre el control de acceso basado en roles de Automatización de Azure, vea [Control de acceso basado en rol en Automatización de Azure](../automation/automation-role-based-access-control.md).
- Para más información sobre los cmdlets de PowerShell que permiten administrar su suscripción, vea [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).
- Para conocer las características del Portal que permiten administrar la suscripción, vea [Uso del Portal de Azure para implementar y administrar los recursos de Azure](../azure-portal/resource-group-portal.md).

<!---HONumber=AcomDC_0713_2016-->