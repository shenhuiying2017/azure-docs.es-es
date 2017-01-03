---
title: Directiva de Azure Resource Manager | Microsoft Docs
description: "Describe cómo usar la directiva del Administrador de recursos de Azure para evitar infracciones en distintos ámbitos como, por ejemplo, la suscripción, los grupos de recursos o los recursos individuales."
services: azure-resource-manager
documentationcenter: na
author: ravbhatnagar
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2016
ms.author: gauravbh;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: bdc759341e1f9707ddf688512249c3297d85c29b


---
# <a name="use-policy-to-manage-resources-and-control-access"></a>Uso de directivas para administrar los recursos y controlar el acceso
El Administrador de recursos de Azure permite controlar el acceso mediante directivas personalizadas. Con las directivas, puede impedir que los usuarios de su organización dividan las convenciones que se necesitan para administrar los recursos de esta. 

Se crean definiciones de directivas que describen las acciones o los recursos que se han denegado específicamente. Esas definiciones de directivas se asignan en el ámbito deseado, como la suscripción, el grupo de recursos o un recurso individual. Todos los recursos secundarios heredan las directivas. De este modo, si una directiva se aplica a un grupo de recursos, será aplicable a todos los recursos de dicho grupo de recursos.

En este artículo se explica la estructura básica del lenguaje de definición de directivas que se puede usar para crear directivas. A continuación, se describe cómo puede aplicar estas directivas en ámbitos diferentes.

## <a name="how-is-it-different-from-rbac"></a>¿En qué se diferencia de RBAC?
Existen algunas diferencias importantes entre el control de acceso basado en roles y las directivas, pero lo primero que hay que entender es que las directivas y RBAC funcionan conjuntamente. Para usar las directivas, debe autenticarse a través de RBAC. A diferencia de RBAC, la directiva es un sistema que permite de manera predeterminada y niega explícitamente. 

RBAC se centra en las acciones que un **usuario** puede realizar en distintos ámbitos. Por ejemplo, un usuario determinado se agrega al rol de colaborador para un grupo de recursos en el ámbito deseado, por lo que el usuario puede realizar cambios en ese grupo de recursos. 

La directiva se centra en acciones de **recursos** en varios ámbitos. Por ejemplo, mediante directivas, puede controlar los tipos de recursos que se pueden aprovisionar o restringir las ubicaciones en las que se pueden aprovisionar los recursos.

## <a name="common-scenarios"></a>Escenarios comunes
Un escenario común es exigir etiquetas departamentales para contracargos. Es posible que una organización solo quiera permitir operaciones cuando se asocie el centro de costo correcto; en caso contrario, denegará la solicitud. Esta directiva ayuda a aplicar el cargo al centro de costo correcto por las operaciones realizadas.

Otro escenario común es que la organización que desee controlar las ubicaciones en las que se crean los recursos. O bien, puede que desee controlar el acceso a los recursos al permitir el aprovisionamiento solo de determinados tipos de recursos.

Del mismo modo, una organización puede controlar el catálogo de servicios o aplicar las convenciones de nombre deseadas para los recursos.

Con las directivas, estos escenarios pueden conseguirse fácilmente.

## <a name="policy-definition-structure"></a>Estructura de definición de directiva
La definición de la directiva se crea mediante JSON. Consta de uno o varios operadores lógicos o condicionales que definen las acciones, así como un efecto que indica lo que ocurre cuando se cumplen las condiciones. El esquema está publicado en [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

Básicamente, una directiva contiene los siguientes elementos:

**Operadores lógicos o condicionales**: un conjunto de condiciones que se pueden manipular mediante un conjunto de operadores lógicos.

**Efecto**: lo que ocurre cuando se cumple la condición (si se deniega o se audita). Un efecto de auditoría emitirá un registro de servicio de eventos de advertencia. Por ejemplo, un administrador puede crear una directiva que genere una auditoría si alguien crea una máquina virtual de gran tamaño. Más adelante, el administrador puede revisar los registros.

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }

## <a name="policy-evaluation"></a>Evaluación de directiva
Las directivas se evalúan cuando se crean recursos. Para la implementación de plantilla, la directiva se evaluará durante la creación de cada uno de los recursos de la plantilla. 

