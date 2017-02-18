---
title: "Creación de plantillas para implementaciones de Azure | Microsoft Docs"
description: Describe la estructura y las propiedades de plantillas de Azure Resource Manager mediante la sintaxis declarativa de JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 52fe8e3ce0c9c94c918818784fd735b5a6486ed8


---
# <a name="authoring-azure-resource-manager-templates"></a>Creación de plantillas del Administrador de recursos de Azure
En este tema se describe la estructura de una plantilla de Azure Resource Manager. Presenta las distintas secciones de una plantilla y las propiedades que están disponibles en esas secciones. La plantilla consta de JSON y expresiones que puede usar para generar valores para su implementación. 

Para ver la plantilla de los recursos que ya ha implementado, consulte [Exportación de plantillas de Azure Resource Manager desde recursos existentes](resource-manager-export-template.md). Para obtener instrucciones sobre cómo crear una plantilla, consulte [Tutorial de la plantilla de Resource Manager](resource-manager-template-walkthrough.md). Para más recomendaciones sobre creación de platillas, consulte [Procedimientos recomendados para crear plantillas de Azure Resource Manager](resource-manager-template-best-practices.md).

Un buen editor JSON puede simplificar la tarea de creación de plantillas. Para información sobre el uso de Visual Studio con las plantillas, consulte [Creación e implementación de grupos de recursos de Azure mediante Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Para información sobre el uso de Visual Studio Code, consulte [Trabajo con plantillas de Azure Resource Manager en Visual Studio Code](resource-manager-vs-code.md).

Limite el tamaño de la plantilla a 1 MB y cada archivo de parámetros a 64 KB. El límite de 1 MB se aplica al estado final de la plantilla una vez se ha ampliado con definiciones de recursos iterativas y los valores de variables y parámetros. 

## <a name="template-format"></a>Formato de plantilla
En la estructura más simple, una plantilla contiene los siguientes elementos:

```json
{
   "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "",
   "parameters": {  },
   "variables": {  },
   "resources": [  ],
   "outputs": {  }
}
```

| Nombre del elemento | Obligatorio | Descripción |
|:--- |:--- |:--- |
| $schema |Sí |Ubicación del archivo de esquema JSON que describe la versión del idioma de la plantilla. Use la dirección URL que se muestra en el ejemplo anterior. |
| contentVersion |Sí |Versión de la plantilla (por ejemplo, 1.0.0.0). Puede especificar cualquier valor para este elemento. Al implementar los recursos con la plantilla, este valor se puede usar para asegurarse de que se está usando la plantilla correcta. |
| parameters |No |Valores que se proporcionan cuando se ejecuta la implementación para personalizar la implementación de recursos. |
| variables |No |Valores que se usan como fragmentos JSON en la plantilla para simplificar expresiones de idioma de la plantilla. |
| resources |Sí |Tipos de servicios que se implementan o actualizan en un grupo de recursos. |
| outputs |No |Valores que se devuelven después de la implementación. |

Examinaremos las secciones de la plantilla con mayor detenimiento más adelante en este tema.

## <a name="expressions-and-functions"></a>Expresiones y funciones
La sintaxis básica de la plantilla es JSON. Sin embargo, las expresiones y funciones amplían el JSON que está disponible en la plantilla. Con las expresiones, se crean valores que no son valores literales estrictos. Las expresiones se incluyen entre los corchetes `[` y `]` y se evalúan cuando se implementa la plantilla. Pueden aparecer expresiones en cualquier lugar de un valor de cadena JSON y devolver siempre otro valor JSON. Si necesita usar una cadena literal que comienza por un corchete `[`, debe usar dos corchetes `[[`.

Normalmente, se usan expresiones con funciones para realizar operaciones con el fin de configurar la implementación. Al igual que en JavaScript, las llamadas de función tienen el formato **functionName(arg1,arg2,arg3)**. Se hace referencia a las propiedades mediante los operadores dot e [index] .

En el ejemplo siguiente se muestra cómo utilizar algunas de las funciones para la construcción de valores:

```json
"variables": {
   "location": "[resourceGroup().location]",
   "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
   "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

Para obtener la lista completa de las funciones de plantilla, consulte [Funciones de la plantilla del Administrador de recursos de Azure](resource-group-template-functions.md). 

## <a name="parameters"></a>parameters
En la sección de parámetros de la plantilla, especifique los valores que el usuario puede introducir al implementar los recursos. Estos valores de parámetros permiten personalizar la implementación al proporcionar valores que son específicos para un entorno concreto (por ejemplo, desarrollo, prueba y producción). No tiene que especificar parámetros en la plantilla, pero sin parámetros la plantilla implementaría siempre los mismos recursos con los mismos nombres, ubicaciones y propiedades.

Puede usar estos valores de parámetros a lo largo de la plantilla para establecer valores para los recursos implementados. Solo los parámetros declarados en la sección de parámetros se pueden usar en otras secciones de la plantilla.

Defina recursos con la estructura siguiente:

```json
"parameters": {
   "<parameter-name>" : {
     "type" : "<type-of-parameter-value>",
     "defaultValue": "<default-value-of-parameter>",
     "allowedValues": [ "<array-of-allowed-values>" ],
     "minValue": <minimum-value-for-int>,
     "maxValue": <maximum-value-for-int>,
     "minLength": <minimum-length-for-string-or-array>,
     "maxLength": <maximum-length-for-string-or-array-parameters>,
     "metadata": {
         "description": "<description-of-the parameter>" 
     }
   }
}
```

| Nombre del elemento | Obligatorio | Description |
|:--- |:--- |:--- |
| parameterName |Sí |Nombre del parámetro. Debe ser un identificador válido de JavaScript. |
| type |Sí |Tipo del valor del parámetro. Consulte la lista de tipos permitidos después de esta tabla. |
| defaultValue |No |Valor predeterminado del parámetro, si no se proporciona ningún valor. |
| allowedValues |No |Matriz de valores permitidos para el parámetro para asegurarse de que se proporciona el valor correcto. |
| minValue |No |El valor mínimo de parámetros de tipo int, este valor es inclusivo. |
| maxValue |No |El valor máximo de parámetros de tipo int, este valor es inclusivo. |
| minLength |No |La longitud mínima de los parámetros de tipo cadena, secureString y matriz, este valor es inclusivo. |
| maxLength |No |La longitud máxima de los parámetros de tipo cadena, secureString y matriz, este valor es inclusivo. |
| Description |No |Descripción del parámetro que se muestra a los usuarios de la plantilla mediante la interfaz de plantilla personalizada del portal. |

Los valores y tipos permitidos son los siguientes:

* **cadena**
* **secureString**
* **int**
* **bool**
* **objeto** 
* **secureObject**
* **matriz**

Para especificar un parámetro como opcional, proporcione un defaultValue (puede ser una cadena vacía). 

Si especifica un nombre de parámetro en la plantilla que coincide con un parámetro en el comando para implementar la plantilla, hay una posible ambigüedad sobre los valores proporcionados. Para resolver esta confusión, Resource Manager agrega el postfijo **FromTemplate** al parámetro de plantilla. Por ejemplo, si incluye un parámetro llamado **ResourceGroupName** en la plantilla, entra en conflicto con el parámetro **ResourceGroupName** del cmdlet [New-AzureRmResourceGroupDeployment][deployment2cmdlet]. Durante la implementación, se le pide que proporcione un valor para **ResourceGroupNameFromTemplate**. Por lo general, debe evitar esta confusión no nombrando los parámetros con el mismo nombre que los parámetros utilizados para operaciones de implementación.

> [!NOTE]
> Todas las contraseñas, claves y otros secretos deben utilizar el tipo **secureString** . Si pasa datos confidenciales en un objeto JSON, use el tipo **secureObject**. No se pueden leer los parámetros con los tipos secureString o secureObject después de la implementación de recursos. 
> 
> Por ejemplo, la siguiente entrada del historial de implementación muestra el valor de una cadena y un objeto, pero no de secureString y secureObject.
>
> ![Visualización de los valores de implementación](./media/resource-group-authoring-templates/show-parameters.png)  
>

En el ejemplo siguiente se muestra cómo definir los parámetros.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  },
  "skuName": {
    "type": "string",
    "defaultValue": "F1",
    "allowedValues": [
      "F1",
      "D1",
      "B1",
      "B2",
      "B3",
      "S1",
      "S2",
      "S3",
      "P1",
      "P2",
      "P3",
      "P4"
    ]
  },
  "skuCapacity": {
    "type": "int",
    "defaultValue": 1,
    "minValue": 1
  }
}
```

Para más información acerca de cómo especificar los valores de los parámetros durante la implementación, consulte [Deploy an application with Azure Resource Manager template](resource-group-template-deploy.md)(Implementación de aplicaciones con la plantilla de Azure Resource Manager). 

## <a name="variables"></a>variables
En la sección de variables, se crean valores que pueden usarse en toda la plantilla. Normalmente, las variables se basan en los valores proporcionados desde los parámetros. No es necesario definir las variables, pero a menudo simplifican la plantilla reduciendo expresiones complejas.

Defina variables con la siguiente estructura:

```json
"variables": {
   "<variable-name>": "<variable-value>",
   "<variable-name>": { 
       <variable-complex-type-value> 
   }
}
```

En el ejemplo siguiente se muestra cómo definir una variable que se construye a partir de dos valores de parámetro:

```json
"variables": {
    "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
}
```

En el ejemplo siguiente se muestra una variable que es un tipo JSON complejo y las variables que se construyen a partir de otras variables:

```json
"parameters": {
   "environmentName": {
     "type": "string",
     "allowedValues": [
       "test",
       "prod"
     ]
   }
},
"variables": {
   "environmentSettings": {
     "test": {
       "instancesSize": "Small",
       "instancesCount": 1
     },
     "prod": {
       "instancesSize": "Large",
       "instancesCount": 4
     }
   },
   "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
   "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
   "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
}
```

## <a name="resources"></a>resources
En la sección de recursos, se define que los recursos se implementan o se actualizan. Esta sección se puede complicar porque debe comprender los tipos que va a implementar para proporcionar los valores adecuados. 

Defina recursos con la siguiente estructura:

```json
"resources": [
   {
     "apiVersion": "<api-version-of-resource>",
     "type": "<resource-provider-namespace/resource-type-name>",
     "name": "<name-of-the-resource>",
     "location": "<location-of-resource>",
     "tags": "<name-value-pairs-for-resource-tagging>",
     "comments": "<your-reference-notes>",
     "dependsOn": [
       "<array-of-related-resource-names>"
     ],
     "properties": "<settings-for-the-resource>",
     "copy": {
       "name": "<name-of-copy-loop>",
       "count": "<number-of-iterations>"
     },
     "resources": [
       "<array-of-child-resources>"
     ]
   }
]
```

| Nombre del elemento | Obligatorio | Descripción |
|:--- |:--- |:--- |
| apiVersion |Sí |Versión de la API de REST que debe usar para crear el recurso. |
| type |Sí |Tipo de recurso. Este valor es una combinación del espacio de nombres del proveedor de recursos y el tipo de recurso (como **Microsoft.Storage/storageAccounts**). |
| name |Sí |Nombre del recurso. El nombre debe cumplir las restricciones de componente URI definidas en RFC3986. Además, los servicios de Azure que exponen el nombre del recurso a partes externas validan el nombre para asegurarse de que no es un intento de suplantar otra identidad. Consulte [Check resource name](https://msdn.microsoft.com/library/azure/mt219035.aspx)(Comprobación de nombre de recurso). |
| location |Varía |Ubicaciones geográficas compatibles del recurso proporcionado. Puede seleccionar cualquiera de las ubicaciones disponibles, pero normalmente tiene sentido elegir aquella que esté más cerca de los usuarios. Normalmente, también tiene sentido colocar los recursos que interactúan entre sí en la misma región. La mayoría de los tipos de recursos requieren una ubicación, pero algunos (por ejemplo, una asignación de roles) no la necesitan. |
| etiquetas |No |Etiquetas asociadas al recurso. |
| comentarios |No |Notas para documentar los recursos de la plantilla |
| dependsOn |No |Recursos que se deben implementar antes de implementar este. Resource Manager evalúa las dependencias entre recursos y los implementa en su orden correcto. Cuando no hay recursos dependientes entre sí, se implementan en paralelo. El valor puede ser una lista separada por comas de nombres de recursos o identificadores de recursos únicos. Solo los recursos de lista que se implementan en esta plantilla. Deben existir los recursos que no estén definidos en esta plantilla. Evite agregar dependencias innecesarias, ya que pueden ralentizar la implementación y crear dependencias circulares. Para obtener instrucciones sobre la configuración de dependencias, consulte [Definición de dependencias en plantillas de Azure Resource Manager](resource-group-define-dependencies.md). |
| propiedades |No |Opciones de configuración específicas de recursos. Los valores de las propiedades son exactamente los mismos valores que se especifican en el cuerpo de la solicitud de la operación de API de REST (método PUT) para crear el recurso. Para obtener vínculos a documentación del esquema de recursos o a la API de REST, consulte [Proveedores, regiones, versiones de API y esquemas de Resource Manager](resource-manager-supported-services.md). |
| copia |No |Si se necesita más de una instancia, el número de recursos que se crearán. Para más información, consulte [Creación de varias instancias de recursos en Azure Resource Manager](resource-group-create-multiple.md). |
| resources |No |Recursos secundarios que dependen del recurso que se está definiendo. Proporcione solo tipos de recursos que permita el esquema del recurso principal. El tipo completo del recurso secundario incluye el tipo del recurso principal, por ejemplo, **Microsoft.Web/sites/extensions**. La dependencia del recurso principal no está implícita. Debe definirla explícitamente. |

No resulta tan obvio saber qué valores especificar para **apiVersion**, **tipo** y **ubicación**. Afortunadamente, puede determinar estos valores mediante Azure PowerShell o la CLI de Azure.

Para obtener todos los proveedores de recursos con **PowerShell**, use:

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Desde la lista devuelta, busque los proveedores de recursos que le interesen. Para obtener los tipos de recursos para un proveedor de recursos (por ejemplo, almacenamiento), use:

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes
```

Para obtener las versiones de API para un tipo de recurso (como cuentas de almacenamiento), use:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).ApiVersions
```

Para obtener las ubicaciones compatibles para un tipo de recurso, use:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).Locations
```

Para obtener todos los proveedores de recursos con **CLI de Azure**, use:

```azurecli
azure provider list
```

Desde la lista devuelta, busque los proveedores de recursos que le interesen. Para obtener los tipos de recursos para un proveedor de recursos (por ejemplo, almacenamiento), use:

```azurecli
azure provider show Microsoft.Storage
```

Para obtener las ubicaciones y las versiones de API admitidas, use:

```azurecli
azure provider show Microsoft.Storage --details --json
```

Para más información sobre los proveedores de recursos, consulte [Proveedores, regiones, versiones de API y esquemas de Resource Manager](resource-manager-supported-services.md).

La sección de recursos contiene una matriz de los recursos para implementar. En cada recurso, puede definir también una matriz de recursos secundarios. Por lo tanto, la sección de recursos podría tener una estructura como:

```json
"resources": [
   {
       "name": "resourceA",
   },
   {
       "name": "resourceB",
       "resources": [
           {
               "name": "firstChildResourceB",
           },
           {   
               "name": "secondChildResourceB",
           }
       ]
   },
   {
       "name": "resourceC",
   }
]
```      

En el ejemplo siguiente se muestra un recurso **Microsoft.Web/serverfarms** y un recurso **Microsoft.Web/sites** con un recurso secundario **Extensions**. Observe que el sitio se ha marcado como dependiente de la granja de servidores ya que la granja de servidores debe existir antes para que se pueda implementar el sitio. Observe también que el recurso **Extensions** es un elemento secundario del sitio.

```json
"resources": [
  {
    "apiVersion": "2015-08-01",
    "name": "[parameters('hostingPlanName')]",
    "type": "Microsoft.Web/serverfarms",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "HostingPlan"
    },
    "sku": {
      "name": "[parameters('skuName')]",
      "capacity": "[parameters('skuCapacity')]"
    },
    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }
  },
  {
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[parameters('siteName')]",
    "location": "[resourceGroup().location]",
    "tags": {
      "environment": "test",
      "team": "Web"
    },
    "dependsOn": [
      "[concat(parameters('hostingPlanName'))]"
    ],
    "properties": {
      "name": "[parameters('siteName')]",
      "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
    },
    "resources": [
      {
        "apiVersion": "2015-08-01",
        "type": "extensions",
        "name": "MSDeploy",
        "dependsOn": [
          "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
        ],
        "properties": {
          "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
          "dbType": "None",
          "connectionString": "",
          "setParameters": {
            "Application Path": "[parameters('siteName')]"
          }
        }
      }
    ]
  }
]
```

## <a name="outputs"></a>outputs
En la sección de salidas, especifique valores que se devuelven de la implementación. Por ejemplo, podría devolver el URI para acceder a un recurso implementado.

En el ejemplo siguiente se muestra la estructura de una definición de salida:

```json
"outputs": {
   "<outputName>" : {
     "type" : "<type-of-output-value>",
     "value": "<output-value-expression>"
   }
}
```

| Nombre del elemento | Obligatorio | Descripción |
|:--- |:--- |:--- |
| outputName |Sí |Nombre del valor de salida. Debe ser un identificador válido de JavaScript. |
| type |Sí |Tipo del valor de salida. Los valores de salida admiten los mismos tipos que los parámetros de entrada de plantilla. |
| value |yes |Expresión de lenguaje de plantilla que se evaluará y devolverá como valor de salida. |

En el ejemplo siguiente se muestra un valor que se devuelve en la sección de salidas.

```json
"outputs": {
   "siteUri" : {
     "type" : "string",
     "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
   }
}
```

Para más información sobre cómo trabajar con resultados, consulte [Uso compartido del estado con las plantillas de Azure Resource Manager](best-practices-resource-manager-state.md).

## <a name="next-steps"></a>Pasos siguientes
* Para ver plantillas completas de muchos tipos diferentes de soluciones, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).
* Para obtener información detallada sobre las funciones que se pueden usar dentro de una plantilla, consulte [Funciones de plantilla de Azure Resource Manager](resource-group-template-functions.md).
* Para combinar varias plantillas en la implementación, consulte [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).
* Puede que necesite usar los recursos que existen dentro de un grupo de recursos diferente. Este escenario es habitual al trabajar con cuentas de almacenamiento o redes virtuales que se comparten entre varios grupos de recursos. Para obtener más información, vea la [función resourceId](resource-group-template-functions.md#resourceid).

[deployment2cmdlet]: https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/new-azurermresourcegroupdeployment



<!--HONumber=Jan17_HO4-->


