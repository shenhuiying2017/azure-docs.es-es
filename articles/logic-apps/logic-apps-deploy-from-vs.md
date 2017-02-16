---
title: "Compilación de Azure Logic Apps en Visual Studio | Microsoft Docs"
description: "Cree un proyecto en Visual Studio para crear e implementar la aplicación lógica."
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
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: a9f786259676a1bc9aa616f2db2935dff45475e2
ms.openlocfilehash: db818b2b76d3a8d8c3324c9556237139d319efb9


---
# <a name="build-and-deploy-logic-apps-in-visual-studio"></a>Creación e implementación de Logic Apps en Visual Studio
Aunque [Azure Portal](https://portal.azure.com/) cuenta con un eficaz mecanismo para diseñar y administrar las aplicaciones lógicas, es posible que desee diseñar e implementar este tipo de aplicaciones desde Visual Studio.  Gracias al sofisticado conjunto de herramientas de Visual Studio incluido en Logic Apps, puede crear aplicaciones lógicas con el diseñador, configurar plantillas de implementación y automatización, y realizar la implementación en cualquier entorno.  

## <a name="installation-steps"></a>Pasos de instalación
A continuación se muestran los pasos para instalar y configurar las herramientas de Visual Studio para Logic Apps.

### <a name="prerequisites"></a>Requisitos previos
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [SDK de Azure más reciente](https://azure.microsoft.com/downloads/) (2.9.1 o superior)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* Acceso a la web para usar el diseñador incrustado

### <a name="install-visual-studio-tools-for-logic-apps"></a>Instalación de herramientas de Visual Studio para Logic Apps
Una vez que tiene los requisitos previos instalados: 

1. Abra Visual Studio 2015 con el menú **Herramientas** y seleccione **Extensiones y actualizaciones**.
2. Seleccione la categoría **En línea** para realizar búsquedas en línea.
3. Busque **Logic Apps** para mostrar las **herramientas de Azure Logic Apps para Visual Studio**
4. Haga clic en el botón **Descargar** para descargar e instalar la extensión.
5. Reinicie Visual Studio después de la instalación.

> [!NOTE]
> También puede descargar la extensión directamente desde [este vínculo](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)
> 
> 

Una vez instalada la extensión, podrá usar el proyecto Grupo de recursos de Azure con el diseñador de aplicaciones lógicas.

## <a name="create-a-project"></a>Creación de un proyecto
1. Vaya al menú **Archivo** y seleccione **Nuevo** >  **Proyecto** (o bien, vaya a **Agregar** y seleccione **Nuevo proyecto** para agregarlo a una solución existente):  ![menú Archivo](./media/logic-apps-deploy-from-vs/filemenu.png)
2. En el cuadro de diálogo, busque **Nube** y seleccione **Azure Resource Group**. Escriba un **nombre** y, a continuación, haga clic en **Aceptar**.
    ![Incorporación de proyecto nuevo](./media/logic-apps-deploy-from-vs/addnewproject.png)
3. Seleccione el plantilla **Logic app** . De este modo, creará una plantilla de implementación de aplicaciones lógicas en blanco con la que empezar.
    ![Selección de plantilla de Azure](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)
4. Una vez que haya seleccionado la **plantilla**, presione **Aceptar**.
   
    Ahora el proyecto de aplicación lógica está agregado a la solución. Debería ver el archivo de implementación en el Explorador de soluciones:  
   
    ![Implementación](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="using-the-logic-app-designer"></a>Uso del diseñador de aplicaciones lógicas
Una vez que tiene un proyecto Azure Resource Group que contiene una aplicación lógica, puede abrir el diseñador en Visual Studio para que le ayude a crear el flujo de trabajo.  El diseñador necesita una conexión a Internet para consultar los datos y propiedades disponibles en los conectores (por ejemplo, si usa el conector de Dynamics CRM Online, el diseñador consultará la instancia de CRM para obtener una lista de las propiedades personalizadas y predeterminadas disponibles).

1. Haga clic con el botón derecho en el archivo `<template>.json` y seleccione **Abrir con diseñador de aplicación lógica** (o `Ctrl+L`).
2. Elija la suscripción, el grupo de recursos y la ubicación para la plantilla de implementación.
   * Es importante tener en cuenta que diseñar una aplicación lógica creará recursos de una **conexión de API** para consultar las propiedades durante el diseño.  El grupo de recursos seleccionado se utilizará para crear dichas conexiones en tiempo de diseño.  Para consultar o modificar las conexiones de API, vaya a Azure Portal y busque **Conexiones de API**.
   
     ![Selector de suscripción](./media/logic-apps-deploy-from-vs/designer_picker.png)
3. El diseñador debe procesarse de acuerdo con la definición del archivo `<template>.json` .
4. Ahora puede crear y diseñar la aplicación lógica; los cambios se actualizarán en la plantilla de implementación.
    ![Diseñador en Visual Studio](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

En el archivo de recursos, se agregan recursos `Microsoft.Web/connections` en todas las conexiones que son necesarias para que la aplicación lógica funcione.  Estas propiedades de conexión pueden establecerse durante la implementación y administrarse después de implementar **Conexiones de API** en Azure Portal.

### <a name="switching-to-the-json-code-view"></a>Cambio a la vista de código JSON
Puede seleccionar la pestaña **Vista Código** en la parte inferior del diseñador para cambiar a la representación JSON de la aplicación lógica.  Para volver al JSON de todos los recursos, haga clic con el botón derecho en el archivo `<template>.json` y seleccione **Abrir**.

### <a name="saving-the-logic-app"></a>Guardado de la aplicación lógica
Puede guardar la aplicación lógica en cualquier momento mediante el botón **Guardar** o `Ctrl+S`.  Si hay errores con la aplicación lógica en el momento de guardar, aparecerán en la ventana **Salidas** de Visual Studio.

## <a name="deploying-your-logic-app"></a>Implementación de la aplicación lógica
Finalmente, después de configurar la aplicación, puede realizar la implementación directamente desde Visual Studio en un par de pasos. 

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y vaya a **Implementar** > **Nueva implementación...**
    ![Nueva implementación](./media/logic-apps-deploy-from-vs/newdeployment.png)
2. Se le indica que inicie sesión en su suscripción o suscripciones de Azure. 
3. Ahora debe elegir los detalles del grupo de recursos en los que desea implementar la aplicación lógica. 
    ![Implementar en el grupo de recursos](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)
   
   > [!NOTE]
   > Asegúrese de seleccionar los archivos de plantilla y parámetros correctos para el grupo de recursos (por ejemplo, si va a realizar la implementación en un entorno de producción, elegirá el archivo de parámetros de producción). 
   > 
   > 
4. Seleccione el botón Implementar.
5. El estado de la implementación aparece en la ventana **Salida** (puede que tenga que elegir **Aprovisionamiento de Azure**. 
    ![Salida](./media/logic-apps-deploy-from-vs/output.png)

En el futuro, puede revisar la aplicación lógica en el control de código fuente y usar Visual Studio para implementar versiones nuevas. 

> [!NOTE]
> Si modifica la definición directamente en Azure Portal, la próxima vez que realice la implementación desde Visual Studio estos cambios se sobrescribirán.
> 
> 

## <a name="adding-a-logic-app-to-an-existing-resource-group-project"></a>Incorporación de una aplicación lógica a un proyecto de grupo de recursos existente
Si tiene un proyecto de grupo de recursos existente, puede agregar una aplicación lógica independiente o agregar una aplicación lógica adicional junto con la que creó previamente a través de la ventana Esquema JSON.
1. Abra el archivo `<template>.json` .
2. Abra la ventana Esquema JSON.  La ventana Esquema JSON se encuentra en **Ver** > **Otras ventanas** > **Esquema JSON**.
3. Para agregar un recurso al archivo de plantillas, haga clic en el botón Agregar recurso situado en la parte superior de la ventana Esquema JSON o haga clic con el botón derecho en **recursos** y seleccione **Agregar nuevo recurso**.

    ![Esquema JSON](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. En el cuadro de diálogo **Agregar recurso**, busque y seleccione **Aplicación lógica**. Después, asigne un nombre a la aplicación lógica y seleccione **Agregar**.

    ![Agregar recurso](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>Pasos siguientes
* Para comenzar con las aplicaciones lógicas, siga el tutorial [Creación de una aplicación lógica](logic-apps-create-a-logic-app.md) .  
* [Ejemplos de aplicaciones lógicas y escenarios comunes](logic-apps-examples-and-scenarios.md)
* [Con las aplicaciones lógicas puede automatizar procesos empresariales.](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Obtenga información acerca de cómo integrar sus sistemas con Aplicaciones lógicas](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Jan17_HO3-->