> [!NOTE]
> Actualmente, la directiva no evalúa los tipos de recursos que no se admiten etiquetas, variante y ubicación, como el tipo de recurso Microsoft.Resources/deployments. Esta compatibilidad se agregará en el futuro. Para evitar problemas de compatibilidad con versiones anteriores, debe especificar explícitamente el tipo al crear directivas. Por ejemplo, una directiva de etiqueta que no especifique tipos se aplicará a todos los tipos. En ese caso, una implementación de plantilla puede dar error si hay un recurso anidado que no admite etiquetas y el tipo de recurso de implementación se ha agregado a la evaluación de directivas. 
> 
> 

## <a name="logical-operators"></a>Operadores lógicos
A continuación, se muestran los operadores lógicos admitidos junto con la sintaxis:

| Nombre del operador | Sintaxis |
|:--- |:--- |
| Not |"not" : {&lt;condición u operador&gt;} |
| y |"allOf" : [ {&lt;condición u operador&gt;},{&lt;condición u operador&gt;}] |
| o |"anyOf" : [ {&lt;condición u operador &gt;},{&lt;condición u operador&gt;}] |

El Administrador de recursos permite especificar una lógica compleja en su directiva a través de operadores anidados. Por ejemplo, puede denegar la creación de recursos en una ubicación determinada para un tipo de recurso especificado. En este tema se muestra un ejemplo de operadores anidados.

## <a name="conditions"></a>Condiciones
Una condición evalúa si un **campo** o un **origen** cumple determinados criterios. La sintaxis y los nombres de condición admitidos son los siguientes:

| Nombre de la condición | Sintaxis |
|:--- |:--- |
| Equals |"equals" : "&lt;valor&gt;" |
| Like |"like" : "&lt;valor&gt;" |
| Contains |"contains" : "&lt;valor&gt;" |
| In |"in" : [ "&lt;valor1&gt;","&lt;valor&gt;" ] |
| ContainsKey |"containsKey" : "&lt;nombreDeClave&gt;" |
| Exists |"exists" : "&lt;booleano&gt;" |

### <a name="fields"></a>Fields
Las condiciones se crean mediante el uso de campos y orígenes. Un campo representa las propiedades de la carga de solicitud de recursos que se usa para describir el estado del recurso. Un origen representa las características de la propia solicitud. 

Estos son los campos y orígenes admitidos:

Campos: **name**, **kind**, **type**, **location**, **tags**, **tags.*** y **property alias**. 

### <a name="property-aliases"></a>Alias de la propiedad
El alias de propiedad es un nombre que se puede usar en una definición de directiva para acceder a las propiedades específicas del tipo de recursos, como la configuración y las SKU. Funciona en todas las versiones de API donde existe la propiedad. Los alias se pueden recuperar mediante la API de REST (en el futuro se agregará compatibilidad con PowerShell):

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01

En el ejemplo siguiente se muestra una definición de un alias. Como puede ver, un alias define rutas de acceso en distintas versiones de API, aunque se cambie el nombre de la propiedad. 

    "aliases": [
        {
          "name": "Microsoft.Storage/storageAccounts/sku.name",
          "paths": [
            {
              "path": "properties.accountType",
              "apiVersions": [
                "2015-06-15",
                "2015-05-01-preview"
              ]
            },
            {
              "path": "sku.name",
              "apiVersions": [
                "2016-01-01"
              ]
            }
          ]
        }
    ]

Actualmente, los alias admitidos son:

| Nombre de alias | Description |
| --- | --- |
| {resourceType}/sku.name |Los tipos de recursos que se admiten son: Microsoft.Compute/virtualMachines,<br />Microsoft.Storage/storageAccounts<br />Microsoft.Web/serverFarms<br /> Microsoft.Scheduler/jobcollections/<br />Microsoft.DocumentDB/databaseAccounts/<br />Microsoft.Cache/Redis<br />Microsoft.CDN/profiles |
| {resourceType}/sku.family |El tipo de recurso admitido es Microsoft.Cache/Redis |
| {resourceType}/sku.capacity |El tipo de recurso admitido es Microsoft.Cache/Redis |
| Microsoft.Compute/virtualMachines/imagePublisher | |
| Microsoft.Compute/virtualMachines/imageOffer | |
| Microsoft.Compute/virtualMachines/imageSku | |
| Microsoft.Compute/virtualMachines/imageVersion | |
| Microsoft.Cache/Redis/enableNonSslPort | |
| Microsoft.Cache/Redis/shardCount | |
| Microsoft.SQL/servers/version | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveId | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveName | |
| Microsoft.SQL/servers/databases/edition | |
| Microsoft.SQL/servers/databases/elasticPoolName | |
| Microsoft.SQL/servers/elasticPools/dtu | |
| Microsoft.SQL/servers/elasticPools/edition | |

