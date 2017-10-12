---
title: "Creación, compilación e implementación de aplicaciones lógicas en Visual Studio: Azure Logic Apps | Microsoft Docs"
description: "Cree proyectos de Visual Studio para diseñar, compilar e implementar Azure Logic Apps."
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 2/14/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: e7f5cf483d22e4c60dedbe5176ceb0bc8b2b6e66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="design-build-and-deploy-azure-logic-apps-in-visual-studio"></a>Diseño, compilación e implementación de Azure Logic Apps en Visual Studio

Aunque [Azure Portal](https://portal.azure.com/) es un medio excelente de crear y administrar Azure Logic Apps, puede usar Visual Studio para diseñar, compilar e implementar sus aplicaciones lógicas. Visual Studio proporciona herramientas muy completas, como Diseñador de aplicación lógica, para crear aplicaciones lógicas, configurar las plantillas de implementación y automatización e implementarlas en cualquier entorno. 

Para empezar a trabajar con Azure Logic Apps, aprenda a [crear su primera aplicación lógica en Azure Portal](logic-apps-create-a-logic-app.md).

## <a name="installation-steps"></a>Pasos de instalación

Para instalar y configurar las herramientas de Visual Studio para Azure Logic Apps siga estos pasos.

### <a name="prerequisites"></a>Requisitos previos

* [Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) o Visual Studio 2015
* [SDK de Azure más reciente](https://azure.microsoft.com/downloads/) (2.9.1 o superior)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* Acceso a la web para usar el diseñador incrustado

### <a name="install-visual-studio-tools-for-azure-logic-apps"></a>Instalación de herramientas de Visual Studio para Azure Logic Apps

Una vez cumplidos los requisitos previos:

1. Abra Visual Studio. En el menú **Herramientas**, seleccione **Extensiones y actualizaciones**.
2. Expanda la categoría **En línea** para poder buscar en línea.
3. Busque **Logic Apps** hasta que encuentre las **herramientas de Azure Logic Apps para Visual Studio**.
4. Para descargar e instalar la extensión, haga clic en el botón **Descargar**.
5. Reinicie Visual Studio después de la instalación.

> [!NOTE]
> También puede descargar las [herramientas de Azure Logic Apps para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551) y las [herramientas de Azure Logic Apps para Visual Studio 2015](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio) directamente desde Visual Studio Marketplace.

Una vez finalizada la instalación, podrá usar el proyecto Grupo de recursos de Azure con el Diseñador de aplicaciones lógicas.

## <a name="create-your-project"></a>Creación del proyecto

1. En el menú **Archivo**, vaya a **Nuevo** y seleccione **Proyecto**. O para agregar el proyecto a una solución existente, vaya a **Agregar** y seleccione **Nuevo proyecto**.

    ![Menú Archivo](./media/logic-apps-deploy-from-vs/filemenu.png)

2. En la ventana **Nuevo proyecto**, busque **Nube** y seleccione **Grupo de recursos de Azure**. Asigne un nombre al proyecto y haga clic en **Aceptar**.

    ![Incorporación de proyecto nuevo](./media/logic-apps-deploy-from-vs/addnewproject.png)

3. Seleccione la plantilla de la **aplicación lógica** para crear una plantilla de implementación de una aplicación lógica en blanco para poder usarla. Cuando la haya seleccionado, haga clic en **Aceptar**.

    ![Selección de la plantilla de aplicación lógica](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)

    Ahora habrá agregado el proyecto de aplicación lógica a la solución. 
    En el Explorador de soluciones debería aparecer el archivo de implementación.

    ![Archivo de implementación](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="create-your-logic-app-with-logic-app-designer"></a>Creación de la aplicación lógica en Diseñador de aplicación lógica

Cuando tenga un proyecto Grupo de recursos de Azure con una aplicación lógica, podrá abrir el Diseñador de aplicaciones lógicas en Visual Studio para crear el flujo de trabajo. 

> [!NOTE]
> El diseñador requiere una conexión a internet a los conectores de consulta de datos y propiedades disponibles. Por ejemplo, si usa el conector de Dynamics CRM Online, el diseñador consulta a la instancia de CRM para que muestre las propiedades personalizadas disponibles y las predeterminadas.

1. Haga clic con el botón derecho en el archivo `<template>.json` y seleccione **Open with Logic App Designer** (Abrir con diseñador de aplicación lógica). (`Ctrl+L`)

2. Elija la suscripción de Azure, el grupo de recursos y la ubicación para la plantilla de implementación.

    > [!NOTE]
    > El diseño de una aplicación lógica creará recursos de una conexión de API para consultar las propiedades durante el diseño. Visual Studio usa el grupo de recursos seleccionado para crear las conexiones durante el diseño. Para consultar o modificar las conexiones de API, vaya a Azure Portal y busque **Conexiones de API**.

    ![Selector de suscripción](./media/logic-apps-deploy-from-vs/designer_picker.png)

    El diseñador utiliza la definición del archivo `<template>.json` para la representación.

4. Cree y diseñe la aplicación lógica. La plantilla de implementación se actualiza con los cambios.

    ![Diseñador de aplicaciones lógicas en Visual Studio](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

Visual Studio agrega recursos `Microsoft.Web/connections` al archivo de recursos para cualquier conexión que necesite la aplicación lógica para funcionar. Estas propiedades de conexión pueden establecerse durante la implementación y administrarse después de implementar **Conexiones de API** en Azure Portal.

### <a name="switch-to-json-code-view"></a>Cambio a la vista de código JSON

Para mostrar la representación JSON de la aplicación lógica, seleccione la pestaña **Vista Código** de la parte inferior del diseñador.

Para volver al JSON de todos los recursos, haga clic con el botón derecho en el archivo `<template>.json` y seleccione **Abrir**.

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

### <a name="save-your-logic-app"></a>Guardado de la aplicación lógica

Para guardar la aplicación lógica en cualquier momento, vaya a **Archivo** > **Guardar**. (`Ctrl+S`) 

Si la aplicación lógica tiene errores al guardarla, se muestran en la ventana **Resultados** de Visual Studio.

## <a name="deploy-your-logic-app-from-visual-studio"></a>Implementación de la aplicación lógica desde Visual Studio

Después de configurar la aplicación, puede realizar la implementación directamente desde Visual Studio en solo un par de pasos. 

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y vaya a **Implementar** > **Nueva implementación...**

    ![Nueva implementación](./media/logic-apps-deploy-from-vs/newdeployment.png)

2. Cuando se le solicite, inicie sesión en su suscripción de Azure. 

3. Ahora debe seleccionar los detalles para el grupo de recursos donde desea implementar la aplicación lógica. Cuando haya terminado, haga clic en **Implementar**.

    > [!NOTE]
    > Asegúrese de seleccionar el archivo de plantilla y de parámetros correcto para el grupo de recursos. Por ejemplo, si desea implementar en un entorno de producción, elija el archivo de parámetros de producción.

    ![Implementación en el grupo de recursos](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)

    El estado de la implementación aparece en la ventana **Resultados**. 
    Es posible que deba seleccionar **Aprovisionamiento de Azure** en la lista **Mostrar salida de**.

    ![Salida del estado de la implementación](./media/logic-apps-deploy-from-vs/output.png)

En el futuro, puede modificar la aplicación lógica en el control de código fuente y usar Visual Studio para implementar versiones nuevas.

> [!NOTE]
> Si modifica directamente la definición en Azure Portal, la próxima vez que realice una implementación desde Visual Studio estos cambios se sobrescriben. 

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Incorporación de la aplicación lógica a un proyecto de grupo de recursos existente

Si tiene un proyecto de grupo de recursos existente, puede agregar la aplicación lógica desde la ventana Esquema JSON. También puede agregar otra aplicación lógica junto con la que creara anteriormente.

1. Abra el archivo `<template>.json` .

2. La ventana Esquema JSON, vaya a en **Ver** > **Otras ventanas** > **Esquema JSON**.

3. Para agregar un recurso al archivo de plantilla, haga clic en **Agregar recurso** en la parte superior de la ventana Esquema de JSON. O, en la ventana Esquema de JSON, haga clic en **recursos** y seleccione **Agregar nuevo recurso**.

    ![Ventana Esquema JSON](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. En el cuadro de diálogo **Agregar recurso**, busque y seleccione **Aplicación lógica**. Asigne un nombre a la aplicación lógica y elija **Agregar**.

    ![Agregar recurso](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>Pasos siguientes

* [Administración de aplicaciones lógicas con Visual Studio Cloud Explorer](logic-apps-manage-from-vs.md)
* [Ejemplos de aplicaciones lógicas y escenarios comunes](logic-apps-examples-and-scenarios.md)
* [Aprenda a automatizar procesos empresariales con Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694)
* [Aprenda a integrar sus sistemas con Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
