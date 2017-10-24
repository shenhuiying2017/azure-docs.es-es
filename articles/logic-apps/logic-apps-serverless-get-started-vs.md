---
title: "Compilación de una aplicación sin servidor en Visual Studio | Microsoft Docs"
description: "Comience a usar su primera aplicación sin servidor con esta guía para crear, implementar y administrar la aplicación en Visual Studio."
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 3672beda8a502e5fe2c8182076a8edef7ee9ebf6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-serverless-app-in-visual-studio-with-logic-apps-and-functions"></a>Cree una aplicación sin servidor en Visual Studio con Logic Apps y Functions

Las herramientas y funcionalidades sin servidor de Azure permiten desarrollar e implementar aplicaciones rápidamente en la nube.  Este documento se centra en cómo empezar a trabajar en Visual Studio creando una aplicación sin servidor.  Encontrará información general sobre soluciones sin servidor en [este artículo](logic-apps-serverless-overview.md).

## <a name="getting-everything-ready"></a>Preparación

Estos son los requisitos previos para crear una aplicación sin servidor de Visual Studio:

* [Visual Studio 2017](https://www.visualstudio.com/vs/) o Visual Studio 2015 - Community, Professional o Enterprise
* [Herramientas de Logic Apps para Visual Studio](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)
* [SDK de Azure más reciente](https://azure.microsoft.com/downloads/) (2.9.1 o superior)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* [Herramientas principales de Azure Functions](https://www.npmjs.com/package/azure-functions-core-tools) para depurar Functions localmente
* Acceso a la web para usar el diseñador incrustado de Logic App

## <a name="getting-started-with-a-deployment-template"></a>Introducción a las plantillas de implementación

La administración de recursos en Azure se realiza en un grupo de recursos.  Un grupo de recursos es una agrupación lógica de recursos.  Los grupos de recursos permiten la implementación y administración de una colección de recursos.  Para una aplicación sin servidor de Azure, nuestro grupo de recursos contiene Azure Logic Apps y Azure Functions.  Con el proyecto del grupo de recursos en Visual Studio se pueden desarrollar, administrar e implementar toda la aplicación como un solo activo.

### <a name="create-a-resource-group-project-in-visual-studio"></a>Creación de un proyecto de grupo de recursos en Visual Studio

1. En Visual Studio, haga clic para agregar un **Nuevo proyecto**.
1. En la categoría **Nube**, seleccione esta opción para crear un proyecto de **grupo de recursos** de Azure  
 * Si no ve la categoría o el proyecto en la lista, asegúrese de haber instalado el SDK de Azure para Visual Studio
1. Asigne al proyecto un nombre y una ubicación y seleccione **Aceptar** para crear Visual Studio. Se le pedirá que seleccione una plantilla.  Puede empezar en blanco, con una lógica de aplicación o con otro recurso.  Sin embargo, en este caso usaremos una plantilla de inicio rápido de Azure para empezar a trabajar con una aplicación sin servidor.
1. Seleccione esta opción para mostrar las plantillas desde **Inicio rápido de Azure** ![Selección de plantillas de inicio rápido de Azure][1].
1. Seleccione la plantilla de inicio rápido sin servidor: **101-logic-app-and-function-app** y haga clic en **Aceptar**

La plantilla de inicio rápido crea una plantilla de implementación en el proyecto del grupo de recursos.  La plantilla contiene una aplicación lógica sencilla que llama a Azure Functions y devuelve el resultado.  Si abre el archivo `azuredeploy.json` en el Explorador de soluciones, verá los recursos de la aplicación sin servidor.

## <a name="deploying-the-serverless-application"></a>Implementación de la aplicación sin servidor

Antes de abrir el diseñador visual de Logic App en Visual Studio, debe haber un grupo de recursos de Azure implementado.  Esto permite al diseñador crear y usar las conexiones a los recursos y servicios en la aplicación lógica.  Para empezar, solo hay que implementar la solución creada.

1. Haga clic con el botón derecho en el proyecto de Visual Studio, seleccione **Implementar**y cree una **Nueva** implementación ![Selección de una nueva implementación de recursos][2].
1. Seleccione una suscripción válida a Azure y el grupo de recursos
1. Seleccione esta opción para **implementar** la solución
1. Escriba el nombre de la aplicación lógica y de la función de Azure Function App.  El nombre de la función de Azure no tiene que ser único globalmente

La solución sin servidor se implementa en el grupo de recursos especificado.  Si observa la **salida** en Visual Studio, puede ver el estado de la implementación.

## <a name="editing-the-logic-app-in-visual-studio"></a>Edición de la aplicación lógica en Visual Studio

Una vez que se haya implementado la solución en cualquier grupo de recursos, el diseñador visual se puede utilizar para editar la aplicación lógica.

1. Haga clic con el botón derecho en el archivo `azuredeploy.json` en el Explorador de soluciones y seleccione **Abrir con el diseñador de aplicaciones lógicas**
1. Seleccione el **grupo de recursos** y la **ubicación** donde se haya implementado la solución y seleccione **Aceptar**

El diseñador visual de la aplicación lógica ahora debería verse en Visual Studio.  Puede continuar agregando pasos, modificando el flujo de trabajo y guardando los cambios.  También puede crear aplicaciones lógicas desde Visual Studio.  Si hace clic con el botón derecho en **Recursos** en el explorador de plantillas, puede agregar una **aplicación lógica** al proyecto.  Las aplicaciones lógicas vacías se cargan en el diseñador visual sin una implementación previa en un grupo de recursos.

### <a name="managing-and-viewing-run-history-for-a-deployed-logic-app"></a>Administración y visualización del historial de ejecución de una aplicación de la lógica implementada

También puede administrar y ver el historial de ejecución de las aplicaciones lógicas implementadas en Azure.  En la herramienta **Cloud Explorer** de Visual Studio, haga clic con el botón derecho en cualquier aplicación lógica para editarla, deshabilitarla, ver sus propiedades o comprobar el historial de ejecución.  Si hace clic en Editar, también podrá descargar una aplicación lógica publicada en un proyecto del grupo de recursos de Visual Studio.  De este modo, podrá importar y administrar la aplicación lógica desde Visual Studio aunque la haya empezado a crear en Azure Portal.

## <a name="developing-an-azure-function-in-visual-studio"></a>Desarrollo de una función de Azure en Visual Studio

La plantilla de implementación implementa cualquier instancia de Azure Functions que se encuentre en la solución para el repositorio git especificado en las variables `azuredeploy.json`.  Si crea un proyecto de función dentro de la solución, actívelo en el control de código fuente (GitHub, Visual Studio Team Services, etc.) y actualice la variable `repo`. La plantilla implementará la función de Azure.

### <a name="creating-an-azure-function-project"></a>Creación de una función de Azure

Si usa JavaScript, Python, F #, Bash, Batch o PowerShell, siga [los pasos de la CLI de Functions](../azure-functions/functions-run-local.md) para crear un proyecto.  Si desarrolla una función en C#, puede usar una [biblioteca de clases de C#](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/) en la solución actual para la función de Azure.

## <a name="next-steps"></a>Pasos siguientes

* [Obtener información sobre cómo crear un panel de redes sociales sin servidor](logic-apps-scenario-social-serverless.md)
* [Administrar una aplicación lógica desde Visual Studio Cloud Explorer](logic-apps-manage-from-vs.md)
* [Lenguaje de definición de flujo de trabajo en una aplicación lógica](logic-apps-workflow-definition-language.md)

<!-- Image references -->
[1]: ./media/logic-apps-serverless-get-started-vs/select-template.png
[2]: ./media/logic-apps-serverless-get-started-vs/deploy.png
