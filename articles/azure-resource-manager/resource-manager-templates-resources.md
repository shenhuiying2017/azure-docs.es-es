---
title: Estructura y sintaxis de las plantillas de Azure Resource Manager | Microsoft Docs
description: Describe la estructura y las propiedades de plantillas de Azure Resource Manager mediante la sintaxis declarativa de JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2017
ms.author: tomfitz
ms.openlocfilehash: 89e4b52e7d306bd495c426bcf775f59d0f30eb55
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Sección de recursos de plantillas de Azure Resource Manager

En la sección de recursos, se define que los recursos se implementan o se actualizan. Esta sección se puede complicar porque debe comprender los tipos que va a implementar para proporcionar los valores adecuados.

## <a name="available-properties"></a>Propiedades disponibles

Defina recursos con la siguiente estructura:

```json
"resources": [
  {
      "condition": "<boolean-value-whether-to-deploy>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": "<number-of-iterations>",
          "mode": "<serial-or-parallel>",
          "batchSize": "<number-to-deploy-serially>"
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--- |:--- |:--- |
| condition | Sin  | Valor booleano que indica si el recurso se implementa. |
| apiVersion |Sí |Versión de la API de REST que debe usar para crear el recurso. |
| Tipo |Sí |Tipo de recurso. Este valor es una combinación del espacio de nombres del proveedor de recursos y el tipo de recurso (como **Microsoft.Storage/storageAccounts**). |
| Nombre |Sí |Nombre del recurso. El nombre debe cumplir las restricciones de componente URI definidas en RFC3986. Además, los servicios de Azure que exponen el nombre del recurso a partes externas validan el nombre para asegurarse de que no es un intento de suplantar otra identidad. |
| location |Varía |Ubicaciones geográficas compatibles del recurso proporcionado. Puede seleccionar cualquiera de las ubicaciones disponibles, pero normalmente tiene sentido elegir aquella que esté más cerca de los usuarios. Normalmente, también tiene sentido colocar los recursos que interactúan entre sí en la misma región. La mayoría de los tipos de recursos requieren una ubicación, pero algunos (por ejemplo, una asignación de roles) no la necesitan. |
| etiquetas |Sin  |Etiquetas asociadas al recurso. Aplique etiquetas para organizar de forma lógica los recursos en su suscripción. |
| comentarios |Sin  |Notas para documentar los recursos de la plantilla |
| copia |Sin  |Si se necesita más de una instancia, el número de recursos que se crearán. El modo predeterminado es paralelo. Si no desea que todos los recursos se implementen al mismo tiempo, especifique el modo serie. Para más información, consulte [Creación de varias instancias de recursos en Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Sin  |Recursos que se deben implementar antes de implementar este. Resource Manager evalúa las dependencias entre recursos y los implementa en su orden correcto. Cuando no hay recursos dependientes entre sí, se implementan en paralelo. El valor puede ser una lista separada por comas de nombres de recursos o identificadores de recursos únicos. Solo los recursos de lista que se implementan en esta plantilla. Deben existir los recursos que no estén definidos en esta plantilla. Evite agregar dependencias innecesarias, ya que pueden ralentizar la implementación y crear dependencias circulares. Para obtener instrucciones sobre la configuración de dependencias, consulte [Definición de dependencias en plantillas de Azure Resource Manager](resource-group-define-dependencies.md). |
| propiedades |Sin  |Opciones de configuración específicas de recursos. Los valores de las propiedades son exactamente los mismos valores que se especifican en el cuerpo de la solicitud de la operación de API de REST (método PUT) para crear el recurso. También puede especificar una matriz de copia para crear varias instancias de una propiedad. |
| resources |Sin  |Recursos secundarios que dependen del recurso que se está definiendo. Proporcione solo tipos de recursos que permita el esquema del recurso principal. El tipo completo del recurso secundario incluye el tipo del recurso principal, por ejemplo, **Microsoft.Web/sites/extensions**. La dependencia del recurso principal no está implícita. Debe definirla explícitamente. |

## <a name="resource-specific-values"></a>Valores específicos de los recursos

Los valores **apiVersion**, **type** y **properties** son diferentes para cada tipo de recurso. Para determinar los valores de estas propiedades, consulte la [referencia de plantillas](/azure/templates/).

## <a name="resource-names"></a>Nombres de recurso
Por lo general, trabaja con tres tipos de nombres de recursos en Resource Manager:

* Nombres de recurso que deben ser únicos.
* Nombres de recursos que no deben ser únicos, pero elige proporcionarles un nombre que pueda ayudarle a identificarlos.
* Nombres de recurso que pueden ser genéricos.

### <a name="unique-resource-names"></a>Nombres de recurso únicos
Debe dar un nombre de recurso único para cualquier tipo de recurso que tenga un punto de conexión de acceso a datos. Entre los tipos de recursos comunes que requieren un nombre único se incluyen los siguientes:

* Azure Storage<sup>1</sup> 
* Característica Web Apps de Azure App Service
* SQL Server
* Azure Key Vault
* Azure Redis Cache
* Azure Batch
* Administrador de tráfico de Azure
* Azure Search
* HDInsight de Azure

<sup>1</sup> Los nombres de las cuentas de almacenamiento deben estar en minúsculas, tener 24 caracteres o menos y no incluir guiones.

Al establecer el nombre, puede crear un nombre único de forma manual o usar la función [uniqueString()](resource-group-template-functions-string.md#uniquestring) para generarlo. También puede que quiera agregar un prefijo o sufijo al resultado **uniqueString**. Modificar el nombre único podría ayudarlo a identificar más fácilmente el tipo de recurso a partir del nombre. Por ejemplo, puede generar un nombre único para una cuenta de almacenamiento si usa la variable siguiente:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Nombres de recurso para la identificación
Algunos tipos de recursos a los que podría querer asignar un nombre, pero no es necesario que los nombres sean únicos. En el caso de estos tipos de recursos, puede proporcionar un nombre que identifique tanto el contexto del recurso como el tipo de recurso.

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

### <a name="generic-resource-names"></a>Nombres de recurso genéricos
En los tipos de recurso a los que tiene acceso mayoritariamente por medio de otro recurso, puede usar un nombre genérico que esté codificado de forma rígida en la plantilla. Por ejemplo, puede establecer un nombre genérico estándar para reglas de firewall en un servidor SQL:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>La ubicación
Al implementar una plantilla, debe proporcionar una ubicación para cada recurso. Se admiten diferentes tipos de recursos en diferentes ubicaciones. Para ver una lista de las ubicaciones que están disponibles con su suscripción para un tipo de recurso en particular, use Azure PowerShell o la CLI de Azure. 

En el ejemplo siguiente se usa PowerShell para obtener las ubicaciones para el tipo de recurso `Microsoft.Web\sites`:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

En el ejemplo siguiente se usa la CLI de Azure 2.0 para obtener las ubicaciones para el tipo de recurso `Microsoft.Web\sites`:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

Después de determinar las ubicaciones admitidas de los recursos, debe establecer esa ubicación en la plantilla. La manera más fácil de establecer este valor consiste en crear un grupo de recursos en una ubicación que admita los tipos de recursos y establecer cada ubicación en `[resourceGroup().location]`. Puede volver a implementar la plantilla en grupos de recursos de diferentes ubicaciones y no cambie ningún valor en la plantilla ni en los parámetros. 

En el ejemplo siguiente se muestra una cuenta de almacenamiento que está implementada en la misma ubicación que el grupo de recursos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Si tiene que codificar la ubicación en la plantilla, indique el nombre de una de las regiones admitidas. En el ejemplo siguiente se muestra una cuenta de almacenamiento que está siempre implementada en Centro-Norte de EE. UU:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="tags"></a>Etiquetas
[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

### <a name="add-tags-to-your-template"></a>Agregar etiquetas a la plantilla

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>Recursos secundarios

En cada tipo recurso puede definir también una matriz de recursos secundarios. Los recursos secundarios son recursos que solo existen en el contexto de otro recurso. Por ejemplo, una base de datos SQL no puede existir sin un servidor SQL, por lo que la base de datos es un elemento secundario del servidor. Puede definir la base de datos dentro de la definición del servidor.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Cuando está anidado, el tipo se establece en `databases`, pero el tipo de recurso completo es `Microsoft.Sql/servers/databases`. No proporciona `Microsoft.Sql/servers/`, porque lo adopta del tipo de recurso primario. El nombre del recurso secundario se establece en `exampledatabase`, pero el nombre completo incluye el primario. No proporciona `exampleserver`, porque lo adopta del recurso primario.

El formato del tipo de recurso secundario es: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

El formato del nombre de recurso secundario es: `{parent-resource-name}/{child-resource-name}`

Sin embargo, no es necesario definir la base de datos en el servidor. Puede definir el recurso secundario en el nivel superior. Este enfoque puede usarse si el recurso primario no está implementado en la misma plantilla o si desea usar `copy` para crear varios recursos secundarios. En este enfoque, es necesario proporcionar el tipo de recurso completo, así como incluir el nombre del recurso primario en el del secundario.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Al construir una referencia completa a un recurso, el orden para combinar los segmentos a partir del tipo y el nombre no es simplemente una concatenación de los dos.  En su lugar, después del espacio de nombres, use una secuencia de pares *tipo/nombre* de menos a más específico:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Por ejemplo: 

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` es correcto `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` no es correcto

