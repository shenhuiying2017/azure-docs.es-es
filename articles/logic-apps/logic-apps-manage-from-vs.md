---
title: "Administración de Azure Logic Apps desde Visual Studio Cloud Explorer | Microsoft Docs"
description: "Administración de Azure Logic Apps desde Visual Studio Cloud Explorer."
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: a9f786259676a1bc9aa616f2db2935dff45475e2
ms.openlocfilehash: dcb0512b4f9e6c94262a3f39ef59f82fcfbf313e


---
# <a name="manage-logic-apps-with-the-visual-studio-cloud-explorer"></a>Administración de Logic Apps con Visual Studio Cloud Explorer
Aunque [Azure Portal](https://portal.azure.com) cuenta con un mecanismo eficaz para diseñar y administrar Logic Apps, Visual Studio Cloud Explorer le permite administrar muchos de los recursos de Azure, como Logic Apps, desde Visual Studio.  Con Cloud Explorer, puede buscar las aplicaciones lógicas publicadas y realizar acciones, como habilitar, deshabilitar, editar y consultar historiales de ejecución. 

## <a name="installation-steps"></a>Pasos de instalación
A continuación se muestran los pasos para instalar y configurar las herramientas de Visual Studio para Logic Apps.

### <a name="prerequisites"></a>Requisitos previos
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [SDK de Azure más reciente](https://azure.microsoft.com/downloads/) (2.9.1 o superior)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
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

## <a name="browsing-logic-apps"></a>Búsqueda de Logic Apps
Para buscar Logic Apps en Cloud Explorer, abra Cloud Explorer en Ver > Cloud Explorer. Puede realizar la búsqueda por grupo de recursos o tipo de recurso.  Si realiza la búsqueda por tipo de recurso, seleccione una suscripción, expanda la sección Logic Apps y seleccione una aplicación lógica.  Para realizar la acción deseada, puede hacer clic con el botón derecho en una de las aplicaciones lógicas o utilizar el menú Acciones situado en la parte inferior de Cloud Explorer.

![Examinar](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-logic-app-with-the-designer"></a>Edición de una aplicación lógica con el diseñador
Para abrir la aplicación lógica que está implementada actualmente con el mismo diseñador de Azure Portal, haga clic con el botón derecho en la aplicación lógica y seleccione "Abrir con el editor de aplicaciones lógicas".  Con el diseñador, puede editar la aplicación lógica, guardarla de nuevo en la nube e iniciar una nueva ejecución con el botón "Ejecutar desencadenador".

![Diseñador](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-logic-app-run-history"></a>Búsqueda del historial de ejecución de una aplicación lógica
Para ver el historial de ejecución de una aplicación lógica, haga clic con el botón derecho en la aplicación lógica y seleccione "Abrir el historial de ejecución".  En esta vista, puede establecer el orden en función de cualquiera de las propiedades mostradas. Para ello, solo tiene que seleccionar el encabezado de columna.  

![Ejecuciones](media/logic-apps-manage-from-vs/runs.png)

Si hace doble clic en una de las instancias en ejecución, aparecerá el historial de ejecución de dicha instancia. En este historial, podrá ver los resultados de la ejecución, incluidas las entradas y salidas de cada paso.

![Historial](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Pasos siguientes
* Para comenzar con las aplicaciones lógicas, siga el tutorial [Creación de una aplicación lógica](logic-apps-create-a-logic-app.md) .  
* [Ejemplos de aplicaciones lógicas y escenarios comunes](logic-apps-examples-and-scenarios.md)
* [Con las aplicaciones lógicas puede automatizar procesos empresariales.](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Obtenga información acerca de cómo integrar sus sistemas con Aplicaciones lógicas](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Jan17_HO3-->


