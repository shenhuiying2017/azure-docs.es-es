<properties 
	pageTitle="Bloqueo de recursos con el Administrador de recursos de Azure" 
	description="Bloquee los recursos para impedir que los usuarios actualicen o eliminen determinados recursos." 
	services="azure-resource-manager" 
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
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>

# Bloqueo de recursos con el Administrador de recursos de Azure

Como administrador, hay escenarios donde deseará colocar un bloqueo en un recurso o en un grupo de recursos para impedir que otros usuarios de su organización realicen acciones de escritura o de eliminación accidental de un recurso crítico.

El Administrador de recursos de Azure proporciona la capacidad para restringir las operaciones en los recursos a través de bloqueos de administración de recursos. Los bloqueos de recursos son directivas que aplican un nivel de bloqueo en un ámbito determinado. El nivel de bloqueo identifica el tipo de cumplimiento para la directiva, que actualmente tiene dos valores: **CanNotDelete** y **ReadOnly**. El ámbito se expresa como un URI y puede ser un recurso o un grupo de recursos.

Los bloqueos son diferentes de la asignación de permisos de usuario para realizar determinadas acciones. Para obtener información sobre cómo establecer permisos para usuarios y roles, vea [Control de acceso basado en rol en el Portal de vista previa](role-based-access-control-configure.md) y [Administración y auditoría del acceso a los recursos](resource-group-rbac.md).

## Escenarios comunes

Un escenario común es cuando tiene un grupo de recursos con algunos recursos que se usa en un patrón de activación y desactivación. Los recursos de máquinas virtuales se activan periódicamente para procesar los datos para un intervalo de tiempo determinado y, luego, se desactivan. En este escenario, querrá habilitar el apagado de las máquinas virtuales, pero es fundamental que no se elimine una cuenta de almacenamiento. En este escenario, se utilizaría un bloqueo de recurso con un nivel de bloqueo **CanNotDelete** en la cuenta de almacenamiento.

En otro escenario, su empresa puede tener períodos en los que no desea que las actualizaciones pasen a la fase de producción. El nivel de bloqueo **ReadOnly** detiene la creación o las actualizaciones. Si es una compañía de venta directa, es posible que no desee permitir las actualizaciones durante los períodos de compra de vacaciones. Si es una empresa de servicios financieros, probablemente tenga restricciones relacionadas con las implementaciones durante ciertas horas del mercado. Un bloqueo de recurso puede proporcionar una directiva para bloquear los recursos según corresponda. Esto puede aplicarse a determinados recursos o a la totalidad del grupo de recursos.

## Creación de un bloqueo en una plantilla

El ejemplo siguiente muestra una plantilla que crea un bloqueo en una cuenta de almacenamiento. La cuenta de almacenamiento en la que se va a aplicar el bloqueo se proporciona como un parámetro que se utiliza junto con la función concat(). El resultado es el nombre del recurso anexado 'Microsoft.Authorization' y, luego, un nombre de bloqueo, en este caso **myLock**.

El tipo proporcionado es específico del tipo de recurso. Para el almacenamiento, este tipo es "Microsoft.Storage/storageaccounts/providers/locks".

El nivel de ámbito se establece mediante la propiedad **level** del recurso. Como el ejemplo se centra en ayudar a evitar la eliminación accidental, el nivel se establece como **CannotDelete**.

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

Puede bloquear los recursos implementados con la [API de REST de bloqueos de administración](https://msdn.microsoft.com/library/azure/mt204563.aspx). La API de REST le permite crear y eliminar bloqueos, y recuperar información acerca de los bloqueos existentes.

Para crear un bloqueo, ejecute:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

El ámbito puede ser una suscripción, un grupo de recursos o un recurso. El nombre del bloqueo es el nombre con el que desee llamar al bloqueo. Para la versión de la API, use **2015-01-01**.

En la solicitud, incluya un objeto JSON que especifique las propiedades para el bloqueo.

    {
        "properties": {
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

Para el nivel de bloqueo, especifique **CanNotDelete** o **ReadOnly**.

Para obtener ejemplos, vea [API de REST de bloqueos de administración](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## Creación de un bloqueo con Azure PowerShell

Puede bloquear los recursos implementados con Azure PowerShell con **New-AzureResourceLock** como se muestra a continuación.

    PS C:\> New-AzureResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName ExampleGroup

PowerShell proporciona otros comandos para bloqueos de trabajo, como **AzureResourceLock Set** para actualizar un bloqueo y **Remove-AzureResourceLock** para eliminar un bloqueo.

## Pasos siguientes

- [Uso de etiquetas para organizar los recursos de Azure](resource-group-using-tags.md)
- [Traslado de los recursos a un nuevo grupo de recursos](resource-group-move-resources.md)

<!---HONumber=August15_HO6-->