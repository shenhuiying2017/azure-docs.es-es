---
title: "Personalización del estilo del portal para desarrolladores en Azure API Management | Microsoft Docs"
description: "Aprenda a modificar los estilos usados en cualquier página del portal para desarrolladores en Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
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
ms.openlocfilehash: 89baf60d0204a1701e93309f09b90bc94c4ca57b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="customize-the-styling-of-the-developer-portal-in-azure-api-management"></a>Personalización del estilo del portal para desarrolladores en Azure API Management
Existen tres maneras fundamentales de personalizar el portal para desarrolladores en Azure API Management:

* [Editar el contenido de las páginas estáticas y los elementos de diseño de página][modify-content-layout]
* [Actualizar los estilos usados para los elementos de página en el portal para desarrolladores][customize-styles] (que se explica en esta guía)
* [Modificar las plantillas usadas para las páginas generadas por el portal][portal-templates] (por ejemplo, documentos de API, productos, autenticación de usuario, etc.)

## <a name="change-headers-styling"></a>Cambio del estilo de los elementos de página

Los colores, las fuentes, los tamaños, los espacios y otros elementos relacionados con el estilo de cualquier página del portal vienen definidos por las reglas de estilo. 

La edición de las reglas de estilo se realiza en el **portal para desarrolladores** durante el inicio de sesión como administrador. Para llegar hasta allí, primero abra Azure Portal y haga clic en **Portal para editores** en la barra de herramientas de servicios de su instancia de API Management.

![Portal del publicador][api-management-management-console]

A continuación, haga clic en **Portal para desarrolladores de** en la parte superior derecha. 

![Vínculo del portal para desarrolladores en el portal para editores][api-management-pp-dp-link]

Para abrir la barra de herramientas de personalización, mueva el mouse sobre el icono de personalización (o selecciónelo) y luego haga clic en "estilos" en la barra de herramientas.

![Botón de la barra de herramientas de personalización][api-management-customization-toolbar-button]

Hay dos formas principales de editar las reglas de estilo: puede buscar en la lista de todas las reglas de estilo usadas en cualquier lugar que se muestre de forma predeterminada y modificar un estilo según sea necesario, o bien elegir **Select an element on the page** (Seleccionar un elemento en la página) y después hacer clic en cualquier parte de la página para ver solo los estilos de ese elemento.

![Customization toolbar][api-management-customization-toolbar]

En este ejemplo, haga clic en la opción **Select an element on the page** (Seleccionar un elemento en la página).  Los elementos se irán resaltando cuando pase el mouse sobre ellos a fin de indicar qué estilos del elemento empezaría a editar si hiciera clic. Mueva el mouse sobre el texto del encabezado (normalmente aquí está el nombre de la empresa) y luego haga clic en él. Aparecerá un conjunto de reglas de estilo con diferentes nombres y categorías dentro del propio editor de estilo. Cada una de las reglas representa un propiedad de estilo del elemento seleccionado. Por ejemplo, en el caso del texto del encabezado seleccionado anteriormente, el tamaño del texto está en @font-size-h1 mientras que el nombre de la fuente con alternativas está en @headings-font-family.

> Si está familiarizado con [bootstrap][bootstrap], estas reglas son en realidad variables [LESS][LESS variables] del tema de bootstrap usado por el portal de desarrolladores.
> 
> 

Ahora cambiaremos el color del texto del encabezado. Seleccione la entrada en el campo **@headings-color** y escriba **#000000**. Este es el código hexadecimal del color negro. Al hacerlo, verá que aparece un indicador cuadrado de color al final del cuadro de texto. Si hace clic en este indicador, un selector de colores le permitirá elegir un color.

![Color picker][api-management-customization-toolbar-color-picker]

Se realiza una vista previa de los cambios en tiempo real, a medida que se llevan a cabo, pero solo son visibles para los administradores. Si desea que todos los usuarios puedan ver estos cambios, haga clic en el botón **Publicar** en el editor de estilo y confirme los cambios.

![Publish menu][api-management-customization-toolbar-publish-form]

> Para cambiar las reglas de estilo que se aplican a cualquier otro elemento en la página, siga el mismo procedimiento que para el encabezado. Haga clic en **Select an element on the page** (Seleccionar un elemento en la página) en el editor de estilos, seleccione el elemento que le interese y empiece a modificar los valores de las reglas de estilo que aparecen en pantalla.
> 
> 


## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo personalizar el contenido de páginas del portal para desarrolladores con las [plantillas del portal para desarrolladores](api-management-developer-portal-templates.md).

[Change the styling of the headers]: #change-headers-styling
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-styles/api-management-management-console.png
[api-management-pp-dp-link]: ./media/api-management-customize-styles/api-management-pp-dp-link.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-styles/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-styles/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-styles/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-styles/api-management-customization-toolbar-publish-form.png

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/
