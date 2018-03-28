---
title: 'Administración de aplicaciones lógicas con Visual Studio: Azure Logic Apps | Microsoft Docs'
description: Administración de aplicaciones lógicas y otros recursos de Azure con Visual Studio Cloud Explorer
author: ecfan
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 03/15/2018
ms.author: estfan; LADocs
ms.openlocfilehash: db847d5fa3d5f5b2b2f0293f1756226870a8b47e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="manage-logic-apps-with-visual-studio"></a>Administración de aplicaciones lógicas con Visual Studio

Si bien puede crear, editar, administrar e implementación aplicaciones lógicas en <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, también puede usar Visual Studio cuando desea agregar aplicaciones lógicas para el control de código fuente, publicar versiones diferentes y crear plantillas de [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para distintos entornos de implementación. Con Visual Studio Cloud Explorer, puede encontrar y administrar las aplicaciones lógicas junto con otros recursos de Azure. Por ejemplo, puede abrir, descargar, editar, ejecutar, ver el historial de ejecución, deshabilitar y habilitar las aplicaciones lógicas que ya están implementadas en Azure Portal. Si es la primera vez que usa Azure Logic Apps en Visual Studio, obtenga información sobre [cómo crear aplicaciones lógicas con Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Al implementar o publicar una aplicación lógica desde Visual Studio se sobrescribe la versión de esa aplicación en Azure Portal. Por tanto, si hace cambios en Azure Portal que desea mantener, asegúrese de [actualizar la aplicación lógica en Visual Studio](#refresh) desde Azure Portal antes de volver a implementar o publicar desde Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>requisitos previos

* Si no tiene una suscripción de Azure, <a href="https://azure.microsoft.com/free/" target="_blank">regístrese para obtener una cuenta gratuita de Azure</a>.

* Descargue e instale estas herramientas si no las tiene aún: 

  * <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2017 o Visual Studio 2015 - Community Edition o superior</a>. 
  Este inicio rápido usa Visual Studio Community 2017, que es gratuito.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Azure SDK (2.9.1 o una versión posterior)</a> y <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Azure PowerShell</a>

  * <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551" target="_blank">Herramientas de Azure Logic Apps para Visual Studio de 2017</a> o la <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio" target="_blank">versión de Visual Studio 2015</a> 
  
    Puede descargar e instalar Herramientas de Azure Logic Apps directamente desde Visual Studio Marketplace o aprender <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">a instalar esta extensión desde Visual Studio</a>. 
    Asegúrese de reiniciar Visual Studio después de finalizar la instalación.

* Acceso a la web al usar el Diseñador de aplicación lógica integrado

  El diseñador requiere una conexión a Internet para crear recursos en Azure, y leer las propiedades y los datos de los conectores de la aplicación lógica. 
  Por ejemplo, si usa el conector de Dynamics CRM Online, el diseñador comprueba en la instancia de CRM si hay propiedades predeterminadas y propiedades personalizadas disponibles.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Búsqueda de las aplicaciones lógicas

En Visual Studio, puede encontrar todas las aplicaciones lógicas que están asociadas con la suscripción de Azure y que están implementadas en Azure Portal mediante Cloud Explorer.

1. Abra Visual Studio. En el menú **Ver**, seleccione **Cloud Explorer**.

2. En Cloud Explorer, elija **Administración de cuentas**. Seleccione la suscripción de Azure asociada con las aplicaciones lógicas y, luego, elija **Aplicar**. Por ejemplo: 

   ![Elección de "Administración de cuentas"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

2. En función de si busca por **Grupos de recursos** o **Tipos de recursos**, siga estos pasos:

   * **Grupos de recursos**: en la suscripción de Azure, Cloud Explorer muestra todos los grupos de recursos que están asociados con esa suscripción. 
   Expanda el grupo de recursos que contiene la aplicación lógica y selecciónela.

   * **Tipos de recursos**: en la suscripción de Azure, expanda **Aplicaciones lógicas**. Una vez que Cloud Explorer muestra todas las aplicaciones lógicas implementadas que están asociadas a la suscripción, seleccione su aplicación lógica.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Apertura en Visual Studio

En Visual Studio, puede abrir las aplicaciones lógicas anteriormente creadas e implementadas de manera directa a través de Azure Portal o como proyectos de Azure Resource Manager con Visual Studio.

1. Abra Cloud Explorer y busque su aplicación lógica. 

2. En el menú contextual de la aplicación lógica, seleccione **Abrir con el editor de aplicaciones lógicas**.

   Este ejemplo muestra las aplicaciones lógicas según el tipo de recurso, por lo que las aplicaciones lógicas aparecen en la sección **Aplicaciones lógicas**.

  ![Abrir aplicación lógica implementada desde Azure Portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Una vez que la aplicación lógica se abre en el Diseñador de aplicaciones lógicas, en la parte inferior del diseñador, puede elegir la **vista Código** para poder revisar la estructura de definición de aplicación lógica subyacente. 
   Si quiere crear una plantilla de implementación para la aplicación lógica, obtenga información sobre [cómo descargar una plantilla de Azure Resource Manager](#download-logic-app) para esa aplicación lógica. Obtenga más información sobre las [plantillas de Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Descarga de Azure

Puede descargar aplicaciones lógicas desde <a href="https://portal.azure.com" target="_blank">Azure Portal</a> y guardarlas como plantillas de [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Luego puede editar de manera local las plantillas con Visual Studio y personalizar las aplicaciones lógicas para distintos entornos de implementación. Al descargar las aplicaciones lógicas, se *parametriza* automáticamente sus definiciones dentro de las [plantillas de Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment), que también usan la notación de objetos JavaScript (JSON).

1. En Visual Studio, abra Cloud Explorer y, luego, busque y seleccione la aplicación lógica que desea descargar de Azure.

2. En el menú contextual de la aplicación, seleccione **Abrir con el editor de aplicaciones lógicas**.

   El Diseñador de aplicaciones lógicas se abre y muestra la aplicación lógica. 
   Para revisar la estructura y la definición subyacentes de la aplicación lógica, en la parte inferior del diseñador, elija la **vista Código**. 

3. En la barra de herramientas del diseñador, elija **Descargar**.

   ![Elegir "Descargar"](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Cuando se le pida una ubicación, vaya a esa ubicación y guarde la plantilla de Resource Manager para la definición de aplicación lógica en formato de archivo JSON (.json). 

La definición de aplicación lógica aparece en la subsección `resources` dentro de la plantilla de Resource Manager. Ahora puede editar la definición de aplicación lógica y la plantilla de Resource Manager con Visual Studio. También puede agregar la plantilla como un proyecto de Azure Resource Manager en una solución de Visual Studio. Obtenga más información sobre los [proyectos de Resource Manager para las aplicaciones lógicas en Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Actualización desde Azure

Si edita la aplicación lógica en Azure Portal y quiere mantener esos cambios, asegúrese de actualizar la versión de esa aplicación en Visual Studio con esos cambios. 

* En Visual Studio, en la barra de herramientas del Diseñador de aplicaciones lógicas, elija **Actualizar**.

  O bien

* En Visual Studio Cloud Explorer, abra el menú contextual de la aplicación lógica y seleccione **Actualizar**. 

![Actualización de aplicación lógica con actualizaciones](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Publicación de actualizaciones de la aplicación lógica

Cuando esté listo para implementar las actualizaciones de la aplicación lógica desde Visual Studio a Azure, en la barra de herramientas del Diseñador de aplicaciones lógicas, elija **Publicar**.

![Publicar aplicación lógica actualizada](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Ejecución manual de la aplicación lógica

Puede desencadenar manualmente una aplicación lógica implementada en Azure desde Visual Studio. En la barra de herramientas del Diseñador de aplicaciones lógicas, elija **Ejecutar desencadenador**.

![Ejecución manual de la aplicación lógica](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Revisar el historial de ejecución.

Para comprobar el estado y diagnosticar problemas con las ejecuciones de la aplicación lógica, puede revisar los detalles, como las entradas y las salidas, para esas ejecuciones en Visual Studio.

1. En Cloud Explorer, abra el menú contextual de la aplicación lógica y seleccione **Abrir el historial de ejecución**.

   ![Abrir el historial de ejecución](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

2. Para ver los detalles de una ejecución específica, haga doble clic en una ejecución. Por ejemplo: 

   ![Historial de ejecución detallado](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Para ordenar la tabla por propiedad, elija el encabezado de columna de esa propiedad. 

3. Expanda los pasos cuyas entradas y salidas desea revisar. Por ejemplo: 

   ![Ver las entradas y salidas de cada paso](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Deshabilitación o habilitación de la aplicación lógica

Sin eliminar la aplicación lógica, puede evitar que el desencadenador se active la próxima vez que se cumpla una condición desencadenadora. Si deshabilita la aplicación lógica, evita que el motor de Logic Apps cree y ejecute futuras instancias de flujo de trabajo para la aplicación lógica.
En Cloud Explorer, abra el menú contextual de la aplicación lógica y seleccione **Deshabilitar**.

![Deshabilitar la aplicación lógica](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

Cuando esté listo para que la aplicación lógica reanude la operación, puede reactivar la aplicación lógica. En Cloud Explorer, abra el menú contextual de la aplicación lógica y seleccione **Habilitar**.

![Habilitar la aplicación lógica](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Eliminación de la aplicación lógica

Para eliminar la aplicación lógica desde Azure Portal, en Cloud Explorer, abra el menú contextual de la aplicación lógica y seleccione **Eliminar**.

![Eliminación de la aplicación lógica](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

## <a name="next-steps"></a>Pasos siguientes

En este artículo, aprendió a administrar las aplicaciones lógicas implementadas con Visual Studio. A continuación, obtenga información sobre cómo personalizar las definiciones de aplicación lógica para la implementación:

> [!div class="nextstepaction"]
> [Creación de definiciones de aplicación lógica en JSON](../logic-apps/logic-apps-author-definitions.md)