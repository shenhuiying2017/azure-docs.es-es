---
title: "Uso de Azure Policy para crear y administrar directivas de aplicación del cumplimiento organizativo | Microsoft Docs"
description: "Use Azure Policy para aplicar estándares, satisfacer los requisitos de cumplimiento normativo y auditar requisitos, controlar los costos, mantener la seguridad y consistencia en el rendimiento e imponer principios de diseño para toda la empresa."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/03/2018
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 882cf3cde71f5154efcd88f055984e72463b3099
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Creación y administración de directivas para aplicar el cumplimiento

Comprender cómo se crean y administran las directivas en Azure es importante para mantener el cumplimiento de los estándares corporativos y los contratos de nivel de servicio. En este tutorial, aprenderá a usar Azure Policy para realizar algunas de las tareas más comunes relacionadas con la creación, asignación y administración de las directivas en toda la organización, como:

> [!div class="checklist"]
> * Asignar una directiva para aplicar una condición a los recursos que se creen en el futuro
> * Crear y asignar una definición de iniciativa para realizar un seguimiento del cumplimiento para varios recursos
> * Resolver un recurso que no cumpla o que sea denegado
> * Implementar una nueva directiva en toda la organización

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="assign-a-policy"></a>Asignación de una directiva

El primer paso para aplicar cumplimientos con Azure Policy es asignar una definición de directiva. Una definición de directiva precisa en qué condiciones se aplica una directiva y qué acción se debe realizar. En este ejemplo, asignamos una definición de directiva integrada denominada *Require SQL Server Version 12.0* (Requerir SQL Server versión 12.0), para aplicar la condición de que todas las bases de datos de SQL Server tienen que ser v12.0 para que cumplan.

1. Inicie el servicio Azure Policy en Azure Portal buscando y seleccionando **Policy** (Directiva) en el panel izquierdo.

   ![Búsqueda de directivas](media/assign-policy-definition/search-policy.png)

2. Seleccione **Assignments** (Asignaciones) en el panel izquierdo de la página Azure Policy. Una asignación es una directiva que se asignó para que se lleve a cabo dentro de un ámbito específico.
3. Seleccione **Assign Policy** (Asignar directiva) en la parte superior del panel **Assignments** (Asignaciones).

   ![Asignación de una definición de directiva](media/create-manage-policy/select-assign-policy.png)

4. En la página **Assign Policy** (Asignar directiva), haga clic en el ![botón Policy definition](media/assign-policy-definition/definitions-button.png) (Definición de directiva) junto al campo **Policy** (Directiva) para abrir la lista de las definiciones disponibles.

   ![Apertura de las definiciones de directiva disponibles](media/create-manage-policy/open-policy-definitions.png)

5. Seleccione **Require SQL Server Version 12.0** (Requerir SQL Server 12.0).

   ![Búsqueda de una directiva](media/create-manage-policy/select-available-definition.png)

6. Proporcione un **Nombre** para mostrar para la asignación de directiva. En este caso, vamos a usar *Require SQL Server version 12.0* (Requerir SQL Server 12.0). También puede agregar una **Descripción** opcional. La descripción proporciona detalles sobre cómo esta asignación de directiva garantiza que todos los servidores SQL Server creados en este entorno tengan la versión 12.0.
7. Cambie el plan de tarifa a **Standard** (Estándar) para asegurarse de que la directiva se aplique a los recursos existentes.

   Azure Policy ofrece dos planes de tarifa: *Free* (Gratis) y *Standard* (Estándar). Con el nivel Gratis, solo puede implementar las directivas en futuros recursos, mientras que con el plan Estándar, también puede hacerlo en los recursos ya existentes, para identificar mejor el estado de cumplimiento. Como se trata de la versión preliminar limitada, aún no se ha publicado el modelo de precios, por lo que no recibirá ninguna factura si selecciona el plan *Standard* (Estándar). Para más información sobre los precios, vea la página sobre [precios de Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy).

8. Seleccione una opción en **Scope** (Ámbito): la suscripción o el grupo de recursos que registró anteriormente. Un ámbito determina en qué recursos o agrupación de recursos se implementa la asignación de directiva. Puede abarcar desde una suscripción hasta grupos de recursos.

   En este ejemplo, se usa la suscripción **Azure Analytics Capacity Dev**. Su suscripción variará.

10. Seleccione **Asignar**.

## <a name="implement-a-new-custom-policy"></a>Implementación de una nueva directiva personalizada

Ahora que se ha asignado la definición de directiva, vamos a crear una directiva nueva para ahorrar costos, asegurando que las máquinas virtuales que se crean en su entorno no estén en la serie G. De este modo, cada vez que un usuario en su organización intenta crear la máquina virtual de la serie G, la solicitud le será denegada.

