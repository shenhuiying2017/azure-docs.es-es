---
title: "Información sobre los errores de implementación de Azure | Microsoft Docs"
description: "Describe cómo obtener información acerca de los errores de implementación de Azure."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "error de implementación, implementación de Azure, implementar en Azure"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: tomfitz
ms.openlocfilehash: b67bb30fa259fa08e37e11afec724c8b8c3eb633
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="understand-azure-deployment-errors"></a>Información sobre los errores de implementación de Azure
En este tema se describen los errores de implementación y cómo obtener más información sobre un error. Para sugerencias de solución de errores de implementación comunes, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="two-types-of-errors"></a>Dos tipos de errores
Hay dos tipos de errores que puede recibir:

* Errores de validación
* Errores de implementación

La siguiente imagen muestra el registro de actividad de una suscripción. Representa dos implementaciones. En una implementación, la plantilla no pudo validarse (**Validar**) y no continuó. En la otra, la plantilla pasó la validación, pero se produjo un error al crear los recursos (**Write deployments** [Escribir implementaciones]). 

![Visualización del código de error](./media/resource-manager-troubleshoot-tips/show-activity-log.png)

Los errores de validación que provienen de escenarios que se pueden determinar antes de la implementación incluyen los errores de sintaxis en la plantilla o tratar de implementar los recursos que superarían las cuotas de suscripción. Errores de implementación que provengan de condiciones que se producen durante el proceso de implementación. Incluyen intentar acceder a un recurso que se está implementando en paralelo.

Los dos tipos de errores devuelven un código de error que utiliza para solucionar problemas de implementación. Ambos aparecen en el [registro de actividad](resource-group-audit.md). Sin embargo, los errores de validación no aparecen en el historial de implementación porque la implementación nunca se inicia.

## <a name="determine-error-code"></a>Determinación del código de error

Puede obtener información acerca de un error al examinar el mensaje de error y el código de error. En el artículo [Solución de errores comunes de implementación de Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md) se enumeran las resoluciones por código de error. El tema muestra cómo usar Azure Portal para descubrir el código de error.

### <a name="validation-errors"></a>Errores de validación

Al implementar a través del portal, verá un error de validación después de enviar sus valores.

![vista del error de validación en el portal](./media/resource-manager-troubleshoot-tips/validation-error.png)

Seleccione el mensaje para más detalles. En la siguiente imagen, verá un error **InvalidTemplateDeployment** y un mensaje que indica que una directiva está bloqueando la implementación.

![vista de los detalles de validación](./media/resource-manager-troubleshoot-tips/validation-details.png)

### <a name="deployment-errors"></a>Errores de implementación

Cuando la operación pasa la validación, pero se produce un error durante la implementación, verá el error en las notificaciones. Seleccione la notificación.

![notificación de error](./media/resource-manager-troubleshoot-tips/notification.png)

Verá más detalles acerca de la implementación. Seleccione la opción para más información sobre el error.

![error de implementación](./media/resource-manager-troubleshoot-tips/deployment-failed.png)

Verá el mensaje de error y los códigos de error. Observe que hay dos códigos de error. El primero (**DeploymentFailed**) es un error general que no proporciona los detalles que necesita para resolverlo. El segundo código de error (**StorageAccountNotFound**) proporciona los detalles que necesita. 

![detalles del error](./media/resource-manager-troubleshoot-tips/error-details.png)

## <a name="enable-debug-logging"></a>Habilitación del registro de depuración
En ciertas ocasiones necesitará más información acerca de la solicitud y respuesta para descubrir qué ha salido mal. Mediante el uso de PowerShell o la CLI de Azure, puede solicitar que se registre información adicional durante la implementación.

- PowerShell

   En PowerShell, establezca el parámetro **DeploymentDebugLogLevel** en Todos, ResponseContent o RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Examine el contenido de la solicitud con el siguiente cmdlet:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   O bien, la respuesta de contenido con:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Esta información puede ayudarlo a determinar si un valor en la plantilla se está estableciendo de forma incorrecta.

