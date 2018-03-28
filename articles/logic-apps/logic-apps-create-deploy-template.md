---
title: Creación de plantillas de implementación para Azure Logic Apps | Microsoft Docs
description: Creación de plantillas de Azure Resource Manager para implementar aplicaciones lógicas
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: SyntaxC4
editor: ''
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; estfan
ms.openlocfilehash: 91d93a02bb9bf48c5bda0304c9d3d52c22e30209
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Creación de plantillas de Azure Resource Manager para implementar aplicaciones lógicas

Una vez creada una aplicación lógica, quizá desee crearla como una plantilla de Azure Resource Manager.
De este modo, podrá implementar fácilmente la aplicación lógica en cualquier entorno o grupo de recursos en los que pueda necesitarla.
Para obtener más información sobre las plantillas de Resource Manager, consulte [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) e [Implementación de recursos con plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Plantilla de implementación de aplicación lógica

Una aplicación lógica consta de tres componentes básicos:

* **Recurso de la aplicación lógica**: contiene información acerca de cuestiones como el plan de precios, la ubicación y la definición del flujo de trabajo.
* **Definición del flujo de trabajo**: describe los pasos del flujo de trabajo de la aplicación lógica y el modo en que el motor de Logic Apps debe ejecutar el flujo de trabajo.
Puede ver esta definición en la ventana **Vista código** de su aplicación lógica.
En el recurso de la aplicación lógica, puede encontrar esta definición en la propiedad `definition`.
* **Conexiones**: consulta recursos independientes que almacenan los metadatos de manera segura sobre las conexiones del conector, como una cadena de conexión y un token de acceso.
En el recurso de la aplicación lógica, su aplicación lógica consulta estos recursos en la sección `parameters`.

Puede ver todas estas piezas de las aplicaciones lógicas existentes mediante herramientas como el [Explorador de recursos de Azure](http://resources.azure.com).

Para crear una plantilla para una aplicación lógica que pueda utilizar con implementaciones de grupos de recursos, es preciso definir los recursos y parametrizarlos según sea necesario.
Por ejemplo, si va a implementar en un entorno de desarrollo, prueba y producción, probablemente deseará usar cadenas de conexión diferentes para una base de datos SQL en cada entorno.
O bien, puede que desee realizar la implementación en diferentes suscripciones o grupos de recursos.  

## <a name="create-a-logic-app-deployment-template"></a>Creación de una plantilla de implementación de aplicación lógica

La manera más fácil tener una plantilla de implementación de aplicación lógica válida es usar el [Visual Studio Tools para Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
Las herramientas de Visual Studio generan una plantilla de implementación válida que puede utilizarse en cualquier suscripción o ubicación.

Algunas herramientas pueden ayudarle a crear una plantilla de implementación de aplicación lógica.
Puede crearla manualmente; es decir, mediante el uso de los recursos ya mencionados aquí para crear parámetros según sea necesario.
Otra opción es usar un módulo de PowerShell [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator) . Este módulo de código abierto evalúa primero la aplicación lógica y las conexiones que utiliza y luego genera los recursos de la plantilla con los parámetros necesarios para la implementación.
Por ejemplo, si tiene una aplicación lógica que recibe un mensaje de una cola de Azure Service Bus y agrega datos a una base de datos SQL de Azure, la herramienta conservará toda la lógica de orquestación y parametrizará las cadenas de conexión de SQL y Service Bus con el fin de que se puedan establecer en la implementación.

> [!NOTE]
> Las conexiones deben estar dentro del mismo grupo de recursos que la aplicación lógica.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>Instalación del módulo de PowerShell de plantilla de aplicación lógica
La forma más fácil de instalar el módulo es a través de la [Galería de PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1) con el comando `Install-Module -Name LogicAppTemplate`.  

También puede instalar el módulo de PowerShell manualmente:

1. Descargue la versión más reciente de [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Extraiga la carpeta en su carpeta del módulo de PowerShell (normalmente `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Para que el módulo funcione con cualquier token de acceso de inquilino y suscripción, se recomienda utilizarlo con la herramienta de línea de comandos [ARMClient](https://github.com/projectkudu/ARMClient).  Esta [entrada de blog ](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) describe ARMClient con más detalle.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Generación de una plantilla de aplicación lógica mediante PowerShell
Después de instalar PowerShell, puede generar una plantilla mediante el comando siguiente:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` es el identificador de la suscripción de Azure. Esta línea primero obtiene acceso al token mediante ARMClient, luego lo canaliza hacia el script de PowerShell y finalmente crea la plantilla en un archivo JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Adición de parámetros a una plantilla de aplicación lógica
Después de crear la plantilla de aplicación lógica, podrá seguir agregando o modificando los parámetros que necesite. Por ejemplo, si la definición incluye un identificador de recurso a una función o un flujo de trabajo anidado de Azure en el que planea implementar una sola implementación, puede agregar más recursos a la plantilla y parametrizar los identificadores según sea necesario. Lo mismo sucede con las referencias a las API personalizadas o a los puntos de conexión de Swagger que espera implementar con cada grupo de recursos.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Incorporación de referencias a los recursos dependientes a las plantillas de implementación de Visual Studio

Si desea que la aplicación lógica haga referencia a los recursos dependientes, puede usar las [funciones de plantilla de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions), como los parámetros, de la plantilla de implementación de aplicación lógica. Por ejemplo, si quiere que la aplicación lógica haga referencia a una función de Azure o cuenta de integración que desee implementar junto con la aplicación lógica. Siga estas instrucciones sobre cómo usar parámetros en la plantilla de implementación para que el Diseñador de aplicaciones lógicas represente correctamente. 

Puede usar parámetros de aplicación lógica en estos tipos de desencadenadores y acciones:

*   Flujo de trabajo secundario
*   Aplicación de función
*   Llamada APIM
*   Dirección URL en tiempo de ejecución de conexión de API
*   Ruta de conexión de API

Y puede utilizar funciones de plantilla como, por ejemplo, parámetros, variables, resourceId, concat, etc. Por ejemplo, aquí verá cómo se puede reemplazar el identificador de recurso de función de Azure:

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

Y dónde usaría parámetros:

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
En otro ejemplo puede parametrizar la operación send message de Service Bus:

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl es opcional y se puede quitar de la plantilla si está presente.
> 


> [!NOTE] 
> Para que el Diseñador de aplicaciones lógicas funcione con parámetros, debe proporcionar valores predeterminados, por ejemplo:
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Incorporación de la aplicación lógica a un proyecto de grupo de recursos existente

Si tiene un proyecto de grupo de recursos existente, puede agregar la aplicación lógica desde la ventana Esquema JSON. También puede agregar otra aplicación lógica junto con la que creara anteriormente.

1. Abra el archivo `<template>.json` .

2. La ventana Esquema JSON, vaya a en **Ver** > **Otras ventanas** > **Esquema JSON**.

3. Para agregar un recurso al archivo de plantilla, haga clic en **Agregar recurso** en la parte superior de la ventana Esquema de JSON. O, en la ventana Esquema de JSON, haga clic en **recursos** y seleccione **Agregar nuevo recurso**.

    ![Ventana Esquema JSON](./media/logic-apps-create-deploy-template/jsonoutline.png)
    
4. En el cuadro de diálogo **Agregar recurso**, busque y seleccione **Aplicación lógica**. Asigne un nombre a la aplicación lógica y elija **Agregar**.

    ![Agregar recurso](./media/logic-apps-create-deploy-template/addresource.png)


## <a name="deploy-a-logic-app-template"></a>Implementación de una plantilla de aplicación lógica

Puede implementar su plantilla mediante cualquier herramienta, entre las que se incluyen PowerShell, API de REST, [Visual Studio Team Services Release Management](#team-services) o la implementación de plantillas de Azure Portal.
Además, para almacenar los valores de parámetros, se recomienda crear un [archivo de parámetros](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Aprenda cómo [implementar recursos con las plantillas de Azure Resource Manager y PowerShell](../azure-resource-manager/resource-group-template-deploy.md) o cómo [implementar recursos con las plantillas de Azure Resource Manager y Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>Autorización de conexiones de OAuth

Después de la implementación, la aplicación lógica funciona de un extremo a otro con parámetros válidos.
Sin embargo, sigue siendo necesario autorizar las conexiones de OAuth para generar un token de acceso válido.
Para autorizar las conexiones de OAuth, abra la aplicación lógica en Diseñador de aplicaciones lógicas y autorice estas conexiones. Para automatizar las implementaciones, puede usar un script para dar el consentimiento a cada conexión de OAuth.
Hay un script de ejemplo en GitHub, en el proyecto [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) .

<a name="team-services"></a>
## <a name="visual-studio-team-services-release-management"></a>Administración de versiones de Visual Studio Team Services

Un escenario común para implementar y administrar un entorno es usar una herramienta como Release Management en Visual Studio Team Services con una plantilla de implementación de aplicaciones lógicas. Visual Studio Team Services incluye la tarea [Implementación de un grupo de recursos de Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) , que se puede agregar a cualquier canalización de la compilación o versión. Es preciso tener una [entidad de servicio](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) para implementar la autorización y, a continuación, generar la definición de la versión.

1. En Release Management, seleccione **Vacío** para crear una definición vacía.

    ![Creación de una definición vacía][1]

2. Elija los recursos que necesite para esto, que probablemente incluirán la plantilla de aplicación lógica que se genera manualmente o como parte del proceso de compilación.
3. Agregue una tarea de **Implementación de un grupo de recursos de Azure** .
4. Configure una [entidad de servicio](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)y haga referencia a los archivos de plantilla y de parámetros de la plantilla.
5. Siga compilando los pasos del proceso de lanzamiento de otros entornos, pruebas automatizadas o aprobadores según sea necesario.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
