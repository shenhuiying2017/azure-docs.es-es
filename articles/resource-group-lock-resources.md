<properties 
	pageTitle="Bloqueo de recursos con el Administrador de recursos | Microsoft Azure" 
	description="Impida que los usuarios actualicen o eliminen determinados recursos al aplicar una restricción a todos los usuarios y roles." 
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
	ms.date="04/25/2016" 
	ms.author="tomfitz"/>

# Bloqueo de recursos con el Administrador de recursos de Azure

Como administrador, es posible que quiera bloquear una suscripción, un grupo de recursos o un recurso para impedir que otros usuarios de su organización eliminen accidentalmente un recurso esencial. Cuando se bloquean, los usuarios autorizados podrán seguir leyendo y modificando los recursos, pero no podrán eliminarlos.

A diferencia de control de acceso basado en rol, puede usar los bloqueos de administración para aplicar una restricción a través de todos los usuarios y roles. Para más información acerca de cómo establecer permisos para usuarios y roles, consulte [Control de acceso basado en rol de Azure](./active-directory/role-based-access-control-configure.md).

Cuando se aplica un bloqueo en un ámbito primario, todos los recursos secundarios heredan el mismo bloqueo.

## Quién puede crear o eliminar bloqueos en su organización

Para crear o eliminar bloqueos de administración, debe tener acceso a las acciones **Microsoft.Authorization/*** o **Microsoft.Authorization/locks/***. Entre los roles integrados, solamente se conceden esas acciones a **Propietario** y **Administrador de acceso de usuarios**.

## Creación de un bloqueo en una plantilla

El ejemplo siguiente muestra una plantilla que crea un bloqueo en una cuenta de almacenamiento. La cuenta de almacenamiento en la que se va a aplicar el bloqueo se proporciona como un parámetro. El nombre del bloqueo se crea concatenando el nombre del recurso con **/Microsoft.Authorization/** y el nombre del bloqueo, en este caso, **myLock**.

El tipo proporcionado es específico del tipo de recurso. Para el almacenamiento, este tipo es "Microsoft.Storage/storageaccounts/providers/locks".

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

## Creación de un bloqueo con la API de REST

Puede bloquear los recursos implementados con la [API de REST para bloqueos de administración](https://msdn.microsoft.com/library/azure/mt204563.aspx). La API de REST le permite crear y eliminar bloqueos, y recuperar información acerca de los bloqueos existentes.

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

Para ejemplos, vea la [API de REST para bloqueos de administración](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## Creación de un bloqueo con Azure PowerShell

Puede bloquear los recursos implementados con Azure PowerShell mediante **New-AzureRmResourceLock**, como se muestra a continuación.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites

Azure PowerShell ofrece otros comandos para bloqueos de trabajo, como **Set-AzureRmResourceLock** para actualizar un bloqueo y **Remove-AzureRmResourceLock** para eliminarlo.

## Pasos siguientes

- Para obtener más información sobre cómo trabajar con bloqueos de recursos, consulte [Bloqueo de los recursos de Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- Para aprender a organizar de manera lógica los recursos, vea [Uso de etiquetas para organizar sus recursos](resource-group-using-tags.md).
- Para cambiar el grupo de recursos en que reside un recurso, vea [Traslado de los recursos a un nuevo grupo de recursos](resource-group-move-resources.md).
- Puede aplicar restricciones y convenciones a través de su suscripción con directivas personalizadas. Para más información, vea [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).

<!---HONumber=AcomDC_0511_2016-->