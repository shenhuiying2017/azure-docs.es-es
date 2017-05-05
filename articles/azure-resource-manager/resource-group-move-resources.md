---
title: "Traslado de recursos de Azure a una nueva suscripción o grupo de recursos | Microsoft Docs"
description: "Use Azure Resource Manager para trasladar recursos a un nuevo grupo de recursos o a una nueva suscripción."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 626b152b8511995413af39a41161c29c88429605
ms.lasthandoff: 04/21/2017


---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción
En este tema se muestra cómo trasladar recursos a una nueva suscripción o un grupo de recursos en la misma suscripción. Puede usar el portal, PowerShell, la CLI de Azure o la API de REST para trasladar recursos. Las operaciones de movimiento de este tema están disponibles sin ayuda del soporte técnico de Azure.

Al mover los recursos, el grupo de origen y el grupo de destino se bloquean durante la operación. Las operaciones de escritura y eliminación están bloqueadas en los grupos de recursos hasta que se completa el movimiento. Este bloqueo significa que no puede agregar, actualizar ni eliminar recursos de los grupos de recursos, pero no que los recursos queden bloqueados. Por ejemplo, si mueve un servidor SQL Server y su base de datos a un nuevo grupo de recursos, una aplicación que utiliza la base de datos no experimenta ningún tiempo de inactividad. Todavía puede leer y escribir en la base de datos. 

No puede cambiar la ubicación del recurso. Si se mueve un recurso, solo se mueve a un nuevo grupo de recursos. El nuevo grupo de recursos puede tener una ubicación diferente, pero no cambia la ubicación del recurso.

> [!NOTE]
> En este artículo se describe cómo mover los recursos de una oferta de cuenta de Azure. Si realmente desea cambiar la oferta de cuenta de Azure (por ejemplo, actualizar de pago por uso a prepago) sin dejar de trabajar con los recursos existentes, consulte [Cambio a otra oferta de Azure](../billing/billing-how-to-switch-azure-offer.md). 
> 
> 

## <a name="checklist-before-moving-resources"></a>Lista de comprobación antes de mover recursos
Hay algunos pasos importantes que deben realizarse antes de mover un recurso. Puede evitar errores mediante la comprobación de estas condiciones.

