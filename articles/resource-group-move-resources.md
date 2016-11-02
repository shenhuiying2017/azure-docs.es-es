<properties 
    pageTitle="Traslado de recursos a un nuevo grupo de recursos | Microsoft Azure" 
    description="Use Azure Resource Manager para trasladar recursos a un nuevo grupo de recursos o a una nueva suscripción." 
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
    ms.date="10/11/2016" 
    ms.author="tomfitz"/>


# <a name="move-resources-to-new-resource-group-or-subscription"></a>Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción

En este tema se muestra cómo trasladar recursos a una nueva suscripción o un grupo de recursos en la misma suscripción. Cuando realiza el traslado a una nueva suscripción, la suscripción debe estar dentro del mismo [inquilino de Active Directory](./active-directory/active-directory-howto-tenant.md) como la suscripción original. Puede usar el portal, PowerShell, la CLI de Azure o la API de REST para trasladar recursos. Las operaciones de movimiento de este tema están disponibles sin ayuda del soporte técnico de Azure.

Normalmente, necesitará trasladar recursos cuando decida que:

- Para fines de facturación, un recurso debe residir en una suscripción diferente.
- Un recurso ya no comparte el mismo ciclo de vida que los recursos con el que estaba agrupado anteriormente. Desea trasladarlo a un nuevo grupo de recursos para administrar ese recurso independientemente de los otros recursos.

Al mover los recursos, el grupo de origen y el grupo de destino se bloquean durante la operación. Las operaciones de escritura y eliminación están bloqueadas en los grupos hasta que se completa el movimiento.

No puede cambiar la ubicación del recurso. Si se mueve un recurso, solo se mueve a un nuevo grupo de recursos. El nuevo grupo de recursos puede tener una ubicación diferente, pero no cambia la ubicación del recurso.

> [AZURE.NOTE] En este artículo se describe cómo mover los recursos de una oferta de cuenta de Azure. Si realmente desea cambiar la oferta de cuenta de Azure (por ejemplo, actualizar de pago por uso a prepago) sin dejar de trabajar con los recursos existentes, consulte [Cambio a otra oferta de Azure](billing-how-to-switch-azure-offer.md). 

## <a name="checklist-before-moving-resources"></a>Lista de comprobación antes de mover recursos

Hay algunos pasos importantes que deben realizarse antes de mover un recurso. Puede evitar errores mediante la comprobación de estas condiciones.

