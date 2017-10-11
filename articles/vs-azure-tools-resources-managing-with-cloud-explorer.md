---
title: "Administración de recursos de Azure con Cloud Explorer | Microsoft Docs"
description: "Obtenga más información sobre cómo usar Cloud Explorer para examinar y administrar recursos de Azure en Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 6347dc53-f497-49d5-b29b-e8b9f0e939d7
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/25/2017
ms.author: kraigb
ms.openlocfilehash: 6e6d8d559f0251b71bfa6d529ead82a246cb3235
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="manage-the-resources-associated-with-your-azure-accounts-in-visual-studio-cloud-explorer"></a>Administración de los recursos asociados con las cuentas de Azure en Visual Studio Cloud Explorer
Cloud Explorer le permite consultar sus recursos y grupos de recursos de Azure, inspeccionar sus propiedades y realizar acciones de diagnóstico de desarrollador clave desde dentro de Visual Studio. 

Del mismo modo que [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040), Cloud Explorer se basa en la pila de Azure Resource Manager. Por lo tanto, Cloud Explorer entiende de recursos como, grupos de recursos de Azure, y de servicios de Azure, como aplicaciones lógicas y aplicaciones de API. Además, admite [control de acceso basado en rol](active-directory/role-based-access-control-configure.md) (RBAC). 

## <a name="prerequisites"></a>Requisitos previos
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) con la **carga de trabajo de Azure** seleccionada o una versión anterior de Visual Studio con [Microsoft Azure SDK para .NET 2.9](https://www.microsoft.com/en-us/download/details.aspx?id=51657).
- Cuenta de Microsoft Azure: si todavía no tiene ninguna cuenta, puede [registrarse para una evaluación gratuita](http://go.microsoft.com/fwlink/?LinkId=623901) o [activar las ventajas de suscriptor de Visual Studio](http://go.microsoft.com/fwlink/?LinkId=623901).

> [!NOTE]
> Para ver Cloud Explorer, seleccione **Ver** > **Cloud Explorer** en la barra de menús.   
> 
> 

## <a name="add-an-azure-account-to-cloud-explorer"></a>Incorporación de una cuenta de Azure en Cloud Explorer
Para consultar los recursos asociados con una cuenta de Azure, primero debe agregarla a Cloud Explorer. 

1. En **Cloud Explorer**, seleccione **Configuración de la cuenta de Azure**.

    ![Icono de configuración de la cuenta de Azure en Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Seleccione **Agregar nueva cuenta**. 

    ![Vínculo para agregar cuenta en Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/add-account-link.png)

1. Inicie sesión en la cuenta de Azure cuyos recursos desea examinar. 

1. Una vez que inicie sesión en una cuenta de Azure, se muestran las suscripciones asociadas con esa cuenta. Active las casillas de las suscripciones de cuenta que desea examinar y, luego, seleccione **Aplicar**. 
 
    ![Cloud Explorer: seleccione las suscripciones de Azure que se van a mostrar](media/vs-azure-tools-resources-managing-with-cloud-explorer/select-subscriptions.png)

1. Después de seleccionar las suscripciones cuyos recursos desea examinar, tanto las suscripciones como los recursos aparecerán en Cloud Explorer.

    ![Listado de recursos de Cloud Explorer para una cuenta de Azure](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-listed.png)

## <a name="remove-an-azure-account-from-cloud-explorer"></a>Eliminación de una cuenta de Azure de Cloud Explorer 

1. En **Cloud Explorer**, seleccione **Configuración de la cuenta de Azure**.

    ![Icono de configuración de la cuenta de Azure en Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Seleccione **Quitar** junto a la cuenta que desea quitar.

    ![Icono de configuración de la cuenta de Azure en Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/remove-account.png)

## <a name="view-resource-types-or-resource-groups"></a>Vista de tipos de recursos o grupos de recursos
Para ver los recursos de Azure, puede elegir la vista **Tipos de recursos** o **Grupos de recursos**.

1. En **Cloud Explorer**, seleccione la lista desplegable de vistas de recursos.

    ![Lista desplegable de Cloud Explorer para seleccionar la vista de recursos deseada](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-view-dropdown.png)

1. En el menú contextual, seleccione la vista deseada: 

    - Vista **Tipos de recursos**: la vista común que se usa en [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). Muestra los recursos de Azure clasificados según su tipo, como aplicaciones web, cuentas de almacenamiento y máquinas virtuales. 
    - Vista **Grupos de recursos**: clasifica los recursos de Azure según el grupo de recursos de Azure al que están asociados. Un grupo de recursos es una agrupación de recursos de Azure, normalmente usados por una aplicación específica. Para más información sobre los grupos de recursos de Azure, consulte [Información general de Azure Resource Manager](./azure-resource-manager/resource-group-overview.md).

    La imagen siguiente muestra una comparación de ambas vistas de recursos:

    ![Comparación de las vistas de recursos de Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/resource-views-comparison.png)

## <a name="view-and-navigate-resources-in-cloud-explorer"></a>Visualización y navegación por los recursos en Cloud Explorer
Para navegar a un recurso de Azure y consultar su información en Cloud Explorer, expanda el tipo del elemento o el grupo de recursos asociado y, luego, seleccione el recurso. Cuando selecciona un recurso, aparece información en las dos pestañas, **Acciones** y **Propiedades**, que se encuentran en la parte inferior de Cloud Explorer. 

- Pestaña **Acciones**: muestra las acciones que puede realizar en Cloud Explorer con el recurso seleccionado. También puede ver estas opciones si hace clic con el botón derecho en el recurso para ver el menú contextual.

- Pestaña **Propiedades**: muestra las propiedades del recurso, como su tipo, configuración local y grupo de recursos al que está asociado.

La imagen siguiente muestra un ejemplo de comparación de lo que ve en cada pestaña para una instancia de App Service:

![](./media/vs-azure-tools-resources-managing-with-cloud-explorer/actions-and-properties.png)

Cada recurso tiene la acción **Abrir en el portal**. Cuando se selecciona esta acción, Cloud Explorer muestra el recurso seleccionado en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). La característica **Abrir en el portal** es especialmente útil para navegar a los recursos profundamente anidados.

También pueden aparecer acciones adicionales y valores de propiedad basados en el recurso de Azure. Por ejemplo, las aplicaciones web y lógicas también tienen las acciones **Abrir en el explorador** y **Adjuntar depurador**, además de **Abrir en el portal**. Cuando se elige un blob, una cola o una tabla de cuenta de almacenamiento, aparecen acciones para abrir editores. Las aplicaciones de Azure tienen las propiedades **URL** y **Status**, mientras que los recursos de almacenamiento tienen propiedades de cadena de conexión y de clave.

## <a name="find-resources-in-cloud-explorer"></a>Búsqueda de recursos en Cloud Explorer
Para buscar recursos con un nombre específico en las suscripciones de cuenta de Azure, escriba el nombre en el cuadro de **búsqueda** en Cloud Explorer.

![Buscar recursos en Cloud Explorer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/search-for-resources.png)

A medida que escribe caracteres en el cuadro de **búsqueda**, solo los recursos que coinciden con los caracteres aparecen en el árbol de recursos.
