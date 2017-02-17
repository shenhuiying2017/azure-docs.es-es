---
title: "Personalización del portal para editores en Azure API Management | Microsoft Docs"
description: "Obtenga información sobre cómo personalizar el portal para desarrolladores en Administración de API de Azure"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 13431648e87d085161ad278dc991d49f7872be34
ms.openlocfilehash: 60213f885020a5ba36d6ada0812f755e06b3c48b


---
# <a name="customize-the-developer-portal-in-azure-api-management"></a>Personalización del portal para desarrolladores en Administración de API de Azure
En esta guía se muestra cómo modificar la apariencia del portal para desarrolladores en Administración de API a fin de unificarlo con su imagen de marca.

## <a name="change-page-headers"> </a>Cambio del texto o el logotipo en los encabezados de página
Uno de los aspectos más importantes de la personalización del portal es la sustitución del texto que aparece en la parte superior de todas las páginas por el logotipo o el nombre de su empresa.

El contenido del portal para desarrolladores se modifica a través del portal para editores, al que se obtiene acceso desde Azure Portal. Para llegar a él, haga clic en **Portal para editores** desde la barra de herramientas de servicio de la instancia de API Management.

![Portal del publicador][api-management-management-console]

El portal para desarrolladores se basa en el sistema de administración de contenido (CMS). El encabezado que aparece en todas las páginas es un tipo especial de contenido conocido como widget. Para editar el contenido de ese widget, haga clic en **Widgets** en el menú **Portal para desarrolladores** que aparece a la izquierda y seleccione el widget **Encabezado** de esa lista.

![Widgets header][api-management-widgets-header]

El contenido del encabezado se puede editar desde el campo **Cuerpo** . Cambie el texto a "Portal para desarrolladores Fabrikam" y haga clic en **Guardar** en la parte inferior de la página.

Ahora debe aparecer el nuevo encabezado en todas las páginas del portal para desarrolladores.

> Para abrir el portal para desarrolladores desde el portal para editores, haga clic en **Portal para desarrolladores** en la barra superior.
> 
> 

## <a name="change-headers-styling"> </a>Cambio del estilo de los encabezados
Los colores, las fuentes, los tamaños, los espacios y otros elementos relacionados con el estilo de cualquier página del portal vienen definidos por las reglas de estilo. Para editar los estilos, en el **portal para desarrolladores**, abra la barra de herramientas de personalización de la izquierda moviéndose por el icono de personalización y seleccionando "estilos" en la barra de herramientas.

![Botón de la barra de herramientas de personalización][api-management-customization-toolbar-button]

Hay dos formas principales de editar las reglas de estilo: puede buscar en la lista de todas las reglas de estilo usadas en cualquier lugar que se muestre de forma predeterminada y modificar un estilo según sea necesario, o bien elegir **Select an element on the page** (Seleccionar un elemento en la página) y después hacer clic en cualquier parte de la página para ver solo los estilos de ese elemento.

En esta sección queremos cambiar solo el estilo de los encabezados. Haga clic en la opción **Select an element on the page** (Seleccionar un elemento en la página) en la barra de herramientas del editor de estilo. 

![Customization toolbar][api-management-customization-toolbar]

Los elementos se irán resaltando cuando pase el mouse sobre ellos a fin de indicar qué estilos del elemento empezaría a editar si hiciera clic. Mueva el mouse sobre el texto que representa el nombre de la empresa en el encabezado ("Portal para desarrolladores Fabrikam" si siguió las instrucciones de la sección anterior) y haga clic en él. Aparecerá un conjunto de reglas de estilo con diferentes nombres y categorías dentro del propio editor de estilo. Cada una de las reglas representa un propiedad de estilo del elemento seleccionado. Por ejemplo, en el caso del texto del encabezado seleccionado anteriormente, el tamaño del texto está en @font-size-h1 mientras que el nombre de la fuente con alternativas está en @headings-font-family.

> Si está familiarizado con [bootstrap][bootstrap], estas reglas son en realidad variables [LESS][LESS variables] del tema de bootstrap usado por el portal de desarrolladores.
> 
> 

Ahora cambiaremos el color del texto del encabezado. Seleccione la entrada en el campo **@headings-color** y escriba **#000000**. Este es el código hexadecimal del color negro. Al hacerlo, verá que aparece un indicador cuadrado de color al final del cuadro de texto. Si hace clic en este indicador, un selector de colores le permitirá elegir un color.

![Color picker][api-management-customization-toolbar-color-picker]

Se realiza una vista previa de los cambios en tiempo real, a medida que se llevan a cabo, pero solo son visibles para los administradores. Si desea que todos los usuarios puedan ver estos cambios, haga clic en el botón **Publicar** en el editor de estilo y confirme los cambios.

![Publish menu][api-management-customization-toolbar-publish-form]

> Para cambiar las reglas de estilo que se aplican a cualquier otro elemento en la página, siga el mismo procedimiento que para el encabezado. Haga clic en **Seleccionar un elemento** desde el editor de estilo, seleccione el elemento que interese y empiece a modificar los valores de las reglas de estilo que aparecen en pantalla.
> 
> 

## <a name="edit-page-contents"> </a>Edición de los contenidos de una página
El portal para desarrolladores consta de páginas generadas automáticamente como API, Productos, Aplicaciones, Emisiones y contenido escrito a mano. Como se basa en un sistema de administración de contenido, puede crear contenido de este tipo a medida que sea necesario.

Para ver una lista de todas las páginas de contenido existentes, haga clic en **Contenido** en el menú **Portal para desarrolladores** del portal para editores.

![Manage content][api-management-customization-manage-content]

Haga clic en la página de **bienvenida** para editar lo que se muestra en la página principal del portal para desarrolladores. Haga los cambios que quiera, obtenga una vista previa de ellos si lo considera necesario y haga clic en **Publicar ahora** para que sean visibles para todos los usuarios.

> La página principal usa un diseño especial que le permite mostrar un banner en la parte superior. Este banner no se puede editar en la sección **Contenido** . Para editarlo, haga clic en **Widgets** en el menú **Portal para desarrolladores**, seleccione **Home page** (Página principal) en la lista desplegable **Current Layer** (Capa actual) y, por último, abra el elemento **Banner** (Banner) en la sección **Featured section** (Destacados). Los contenidos de este widget se pueden editar del mismo modo que cualquier otra página.
> 
> 

## <a name="next-steps"> </a>Pasos siguientes
* Obtenga información sobre cómo personalizar el contenido de páginas del portal para desarrolladores con las [plantillas del portal para desarrolladores](api-management-developer-portal-templates.md).

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-portal/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/



<!--HONumber=Feb17_HO1-->