- CLI de Azure

   Examine las operaciones de implementación con el siguiente comando:

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- Plantilla anidada

   Para registrar la información de depuración de una plantilla anidada, use el elemento **debugSetting**.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```


## <a name="create-a-troubleshooting-template"></a>Creación de una plantilla de solución de problemas
En algunos casos, la manera más fácil de solucionar problemas de plantillas es comprobando sus elementos. Puede crear una plantilla simplificada que le permita centrarse en lo que crea que está provocando el error. Por ejemplo, supongamos que se recibe un error al hacer referencia a un recurso. En lugar de trabajar directamente con una plantilla de toda, cree una plantilla que devuelva la parte que pueda estar causando el problema. Esto puede ayudarlo a determinar si está pasando los parámetros correctos con las funciones de plantilla correctamente y obteniendo el recurso esperado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Supongamos que se están produciendo errores de implementación que cree que están relacionados con dependencias establecidas incorrectamente. Pruebe la plantilla dividiéndola en plantillas simplificadas. En primer lugar, cree una plantilla que implemente solo un único recurso (por ejemplo, un servidor SQL Server). Cuando esté seguro de que tiene dicho recurso definido correctamente, agregue un recurso que dependa de él (por ejemplo, una base de datos SQL). Cuando esos dos recursos se definan correctamente, agregue otros recursos dependientes (por ejemplo, las directivas de auditoría). Entre cada implementación de prueba, elimine el grupo de recursos para asegurarse de que se prueban adecuadamente las dependencias. 

## <a name="check-deployment-sequence"></a>Comprobación de la secuencia de implementación

Muchos errores de implementación se producen cuando los recursos se implementan en una secuencia inesperada. Estos errores se producen cuando las dependencias no se han establecido correctamente. Cuando falta una dependencia necesaria, un recurso intenta usar un valor para otro recurso, pero este otro recurso no existe aún. Recibirá un error que indica que no se encuentra el recurso. Puede que este tipo de error aparezca de forma intermitente porque el tiempo de implementación de cada recurso puede variar. Por ejemplo, su primer intento de implementar los recursos tendrá éxito porque un recurso necesario aleatoriamente finaliza a tiempo. Sin embargo, el segundo intento produce error porque el recurso necesario no finalizó a tiempo. 

No obstante, desea evitar configurar dependencias que no sean necesarias. Cuando hay dependencias innecesarias, prolonga la duración de la implementación al impedir que los recursos que no son dependientes entre sí se implementen en paralelo. Además, puede crear dependencias circulares que bloqueen la implementación. La función [reference](resource-group-template-functions-resource.md#reference) crea una dependencia implícita del recurso al que hace referencia, cuando este se implementa en la misma plantilla. Por lo tanto, puede tener más dependencias que las especificadas en la propiedad **dependsOn**. La función [resourceId](resource-group-template-functions-resource.md#resourceid) no crea una dependencia implícita ni valida que el recurso existe.

Cuando se encuentre con problemas de dependencia, debe comprender mejor el orden de la implementación de recursos. Para ver el orden de las operaciones de implementación:

1. Seleccione el historial de implementaciones para el grupo de recursos.

   ![seleccionar el historial de implementaciones](./media/resource-manager-troubleshoot-tips/select-deployment.png)

2. Seleccione una implementación del historial y seleccione **Eventos**.

   ![seleccionar eventos de implementación](./media/resource-manager-troubleshoot-tips/select-deployment-events.png)

3. Examine la secuencia de eventos para cada recurso. Preste atención para el estado de cada operación. Por ejemplo, la siguiente imagen muestra tres cuentas de almacenamiento que se implementan en paralelo. Tenga en cuenta que las tres cuentas de almacenamiento se inician al mismo tiempo.

   ![implementación paralela](./media/resource-manager-troubleshoot-tips/deployment-events-parallel.png)

   La siguiente imagen muestra tres cuentas de almacenamiento que no se implementan en paralelo. La segunda cuenta de almacenamiento depende de la primera cuenta de almacenamiento y la tercera cuenta de almacenamiento depende de la segunda cuenta de almacenamiento. Por lo tanto, la primera cuenta de almacenamiento se inicia, acepta y completa antes de que se inicie la siguiente.

   ![implementación secuencial](./media/resource-manager-troubleshoot-tips/deployment-events-sequence.png)

Hasta para los escenarios más complicados puede usar la misma técnica para detectar cuándo se inicia la implementación y finaliza para cada recurso. Examine los eventos de implementación para ver si la secuencia es diferente de la que cabría esperar. Si es así, vuelva a evaluar las dependencias de este recurso.

Resource Manager identifica dependencias circulares durante la validación de plantillas. y devuelve un mensaje de error que indica específicamente que existe una dependencia circular. Para resolver una dependencia circular:

1. En la plantilla, busque el recurso identificado en la dependencia circular. 
2. Para ese recurso, examine la propiedad **dependsOn** y todos los usos de la función **reference** para ver de qué recursos depende. 
3. Examine esos recursos para ver de qué recursos dependen. Siga las dependencias hasta que observe un recurso que dependa del recurso original.
5. Para los recursos que intervienen en la dependencia circular, examine con cuidado todos los usos de la propiedad **dependsOn** para identificar las dependencias que no sean necesarias. Quite esas dependencias. Si no está seguro de si una dependencia es necesaria, pruebe a quitarla. 
6. Vuelva a implementar la plantilla.

Al quitar los valores de la propiedad **dependsOn**, se pueden provocar errores al implementar la plantilla. Si se produce un error, vuelva a agregar la dependencia en la plantilla. 

Si ese enfoque no soluciona la dependencia circular, considere la posibilidad de mover parte de la lógica de implementación a recursos secundarios (por ejemplo, extensiones o valores de configuración). Configure esos recursos secundarios para que se implementen después de los recursos que intervienen en la dependencia circular. Por ejemplo, suponga que va a implementar dos máquinas virtuales pero debe establecer propiedades en cada una que hagan referencia a la otra. Puede implementarlas en el orden siguiente:

1. vm1
2. vm2
3. La extensión en vm1 depende de vm1 y vm2. La extensión establece valores en vm1 que obtiene de vm2.
4. La extensión en vm2 depende de vm1 y vm2. La extensión establece valores en vm2 que obtiene de vm1.

El mismo enfoque funciona para las aplicaciones de App Service. Considere la posibilidad de mover los valores de configuración a un recurso secundario del recurso de aplicación. Puede implementar dos aplicaciones web en el orden siguiente:

1. webapp1
2. webapp2
3. La configuración de webapp1 depende de webapp1 y webapp2. Contiene la configuración de aplicación con valores de webapp2.
4. La configuración de webapp2 depende de webapp1 y webapp2. Contiene la configuración de aplicación con valores de webapp1.


## <a name="next-steps"></a>Pasos siguientes
* Para sugerencias de solución de errores de implementación comunes, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para más información sobre las acciones de auditoría, consulte [Operaciones de auditoría con Resource Manager](resource-group-audit.md).
* Si desea conocer más detalles sobre las acciones que permiten determinar los errores durante la implementación, consulte [Visualización de operaciones de implementación con el Portal de Azure](resource-manager-deployment-operations.md).
