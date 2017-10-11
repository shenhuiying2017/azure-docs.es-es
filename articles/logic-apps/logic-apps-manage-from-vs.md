---
title: "Administración de aplicaciones lógicas en Visual Studio: Azure Logic Apps | Microsoft Docs"
description: "Administración de aplicaciones lógicas y otros recursos de Azure con Visual Studio Cloud Explorer"
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
ms.custom: H1Hack27Feb2017
ms.date: 12/19/2016
ms.author: LADocs; klam
ms.openlocfilehash: a5bf24de1a7a2b6d4c1ae6416c95d83ef7506da3
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Administración de sus aplicaciones lógicas con Visual Studio Cloud Explorer

Aunque [Azure Portal](https://portal.azure.com/) ofrece un medio excelente para diseñar y administrar Azure Logic Apps, puede usar Visual Studio Cloud Explorer para administrar muchos recursos de Azure, como las aplicaciones lógicas. Visual Studio Cloud Explorer le permite explorar, administrar, editar y descargar aplicaciones lógicas publicadas. Las tareas de administración incluyen habilitar, deshabilitar y ver el historial de ejecuciones. 

Para poder acceder a las aplicaciones lógicas y administrarlas en Visual Studio, instale y configure estas herramientas de Visual Studio para Azure Logic Apps. 

## <a name="prerequisites"></a>Requisitos previos

* [Visual Studio 2015 o Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [SDK de Azure más reciente](https://azure.microsoft.com/downloads/) (2.9.1 o superior)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Acceso a la web para usar el diseñador incrustado

## <a name="install-visual-studio-tools-for-logic-apps"></a>Instalación de herramientas de Visual Studio para Logic Apps

Después de instalar los requisitos previos, descargue e instale las Herramientas de Azure Logic Apps para Visual Studio.

1. Abra Visual Studio. En el menú **Herramientas**, seleccione **Extensiones y actualizaciones**.
2. Expanda la categoría **En línea** para que pueda buscar en línea en la Galería de Visual Studio.
3. Busque **Logic Apps** hasta que encuentre las **herramientas de Azure Logic Apps para Visual Studio**.
4. Para descargar e instalar la extensión, haga clic en el botón **Descargar**.
5. Reinicie Visual Studio después de la instalación.

> [!NOTE]
> Para descargar directamente las Herramientas de Azure Logic Apps para Visual Studio, vaya a [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Búsqueda de aplicaciones lógicas en Cloud Explorer

1.  Para abrir Cloud Explorer, en el menú **Vista**, elija **Cloud Explorer**.
2.  Busque la aplicación lógica. Puede hacerlo por grupo de recursos o por tipo de recurso. 

    * Si explora por tipo de recurso, seleccione su suscripción de Azure y expanda la sección **Logic Apps**. Luego, seleccione la aplicación lógica. 
    * Si explora por grupo de recursos, expanda el grupo de recursos que tiene la aplicación lógica y seleccione la aplicación lógica.

    Para ver los comandos de la aplicación lógica, haga clic con el botón derecho en la aplicación lógica; o bien, en la parte inferior de Cloud Explorer, elija del menú **Acciones**.

    ![Búsqueda de su aplicación lógica](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-apps-designer"></a>Edición de la aplicación lógica con el Diseñador de Logic Apps

En Cloud Explorer, puede abrir una aplicación lógica implementada actualmente en el mismo diseñador que usa en Azure Portal. 

* Para editar la aplicación lógica, en Cloud Explorer, haga clic con el botón derecho en la aplicación lógica y seleccione **Abrir con el editor de aplicaciones lógicas**. 

* Para publicar las actualizaciones en la nube, elija **Publicar**. 

* Para iniciar una nueva ejecución, elija **Ejecutar desencadenador**.

![Diseñador de Logic Apps](./media/logic-apps-manage-from-vs/designer.png)

En el diseñador, también puede **descargar** una aplicación lógica. Esta acción parametriza automáticamente la definición de aplicación lógica y la guarda como una plantilla de implementación de Azure Resource Manager. Puede agregar esta plantilla de implementación al proyecto Grupo de recursos de Azure.

## <a name="browse-your-logic-app-run-history"></a>Búsqueda del historial de ejecución de su aplicación lógica

Para ver el historial de ejecución de su aplicación lógica, haga clic con el botón derecho en la aplicación lógica y seleccione **Abrir el historial de ejecución**. Para reordenar su historial de ejecución basándose en cualquiera de las propiedades mostradas, seleccione el encabezado de columna.

![Historial de ejecuciones](media/logic-apps-manage-from-vs/runs.png)

Para mostrar el historial de ejecuciones de una instancia y revisar los resultados de la ejecución, incluidas las entradas y salidas de cada paso, haga doble clic en una de las instancias de ejecución.

![Resultados del historial de ejecuciones, entradas y salidas de los pasos](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Pasos siguientes

* [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](logic-apps-create-a-logic-app.md)
* [Creación e implementación de aplicaciones lógicas en Visual Studio](logic-apps-deploy-from-vs.md)
* [Ejemplos de aplicaciones lógicas y escenarios comunes](logic-apps-examples-and-scenarios.md)
* [Vídeo: Automate business processes with Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) (Automatización de los procesos de negocio con Azure Logic Apps)
* [Vídeo: Integrate your systems with Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462) (Integración de los sistemas con Azure Logic Apps)