1. Seleccione **Definition** (Definición) en **Authoring** (Creación) en el panel izquierdo.

   ![Definición en creación](media/create-manage-policy/definition-under-authoring.png)

2. Seleccione **+ Policy Definition** (+Definición de directiva).
3. Escriba lo siguiente:

   - El nombre de la definición de directiva: *Require VM SKUs smaller than the G series* (Requerir SKU de máquina virtual inferiores a la serie G)
   - La descripción de para qué está diseñada la definición de directiva: esta definición de directiva exige que todas las máquinas virtuales creadas en este ámbito tengan SKU inferiores a la serie G para reducir el costo.
   - La suscripción en la que residirá la definición de directiva: en este caso, la definición de directiva residirá en **Advisor Analytics Capacity Dev**. Su lista de suscripción variará.
   - Copie el siguiente código json y, a continuación, actualícelo según sus necesidades con:
      - Los parámetros de directiva.
      - Las reglas/condiciones de la directiva, en este caso: tamaño de SKU de máquina virtual igual a la serie G
      - El efecto de la directiva, en este caso: **Deny** (Denegar).

    Este es el aspecto que debería tener el json. Pegue el código revisado en Azure Portal.

    ```json
{
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/sku.name",
            "like": "Standard_G*"
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
}
    ```

    Para ver ejemplos de código de JSON, lea el artículo [Templates for Azure Policy](json-samples.md) (Plantillas para Azure Policy).

4. Seleccione **Guardar**.

## <a name="create-a-policy-definition-with-rest-api"></a>Creación de una definición de directiva con la API de REST

Puede crear una directiva con la API de REST para definiciones de directiva. La API de REST permite crear y eliminar definiciones de directiva, así como recuperar información sobre las definiciones existentes.
Para crear una definición de directiva, use el siguiente ejemplo:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

```
Incluya un cuerpo de solicitud similar al ejemplo siguiente:

```
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Creación de una definición de directiva con PowerShell

Antes de continuar con el ejemplo de PowerShell, asegúrese de que tiene instalada la última versión de Azure PowerShell. Se agregaron parámetros de directiva en la versión 3.6.0. Si tiene una versión anterior, los ejemplos devuelven un error que indica que no se encuentra el parámetro.

Puede crear una definición de directiva con el cmdlet `New-AzureRmPolicyDefinition`.

Para crear una definición de directiva desde un archivo, pase la ruta de acceso al archivo. Para un archivo externo, use el ejemplo siguiente:

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Para un archivo local, use el ejemplo siguiente:

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Para crear una definición de directiva con una regla insertada, use el ejemplo siguiente:

```
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

La salida se almacena en un objeto `$definition`, que se usa durante la asignación de directivas.
En el ejemplo siguiente se crea una definición de directiva que incluye parámetros:

```
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters
```

## <a name="view-policy-definitions"></a>Visualización de definiciones de directiva

Para ver todas las definiciones de directiva en su suscripción, utilice el siguiente comando:

```
Get-AzureRmPolicyDefinition
```

Devuelve todas las definiciones de directiva disponibles, incluidas las directivas integradas. Cada directiva se devuelve con el formato siguiente:

```
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Creación de una definición de directiva con la CLI de Azure

Puede crear una definición de directiva mediante la CLI de Azure con el comando de definición de directiva.
Para crear una definición de directiva con una regla insertada, use el ejemplo siguiente:

```
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

## <a name="view-policy-definitions"></a>Visualización de definiciones de directiva

Para ver todas las definiciones de directiva en su suscripción, utilice el siguiente comando:

```
az policy definition list
```

Devuelve todas las definiciones de directiva disponibles, incluidas las directivas integradas. Cada directiva se devuelve con el formato siguiente:

```
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Creación y asignación de una definición de iniciativa

Con una definición de iniciativa, puede agrupar varias definiciones de directiva para lograr un objetivo general. Cree una definición de iniciativa para asegurarse de que los recursos en el ámbito de la definición permanezcan en conformidad con las definiciones de directiva que componen la definición de iniciativa.  Consulte la [Introducción a Azure Policy](./azure-policy-introduction.md) para más información sobre las definiciones de iniciativa.

### <a name="create-an-initiative-definition"></a>Creación de una definición de iniciativa

1. Seleccione **Definition** (Definición) en **Authoring** (Creación) en el panel izquierdo.

   ![Selección de definiciones](media/create-manage-policy/select-definitions.png)

2. Seleccione **Initiative Definition** (Definición de iniciativa) en la parte superior de la página, esta selección le lleva hasta el formulario **Initiative Definition** (Definición de iniciativa).
3. Escriba el nombre y la descripción de la iniciativa.

   En este ejemplo, queremos asegurarnos de que los recursos están en conformidad con las definiciones de directiva sobre la obtención de recursos seguros, el nombre de la iniciativa sería **Get Secure** (Obtener con seguridad), y la descripción sería: **This initiative has been created to handle all policy definitions associated with securing resources** (Esta iniciativa se ha creado para controlar todas las definiciones de directiva asociadas con la obtención segura de recursos).

   ![Definición de iniciativa](media/create-manage-policy/initiative-definition.png)

