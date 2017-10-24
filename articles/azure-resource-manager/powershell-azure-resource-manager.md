---
title: "Administración de soluciones de Azure con PowerShell | Microsoft Docs"
description: Uso de Azure PowerShell y Resource Manager para administrar recursos.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: ae5ccb83a0088cb7c9668f18620b74f9f3f1e9b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-resources-with-azure-powershell-and-resource-manager"></a>Administración de recursos con Azure PowerShell y Resource Manager

En este artículo aprenderá a administrar las soluciones con Azure PowerShell y Azure Resource Manager. Si no está familiarizado con Resource Manager, consulte [Información general de Resource Manager](resource-group-overview.md). Este artículo se centra en las tareas de administración. Podrá:

1. Crear un grupo de recursos
2. Adición de un recurso al grupo de recursos
3. Adición de una etiqueta al recurso
4. Consulta de recursos según nombres y valores de etiqueta
5. Aplicación y eliminación de un bloqueo en el recurso
6. Eliminación de un grupo de recursos

En este artículo no se muestra cómo implementar una plantilla de Resource Manager en la suscripción. Esa información la puede encontrar en [Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell](resource-group-template-deploy.md).

## <a name="get-started-with-azure-powershell"></a>Introducción a Azure PowerShell

Si no tiene instalado Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

Si ha instalado Azure PowerShell en el pasado, pero no lo ha actualizado recientemente, considere la posibilidad de instalar la versión más reciente. Puede actualizar la versión con el mismo método usado para instalarla. Por ejemplo, si ha usado el Instalador de plataforma web, inícielo de nuevo y busque una actualización.

Para comprobar la versión del módulo de recursos de Azure, use el siguiente cmdlet:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Este artículo se ha actualizado para la versión 3.3.0. Si tiene una versión anterior, puede que su experiencia no coincida con los pasos mostrados en este artículo. Para ver documentación sobre los cmdlets de esta versión, consulte el [módulo AzureRM.Resources](/powershell/module/azurerm.resources).

## <a name="log-in-to-your-azure-account"></a>Inicie sesión en la cuenta de Azure.
Antes de trabajar en la solución, debe iniciar sesión en su cuenta.

Para iniciar sesión en su cuenta de Azure, use el cmdlet **Login-AzureRmAccount**.

```powershell
Login-AzureRmAccount
```

El cmdlet pide las credenciales de inicio de sesión para la cuenta de Azure. Después de iniciar la sesión, se descarga la configuración de la cuenta a fin de que esté disponible para Azure PowerShell.

El cmdlet devuelve información sobre su cuenta y la suscripción que se usará en las tareas.

```powershell
Environment           : AzureCloud
Account               : example@contoso.com
TenantId              : {guid}
SubscriptionId        : {guid}
SubscriptionName      : Example Subscription One
CurrentStorageAccount :

```

Si tiene más de una suscripción, puede cambiar a otra diferente. En primer lugar, vamos a ver todas las suscripciones de su cuenta.

```powershell
Get-AzureRmSubscription
```

Se devuelven las suscripciones habilitadas y deshabilitadas.

```powershell
SubscriptionName : Example Subscription One
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Two
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Three
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Disabled
```

Para cambiar a una suscripción diferente, proporcione el nombre de la suscripción con el cmdlet **Set-AzureRmContext**.

```powershell
Set-AzureRmContext -SubscriptionName "Example Subscription Two"
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Antes de implementar los recursos en la suscripción, debe crear un grupo de recursos que contendrá los recursos.

Para crear un grupo de recursos, use el cmdlet **New-AzureRmResourceGroup** . El comando usa el parámetro **Name** para especificar un nombre para el grupo de recursos y el parámetro **Location** para definir su ubicación.

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location "South Central US"
```

La salida tiene el siguiente formato:

```powershell
ResourceGroupName : TestRG1
Location          : southcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1
```

Si más tarde tiene que recuperar el grupo de recursos, use el siguiente cmdlet:

