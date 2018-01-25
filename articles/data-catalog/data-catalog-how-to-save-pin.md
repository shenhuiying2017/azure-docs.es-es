---
title: "Guardar búsquedas y anclar recursos de datos en Azure Data Catalog | Microsoft Docs"
description: "Artículo de procedimientos que destaca las funciones de Azure Data Catalog para guardar orígenes de datos y recursos de datos para su uso posterior."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: cced7b8253a45d990371d8f95fe36b580c2debbf
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Guardar búsquedas y anclar recursos de datos en Azure Data Catalog
## <a name="introduction"></a>Introducción
Azure Data Catalog proporciona funciones de detección de orígenes de datos. Puede buscar y filtrar rápidamente el catálogo para buscar orígenes de datos y entender su finalidad prevista, lo que permite encontrar fácilmente los datos correctos para el trabajo en cuestión.

Pero ¿qué ocurre cuando necesita trabajar regularmente con los mismos datos? ¿Qué sucede cuando usted y otros usuarios aportan regularmente sus conocimientos a los mismos orígenes de datos en el catálogo? En estas situaciones, puede no ser eficaz tener que emitir repetidamente las mismas búsquedas. Aquí es donde pueden ayudar las búsquedas guardadas y los recursos de datos anclados.

## <a name="saved-searches"></a>Búsquedas guardadas
Una búsqueda guardada en Data Catalog es una definición de búsqueda por usuario reutilizable. Puede definir una búsqueda, incluidos los términos, etiquetas y otros filtros de la búsqueda y, a continuación, guardarla. La definición de la búsqueda guardada puede volver a ejecutarse posteriormente para devolver los recursos de datos que coincidan con los criterios de búsqueda.

### <a name="create-a-saved-search"></a>Creación de una búsqueda guardada
Para crear una búsqueda guardada, haga lo siguiente:
1. En el portal de Azure Data Catalog, en la ventana **Búsqueda actual**, haga clic en **Guardar**. 

    ![Vínculo Guardar en la configuración de Búsqueda actual](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Escriba los criterios de búsqueda que desea volver a usar y, a continuación, haga clic en **Guardar**.

    ![Nombre de la búsqueda guardada en la configuración de Búsqueda actual](./media/data-catalog-how-to-save-pin/02-name.png)

3. Cuando se le solicite, escriba un nombre para la búsqueda guardada. Elija un nombre que sea significativo y que describa los recursos de datos que la búsqueda devolverá.

### <a name="manage-saved-searches"></a>Administración de búsquedas guardadas
Después de haber guardado una o varias búsquedas, aparece una opción **Búsquedas guardadas** debajo del cuadro **Búsqueda actual**. Cuando se expande la lista, aparecen todas las búsquedas guardadas.

 ![Lista de búsquedas guardadas](./media/data-catalog-how-to-save-pin/03-list.png)

Realice alguna de las acciones siguientes:

* Para ejecutar una búsqueda, seleccione una búsqueda guardada en la lista.

* Para ver una lista de opciones de administración de una búsqueda guardada, haga clic en la flecha abajo situada junto al nombre de la búsqueda.

    ![Opción para administrar búsquedas guardadas](./media/data-catalog-how-to-save-pin/04-managing.png)

* Para especificar un nuevo nombre para la búsqueda guardada, seleccione **Cambiar nombre**. No se cambiará la definición de la búsqueda.

* Para quitar la búsqueda guardada de la lista, seleccione **Eliminar** y, a continuación, confirme la eliminación.

* Para marcar la búsqueda guardada como búsqueda predeterminada, seleccione **Guardar como predeterminado**. Si realiza una búsqueda "vacía" desde la página principal de Azure Data Catalog, se ejecutará su búsqueda predeterminada. Además, la búsqueda que se marca como predeterminada aparece en la parte superior de la lista **Búsquedas guardadas**.

### <a name="organizational-saved-searches"></a>Búsquedas guardadas de la organización
Todos los usuarios de la organización pueden guardar búsquedas para su propio uso. Los administradores de Azure Data Catalog también pueden guardar búsquedas para todos los usuarios de la organización. Cuando los administradores guarden una búsqueda, les aparecerá la opción **Compartir dentro de la compañía**. Si selecciona esta opción, compartirá la búsqueda guardada con todos los usuarios de la organización.

 ![Búsquedas guardadas de la organización](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Recursos de datos anclados
Las búsquedas guardadas le permiten guardar y volver a usar las definiciones de búsqueda. Los recursos de datos que devuelven las búsquedas pueden cambiar con el tiempo a medida que cambia el contenido del catálogo. Cuando ancla recursos de datos, puede identificar explícitamente recursos de datos específicos para facilitar su acceso sin necesidad de usar una búsqueda.

Anclar un recurso de datos es sencillo. Para agregar el recurso de datos a la lista anclada, solo tiene que hacer clic en el icono de **anclaje**. Este icono aparece en la esquina del icono del recurso en la vista de iconos, y en la columna situada más a la izquierda en la vista de lista del portal de Azure Data Catalog.

![El icono de anclaje de recurso de datos](./media/data-catalog-how-to-save-pin/05-pinning.png)

Desanclar un recurso de datos es igual de sencillo. Solo tiene que hacer clic en el icono de **desanclaje** para cambiar la configuración del recurso seleccionado.

![El icono de desanclaje de recurso de datos](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>La sección Mis recursos
La página principal del portal de Data Catalog incluye una sección **Mis recursos** que muestra los recursos de interés para el usuario actual. Esta sección incluye tanto recursos anclados como búsquedas guardadas.

![La sección Mis recursos de la página principal](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Resumen
Azure Data Catalog proporciona funciones que le permiten detectar fácilmente los orígenes de datos que necesita, por lo que usted y los demás miembros de la organización pueden dedicar menos tiempo a buscar datos y más tiempo a trabajar con ellos. Las búsquedas guardadas y los recursos de datos anclados se basan en estas funciones centrales para que los usuarios puedan identificar fácilmente los orígenes de datos con los que trabajan repetidamente.
