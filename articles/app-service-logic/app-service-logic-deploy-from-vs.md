---
title: "Compilación de Logic Apps en Visual Studio | Microsoft Docs"
description: "Cree un proyecto en Visual Studio para crear e implementar la aplicación lógica."
author: jeffhollan
manager: erikre
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
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 73a57310c07ed946480f60f6564d8619e54155a5


---
# <a name="build-and-deploy-logic-apps-in-visual-studio"></a>Creación e implementación de Logic Apps en Visual Studio
Aunque el [Portal de Azure](https://portal.azure.com/) le ofrece una excelente manera de diseñar y administrar las aplicaciones lógicas, es posible que también desee diseñar e implementar la aplicación lógica desde Visual Studio.  Logic Apps viene con un sofisticado conjunto de herramientas de Visual Studio que permite crear una aplicación lógica mediante el diseñador, configurar las plantillas de implementación y automatización e implementarla en cualquier entorno.  

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

Una vez instalado, podrá usar el proyecto Azure Resource Group con el diseñador de aplicaciones lógicas.

## <a name="create-a-project"></a>Creación de un proyecto
1. Vaya al menú **Archivo** y seleccione **Nuevo** >  **Proyecto** (o bien, vaya a **Agregar** y seleccione **Nuevo proyecto** para agregarlo a una solución existente):  ![menú Archivo](./media/app-service-logic-deploy-from-vs/filemenu.png)
2. En el cuadro de diálogo, busque **Nube** y seleccione **Azure Resource Group**. Escriba un **nombre** y, a continuación, haga clic en **Aceptar**.
    ![Incorporación de proyecto nuevo](./media/app-service-logic-deploy-from-vs/addnewproject.png)
3. Seleccione el plantilla **Logic app** . Esto creará una plantilla de implementación de aplicaciones lógicas en blanco con la que empezar.
    ![Selección de plantilla de Azure](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)
4. Una vez que haya seleccionado la **plantilla**, presione **Aceptar**.
   
    Ahora el proyecto de aplicación lógica está agregado a la solución. Debería ver el archivo de implementación en el Explorador de soluciones:  
   
    ![Implementación](./media/app-service-logic-deploy-from-vs/deployment.png)

## <a name="using-the-logic-app-designer"></a>Uso del diseñador de aplicaciones lógicas
Una vez que tiene un proyecto Azure Resource Group que contiene una aplicación lógica, puede abrir el diseñador en Visual Studio para que le ayude a crear el flujo de trabajo.  El diseñador requiere una conexión a Internet para consultar en los conectores los datos y propiedades disponibles (por ejemplo, si usa el conector de Dynamics CRM Online, el diseñador consultará la instancia de CRM para obtener una lista personalizada de las propiedades personalizadas y predeterminadas disponibles).

1. Haga clic con el botón derecho en el archivo `<template>.json` y seleccione **Abrir con diseñador de aplicación lógica** (o `Ctrl+L`).
2. Elija la suscripción, el grupo de recursos y la ubicación para la plantilla de implementación.
   * Es importante tener en cuenta que diseñar una aplicación lógica creará recursos de una **conexión de API** para consultar las propiedades durante el diseño.  El grupo de recursos seleccionado será el grupo de recursos utilizado para crear dichas conexiones durante el diseño.  Para ver o modificar las conexiones de API, vaya a Azure Portal y busque **Conexiones de API**.
     ![Selector de suscripción](./media/app-service-logic-deploy-from-vs/designer_picker.png)
3. El diseñador debe procesarse de acuerdo con la definición del archivo `<template>.json` .
4. Ahora puede crear y diseñar la aplicación lógica; los cambios se actualizarán en la plantilla de implementación.
    ![Diseñador en Visual Studio](./media/app-service-logic-deploy-from-vs/designer_in_vs.png)

También verá los recursos `Microsoft.Web/connections` que se agregan al archivo de recursos para todas las conexiones necesarias para que la aplicación lógica funcione.  Estas propiedades de conexión se pueden establecer durante la implementación y la administración después de implementar en **Conexiones de API** en Azure Portal.

### <a name="switching-to-the-json-code-view"></a>Cambio a la vista de código JSON
Puede seleccionar la pestaña **Vista Código** en la parte inferior del diseñador para cambiar a la representación JSON de la aplicación lógica.  Para volver al JSON de todos los recursos, haga clic con el botón derecho en el archivo `<template>.json` y seleccione **Abrir**.

### <a name="saving-the-logic-app"></a>Guardado de la aplicación lógica
Puede guardar la aplicación lógica en cualquier momento mediante el botón **Guardar** o `Ctrl+S`.  Si hay errores con la aplicación lógica en el momento de guardar, aparecerán en la ventana **Salidas** de Visual Studio.

## <a name="deploying-your-logic-app"></a>Implementación de la aplicación lógica
Finalmente, después de configurar la aplicación, puede realizar la implementación directamente desde Visual Studio en un par de pasos. 

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y vaya a **Implementar** > **Nueva implementación...**
    ![Nueva implementación](./media/app-service-logic-deploy-from-vs/newdeployment.png)
2. Se le indica que inicie sesión en su suscripción o suscripciones de Azure. 
3. Ahora debe elegir los detalles del grupo de recursos en los que desea implementar la aplicación lógica. 
    ![Implementar en el grupo de recursos](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)
   
   > [!NOTE]
   > Asegúrese de seleccionar los archivos de plantilla y parámetros correctos para el grupo de recursos (por ejemplo, si va a realizar la implementación en un entorno de producción, elegirá el archivo de parámetros de producción). 
   > 
   > 
4. Seleccione el botón Implementar.
5. El estado de la implementación aparece en la ventana **Salida** (puede que tenga que elegir **Aprovisionamiento de Azure**. 
    ![Salida](./media/app-service-logic-deploy-from-vs/output.png)

En el futuro, puede revisar la aplicación lógica en el control de código fuente y usar Visual Studio para implementar versiones nuevas. 

> [!NOTE]
> Si modifica la definición directamente en el Portal de Azure, la próxima vez que realice la implementación desde Visual Studio, estos cambios se sobrescribirán.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* Para comenzar con las aplicaciones lógicas, siga el tutorial [Creación de una aplicación lógica](app-service-logic-create-a-logic-app.md) .  
* [Ejemplos de aplicaciones lógicas y escenarios comunes](app-service-logic-examples-and-scenarios.md)
* [Con las aplicaciones lógicas puede automatizar procesos empresariales.](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Obtenga información acerca de cómo integrar sus sistemas con Aplicaciones lógicas](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Nov16_HO3-->


