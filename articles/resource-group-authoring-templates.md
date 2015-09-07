<properties
   pageTitle="Crear plantillas del Administrador de recursos de Azure"
	description="Cree plantillas del Administrador de recursos de Azure mediante la sintaxis declarativa de JSON para implementar aplicaciones en Azure."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
   ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/20/2015"
	ms.author="tomfitz"/>

# Creación de plantillas del Administrador de recursos de Azure

Normalmente, las aplicaciones de Azure requieren una combinación de recursos (por ejemplo, un servidor de base de datos, una base de datos o un sitio web) para cumplir los objetivos deseados. En lugar de implementar y administrar cada recurso por separado, puede crear una plantilla del Administrador de recursos de Azure que implementa y aprovisiona todos los recursos de su aplicación en una operación única y coordinada. En la plantilla, se definen los recursos necesarios para la aplicación y se especifican los parámetros de implementación para especificar valores para diferentes entornos. La plantilla consta de JSON y expresiones que puede usar para generar valores para su implementación.

En este tema se describen las secciones de la plantilla. Para los esquemas reales, consulte [Esquemas del Administrador de recursos de Azure](https://github.com/Azure/azure-resource-manager-schemas).

Debe limitar el tamaño de la plantilla a 1 MB y cada archivo de parámetros a 64 KB. El límite de 1 MB se aplica al estado final de la plantilla una vez se ha ampliado con definiciones de recursos iterativas y los valores de variables y parámetros.

## Formato de plantilla

En el ejemplo siguiente se muestran las secciones que componen la estructura básica de una plantilla.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Nombre del elemento | Obligatorio | Descripción
| :------------: | :------: | :----------
| $schema | Sí | Ubicación del archivo de esquema JSON que describe la versión del idioma de la plantilla.
| contentVersion | Sí | Versión de la plantilla (por ejemplo, 1.0.0.0). Al implementar los recursos con la plantilla, este valor se puede usar para asegurarse de que se está usando la plantilla correcta.
| parameters | No | Valores que se proporcionan cuando se ejecuta la implementación para personalizar la implementación de recursos.
| variables | No | Valores que se usan como fragmentos JSON en la plantilla para simplificar expresiones de idioma de la plantilla.
| resources | Sí | Tipos de servicios que se implementan o actualizan en un grupo de recursos.
| outputs | No | Valores que se devuelven después de la implementación.

Examinaremos las secciones de la plantilla con mayor detenimiento más adelante en este tema. Por ahora, voy a revisar parte de la sintaxis que conforma la plantilla.

## Expresiones y funciones

La sintaxis básica de la plantilla es JSON; sin embargo, las expresiones y funciones amplían el JSON que está disponible en la plantilla y le permiten crear valores que no son valores literales estrictos. Las expresiones se incluyen entre corchetes ([ y ]) y se evalúan cuando se implementa la plantilla. Pueden aparecer expresiones en cualquier lugar de un valor de cadena JSON y devolver siempre otro valor JSON. Si necesita usar una cadena literal que comienza por un corchete [, debe usar dos corchetes [[.

Normalmente, se usan expresiones con funciones para realizar operaciones con el fin de configurar la implementación. Al igual que en JavaScript, las llamadas de función tienen el formato **functionName(arg1,arg2,arg3)**. Se hace referencia a las propiedades mediante los operadores dot e [index] .

La siguiente lista muestra las funciones comunes.

- **parameters(parameterName)**

    Devuelve un valor de parámetro que se proporciona cuando se ejecuta la implementación.

- **variables(variableName)**

    Devuelve una variable que se define en la plantilla.

- **concat(arg1,arg2,arg3,...)**

    Combina varios valores de cadena. Esta función puede tomar cualquier número de argumentos.

- **base64(inputString)**

    Devuelve la representación de base64 de la cadena de entrada.

- **resourceGroup()**

    Devuelve un objeto estructurado (con las propiedades de identificador, nombre y ubicación) que representa el grupo de recursos actual.

- **resourceId([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

    Devuelve el identificador único de un recurso. Se puede usar para recuperar recursos de otro grupo de recursos.

En el ejemplo siguiente se muestra cómo utilizar algunas de las funciones para la construcción de valores:
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Por ahora, ya sabe lo suficiente acerca de expresiones y funciones para comprender las secciones de la plantilla. Para obtener más información acerca de todas las funciones de plantilla, incluidos los parámetros y el formato de valores devueltos, consulte [Funciones de la plantilla del Administrador de recursos de Azure](./resource-group-template-functions.md).


## Parámetros

En la sección de parámetros de la plantilla, especifique los valores que el usuario puede introducir al implementar los recursos. Puede usar estos valores de parámetros a lo largo de la plantilla para establecer valores para los recursos implementados. Solo los parámetros declarados en la sección de parámetros se pueden usar en otras secciones de la plantilla.

En la sección de parámetros, no puede usar un valor de parámetro para construir otro valor de parámetro. Ese tipo de operación suele producirse en la sección de variables.

Defina recursos con la estructura siguiente:

    "parameters": {
       "<parameterName>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<optional-default-value-of-parameter>",
         "allowedValues": [ "<optional-array-of-allowed-values>" ]
       }
    }

| Nombre del elemento | Obligatorio | Descripción
| :------------: | :------: | :----------
| parameterName | Sí | Nombre del parámetro. Debe ser un identificador válido de JavaScript.
| type | Sí | Tipo del valor del parámetro. Consulte la lista siguiente de tipos permitidos.
| defaultValue | No | Valor predeterminado del parámetro, si no se proporciona ningún valor.
| allowedValues | No | Matriz de valores permitidos para el parámetro para asegurarse de que se proporciona el valor correcto.

Los valores y tipos permitidos son los siguientes:

- string o secureString: cualquier cadena JSON válida
- int: cualquier entero JSON válido
- bool: cualquier booleano JSON válido
- object o secureObject: cualquier objeto JSON válido
- array: cualquier matriz JSON válida


>[AZURE.NOTE]Todas las contraseñas, claves y otros secretos deben utilizar el tipo **secureString**. No se pueden leer los parámetros de plantilla con el tipo secureString después de la implementación de recursos.

En el ejemplo siguiente se muestra cómo definir los parámetros.

    "parameters": {
       "siteName": {
          "type": "string"
       },
       "siteLocation": {
          "type": "string"
       },
       "hostingPlanName": {
          "type": "string"
       },  
       "hostingPlanSku": {
          "type": "string",
          "allowedValues": [
            "Free",
            "Shared",
            "Basic",
            "Standard",
            "Premium"
          ],
          "defaultValue": "Free"
       }
    }

## Variables

En la sección de variables puede construir valores que se utilizan para simplificar las expresiones de idioma de la plantilla. Normalmente, estas variables se basarán en los valores proporcionados de los parámetros.

En el ejemplo siguiente se muestra cómo definir una variable que se construye a partir de dos valores de parámetro:

    "parameters": {
       "username": {
         "type": "string"
       },
       "password": {
         "type": "secureString"
       }
     },
     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

En el ejemplo siguiente se muestra una variable que es un tipo JSON complejo y las variables que se construyen a partir de otras variables:

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
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount"
    }

## Recursos

En la sección de recursos, se define que los recursos se implementan o se actualizan.

Defina recursos con la siguiente estructura:

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "resources": [
           "<array-of-dependent-resources>"
         ]
       }
    ]

| Nombre del elemento | Obligatorio | Descripción
| :----------------------: | :------: | :----------
| apiVersion | Sí | Versión de la API que admite el recurso. Para ver las versiones y los esquemas disponibles para los recursos, consulte [Esquemas del Administrador de recursos de Azure](https://github.com/Azure/azure-resource-manager-schemas).
| type | Sí | Tipo de recurso. Este valor es una combinación del espacio de nombres del proveedor de recursos y el tipo de recurso que admite el proveedor de recursos.
| name | Sí | Nombre del recurso. El nombre debe cumplir las restricciones de componente URI definidas en RFC3986.
| location | No | Ubicaciones geográficas compatibles del recurso proporcionado.
| etiquetas | No | Etiquetas asociadas al recurso.
| dependsOn | No | Recursos de los que depende el recurso que se está definiendo. Las dependencias entre los recursos se evalúan y los recursos se implementan en su orden dependiente. Cuando no hay recursos dependientes entre sí, se intenta implementarlos en paralelo. El valor puede ser una lista separada por comas de nombres de recursos o identificadores de recursos únicos.
| propiedades | No | Opciones de configuración específicas de recursos.
| resources | No | Recursos secundarios que dependen del recurso que se está definiendo.

Si el nombre del recurso no es único, puede usar la función auxiliar **resourceId** (descrita a continuación) para obtener el identificador único para cualquier recurso.

En el ejemplo siguiente se muestra un recurso **Microsoft.Web/serverfarms** y un recurso **Microsoft.Web/Sites** con un recurso **Extensions** anidado:

    "resources": [
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/serverfarms",
          "name": "[parameters('hostingPlanName')]",
          "location": "[resourceGroup().location]",
          "properties": {
              "name": "[parameters('hostingPlanName')]",
              "sku": "[parameters('hostingPlanSku')]",
              "workerSize": "0",
              "numberOfWorkers": 1
          }
        },
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          "tags": {
              "environment": "test",
              "team": "ARM"
          },
          "dependsOn": [
              "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
          ],
          "properties": {
              "name": "[parameters('siteName')]",
              "serverFarm": "[parameters('hostingPlanName')]"
          },
          "resources": [
              {
                  "apiVersion": "2014-06-01",
                  "type": "Extensions",
                  "name": "MSDeploy",
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


## Salidas

En la sección de salidas, especifique valores que se devuelven de la implementación. Por ejemplo, podría devolver el URI para acceder a un recurso implementado.

En el ejemplo siguiente se muestra la estructura de una definición de salida:

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>",
       }
    }

| Nombre del elemento | Obligatorio | Descripción
| :------------: | :------: | :----------
| outputName | Sí | Nombre del valor de salida. Debe ser un identificador válido de JavaScript.
| type | Sí | Tipo del valor de salida. Los valores de salida admiten los mismos tipos que los parámetros de entrada de plantilla.
| value | Sí | Expresión de lenguaje de plantilla que se evaluará y devolverá como valor de salida.


En el ejemplo siguiente se muestra un valor que se devuelve en la sección de salidas.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

## Escenarios más avanzados.
En este tema se ofrece una visión preliminar de la plantilla. Sin embargo, el escenario puede requerir tareas más avanzadas.

Puede que necesite combinar dos plantillas o usar una plantilla secundaria dentro de una plantilla principal. Para obtener más información, consulte [Uso de plantillas vinculadas con el Administrador de recursos de Azure](resource-group-linked-templates.md).

Para iterar una cantidad de veces específica al crear un tipo de recurso, consulte [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).

Puede que necesite usar los recursos que existen dentro de un grupo de recursos diferente. Esto es habitual al trabajar con cuentas de almacenamiento o redes virtuales que se comparten entre varios grupos de recursos. Para obtener más información, vea la [función resourceId](../resource-group-template-functions#resourceid).

## Plantilla completa
La siguiente plantilla implementa una aplicación web y aprovisiona con código desde un archivo. zip.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "siteName": {
           "type": "string"
         },
         "hostingPlanName": {
           "type": "string"
         },
         "hostingPlanSku": {
           "type": "string",
           "allowedValues": [
             "Free",
             "Shared",
             "Basic",
             "Standard",
             "Premium"
           ],
           "defaultValue": "Free"
         }
       },
       "resources": [
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/serverfarms",
           "name": "[parameters('hostingPlanName')]",
           "location": "[resourceGroup().location]",
           "properties": {
             "name": "[parameters('hostingPlanName')]",
             "sku": "[parameters('hostingPlanSku')]",
             "workerSize": "0",
             "numberOfWorkers": 1
           }
         },
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/sites",
           "name": "[parameters('siteName')]",
           "location": "[resourceGroup().location]",
           "tags": {
             "environment": "test",
             "team": "ARM"
           },
           "dependsOn": [
             "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
           ],
           "properties": {
             "name": "[parameters('siteName')]",
             "serverFarm": "[parameters('hostingPlanName')]"
           },
           "resources": [
             {
               "apiVersion": "2014-06-01",
               "type": "Extensions",
               "name": "MSDeploy",
               "dependsOn": [
                 "[resourceId('Microsoft.Web/sites', parameters('siteName'))]"
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
       ],
       "outputs": {
         "siteUri": {
           "type": "string",
           "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
         }
       }
    }

## Pasos siguientes
- Para obtener información detallada sobre las funciones que puede usar desde una plantilla, consulte [Funciones de la plantilla del Administrador de recursos de Azure](resource-group-template-functions.md).
- Para ver cómo implementar la plantilla que creó, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](azure-portal/resource-group-template-deploy.md).
- Para obtener un ejemplo en profundidad de la implementación de una aplicación, consulte [Aprovisionamiento e implementación predecibles de microservicios en Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Para ver los esquemas disponibles, consulte [Esquemas del Administrador de recursos de Azure](https://github.com/Azure/azure-resource-manager-schemas).

<!---HONumber=August15_HO9-->