Actualmente, la directiva solo funciona en las solicitudes PUT. 

## <a name="effect"></a>Efecto
La directiva admite tres tipos de efecto: **deny**, **audit** y **append**. 

* Deny genera un evento en el registro de auditoría y se produce un error en la solicitud
* Audit genera un evento en el registro de auditoría pero no producirá un error en la solicitud
* Append agrega el conjunto de campos definido a la solicitud 

En el caso de **append**, debe proporcionar los detalles tal y como se muestra a continuación:

    ....
    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

El valor puede ser una cadena o un objeto con formato JSON. 

## <a name="policy-definition-examples"></a>Ejemplos de definición de directiva
Ahora, analicemos cómo definir la directiva para lograr los escenarios anteriores.

### <a name="chargeback-require-departmental-tags"></a>Contracargo: requiere etiquetas departamentales.
La directiva siguiente deniega todas las solicitudes que no tengan una etiqueta que contiene la clave "costCenter".

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

La siguiente directiva anexa la etiqueta costCenter con un valor predefinido si no hay ninguna etiqueta. 

    {
      "if": {
        "field": "tags",
        "exists": "false"
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags",
            "value": {"costCenter":"myDepartment" }
          }
        ]
      }
    }

La siguiente directiva anexa la etiqueta costCenter con un valor predefinido cuando dicha etiqueta no se encuentra, pero sí hay otras. 

    {
      "if": {
        "allOf": [
          {
            "field": "tags",
            "exists": "true"
          },
          {
            "field": "tags.costCenter",
            "exists": "false"
          }
        ]

      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags.costCenter",
            "value": "myDepartment"
          }
        ]
      }
    }


### <a name="geo-compliance-ensure-resource-locations"></a>Cumplimiento geográfico: garantiza las ubicaciones de los recursos.
En el ejemplo siguiente se muestra una directiva que denegará las solicitudes cuya ubicación no sea Europa del Norte o Europa Occidental.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="service-curation-select-the-service-catalog"></a>Selección de servicio: selecciona el catálogo de servicios.
En el siguiente ejemplo se muestra una directiva que solo permite acciones en los servicios de tipo Microsoft.Resources/\*, Microsoft.Compute/\*, Microsoft.Storage/\* y Microsoft.Network/\*. Todo lo demás se deniega.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "field" : "type",
              "like" : "Microsoft.Resources/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Compute/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Storage/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="use-approved-skus"></a>Uso de SKU aprobadas
En el ejemplo siguiente se muestra el uso del alias de propiedad para restringir las SKU. En el ejemplo, solo se aprueba el uso de Standard_LRS y Standard_GRS para cuentas de almacenamiento.

    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/sku.name",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }


### <a name="naming-convention"></a>Convención de nomenclatura
En el ejemplo siguiente se muestra el uso de caracteres comodín compatibles con la condición "like". La condición indica que se denegará la solicitud si el nombre coincide con el patrón mencionado (namePrefix\*nameSuffix).

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="tag-requirement-just-for-storage-resources"></a>Requisito de etiqueta solo para recursos de almacenamiento
En el ejemplo siguiente se muestra cómo anidar operadores lógicos para que requieran una etiqueta de aplicación solo para los recursos de almacenamiento.

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## <a name="create-and-assign-a-policy"></a>Creación y asignación de directivas
Para aplicar una directiva, hay que crear una definición de directiva y, luego, aplicarla en un ámbito. 

### <a name="rest-api"></a>API de REST
Puede crear una directiva con la [API de REST para definiciones de directiva](https://docs.microsoft.com/rest/api/resources/policydefinitions). La API de REST permite crear y eliminar definiciones de directiva, así como recuperar información sobre las definiciones existentes.

Para crear una directiva, siga estos pasos:

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Como versión de la API, use *2016-04-01*. Incluya un cuerpo de solicitud similar al ejemplo siguiente:

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "name":"testdefinition"
    }

Puede aplicar la definición de la directiva en el ámbito deseado a través de la [API de REST para asignaciones de directivas](https://docs.microsoft.com/rest/api/resources/policyassignments). La API de REST permite crear y eliminar asignaciones de directiva, así como recuperar información sobre las asignaciones existentes.

Para crear una nueva asignación de directiva, ejecute lo siguiente:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policy-assignment} es el nombre de la asignación de directiva. Como versión de la API, use *2016-04-01*. 

