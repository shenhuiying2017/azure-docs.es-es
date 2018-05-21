---
title: Errores de recursos de Azure no encontrados | Microsoft Docs
description: Describe cómo resolver errores cuando no se encuentra un recurso.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: f5da2a74b3a399c60c518f386ccf2e60a617aeda
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Resolver errores de recursos de Azure no encontrados

En este artículo se describen los errores que pueden producirse cuando no se encuentra un recurso durante la implementación.

## <a name="symptom"></a>Síntoma

Cuando la plantilla incluye el nombre de un recurso que no se puede resolver, recibe un error similar al siguiente:

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

## <a name="solution-1---set-dependencies"></a>Solución 1: Establecimiento de dependencias

Si está tratando de implementar el recurso que falta en la plantilla, compruebe si tiene que agregar una dependencia. Cuando es posible, Resource Manager optimiza la implementación mediante la creación de recursos en paralelo. Si un recurso se debe implementar después de otro, debe usar el elemento **dependsOn** de la plantilla. Por ejemplo, al implementar una aplicación web, debe existir el plan de App Service. Si no ha especificado que la aplicación web dependa del plan de App Service, Resource Manager crea ambos recursos al mismo tiempo. Recibirá un error que indica que no se encuentra el recurso del plan de App Service, porque aún no existe cuando se intenta establecer una propiedad en la aplicación web. Este error se puede evitar estableciendo la dependencia en la aplicación web.

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

No obstante, desea evitar configurar dependencias que no sean necesarias. Cuando hay dependencias innecesarias, prolonga la duración de la implementación al impedir que los recursos que no son dependientes entre sí se implementen en paralelo. Además, puede crear dependencias circulares que bloqueen la implementación. La función [reference](resource-group-template-functions-resource.md#reference) crea una dependencia implícita del recurso al que hace referencia, cuando este se implementa en la misma plantilla. Por lo tanto, puede tener más dependencias que las especificadas en la propiedad **dependsOn**. La función [resourceId](resource-group-template-functions-resource.md#resourceid) no crea una dependencia implícita ni valida que el recurso existe.

Cuando se encuentre con problemas de dependencia, debe comprender mejor el orden de la implementación de recursos. Para ver el orden de las operaciones de implementación:

1. Seleccione el historial de implementaciones para el grupo de recursos.

   ![seleccionar el historial de implementaciones](./media/resource-manager-not-found-errors/select-deployment.png)

2. Seleccione una implementación del historial y seleccione **Eventos**.

   ![seleccionar eventos de implementación](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. Examine la secuencia de eventos para cada recurso. Preste atención para el estado de cada operación. Por ejemplo, la siguiente imagen muestra tres cuentas de almacenamiento que se implementan en paralelo. Tenga en cuenta que las tres cuentas de almacenamiento se inician al mismo tiempo.

   ![implementación paralela](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   La siguiente imagen muestra tres cuentas de almacenamiento que no se implementan en paralelo. La segunda cuenta de almacenamiento depende de la primera cuenta de almacenamiento y la tercera cuenta de almacenamiento depende de la segunda cuenta de almacenamiento. La primera cuenta de almacenamiento se inicia, se acepta y se completa antes de que se inicie la siguiente.

   ![implementación secuencial](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Solución 2: Obtención de recursos de otro grupo de recursos

Si el recurso existe en un grupo de recursos diferente al que se va a implementar, utilice la [función resourceId](resource-group-template-functions-resource.md#resourceid) para obtener el nombre completo del recurso.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Solución 3: Comprobación de la función de referencia

Busque una expresión que incluya la función [reference](resource-group-template-functions-resource.md#reference). Los valores que proporcione varían en función de si el recurso se está en la misma plantilla, grupo de recursos y suscripción. Compruebe que está proporcionando los valores de parámetro necesarios para su escenario. Si el recurso está en otro grupo de recursos, proporcione el identificador de recurso completo. Por ejemplo, para hacer referencia a una cuenta de almacenamiento en otro grupo de recursos, use:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```