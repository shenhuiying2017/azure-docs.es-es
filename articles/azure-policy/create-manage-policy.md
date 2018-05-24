---
title: Uso de Azure Policy para crear y administrar directivas de aplicación del cumplimiento organizativo
description: Use Azure Policy para aplicar estándares, satisfacer los requisitos de cumplimiento normativo y auditar requisitos, controlar los costos, mantener la seguridad y consistencia en el rendimiento e imponer principios de diseño para toda la empresa.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 1d13c5c0099b8e4c9ed83221edf6a2ee92fc3c8e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194876"
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Creación y administración de directivas para aplicar el cumplimiento

Comprender cómo se crean y administran las directivas en Azure es importante para mantener el cumplimiento de los estándares corporativos y los contratos de nivel de servicio. En este tutorial, aprenderá a usar Azure Policy para realizar algunas de las tareas más comunes relacionadas con la creación, la asignación y la administración de directivas en toda la organización, como, por ejemplo:

> [!div class="checklist"]
> - Asignar una directiva para aplicar una condición a los recursos que se creen en el futuro
> - Crear y asignar una definición de iniciativa para realizar un seguimiento del cumplimiento para varios recursos
> - Resolver un recurso que no cumpla o que sea denegado
> - Implementar una nueva directiva en toda la organización

Si desea asignar una directiva para identificar el estado de cumplimiento actual de los recursos existentes, en los artículos de inicio rápido se examina cómo hacerlo. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="assign-a-policy"></a>Asignación de una directiva

El primer paso para aplicar cumplimientos con Azure Policy es asignar una definición de directiva. Una definición de directiva precisa en qué condiciones se aplica una directiva y qué efecto debe tener. En este ejemplo, asigne una definición de directiva integrada denominada *Require SQL Server version 12.0* (Requerir SQL Server 12.0), para aplicar la condición de que todas las bases de datos SQL Server tengan que ser v12.0 para que satisfagan los requisitos de cumplimiento.

1. Inicie el servicio Azure Policy en Azure Portal. Para ello, haga clic en **Todos los servicios** y, a continuación, busque y seleccione **Directiva**.

   ![Búsqueda de directivas](media/create-manage-policy/search-policy.png)

2. Seleccione **Asignaciones** en el panel izquierdo de la página de Azure Policy. Una asignación es una directiva que se asignó para que se lleve a cabo dentro de un ámbito específico.
3. Seleccione **Asignar directiva** en la parte superior de la página **Policy - Asignaciones**.

   ![Asignación de una definición de directiva](media/create-manage-policy/select-assign-policy.png)

4. En la página **Asignar directiva**, haga clic en los puntos suspensivos para seleccionar una opción de **Ámbito** y seleccione una suscripción (obligatorio) y un grupo de recursos (opcional). Un ámbito determina en qué recursos o agrupación de recursos se implementa la asignación de directiva.  Después, haga clic en **Seleccionar** en la parte inferior de la página **Ámbito**.

   En este ejemplo se utiliza la **suscripción de Contoso**. Su suscripción variará.

5. Si quiere excluir uno o más grupos de recursos (en caso de que solo haya agregado ámbito a una suscripción) o recursos específicos dentro de un grupo de recursos (cualquier caso de ámbito), puede configurar **Exclusiones** en la asignación de directiva. Déjelo en blanco por ahora.

6. Seleccione los puntos suspensivos de **Definición de directiva** para abrir la lista de definiciones disponibles. Puede filtrar el campo **Tipo** de la definición de directiva por *Integrada* para verlas todas y leer sus descripciones.

7. Seleccione **Require SQL Server version 12.0** (Requerir SQL Server 12.0). Si no la encuentra inmediatamente, escriba **require sql server** en el cuadro de búsqueda y, a continuación, presione ENTRAR o haga clic fuera del cuadro de búsqueda. Haga clic en **Seleccionar** en la parte inferior de la página **Definiciones disponibles** después de encontrar y seleccionar la definición de directiva.

   ![Búsqueda de una directiva](media/create-manage-policy/select-available-definition.png)

8. **Nombre de asignación** se rellena automáticamente con el nombre de directiva seleccionado, pero puede cambiarlo. En este ejemplo, deje *Require SQL Server version 12.0* (Requerir SQL Server 12.0). También puede agregar una **Descripción** opcional. La descripción ofrece detalles sobre esta asignación de directiva.