4. Examine la lista **Available Definitions** (Definiciones disponibles) y seleccione las definiciones de directiva que le gustaría agregar a esa iniciativa. Para nuestra iniciativa **Get secure** (Obtener con seguridad), agregue las siguientes definiciones de directiva integradas:
   - Requisito de la versión 12.0 de SQL Server
   - Monitor unprotected web applications in Security Center (Supervisar las aplicaciones web no protegidas en Security Center).
   - Monitor permissive network across in Security Center (Supervisar la red permisiva en Security Center).
   - Monitor possible app Whitelisting in Security Center (Supervisar la creación de listas blancas por la aplicación en Security Center).
   - Monitor unencrypted VM Disks in Security Center (Supervisar los discos de máquina virtual sin cifrar en Security Center).

   ![Definiciones de iniciativa](media/create-manage-policy/initiative-definition-2.png)

   Después de seleccionar las definiciones de directiva de la lista las verá en **Policies and parameters** (Directivas y parámetros), como se muestra en la ilustración anterior.

5. Seleccione **Crear**.

### <a name="assign-an-initiative-definition"></a>Asignación de una definición de iniciativa

1. Vaya a la pestaña **Definitions** (Definiciones) en **Authoring** (Creación).
2. Busque la definición de iniciativa **Get secure** (Obtener con seguridad) que creó.
3. Seleccione la definición de iniciativa y, a continuación, seleccione **Assign** (Asignar).

   ![Asignación de una definición](media/create-manage-policy/assign-definition.png)

4. Rellene el formulario **Assignment** (Asignación), escriba:
   - name (nombre): Asignación Get secure
   - description (descripción): esta asignación de iniciativa está adaptada para aplicar este grupo de definiciones de directiva en la suscripción **Azure Advisor capacidad Dev**
   - pricing tier (plan de tarifa): Standard
   - scope you would like this assignment applied to (ámbito al que desea que se aplique esta asignación): **Azure Advisor Capacity Dev**

5. Seleccione **Asignar**.

## <a name="resolve-a-non-compliant-or-denied-resource"></a>Resolución de un recurso que no cumpla o que sea denegado

Siguiendo el ejemplo anterior, después de asignar la definición de directiva para requerir SQL Server versión 12.0, un servidor de SQL Server que se cree con una versión diferente será denegado. En esta sección vamos a describir la resolución de un intento denegado de crear un servidor SQL Server de una versión diferente solicitando una exclusión.

1. Seleccione **Assignments** (Asignaciones) en el panel izquierdo.
2. Examine todas las asignaciones de directiva e inicie la asignación *Require SQL Server version 12.0* (Requerir SQL Server 12.0).
3. Solicite una exclusión para los grupos de recurso en los que está intentando crear el servidor SQL Server. En este caso, estamos excluyendo Microsoft.Sql/servers/databases: *baconandbeer/Cheetos* y *baconandbeer/Chorizo*.

   ![Solicitud de exclusión](media/create-manage-policy/request-exclusion.png)

   Otras maneras para resolver un recurso denegado incluyen: llegar al contacto asociado con la directiva si tiene algo que justifique claramente la necesidad de crear el servidor SQL Server y editar directamente la directiva si tiene acceso a ella.

4. Seleccione **Guardar**.

En esta sección, se resuelve la denegación de su intento de crear un servidor SQL Server con la versión 12.0, solicitando una exclusión para los recursos.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si tiene previsto seguir trabajando con los tutoriales siguientes, no elimine los recursos creados en esta guía. Si no va a continuar, use los pasos a continuación para eliminar todas las asignaciones y definiciones creadas:

1. Seleccione **Definitions** (Definiciones), o **Assignments** (Asignaciones) si está intentando eliminar una asignación en el panel izquierdo.
2. Busque la nueva iniciativa o definición de directiva (o asignación) recién creada.
3. Seleccione los puntos suspensivos al final de la definición o la asignación y seleccione **Delete Definition** (Eliminar definición) o **Delete Assignment** (Eliminar asignación).

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha realizado satisfactoriamente lo siguiente:

> [!div class="checklist"]
> * Ha asignado una directiva para aplicar una condición a los recursos que se creen en el futuro
> * Ha creado y asignado una definición de iniciativa para realizar un seguimiento del cumplimiento para varios recursos
> * Ha resuelto un recurso que no cumplía o que se había denegado
> * Ha implementado una nueva directiva en toda la organización

Para más información sobre las estructuras de las definiciones de directiva, consulte este artículo:

> [!div class="nextstepaction"]
> [Estructura de definición de Azure Policy](policy-definition.md)