```powershell
Get-AzureRmResourceGroup -ResourceGroupName TestRG1
```

Para obtener todos los grupos de recursos de su suscripción, no especifique un nombre:

```powershell
Get-AzureRmResourceGroup
```

## <a name="add-resources-to-a-resource-group"></a>Adición de recursos a un grupo de recursos

Para agregar un recurso al grupo de recursos, puede usar el cmdlet **New-AzureRmResource** o un cmdlet específico del tipo de recurso que está creando (como **AzureRmStorageAccount New**). Quizás le resulte más fácil usar un cmdlet que sea específico de un tipo de recurso ya que incluye parámetros de las propiedades que son necesarias para el nuevo recurso. Para usar **New-AzureRmResource**, debe conocer todas las propiedades que se deben establecer sin que se soliciten.

Sin embargo, el hecho de agregar un recurso mediante cmdlets puede provocar confusión en el futuro porque el nuevo recurso no existe en una plantilla de Resource Manager. Microsoft recomienda definir la infraestructura de la solución de Azure en una plantilla de Resource Manager. Las plantillas le permiten implementar la solución de forma confiable y repetida. En este artículo, creará una cuenta de almacenamiento con un cmdlet de PowerShell, pero más tarde generará una plantilla a partir de su grupo de recursos.

El siguiente cmdlet crea una cuenta de almacenamiento. En lugar de usar el nombre que aparece en el ejemplo, proporcione un nombre único para la cuenta de almacenamiento. El nombre debe tener entre 3 y 24 caracteres y usar solo números y letras minúsculas. Si usa el nombre que aparece en el ejemplo, recibirá un error porque ese nombre ya está en uso.

```powershell
New-AzureRmStorageAccount -ResourceGroupName TestRG1 -AccountName mystoragename -Type "Standard_LRS" -Location "South Central US"
```

Si posteriormente necesita recuperar este recurso, use el siguiente cmdlet:

```powershell
Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1
```

## <a name="add-a-tag"></a>Agregar una etiqueta

Las etiquetas permiten organizar los recursos de acuerdo con diferentes propiedades. Por ejemplo, puede tener varios recursos en distintos grupos de recursos que pertenecen al mismo departamento. Puede aplicar una etiqueta de departamento y un valor a esos recursos para marcarlos como pertenecientes a la misma categoría. O bien, puede marcar si un recurso se usa en un entorno de producción o de prueba. En este artículo, aplicará etiquetas a un solo recurso; sin embargo, es probable que en su entorno tenga más sentido aplicar etiquetas a todos los recursos.

