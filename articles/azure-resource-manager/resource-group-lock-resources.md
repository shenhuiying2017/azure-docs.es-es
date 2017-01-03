---
title: Bloqueo de recursos con Resource Manager | Microsoft Docs
description: "Impida que los usuarios actualicen o eliminen determinados recursos al aplicar una restricción a todos los usuarios y roles."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: fd22c8e0a02a3d0b1286d51d6506a82de2296574


---
# <a name="lock-resources-with-azure-resource-manager"></a>Bloqueo de recursos con el Administrador de recursos de Azure
Como administrador, puede que tenga que bloquear una suscripción, un grupo de recursos o un recurso para impedir que otros usuarios de su organización eliminen o modifiquen accidentalmente recursos esenciales. Puede establecer el bloqueo de nivel en **CanNotDelete** o **ReadOnly**. 

* **CanNotDelete** significa que los usuarios autorizados pueden leer y modificar recursos, pero no eliminarlos. 
* **ReadOnly** significa que los usuarios autorizados solo pueden leer recursos, pero no realizar acciones en ellos ni eliminarlos. El permiso en el recurso está restringido por el rol **Reader** . 

Si aplica **ReadOnly** , pueden producirse resultados inesperados, ya que algunas operaciones que parecen ser similares a operaciones de lectura realmente requieren acciones adicionales. Por ejemplo, al colocar un bloqueo **ReadOnly** en una cuenta de almacenamiento, evita que se muestren las claves a todos los usuarios. La operación de visualización claves se administra mediante solicitudes POST debido a que las claves devueltas están disponibles para las operaciones de escritura. Otro ejemplo: al colocar un bloqueo **ReadOnly** en un recurso de servicio del Servicio de aplicaciones, evita que el Explorador de servidores de Visual Studio muestre los archivos del recurso, ya que esa interacción requiere acceso de escritura.

Al diferencia del control de acceso basado en rol, los bloqueos de administración se usan para aplicar una restricción a todos los usuarios y roles. Para obtener información sobre cómo establecer permisos para usuarios y roles, vea [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md).

Cuando se aplica un bloqueo en un ámbito primario, todos los recursos secundarios heredan el mismo bloqueo. Incluso los recursos que agregue posteriormente heredan el bloqueo del elemento primario. El bloqueo más restrictivo de toda la herencia tiene prioridad.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Quién puede crear o eliminar bloqueos en su organización
Para crear o eliminar bloqueos de administración, debe tener acceso a las acciones **Microsoft.Authorization/\*** o **Microsoft.Authorization/locks/\***. Entre los roles integrados, solamente se conceden esas acciones al **propietario** y al **administrador de acceso de usuarios**.

## <a name="creating-a-lock-through-the-portal"></a>Creación de un bloqueo a través del portal
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="creating-a-lock-in-a-template"></a>Creación de un bloqueo en una plantilla
El ejemplo siguiente muestra una plantilla que crea un bloqueo en una cuenta de almacenamiento. La cuenta de almacenamiento en la que se va a aplicar el bloqueo se proporciona como un parámetro. El nombre del bloqueo se crea mediante la concatenación del nombre del recurso con **/Microsoft.Authorization/** y el propio nombre del bloqueo (en este caso, **myLock**).

El tipo proporcionado es específico del tipo de recurso. Para el almacenamiento, establezca este tipo en "Microsoft.Storage/storageaccounts/providers/locks".

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="creating-a-lock-with-rest-api"></a>Creación de un bloqueo con la API de REST
Puede bloquear los recursos implementados con la [API de REST para bloqueos de administración](https://docs.microsoft.com/rest/api/resources/managementlocks). La API de REST le permite crear y eliminar bloqueos, y recuperar información acerca de los bloqueos existentes.

Para crear un bloqueo, ejecute:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

El ámbito puede ser una suscripción, un grupo de recursos o un recurso. El nombre del bloqueo es el nombre con el que desee llamar al bloqueo. Como versión de la API, use **2015-01-01**.

En la solicitud, incluya un objeto JSON que especifique las propiedades para el bloqueo.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 


## <a name="creating-a-lock-with-azure-powershell"></a>Creación de un bloqueo con Azure PowerShell
Puede bloquear los recursos implementados con Azure PowerShell mediante **New-AzureRmResourceLock** , como se muestra en el ejemplo siguiente.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup

Azure PowerShell ofrece otros comandos para bloqueos de trabajo, como **Set-AzureRmResourceLock** para actualizar un bloqueo y **Remove-AzureRmResourceLock** para eliminarlo.

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre cómo trabajar con bloqueos de recursos, consulte [Bloqueo de los recursos de Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
* Para aprender a organizar de manera lógica los recursos, vea [Uso de etiquetas para organizar sus recursos](resource-group-using-tags.md)
* Para cambiar el grupo de recursos en que reside un recurso, vea [Traslado de los recursos a un nuevo grupo de recursos](resource-group-move-resources.md)
* Puede aplicar restricciones y convenciones a través de su suscripción con directivas personalizadas. Para más información, vea [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).




<!--HONumber=Nov16_HO3-->