9. Haga clic en **Asignar**.

## <a name="implement-a-new-custom-policy"></a>Implementación de una nueva directiva personalizada

Ahora que ha asignado una definición de directiva integrada, puede hacer más cosas con Azure Policy. A continuación, creará una nueva directiva personalizada para ahorrar costos, para lo cual se asegurará de que las máquinas virtuales creadas en el entorno no puedan estar en la serie G. De este modo, cada vez que un usuario de su organización intente crear una máquina virtual de la serie G, la solicitud le será denegada.

1. Seleccione **Definiciones** en **CREACIÓN** en el lado izquierdo de la página de Azure Policy.

   ![Definición en creación](media/create-manage-policy/definition-under-authoring.png)

2. Seleccione **+ Definición de directiva** en la parte superior de la página. Se abrirá la página **Definición de directiva**.
3. Escriba lo siguiente:

   - El grupo de administración o la suscripción donde está guardada la definición de directiva. Para la selección use los puntos suspensivos en **Ubicación de definición**.

     > [!NOTE]
     > Si planea aplicar esta definición de directiva a varias suscripciones, la ubicación debe ser un grupo de administración que contenga las suscripciones a las que asignará la directiva. Lo mismo puede aplicarse a una definición de iniciativa.

   - El nombre de la definición de directiva: *Require VM SKUs smaller than the G series* (Requerir SKU de máquina virtual inferiores a la serie G)
   - La descripción de la finalidad de la definición de directiva: *esta definición de directiva exige que todas las máquinas virtuales creadas en este ámbito tengan SKU inferiores a la serie G para reducir el costo.*
   - Elija entre las opciones existentes o cree una nueva categoría para esta definición de directiva.
   - Copie el siguiente código json y, a continuación, actualícelo según sus necesidades con:
      - Los parámetros de directiva.
      - Las reglas/condiciones de la directiva, en este caso: tamaño de SKU de máquina virtual igual a la serie G
      - El efecto de la directiva, en este caso: **Deny** (Denegar).

    Este es el aspecto que debería tener el json. Pegue el código revisado en Azure Portal.

    ```json
    {
        "policyRule": {
            "if": {
                "allOf": [{
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

    El valor de la propiedad *field* de la regla de directiva debe ser uno de los siguientes: nombre, tipo, ubicación, etiquetas o un alias. Un ejemplo de un alias podría ser `"Microsoft.Compute/VirtualMachines/Size"`.

    Para ver más ejemplos de directivas de Azure, consulte [Plantillas para Azure Policy](json-samples.md).

4. Seleccione **Guardar**.

## <a name="create-a-policy-definition-with-rest-api"></a>Creación de una definición de directiva con la API de REST

Puede crear una directiva con la API de REST para definiciones de directiva. La API de REST permite crear y eliminar definiciones de directiva, así como recuperar información sobre las definiciones existentes.
Para crear una definición de directiva, use el siguiente ejemplo:

```http-interactive
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Incluya un cuerpo de solicitud similar al ejemplo siguiente:

```json
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

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Para un archivo local, use el ejemplo siguiente:

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Para crear una definición de directiva con una regla insertada, use el ejemplo siguiente:

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
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

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
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

$definition = New-AzureRmPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Visualización de definiciones de directiva con PowerShell

Para ver todas las definiciones de directiva en su suscripción, utilice el siguiente comando:

```azurepowershell-interactive
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

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
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

### <a name="view-policy-definitions-with-azure-cli"></a>Visualización de definiciones de directiva con la CLI de Azure

Para ver todas las definiciones de directiva en su suscripción, utilice el siguiente comando:

```azurecli-interactive
az policy definition list
```

Devuelve todas las definiciones de directiva disponibles, incluidas las directivas integradas. Cada directiva se devuelve con el formato siguiente:

```json
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

Con una definición de iniciativa, puede agrupar varias definiciones de directiva para lograr un objetivo general. Cree una definición de iniciativa para asegurarse de que los recursos en el ámbito de la definición permanezcan en conformidad con las definiciones de directiva que componen la definición de iniciativa.  Para más información sobre las definiciones de iniciativa, consulte [Introducción a Azure Policy](azure-policy-introduction.md).

### <a name="create-an-initiative-definition"></a>Creación de una definición de iniciativa

1. Seleccione **Definiciones** en **CREACIÓN** en el lado izquierdo de la página de Azure Policy.

   ![Selección de definiciones](media/create-manage-policy/select-definitions.png)

2. Seleccione **+ Initiative Definition** (+ Definición de iniciativa) en la parte superior de la página para abrir la página **Definición de iniciativa** (Definición de iniciativa).

   ![Definición de iniciativa](media/create-manage-policy/initiative-definition.png)

3. Use **Ubicación de definición** para seleccionar una suscripción para almacenar la definición.

4. Defina los campos **Nombre** y **Descripción** de la iniciativa.

   En este ejemplo, se asegurará de que los recursos guarden conformidad con las definiciones de directiva sobre protección. Por tanto, el nombre de la iniciativa sería **Get Secure** (Estar protegido) y la descripción sería: **This initiative has been created to handle all policy definitions associated with securing resources** (Esta iniciativa se ha creado para administrar todas las definiciones de directiva asociadas con la protección de los recursos).

5. En **Categoría**, elija entre las opciones existentes o cree una nueva categoría.

6. Examine la lista **Definiciones disponibles** [mitad derecha de la página **Initiative definition** (definición de iniciativa)] y seleccione las definiciones de directiva que le gustaría agregar a esta iniciativa. En el caso de la iniciativa **Get secure** (Estar protegido), para agregar las siguientes definiciones de directiva integradas, haga clic en **+** junto a la información de definición de directiva o en una fila de definición de directiva y, a continuación, en la opción **+ Agregar** de la página de detalles:
   - Requisito de la versión 12.0 de SQL Server
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   Después de seleccionar la definición de directiva de la lista, esta se agregará a **POLICIES AND PARAMETERS** (DIRECTIVAS Y PARÁMETROS).

   ![Definiciones de iniciativa](media/create-manage-policy/initiative-definition-2.png)

7. Haga clic en **Save**(Guardar).

### <a name="assign-an-initiative-definition"></a>Asignación de una definición de iniciativa

1. Seleccione **Definiciones** en **CREACIÓN** en el lado izquierdo de la página de Azure Policy.
2. Busque la definición de directiva **Get Secure** (Estar protegido) que creó anteriormente y selecciónela.
3. Seleccione **Asignar** en la parte superior de la página para abrir la página **Get Secure: Assign Initiative** (Estar protegido: asignar iniciativa).

   ![Asignación de una definición](media/create-manage-policy/assign-definition.png)

   Como alternativa, puede hacer doble clic en la fila seleccionada o hacer clic en los puntos suspensivos al final de la fila de un menú contextual.  A continuación, seleccione **Asignar**.

   ![Haga clic con el botón derecho en una fila.](media/create-manage-policy/select-right-click.png)

4. Rellene la página **Get Secure: Assign Initiative** (Estar protegido: asignar iniciativa) con la siguiente información de ejemplo. Puede usar su propia información.

   - Ámbito: la suscripción donde guardó la iniciativa será la predeterminada.  También puede cambiar ámbito para asignar la iniciativa a un grupo de recursos en la ubicación de almacenamiento de la suscripción.
   - Exclusiones: configure los recursos del ámbito para evitar que se les aplique la asignación de iniciativa.
   - Initiative definition (Definición de iniciativa) y Nombre de asignación: Get Secure (Estar protegido; rellenado previamente como el nombre de la iniciativa que se está asignando).
   - Descripción: esta asignación de iniciativa está adaptada para aplicar este grupo de definiciones de directiva.

5. Haga clic en **Asignar**.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Exclusión de un recurso no conforme o denegado mediante Exclusión

Siguiendo el ejemplo anterior, después de asignar la definición de directiva para requerir SQL Server versión 12.0, un servidor de SQL Server que se cree con cualquier otra versión diferente será denegado. En esta sección se va a examinar cómo resolver un intento denegado de crear un servidor de SQL Server mediante la creación de un exclusión de un grupo de recursos único. La exclusión impide el cumplimiento de la directiva (o iniciativa) en ese recurso. En el ejemplo siguiente, se permitirá cualquier versión de SQL Server en un único grupo de recursos. Se puede aplicar una exclusión a un grupo de recursos o se puede restringir la exclusión a recursos individuales.

Una implementación impedida debido a una directiva o una iniciativa asignada puede verse en dos ubicaciones:

- En el grupo de recursos de destino de la implementación: seleccione **Implementaciones** en el lado izquierdo de la página y haga clic en el valor de **Nombre de la implementación** de la implementación con errores. El recurso denegado se mostrará con el estado _Prohibido_. Para determinar la directiva o iniciativa y la asignación que denegaron el recurso, haga clic en **Failed. Click here for details ->** (Error. Haga clic aquí para ver los detalles ->) en la página de información general de la implementación. Se abrirá una ventana en el lado derecho de la página con información sobre el error. En **Detalles del error** se mostrarán los GUID de los objetos de directiva relacionados.

   ![Implementación denegada por la asignación de directiva](media/create-manage-policy/rg-deployment-denied.png)

- En la página de Azure Policy: seleccione **Cumplimiento** en el lado izquierdo de la página y haga clic en la directiva **Require SQL Server version 12.0** (Requerir SQL Server 12.0). En la página que se abre, debería ver un aumento en el recuento de **Denegar**. En la pestaña **Eventos**, también debería ver quién intentó realizar la implementación denegada por la directiva.

   ![Información general del cumplimiento de una directiva asignada](media/create-manage-policy/compliance-overview.png)

En este ejemplo, Trent Baker, uno de los especialistas de virtualización de Contoso, estaba realizando el trabajo que tenía asignado. Es necesario concederle una excepción, pero no queremos servidores SQL Server de una versión que no sea 12.0 en ningún grupo de recursos. Hemos creado un nuevo grupo de recursos, **SQLServers_Excluded** y ahora concederemos una excepción a esta asignación de directiva.

### <a name="update-assignment-with-exclusion"></a>Actualización de la asignación con la exclusión

1. Seleccione **Asignaciones** en **CREACIÓN** en el lado izquierdo de la página de Azure Policy.
2. Examine todas las asignaciones de directiva y abra la asignación *Require SQL Server version 12.0* (Requerir SQL Server 12.0).
3. Para establecer la **exclusión**, haga clic en los puntos suspensivos y seleccione el grupo de recursos que quiere excluir, *SQLServers_Excluded*, en este ejemplo.

   ![Solicitud de exclusión](media/create-manage-policy/request-exclusion.png)

   > [!NOTE]
   > Dependiendo de la directiva y de su efecto, es posible que la exclusión también se conceda a los recursos específicos dentro de un grupo de recursos dentro del ámbito de la asignación. Dado que se usó un efecto **Denegar** en este tutorial, no tendría sentido establecer la exclusión en un recurso concreto que ya existe.

4. Haga clic en **Seleccionar** y después en **Guardar**.

En esta sección, ha resuelto la denegación del intento para crear un servidor de SQL Server mediante la creación de una exclusión de un grupo de recursos único.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si terminó de trabajar con los recursos de este tutorial, use los pasos siguientes para eliminar todas las asignaciones y definiciones creadas anteriormente:

1. Seleccione **Definiciones** (o **Asignaciones** si intenta eliminar una asignación) en **CREACIÓN** en el lado izquierdo de la página de Azure Policy.
2. Busque la nueva definición de iniciativa o directiva (o asignación) que quiere quitar.
3. Haga clic con el botón derecho en la fila o seleccione los puntos suspensivos al final de la definición (o asignación) y elija **Eliminar definición** (o **Eliminar asignación** ).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha realizado satisfactoriamente lo siguiente:

> [!div class="checklist"]
> - Ha asignado una directiva para aplicar una condición a los recursos que se creen en el futuro
> - Ha creado y asignado una definición de iniciativa para realizar un seguimiento del cumplimiento para varios recursos
> - Ha resuelto un recurso que no cumplía o que se había denegado
> - Ha implementado una nueva directiva en toda la organización

Para más información sobre las estructuras de las definiciones de directiva, consulte este artículo:

> [!div class="nextstepaction"]
> [Estructura de definición de Azure Policy](policy-definition.md)