El siguiente cmdlet aplica dos etiquetas a la cuenta de almacenamiento:

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
 ```

Las etiquetas se actualizan como un único objeto. Para agregar una etiqueta a un recurso que ya incluye etiquetas, primero recupere las etiquetas existentes. Agregue la nueva etiqueta al objeto que contiene las etiquetas existentes y vuelva a aplicar todas las etiquetas al recurso.

```powershell
$tags = (Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
```

## <a name="search-for-resources"></a>Búsqueda de recursos

Use el cmdlet **Find-AzureRmResource** para recuperar recursos de diferentes condiciones de búsqueda.

* Para obtener un recurso por su nombre, proporcione el parámetro **ResourceNameContains**:

  ```powershell
  Find-AzureRmResource -ResourceNameContains mystoragename
  ```

* Para obtener todos los recursos de un grupo de recursos, proporcione el parámetro **ResourceGroupNameContains**:

  ```powershell
  Find-AzureRmResource -ResourceGroupNameContains TestRG1
  ```

* Para obtener todos los recursos con un nombre de etiqueta y un valor, proporcione los parámetros **TagName** y **TagValue**:

  ```powershell
  Find-AzureRmResource -TagName Dept -TagValue IT
  ```

* Para obtener todos los recursos con un tipo de recurso en particular, proporcione el parámetro **ResourceType**:

  ```powershell
  Find-AzureRmResource -ResourceType Microsoft.Storage/storageAccounts
  ```

## <a name="get-resource-id"></a>Obtener el identificador de recurso

Muchos comandos toman un identificador de recurso como un parámetro. Para obtener el identificador para un recurso y almacenarlo en una variable, use:

```powershell
$webappID = (Get-AzureRmResource -ResourceGroupName exampleGroup -ResourceName exampleSite).ResourceId
```

## <a name="lock-a-resource"></a>Bloqueo de un recurso

Cuando deba asegurarse de que un recurso crítico no se elimine o modifique por accidente, aplique un bloqueo al recurso. Puede especificar **CanNotDelete** o **ReadOnly**.

Para crear o eliminar bloqueos de administración, debe tener acceso a las acciones `Microsoft.Authorization/*` o `Microsoft.Authorization/locks/*`. Entre los roles integrados, solamente se conceden esas acciones al propietario y al administrador de acceso de usuarios.

Para aplicar un bloqueo, use el siguiente cmdlet:

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

El recurso bloqueado en el ejemplo anterior no se puede eliminar hasta que no se quite el bloqueo. Para quitar un bloqueo, use:

```powershell
Remove-AzureRmResourceLock -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

Para más información sobre el establecimiento de bloqueos, consulte [Bloqueo de recursos con Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Eliminación de recursos o grupos de recursos
Puede quitar un recurso o un grupo de recursos. Al quitar un grupo de recursos, también se quitan todos los recursos que contiene.

* Para eliminar un recurso del grupo de recursos, use el cmdlet **Remove-AzureRmResource** . Este cmdlet elimina el recurso, pero no el grupo de recursos.

  ```powershell
  Remove-AzureRmResource -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
  ```

* Para eliminar un grupo de recursos, y todos sus recursos, use el cmdlet **Remove-AzureRmResourceGroup**.

  ```powershell
  Remove-AzureRmResourceGroup -Name TestRG1
  ```

En ambos cmdlets, se le pide que confirme que quiere quitar el recurso o el grupo de recursos. Si la operación elimina correctamente el recurso o el grupo de recursos, devuelve **True**.

## <a name="run-resource-manager-scripts-with-azure-automation"></a>Ejecución de scripts de Resource Manager con Azure Automation

En este artículo se muestra cómo realizar operaciones básicas en los recursos con Azure PowerShell. En escenarios de administración más avanzados, lo habitual es que quiera crear un script y volver a usar ese script según sea necesario o según una programación. [Azure Automation](../automation/automation-intro.md) ofrece una forma de automatizar los scripts usados con mayor frecuencia que administran sus soluciones de Azure.

En los temas siguientes se muestra cómo usar Azure Automation, Resource Manager y PowerShell para realizar tareas de administración de manera efectiva:

- Para más información sobre la creación de un Runbook, consulte [Mi primer runbook de PowerShell](../automation/automation-first-runbook-textual-powershell.md).
- Para más información sobre cómo trabajar con galerías de scripts, consulte [Galerías de runbooks y módulos para Azure Automation](../automation/automation-runbook-gallery.md).
- Para más información sobre Runbooks que inician y detienen máquinas virtuales, consulte [Escenario de Azure Automation: uso de etiquetas con formato JSON para crear una programación de inicio y apagado de VM de Azure](../automation/automation-scenario-start-stop-vm-wjson-tags.md).
- Para más información sobre Runbooks que iniciar y detienen máquinas virtuales fuera de las horas de trabajo, consulte [Inicio o detención de máquinas virtuales fuera de las horas de trabajo en Automation](../automation/automation-solution-vm-management.md).

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre la creación de plantillas de Resource Manager, consulte [Authoring Azure Resource Manager templates](resource-group-authoring-templates.md) (Creación de plantillas de Azure Resource Manager).
* Para obtener información sobre cómo implementar plantillas, consulte [Implementación de una aplicación con la plantilla de Azure Resource Manager](resource-group-template-deploy.md).
* Puede mover recursos existentes a un nuevo grupo de recursos. Para consultar ejemplos, vea [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](resource-group-move-resources.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).

