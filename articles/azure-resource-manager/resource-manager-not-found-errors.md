---
title: Errores de recursos de Azure no encontrados | Microsoft Docs
description: "Describe cómo resolver errores cuando no se encuentra un recurso."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: c76c965c43ca8217faa9488c01975ce09a21daaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Resolver errores de recursos de Azure no encontrados

Este artículo describen los errores que pueden producirse cuando no se encuentra un recurso durante la implementación. 

## <a name="symptom"></a>Síntoma

Cuando la plantilla incluye el nombre de un recurso que no se puede resolver, recibirá un error similar al siguiente:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Si trata de usar las funciones [reference](resource-group-template-functions-resource.md#reference) o [listKeys](resource-group-template-functions-resource.md#listkeys) con un recurso que no se puede resolver, recibirá el error siguiente:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Causa

Resource Manager necesita recuperar las propiedades de un recurso, pero no puede identificar el recurso en su suscripción.

## <a name="solution"></a>Solución

### <a name="solution-1"></a>Solución 1

Si está tratando de implementar el recurso que falta en la plantilla, compruebe si tiene que agregar una dependencia. Cuando es posible, Resource Manager optimiza la implementación mediante la creación de recursos en paralelo. Si un recurso se debe implementar después de otro recurso, debe usar el elemento **dependsOn** en la plantilla para crear una dependencia en el otro recurso. Por ejemplo, al implementar una aplicación web, debe existir el plan de Servicio de aplicaciones. Si no ha especificado que la aplicación web dependa del plan de App Service, Resource Manager creará ambos recursos al mismo tiempo. Recibirá un error que indica que no se encuentra el recurso del plan de App Service, porque aún no existe cuando se trata de establecer una propiedad en la aplicación web. Este error se puede evitar estableciendo la dependencia en la aplicación web.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Para obtener sugerencias sobre cómo solucionar los errores de dependencia, consulte [Comprobación de la secuencia de implementación](resource-manager-troubleshoot-tips.md#check-deployment-sequence).

### <a name="solution-2"></a>Solución 2

Si el recurso existe en un grupo de recursos diferente al que se va a implementar, utilice la [función resourceId](resource-group-template-functions-resource.md#resourceid) para obtener el nombre completo del recurso.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

### <a name="solution-3"></a>Solución 3

Busque una expresión que incluya la función [reference](resource-group-template-functions-resource.md#reference). Los valores que proporcione varían en función de si el recurso se está en la misma plantilla, grupo de recursos y suscripción. Compruebe que está proporcionando los valores de parámetro necesarios para su escenario. Si el recurso está en otro grupo de recursos, proporcione el identificador de recurso completo. Por ejemplo, para hacer referencia a una cuenta de almacenamiento en otro grupo de recursos, use:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```