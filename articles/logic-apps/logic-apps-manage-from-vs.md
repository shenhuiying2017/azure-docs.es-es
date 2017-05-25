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
ms.translationtype: Human Translation
ms.sourcegitcommit: 2fafb3c127183c4903ed7ee0da5811988db9116c
ms.openlocfilehash: 9a542f83eb35b159de04f0395cd850376da498de
ms.contentlocale: es-es
ms.lasthandoff: 03/02/2017

---

# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Administración de sus aplicaciones lógicas con Visual Studio Cloud Explorer

Aunque [Azure Portal](https://portal.azure.com/) es un medio excelente para diseñar y administrar Azure Logic Apps, puede administrar muchos de los recursos de Azure, incluidas las aplicaciones lógicas, desde Visual Studio usando Visual Studio Cloud Explorer. Puede examinar aplicaciones lógicas publicadas y realizar tareas como habilitar y deshabilitar sus aplicaciones lógicas o editar y consultar historiales de ejecución. 

## <a name="installation-steps"></a>Pasos de instalación

Para instalar y configurar las herramientas de Visual Studio para Azure Logic Apps siga estos pasos.

### <a name="prerequisites"></a>Requisitos previos

* [Visual Studio 2015 o Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [SDK de Azure más reciente](https://azure.microsoft.com/downloads/) (2.9.1 o superior)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Acceso a la web para usar el diseñador incrustado

### <a name="install-visual-studio-tools-for-logic-apps"></a>Instalación de herramientas de Visual Studio para Logic Apps

Una vez cumplidos los requisitos previos:

1. Abra Visual Studio. En el menú **Herramientas**, seleccione **Extensiones y actualizaciones**.
2. Expanda la categoría **En línea** para poder buscar en línea.
3. Busque **Logic Apps** hasta que encuentre las **herramientas de Azure Logic Apps para Visual Studio**.
4. Para descargar e instalar la extensión, haga clic en el botón **Descargar**.
5. Reinicie Visual Studio después de la instalación.

> [!NOTE]
> También puede descargar las herramientas de Azure Logic Apps para Visual Studio directamente desde el [Marketplace de Visual Studio](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Búsqueda de aplicaciones lógicas en Cloud Explorer

1.    Para abrir Cloud Explorer, en el menú **Vista**, elija **Cloud Explorer**.
2.    Busque la aplicación lógica. Puede hacerlo por grupo de recursos o por tipo de recurso. 

    Si realiza la búsqueda por tipo de recurso, seleccione una suscripción de Azure, expanda la sección Logic Apps y seleccione una aplicación lógica. 
    Puede hacer clic con el botón derecho en una aplicación lógica o elegir una del menú **Acciones** en la parte inferior de Cloud Explorer.

    ![Búsqueda de su aplicación lógica](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-app-designer"></a>Edición de la aplicación lógica en Diseñador de aplicación lógica

Para abrir una aplicación lógica que está implementada actualmente con el mismo diseñador que usa en Azure Portal, haga clic con el botón derecho en su aplicación lógica y seleccione **Abrir con el editor de aplicaciones lógicas**. 

En el diseñador, puede editar su aplicación lógica, guardar las actualizaciones en la nube e iniciar una nueva ejecución eligiendo **Ejecutar desencadenador**.

![Diseñador de aplicaciones lógicas](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-your-logic-app-run-history"></a>Búsqueda del historial de ejecución de su aplicación lógica

Para ver el historial de ejecución de su aplicación lógica, haga clic con el botón derecho en la aplicación lógica y seleccione **Abrir el historial de ejecución**. Para reordenar su historial de ejecución basándose en cualquiera de las propiedades mostradas, seleccione el encabezado de columna.

![Historial de ejecuciones](media/logic-apps-manage-from-vs/runs.png)

Para mostrar el historial de ejecución de una instancia y ver los resultados de la ejecución, incluidas las entradas y salidas de cada paso, haga doble clic en una de las instancias ejecutadas.

![Resultados de historial de ejecución, entradas y salidas de los pasos](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Pasos siguientes

*    Para empezar a trabajar con Azure Logic Apps, aprenda a [crear su primera aplicación lógica en Azure Portal](logic-apps-create-a-logic-app.md)
* [Creación e implementación de aplicaciones lógicas en Visual Studio](logic-apps-deploy-from-vs.md)
* [Ejemplos de aplicaciones lógicas y escenarios comunes](logic-apps-examples-and-scenarios.md)
* [Aprenda a automatizar procesos empresariales con Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694)
* [Aprenda a integrar sus sistemas con Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)