1. Las suscripciones de origen y destino deben existir en el mismo [inquilino de Azure Active Directory](../active-directory/active-directory-howto-tenant.md). Para comprobar que ambas suscripciones tienen el mismo identificador de inquilino, utilice Azure PowerShell o la CLI de Azure.

  Para Azure PowerShell, use:

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName "Example Subscription").TenantId
  ```

  Para la CLI de Azure 2.0, use:

  ```azurecli
  az account show --subscription "Example Subscription" --query tenantId
  ```

  Si los identificadores de inquilino para las suscripciones de origen y destino no son los mismos, puede intentar cambiar el directorio de la suscripción. Sin embargo, esta opción solo está disponible para los administradores de servicios que han iniciado sesión con una cuenta de Microsoft (no una cuenta de organización). Para tratar de cambiar el directorio, inicie sesión en el [portal clásico](https://manage.windowsazure.com/) y seleccione **Configuración** y, después, la suscripción. Si el icono **Editar directorio** está disponible, selecciónelo para cambiar el entorno de Azure Active Directory asociado. 

  ![editar directorio](./media/resource-group-move-resources/edit-directory.png) 

  Si este icono no está disponible, debe ponerse en contacto con el soporte técnico para mover los recursos a un nuevo inquilino.

2. El servicio debe permitir la capacidad de traslado de recursos. Este tema enumeran los servicios que permiten mover recursos y los servicios que no permiten el traslado de recursos.
3. La suscripción de destino correspondiente al proveedor de recursos del recurso que se traslada debe estar registrada. Si no es así, recibirá un error en el que se indicará que la **suscripción no está registrada para un tipo de recurso**. Podría encontrar este problema al mover un recurso a una nueva suscripción que nunca se ha utilizado el suscripción con ese tipo de recurso. Para obtener más información sobre cómo comprobar el estado de registro y registrar proveedores de recursos, consulte [Tipos y proveedores de recursos](resource-manager-supported-services.md#resource-providers-and-types).

## <a name="when-to-call-support"></a>Al llamar al soporte técnico
Puede trasladar la mayoría de los recursos a través de las operaciones de autoservicio que se muestran en este tema. Utilice las operaciones de autoservicio para:

* Trasladar recursos de Resource Manager.
* Trasladar recursos clásicos conforme a las [limitaciones de implementación clásica](#classic-deployment-limitations). 

Llame a soporte técnico cuando necesite:

* Mueva los recursos a una nueva cuenta de Azure (y el inquilino de Azure Active Directory).
* Trasladar recursos clásicos, pero que tienen problemas con las limitaciones.

## <a name="services-that-enable-move"></a>Servicios que permiten el traslado
Por ahora, los servicios que permiten el traslado a un nuevo grupo de recursos y a una nueva suscripción son:

* API Management
* App Service apps (Web Apps) - consulte las [limitaciones de App Service](#app-service-limitations)
* Automation
* Batch
* Mapas de Bing
* CDN
* Cloud Services (consulte las [limitaciones de la implementación clásica](#classic-deployment-limitations)
* Cognitive Services
* Content Moderator
* Data Catalog
* Data Factory
* Data Lake Analytics
* Almacén de Data Lake
* DNS
* DocumentDB
* Event Hubs
* Clústeres de HDInsight: consulte [Limitaciones de HDInsight](#hdinsight-limitations).
* IoT Hubs
* Key Vault 
* Equilibradores de carga
* Logic Apps
* Machine Learning
* Media Services
* Mobile Engagement
* Notification Hubs
* Operational Insights
* Operations Management
* Power BI
* Redis Cache
* Scheduler
* Search
* Servidor de administración
* Service Bus
* Service Fabric
* Storage
* Storage (clásico); consulte las [limitaciones de la implementación clásica](#classic-deployment-limitations)
* Stream Analytics
* Servidor de SQL Database: la base de datos y el servidor deben residir en el mismo grupo de recursos. Cuando se mueve un servidor SQL Server, se mueven también todas sus bases de datos.
* Traffic Manager
* Virtual Machines: no permite trasladarse a una nueva suscripción cuando sus certificados se almacenan en una instancia de Key Vault
* Virtual Machines (clásico); consulte las [limitaciones de la implementación clásica](#classic-deployment-limitations)
* Conjuntos de escalado de máquina virtual
* Virtual Networks: por el momento, no se puede mover una red virtual emparejada hasta que el emparejamiento de la red virtual se haya inhabilitado. Una vez deshabilitada, se podrá mover correctamente la Virtual Network y habilitar el emparejamiento de VNet.
* VPN Gateway 

 
## <a name="services-that-do-not-enable-move"></a>Servicios que no permiten el traslado
Los servicios que actualmente no permiten trasladar un recurso son:

* Servicio de mantenimiento híbrido de AD
* Application Gateway
* Application Insights
* BizTalk Services
* Container Service
* ExpressRoute
* DevTest Labs: el traslado al nuevo grupo de recursos en la misma suscripción está habilitado pero no el traslado de suscripción cruzado.
* Dynamics LCS
* Almacén de Recovery Services: no mueva tampoco los recursos de Compute, Network y Storage asociados con el almacén de Recovery Services, vea [Limitaciones de Recovery Services](#recovery-services-limitations).
* Seguridad
* Virtual Machines con certificados almacenados en Key Vault
* Virtual Machines con discos administrados
* Conjuntos de disponibilidad con Virtual Machines con discos administrados
* Managed Disks
* Imágenes creadas a partir de discos administrados
* Instantáneas creadas a partir de discos administrados
* Virtual Networks (clásico); consulte las [limitaciones de la implementación clásica](#classic-deployment-limitations)
* Virtual Machines creadas a partir de recursos de Marketplace (no se pueden mover entre suscripciones). Es necesario desaprovisionar el recurso en la suscripción activa y volver a implementarlo en la nueva suscripción

## <a name="app-service-limitations"></a>Limitaciones de App Service
Si se trabaja con aplicaciones de App Service, no se puede mover solo un plan de App Service. Para mover las aplicaciones de App Service, las opciones son:

* Trasladar el plan de App Service y el resto de recursos de App Service de ese grupo de recursos a un nuevo grupo que aún no tenga recursos de App Service. Este requisito significa que debe trasladar incluso los recursos de App Service que no estén asociados al plan de App Service. 
* Mover las aplicaciones a un grupo de recursos distinto, pero mantener todos los planes de App Service del grupo de recursos original.

Si el grupo de recursos original también incluye un recurso de Application Insights, no podrá mover ese recurso porque Application Insights no admite actualmente la operación de traslado. Si se incluye el recurso de Application Insights al mover las aplicaciones de App Service, se producirá un error en toda la operación de traslado. Sin embargo, no es necesario que el plan de App Service y Application Insights residan en el mismo grupo de recursos que la aplicación para que esta funcione correctamente.

Por ejemplo, si el grupo de recursos contiene:

* **web-a**, asociado a **plan-a** y **app-insights-a**
* **web-b**, asociado a **plan-b** y **app-insights-b**

Tendrá las siguientes opciones:

* Trasladar **web-a**, **plan-a**, **web-b** y **plan-b**
* Trasladar **web-a** y **web-b**
* Mover **web-a**
* Mover **web-b**

Todas las demás combinaciones implican el traslado de un tipo de recurso que no se puede mover (Application Insights) o se dejan un tipo de recurso que debe trasladarse al mover un plan de App Service (cualquier tipo de recurso de App Service).

Si la aplicación web se encuentra en un grupo de recursos distinto al de su plan de App Service, pero desea mover ambos a un nuevo grupo de recursos, debe realizar el traslado en dos pasos. Por ejemplo:

* **web-a** reside en **web-group**.
* **plan-a** reside en **plan-group**.
* Desea que **web-a** y **plan-a** residan en **combined-group**.

Para realizar este movimiento, debe llevar a cabo dos operaciones de traslado distintas en el siguiente orden:

1. Trasladar **web-a** a **plan-group**.
2. Trasladar **web-a** y **plan-a** a **combined-group**.

Puede mover una instancia de App Service Certificate a un nuevo grupo de recursos o a una nueva suscripción sin ningún problema. Sin embargo, si la aplicación web incluye un certificado SSL que adquirió externamente y que cargó en la aplicación, debe eliminar el certificado antes de trasladar la aplicación web. Por ejemplo, puede realizar los pasos siguientes:

1. Eliminar el certificado cargado desde la aplicación web
2. Trasladar la aplicación web
3. Cargar el certificado a la aplicación web

## <a name="recovery-services-limitations"></a>Limitaciones de Recovery Services
No se admite el traslado para recursos de Storage, Network o Compute que se usan para configurar la recuperación ante desastres de Azure Site Recovery. 

Por ejemplo, suponga que ha configurado la replicación de las máquinas locales en una cuenta de almacenamiento (Storage1) y desea que la máquina protegida aparezca después de la conmutación por error en Azure como una máquina virtual (VM1) conectada a una red virtual (Network1). No puede mover ninguno de estos recursos de Azure, Storage1, VM1 y Network1, en grupos de recursos dentro de la misma suscripción o entre suscripciones.

## <a name="hdinsight-limitations"></a>Limitaciones de HDInsight

Puede mover clústeres de HDInsight a una nueva suscripción o un nuevo grupo de recursos. Sin embargo, no puede mover entre suscripciones los recursos de red vinculados al clúster de HDInsight (por ejemplo, la red virtual, una NIC o un equilibrador de carga). Además, tampoco se puede mover a un nuevo grupo de recursos una NIC que está conectada a una máquina virtual del clúster.

Al mover un clúster de HDInsight a una nueva suscripción, mueva primero otros recursos (por ejemplo, la cuenta de almacenamiento). Después, mover el clúster de HDInsight.

## <a name="classic-deployment-limitations"></a>limitaciones de la implementación clásica
Las opciones para mover recursos implementados mediante el modelo clásico varían en función de si traslada los recursos dentro de una misma suscripción o a una nueva suscripción. 

### <a name="same-subscription"></a>Misma suscripción
Al mover recursos de un grupo de recursos a otro dentro de la misma suscripción, se aplican las restricciones siguientes:

* No se pueden mover redes virtuales (clásico).
* Las máquinas virtuales (clásico) se deben mover con el servicio en la nube. 
* El servicio en la nube solo se puede mover cuando el traslado incluye todas sus máquinas virtuales.
* Solo se puede mover un servicio en la nube cada vez.
* Solo se puede mover una cuenta de almacenamiento (clásico) cada vez.
* No se puede mover una cuenta de almacenamiento (clásico) con una máquina virtual o un servicio en la nube en la misma operación de traslado.

Para trasladar recursos clásicos a un grupo de recursos nuevo dentro de la misma suscripción, utilice las operaciones de traslado estándar a través del [portal](#use-portal), [Azure PowerShell](#use-powershell), la [CLI de Azure](#use-azure-cli) o la [API de REST](#use-rest-api). Utilice las mismas operaciones que utiliza para trasladar recursos de Resource Manager.

### <a name="new-subscription"></a>Suscripción nueva
Al trasladar recursos a una nueva suscripción, se aplican las restricciones siguientes:

* Todos los recursos clásicos de la suscripción se deben mover en la misma operación.
* La suscripción de destino no debe contener otros recursos clásicos.
* El traslado solo puede solicitarse a través de una API de REST independiente para el traslado de recursos clásicos. Los comandos de movimiento estándar de Resource Manager no funcionan para mover recursos clásicos a una nueva suscripción.

Para trasladar recursos clásicos a una nueva suscripción, use el portal u operaciones REST específicas para recursos clásicos. Para obtener información acerca de cómo mover los recursos clásicos a través del portal, consulte [Use portal](#use-portal) (Uso del portal). Para usar REST, siga estos pasos:

1. Compruebe si la suscripción de origen puede participar en un movimiento entre suscripciones. Utilice la siguiente operación:

  ```HTTP   
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```
   
     En el cuerpo de la solicitud, incluya:

  ```json 
  {
    "role": "source"
  }
  ```
  
     La respuesta para la operación de validación está en el formato siguiente:

  ```json 
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. Compruebe si la suscripción de destino puede participar en un movimiento entre suscripciones. Utilice la siguiente operación:

  ```HTTP 
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     En el cuerpo de la solicitud, incluya:

  ```json 
  {
    "role": "target"
  }
  ```
   
     La respuesta está en el mismo formato que la validación de la suscripción de origen.
3. Si ambas suscripciones superan la validación, traslade todos los recursos clásicos de una suscripción a otra con la siguiente operación:

  ```HTTP 
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    En el cuerpo de la solicitud, incluya:

  ```json 
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

Es posible que esta operación tarde varios minutos. 

## <a name="use-portal"></a>Mediante el portal
Para trasladar recursos, seleccione el grupo de recursos que contiene esos recursos y, después, el botón **Mover**.

![Mover recursos](./media/resource-group-move-resources/select-move.png)

Seleccione si va a mover los recursos a un nuevo grupo de recursos o a una nueva suscripción.

Seleccione los recursos que trasladar y el grupo de recursos de destino. Confirme que tiene que actualizar los scripts para estos recursos y seleccione **Aceptar**. Si ha seleccionado el icono de edición de la suscripción en el paso anterior, también debe seleccionar la suscripción de destino.

![seleccionar destino](./media/resource-group-move-resources/select-destination.png)

En **Notificaciones**, podrá ver que la operación de traslado está en curso.

![mostrar el estado del traslado](./media/resource-group-move-resources/show-status.png)

Cuando haya finalizado, se le notificará del resultado.

![mostrar el resultado del traslado](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Uso de PowerShell
Para trasladar recursos existentes a otro grupo de recursos o a una suscripción, use el comando `Move-AzureRmResource`.

El primer ejemplo muestra cómo trasladar un recurso a un nuevo grupo de recursos.

```powershell
$resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId
```

El segundo ejemplo muestra cómo trasladar varios recursos a un nuevo grupo de recursos.

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Para moverlos a una nueva suscripción, especifique un valor para el parámetro `DestinationSubscriptionId`.

Se le pedirá que confirme que quiere mover los recursos especificados.

```powershell
Confirm
Are you sure you want to move these resources to the resource group
'/subscriptions/{guid}/resourceGroups/newRG' the resources:

/subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
/subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
```

## <a name="use-azure-cli-20"></a>Uso de la CLI de Azure 2.0
Para trasladar recursos existentes a otro grupo de recursos o a una suscripción, use el comando `az resource move`. Proporcione los identificadores de recursos de los recursos que se van a mover. Puede obtener los identificadores de recurso con el siguiente comando:

```azurecli
az resource show -g sourceGroup -n storagedemo --resource-type "Microsoft.Storage/storageAccounts" --query id
```

En el siguiente ejemplo se muestra cómo trasladar una cuenta de almacenamiento a un nuevo grupo de recursos. En el parámetro `--ids`, ofrezca una lista separada por espacios de los identificadores de recurso que se van a trasladar.

```azurecli
az resource move --destination-group newgroup --ids "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo"
```

Para mover a una nueva suscripción, proporcione el parámetro `--destination-subscription-id`.

## <a name="use-azure-cli-10"></a>Uso de CLI de Azure 1.0
Para trasladar recursos existentes a otro grupo de recursos o a una suscripción, use el comando `azure resource move`. Proporcione los identificadores de recursos de los recursos que se van a mover. Puede obtener los identificadores de recurso con el siguiente comando:

```azurecli
azure resource list -g sourceGroup --json
```

Que devuelve el siguiente formato:

```azurecli
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
```

En el siguiente ejemplo se muestra cómo trasladar una cuenta de almacenamiento a un nuevo grupo de recursos. En el parámetro `-i`, ofrezca una lista separada por comas de los identificadores de recurso que se van a trasladar.

```azurecli
azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
```

Se le pedirá que confirme que quiere mover el recurso especificado.

## <a name="use-rest-api"></a>Use la API de REST
Para trasladar recursos existentes a otro grupo de recursos o a una suscripción, ejecute:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 
```

En el cuerpo de la solicitud, especifique el grupo de recursos de destino y los recursos a mover. Para obtener más información acerca de la operación REST de movimiento, consulte [Mover recursos](https://msdn.microsoft.com/library/azure/mt218710.aspx).

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información sobre los cmdlets de PowerShell que permiten administrar su suscripción, vea [Uso de Azure PowerShell con Azure Resource Manager](powershell-azure-resource-manager.md).
* Para obtener información sobre los comandos de la CLI de Azure para administrar su suscripción, vea [Uso de la CLI de Azure para Mac, Linux y Windows con Azure Resource Manager](xplat-cli-azure-resource-manager.md).
* Si desea conocer las funciones del portal que permiten administrar la suscripción, consulte [Uso del Azure Portal para implementar y administrar los recursos de Azure](resource-group-portal.md).
* Para aprender a aplicar una organización lógica a los recursos, consulte [Uso de etiquetas para organizar los recursos de Azure](resource-group-using-tags.md).


