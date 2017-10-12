---
title: "Modificación del contenido de una página en el portal para desarrolladores de Azure API Management | Microsoft Docs"
description: "Aprenda a editar el contenido de una página en el portal para desarrolladores de Azure API Management."
services: api-management
documentationcenter: 
author: antonba
manager: vlvinogr
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
ms.openlocfilehash: 708c803c36c182ed90e04731b12d4ade00ae7ffb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Modificación del contenido y el diseño de páginas en el portal para desarrolladores de Azure API Management
Existen tres maneras fundamentales de personalizar el portal para desarrolladores en Azure API Management:

* [Editar el contenido de las páginas estáticas y los elementos de diseño de página][modify-content-layout] (que se explica en esta guía)
* [Actualizar los estilos usados para los elementos de página en el portal para desarrolladores][customize-styles]
* [Modificar las plantillas usadas para las páginas generadas por el portal][portal-templates] (por ejemplo, documentos de API, productos, autenticación de usuario, etc.)

## <a name="page-structure"></a>Estructura de las páginas del portal para desarrolladores

El portal para desarrolladores se basa en el sistema de administración de contenido. El diseño de cada página se compila en función de un conjunto de pequeños elementos de página conocidos como widgets:

![Estructura de las páginas del portal para desarrolladores][api-management-customization-widget-structure]

Todos los widgets son editables. 
* El contenido principal específico de cada página reside en el widget "Contenido". La edición de una página significa modificar el contenido de este widget.
* Todos los elementos de diseño de página están contenidos con los widgets restantes. Los cambios realizados en estos widgets se aplican a todas las páginas. Se hará referencia a ellos como "widgets de diseño".

En las páginas del día a día, al editar una solo se modifica normalmente el widget Contenido, que tendrá contenido diferente para cada página.

## <a name="modify-layout-widget"></a>Modificación del contenido de un widget de diseño

El contenido del portal para desarrolladores se modifica a través del portal para editores, al que se obtiene acceso desde Azure Portal. Para llegar a él, haga clic en **Portal para editores** desde la barra de herramientas de servicio de la instancia de API Management.

![Portal del publicador][api-management-management-console]

Para editar el contenido de ese widget, haga clic en **Widgets** desde el menú **Portal para desarrolladores** de la izquierda. En este ejemplo, vamos a modificar el contenido del widget Encabezado. Seleccione el widget **Encabezado** de la lista.

![Widgets header][api-management-widgets-header]

El contenido del encabezado se puede editar desde el campo **Cuerpo** . Cambie el texto como quiera y luego haga clic en **Guardar** en la parte inferior de la página.

Ahora debe aparecer el nuevo encabezado en todas las páginas del portal para desarrolladores.

> Para abrir el portal para desarrolladores desde el portal para editores, haga clic en **Portal para desarrolladores** en la barra superior.
> 
> 

## <a name="edit-page-contents"></a>Edición de los contenidos de una página

Para ver una lista de todas las páginas de contenido existentes, haga clic en **Contenido** en el menú **Portal para desarrolladores** del portal para editores.

![Manage content][api-management-customization-manage-content]

Haga clic en la página de **bienvenida** para editar lo que se muestra en la página principal del portal para desarrolladores. Haga los cambios que quiera, obtenga una vista previa de ellos si lo considera necesario y haga clic en **Publicar ahora** para que sean visibles para todos los usuarios.

> La página principal usa un diseño especial que le permite mostrar un banner en la parte superior. Este banner no se puede editar en la sección **Contenido** . Para editarlo, haga clic en **Widgets** en el menú **Portal para desarrolladores**, seleccione **Home page** (Página principal) en la lista desplegable **Current Layer** (Capa actual) y, por último, abra el elemento **Banner** (Banner) en la sección **Featured section** (Destacados). Los contenidos de este widget se pueden editar del mismo modo que cualquier otra página.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* [Actualizar los estilos usados para los elementos de página en el portal para desarrolladores][customize-styles]
* [Modificar las plantillas usadas para las páginas generadas por el portal][portal-templates] (por ejemplo, documentos de API, productos, autenticación de usuario, etc.)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