Con un cuerpo de solicitud similar al ejemplo siguiente:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "name":"VMPolicyAssignment"
    }

### <a name="powershell"></a>PowerShell
Puede crear una nueva definición de directiva mediante el cmdlet New-AzureRmPolicyDefinition. En los ejemplos siguientes se crea una directiva para permitir los recursos solo en Europa del Norte y Europa occidental.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'            

La salida de la ejecución se almacena en el objeto $policy y se puede usar posteriormente durante la asignación de la directiva. Para el parámetro de directiva, también se puede proporcionar la ruta de acceso a un archivo .json que contenga la directiva en lugar de especificar la directiva en línea.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain     regions" -Policy "path-to-policy-json-on-disk"

Puede aplicar la directiva en el ámbito que quiera mediante el cmdlet New-AzureRmPolicyAssignment:

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Aquí, $policy es el objeto de la directiva que se devolvió como resultado de ejecutar el cmdlet New-AzureRmPolicyDefinition. En este caso, el ámbito es el nombre del grupo de recursos que especifique.

Para quitar una asignación de directiva, use lo siguiente:

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Puede obtener, cambiar o quitar definiciones de la directiva mediante los cmdlets Get-AzureRmPolicyDefinition, Set-AzureRmPolicyDefinition y Remove-AzureRmPolicyDefinition respectivamente.

De forma similar, puede obtener, cambiar o quitar las asignaciones de directivas mediante los cmdlets Get-AzureRmPolicyAssignment, Set-AzureRmPolicyAssignment y Remove-AzureRmPolicyAssignment respectivamente.

### <a name="azure-cli"></a>CLI de Azure
Puede crear una definición de directiva mediante la CLI de Azure con el comando de definición de directiva. En los ejemplos siguientes se crea una directiva para permitir los recursos solo en Europa del Norte y Europa occidental.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'    


Se puede proporcionar la ruta de acceso a un archivo .json que contenga la directiva, en lugar de especificarla en línea.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"

Puede aplicar la directiva en el ámbito que quiera mediante el comando de asignación de directiva:

    azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/<policy-name> --scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

En este caso, el ámbito es el nombre del grupo de recursos que especifique. Si no se conoce el valor del parámetro policy-definition-id, se puede obtener con la CLI de Azure. 

    azure policy definition show <policy-name>

Para quitar una asignación de directiva, use lo siguiente:

    azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Puede obtener, cambiar o quitar las definiciones de directiva utilizando los comandos de visualización, establecimiento y eliminación de definiciones de directiva, respectivamente.

De forma similar, puede obtener, cambiar o quitar asignaciones de directiva utilizando los comandos de visualización y eliminación de asignaciones de directiva.

## <a name="policy-audit-events"></a>Eventos de auditoría de directiva
Después de aplicar la directiva, puede empezar a ver los eventos relacionados con ella. Puede ir al portal, o bien usar PowerShell o la CLI de Azure para obtener estos datos. 

### <a name="powershell"></a>PowerShell
Para ver todos los eventos relacionados con el efecto de denegación, puede usar el siguiente comando de PowerShell:

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

Para ver todos los eventos relacionados con el efecto de auditoría, puede usar el siguiente comando:

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 

### <a name="azure-cli"></a>CLI de Azure
Para ver todos los eventos de un grupo de recursos relacionados con el efecto de denegación, puede usar el siguiente comando de la CLI de Azure:

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/deny/action\")"

Para ver todos los eventos relacionados con el efecto de auditoría, puede usar el siguiente comando de la CLI de Azure:

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/audit/action\")"

## <a name="view-a-policy"></a>Visualización de directivas
Use PowerShell, la CLI de Azure o la API de REST para ver una directiva. Puede que tenga que ver una directiva después de un error de implementación, y quiere ver la regla que ha denegado dicha implementación. El mensaje de error incluye un identificador de la definición de directiva.

### <a name="powershell"></a>PowerShell
Para obtener una directiva, use el siguiente cmdlet:

    (Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}").Properties.policyRule | ConvertTo-Json

Esta operación devuelve el JSON de la definición de directiva.

### <a name="azure-cli"></a>CLI de Azure
Para obtener una directiva, use el siguiente comando:

    azure policy definition show {definition-name} --json

### <a name="rest-api"></a>API de REST
Para obtener una directiva, use la operación [Obtener definición de directiva](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get).

## <a name="next-steps"></a>Pasos siguientes
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).




<!--HONumber=Nov16_HO3-->