1. El servicio debe permitir la capacidad de traslado de recursos. Consulte la siguiente lista para obtener más información sobre [los servicios que permiten el traslado de recursos](#services-that-enable-move).
2. La suscripción de destino correspondiente al proveedor de recursos del recurso que se traslada debe estar registrada. Si no es así, recibirá un error en el que se indicará que la **suscripción no está registrada para un tipo de recurso**. Podría encontrar este problema al mover un recurso a una nueva suscripción que nunca se ha utilizado el suscripción con ese tipo de recurso. Para obtener más información sobre cómo comprobar el estado de registro y registrar proveedores de recursos, consulte [Tipos y proveedores de recursos](../resource-manager-supported-services.md#resource-providers-and-types).
4. Si traslada la aplicación del Servicio de aplicaciones, tiene que revisar las [limitaciones del Servicio de aplicaciones](#app-service-limitations).
4. Si va a trasladar recursos asociados con Recovery Services, revise las [Limitaciones de Recovery Services](#recovery-services-limitations).
5. Si traslada recursos implementados mediante el modelo clásico, tiene que revisar las [limitaciones de la implementación clásica](#classic-deployment-limitations).

## <a name="when-to-call-support"></a>Al llamar al soporte técnico

Puede trasladar la mayoría de los recursos a través de las operaciones de autoservicio que se muestran en este tema. Utilice las operaciones de autoservicio para:

- Trasladar recursos de Resource Manager.
- Trasladar recursos clásicos conforme a las [limitaciones de implementación clásica](#classic-deployment-limitations). 

Llame a soporte técnico cuando necesite:

- Trasladar los recursos a una nueva cuenta de Azure (y el inquilino de Active Directory).
- Trasladar recursos clásicos, pero que tienen problemas con las limitaciones.

## <a name="services-that-enable-move"></a>Servicios que permiten el traslado

Por ahora, los servicios que permiten el traslado a un nuevo grupo de recursos y a una nueva suscripción son:

- Administración de API
- Aplicaciones del Servicio de aplicaciones (aplicaciones web) - consulte las [limitaciones del Servicio de aplicaciones](#app-service-limitations)
- Automatización
- Lote
- Servicio CDN
- Servicios en la nube (consulte las [limitaciones de la implementación clásica](#classic-deployment-limitations)
- Factoría de datos
- DNS
- DocumentDB
- Clústeres de HDInsight
- IoT Hubs
- Almacén de claves
- Servicios multimedia
- Mobile Engagement
- Centros de notificaciones
- Visión operativa
- Caché en Redis
- Programador
- Search
- Bus de servicio
- Almacenamiento
- Almacenamiento (clásico); consulte las [limitaciones de la implementación clásica](#classic-deployment-limitations)
- Servidor de base de datos SQL: la base de datos y el servidor deben residir en el mismo grupo de recursos. Cuando se mueve un servidor SQL Server, se mueven también todas sus bases de datos.
- Máquinas virtuales
- Máquinas virtuales (clásico); consulte las [limitaciones de la implementación clásica](#classic-deployment-limitations)
- Redes virtuales

## <a name="services-that-do-not-enable-move"></a>Servicios que no permiten el traslado

Los servicios que actualmente no permiten trasladar un recurso son:

- Puerta de enlace de aplicaciones
- Application Insights
- ExpressRoute
- Almacén de Servicios de recuperación: no mueva tampoco los recursos de Compute, Network y Storage asociados con el almacén de Servicios de recuperación, vea [Limitaciones de Recovery Services](#recovery-services-limitations).
- Conjuntos de escalado de máquinas virtuales
- Redes virtuales (clásico); consulte las [limitaciones de la implementación clásica](#classic-deployment-limitations)
- Puerta de enlace de VPN

## <a name="app-service-limitations"></a>limitaciones del Servicio de aplicaciones

Si se trabaja con aplicaciones del Servicio de aplicaciones, no se puede mover solo un plan del Servicio de aplicaciones. Para mover las aplicaciones del Servicio de aplicaciones, las opciones son:

- Trasladar el plan del Servicio de aplicaciones y el resto de recursos del Servicio de aplicaciones de ese grupo de recursos a un nuevo grupo que aún no tenga recursos del Servicio de aplicaciones. Este requisito significa que debe trasladar incluso los recursos del Servicio de aplicaciones que no estén asociados al plan de App Service. 
- Mover las aplicaciones a un grupo de recursos distinto, pero mantener todos los planes del Servicio de aplicaciones del grupo de recursos original.

Si el grupo de recursos original también incluye un recurso de Application Insights, no podrá mover ese recurso porque Application Insights no admite actualmente la operación de traslado. Si se incluye el recurso de Application Insights al mover las aplicaciones de App Service, se producirá un error en toda la operación de traslado. Sin embargo, no es necesario que el plan del Servicio de aplicaciones y Application Insights residan en el mismo grupo de recursos que la aplicación para que esta funcione correctamente.

Por ejemplo, si el grupo de recursos contiene:

- **web-a**, asociado a **plan-a** y **app-insights-a**
- **web-b**, asociado a **plan-b** y **app-insights-b**

Tendrá las siguientes opciones:

- Trasladar **web-a**, **plan-a**, **web-b** y **plan-b**
- Trasladar **web-a** y **web-b**
- Mover **web-a**
- Mover **web-b**

Todas las demás combinaciones implican el traslado de un tipo de recurso que no se puede mover (Application Insights) o se dejan un tipo de recurso que debe trasladarse al mover un plan del Servicio de aplicaciones (cualquier tipo de recurso del Servicio de aplicaciones).

Si la aplicación web se encuentra en un grupo de recursos distinto al de su plan del Servicio de aplicaciones, pero desea mover ambos a un nuevo grupo de recursos, debe realizar el traslado en dos pasos. Por ejemplo:

- **web-a** reside en **web-group**.
- **plan-a** reside en **plan-group**.
- Desea que **web-a** y **plan-a** residan en **combined-group**.

Para realizar este movimiento, debe llevar a cabo dos operaciones de traslado distintas en el siguiente orden:

1. Trasladar **web-a** a **plan-group**.
2. Trasladar **web-a** y **plan-a** a **combined-group**.

Actualmente, si la aplicación web incluye un certificado SSL que adquirió externamente y que cargó en la aplicación, debe eliminar el certificado antes de trasladarlo a la aplicación web. Por ejemplo, puede realizar los pasos siguientes:

1. Eliminar el certificado cargado desde la aplicación web
2. Trasladar la aplicación web
3. Cargar el certificado a la aplicación web

Si su aplicación web utiliza un App Service Certificate, puede trasladar la aplicación web y el certificado a un nuevo grupo de recursos sin ningún problema. Para mover un App Service Certificate a una nueva suscripción, póngase en contacto con el soporte técnico de Azure.

## <a name="recovery-services-limitations"></a>Limitaciones de Recovery Services

No se admite el traslado para recursos de Storage, Network o Compute que se usan para configurar la recuperación ante desastres de Azure Site Recovery. 

Por ejemplo, suponga que ha configurado la replicación de las máquinas locales en una cuenta de almacenamiento (Storage1) y desea que la máquina protegida aparezca después de la conmutación por error en Azure como una máquina virtual (VM1) conectada a una red virtual (Network1). No puede mover ninguno de estos recursos de Azure, Storage1, VM1 y Network1, en grupos de recursos dentro de la misma suscripción o entre suscripciones.

## <a name="classic-deployment-limitations"></a>limitaciones de la implementación clásica

Las opciones para mover recursos implementados mediante el modelo clásico varían en función de si traslada los recursos dentro de una misma suscripción o a una nueva suscripción. 

### <a name="same-subscription"></a>Misma suscripción

Al mover recursos de un grupo de recursos a otro dentro de la misma suscripción, se aplican las restricciones siguientes:

- No se pueden mover redes virtuales (clásico).
- Las máquinas virtuales (clásico) se deben mover con el servicio en la nube. 
- El servicio en la nube solo se puede mover cuando el traslado incluye todas sus máquinas virtuales.
- Solo se puede mover un servicio en la nube cada vez.
- Solo se puede mover una cuenta de almacenamiento (clásico) cada vez.
- No se puede mover una cuenta de almacenamiento (clásico) con una máquina virtual o un servicio en la nube en la misma operación de traslado.

Para trasladar recursos clásicos a un grupo de recursos nuevo dentro de la misma suscripción, utilice las operaciones de traslado estándar a través del [portal](#use-portal), [Azure PowerShell](#use-powershell), la [CLI de Azure](#use-azure-cli) o la [API de REST](#use-rest-api). Utilice las mismas operaciones que utiliza para trasladar recursos de Resource Manager.

### <a name="new-subscription"></a>Suscripción nueva

Al trasladar recursos a una nueva suscripción, se aplican las restricciones siguientes:

- Todos los recursos clásicos de la suscripción se deben mover en la misma operación.
- La suscripción de destino no debe contener otros recursos clásicos.
- El traslado solo puede solicitarse a través de una API de REST independiente para el traslado de recursos clásicos. Los comandos de movimiento estándar de Resource Manager no funcionan para mover recursos clásicos a una nueva suscripción.

Para trasladar recursos clásicos a una nueva suscripción, debe usar operaciones REST específicas para recursos clásicos. Realice los pasos siguientes para trasladar los recursos clásicos a una nueva suscripción.

1. Compruebe si la suscripción de origen puede participar en un movimiento entre suscripciones. Utilice la siguiente operación:

         POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
     En el cuerpo de la solicitud, incluya:

         {
           "role": "source"
         }

     La respuesta para la operación de validación está en el formato siguiente:

         {
           "status": "{status}",
           "reasons": [
             "reason1",
             "reason2"
           ]
         }

2. Compruebe si la suscripción de destino puede participar en un movimiento entre suscripciones. Utilice la siguiente operación:

         POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01

     En el cuerpo de la solicitud, incluya:

         {
           "role": "target"
         }

     La respuesta está en el mismo formato que la validación de la suscripción de origen.

3. Si ambas suscripciones superan la validación, traslade todos los recursos clásicos de una suscripción a otra con la siguiente operación:

         POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

    En el cuerpo de la solicitud, incluya:

         {
           "target": "/subscriptions/{target-subscription-id}"
         }

Es posible que esta operación tarde varios minutos. 

## <a name="use-portal"></a>Mediante el portal

Para trasladar recursos a un nuevo grupo de recursos en la **misma suscripción**, seleccione el grupo de recursos que contiene esos recursos y, a continuación, seleccione el botón **Mover**.

![Mover recursos](./media/resource-group-move-resources/edit-rg-icon.png)

Para trasladar recursos a una **nueva suscripción**, seleccione el grupo de recursos que contenga esos recursos y, a continuación, seleccione el icono de edición de la suscripción.

![Mover recursos](./media/resource-group-move-resources/change-subscription.png)

Seleccione los recursos que trasladar y el grupo de recursos de destino. Confirme que tiene que actualizar los scripts para estos recursos y seleccione **Aceptar**. Si ha seleccionado el icono de edición de la suscripción en el paso anterior, también debe seleccionar la suscripción de destino.

![seleccionar destino](./media/resource-group-move-resources/select-destination.png)

En **Notificaciones**, podrá ver que la operación de traslado está en curso.

![mostrar el estado del traslado](./media/resource-group-move-resources/show-status.png)

Cuando haya finalizado, se le notificará del resultado.

![mostrar el resultado del traslado](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Uso de PowerShell

Para mover recursos existentes a otro grupo de recursos o a otra suscripción, use el comando **Move-AzureRmResource** .

El primer ejemplo muestra cómo trasladar un recurso a un nuevo grupo de recursos.

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

El segundo ejemplo muestra cómo trasladar varios recursos a un nuevo grupo de recursos.

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Para moverlos a una nueva suscripción, especifique un valor para el parámetro **DestinationSubscriptionId** .

Se le pedirá que confirme que quiere mover los recursos especificados.

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## <a name="use-azure-cli"></a>Uso de CLI de Azure

Para trasladar recursos existentes a otro grupo de recursos o a otra suscripción, use el comando **azure resource move**. Debe proporcionar los identificadores de recursos de los recursos que trasladar. Puede obtener los identificadores de recursos con el siguiente comando:

    azure resource list -g sourceGroup --json

Que devuelve el siguiente formato:

    [
      {
        "id": "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo",
        "name": "storagedemo",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "southcentralus",
        "tags": {},
        "kind": "Storage",
        "sku": {
          "name": "Standard_RAGRS",
          "tier": "Standard"
        }
      }
    ]

En el siguiente ejemplo se muestra cómo trasladar una cuenta de almacenamiento a un nuevo grupo de recursos. En el parámetro **-i**, ofrezca una lista separada por comas del identificador de recurso que se va a trasladar.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
    
Se le pedirá que confirme que quiere mover el recurso especificado.

## <a name="use-rest-api"></a>Use la API de REST

Para trasladar recursos existentes a otro grupo de recursos o a una suscripción, ejecute:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

En el cuerpo de la solicitud, especifique el grupo de recursos de destino y los recursos a mover. Para obtener más información acerca de la operación REST de movimiento, consulte [Mover recursos](https://msdn.microsoft.com/library/azure/mt218710.aspx).


## <a name="next-steps"></a>Pasos siguientes
- Para obtener información sobre los cmdlets de PowerShell que permiten administrar su suscripción, vea [Uso de Azure PowerShell con Azure Resource Manager](powershell-azure-resource-manager.md).
- Para obtener información sobre los comandos de la CLI de Azure para administrar su suscripción, vea [Uso de la CLI de Azure para Mac, Linux y Windows con Azure Resource Manager](xplat-cli-azure-resource-manager.md).
- Si desea conocer las funciones del portal que permiten administrar la suscripción, consulte [Uso del Azure Portal para implementar y administrar los recursos de Azure](./azure-portal/resource-group-portal.md).
- Para aprender a aplicar una organización lógica a los recursos, consulte [Uso de etiquetas para organizar los recursos de Azure](resource-group-using-tags.md).



<!--HONumber=Oct16_HO2-->