## <a name="recommendations"></a>Recomendaciones
La información siguiente puede ser útil cuando se trabaja con recursos:

* Para ayudar a otros colaboradores a comprender el propósito del recurso, especifique **comments** para cada recurso de la plantilla:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Si usa un *punto de conexión público* en la plantilla (como un punto de conexión público de Azure Blob Storage), *no codifique de forma rígida* el espacio de nombres. Use la función **reference** para recuperar dinámicamente el espacio de nombres. Puede usar este enfoque para implementar la plantilla en diversos entornos de espacios de nombres públicos sin cambiar manualmente el punto de conexión de la plantilla. Establezca la versión de API en la misma versión que usa para la cuenta de almacenamiento de la plantilla:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Si la cuenta de almacenamiento se implementa en la misma plantilla que se está creando, no necesita especificar el espacio de nombres del proveedor cuando haga referencia al recurso. En el siguiente ejemplo se muestra la sintaxis simplificada:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Si tiene otros valores en la plantilla que estén configurados para usar un espacio de nombres público, cámbielos para que reflejen la misma función **reference**. Por ejemplo, puede establecer la propiedad **storageUri** del perfil de diagnóstico de la máquina virtual:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   También puede hacer referencia a una cuenta de almacenamiento existente que se encuentra en un grupo de recursos distinto:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Asigne direcciones IP públicas a una máquina virtual solo cuando lo requiera una aplicación. Para conectarse a una máquina virtual (VM) para la depuración o con fines administrativos, use reglas NAT de entrada, una puerta de enlace de red o Jumpbox.
   
     Para obtener más información sobre cómo conectarse a máquinas virtuales, consulte:
   
   * [Ejecución de máquinas virtuales para una arquitectura de n niveles en Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Configuración de acceso a WinRM para máquinas virtuales en Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Habilitación del acceso externo a la máquina virtual mediante Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Habilitación del acceso externo a la máquina virtual mediante PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Habilitación del acceso externo a la máquina virtual Linux mediante la CLI de Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* La propiedad **domainNameLabel** para las direcciones IP públicas debe ser única. El valor **domainNameLabel** debe contener entre 3 y 63 caracteres y seguir las reglas especificadas por esta expresión regular: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Como la función **uniqueString** genera una cadena de 13 caracteres, el parámetro **dnsPrefixString** se limita a no más de 50 caracteres:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Cuando agrega una contraseña a una extensión de script personalizada, use la propiedad **commandToExecute** en la propiedad **protectedSettings**:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Para garantizar que los secretos se cifran cuando se transmiten como parámetros a máquinas virtuales y extensiones, use la propiedad **protectedSettings** de las extensiones pertinentes.
   > 
   > 


## <a name="next-steps"></a>pasos siguientes
* Para ver plantillas completas de muchos tipos diferentes de soluciones, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).
* Para obtener información detallada sobre las funciones que se pueden usar dentro de una plantilla, consulte [Funciones de plantilla de Azure Resource Manager](resource-group-template-functions.md).
* Para combinar varias plantillas en la implementación, consulte [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).
* Puede que necesite usar los recursos que existen dentro de un grupo de recursos diferente. Este escenario es habitual al trabajar con cuentas de almacenamiento o redes virtuales que se comparten entre varios grupos de recursos. Para obtener más información, vea la [función resourceId](resource-group-template-functions-resource.md#resourceid).
* Para obtener información sobre las restricciones de los nombres de recurso, consulte [Recommended naming conventions for Azure resources](../guidance/guidance-naming-conventions.md)(Convenciones de nomenclatura recomendadas para los recursos de Azure).