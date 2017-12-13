---
title: "Personalización del estilo de página en el portal para desarrolladores de Azure API Management | Microsoft Docs"
description: "Siga los pasos de esta guía de inicio rápido para personalizar el estilo de los elementos del portal para desarrolladores de Azure API Management."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: f427663ba1c437785c8c521925d9f733c45cb40d
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Personalización del estilo de las páginas del portal para desarrolladores

Existen tres maneras comunes de personalizar el portal para desarrolladores en Azure API Management:
 
* [Editar el contenido de las páginas estáticas y los elementos de diseño de página](api-management-modify-content-layout.md)
* Actualizar los estilos usados en los elementos de página en el portal para desarrolladores (que se explica en esta guía)
* [Modificar las plantillas usadas en las páginas generadas por el portal](api-management-developer-portal-templates.md) (por ejemplo, documentos de API, productos, autenticación de usuario)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Personalizar el estilo de los elementos de las páginas del portal para **desarrolladores**
> * Ver el cambio

![Personalizar el estilo](./media/modify-developer-portal-style/developer_portal.png)

## <a name="prerequisites"></a>Requisitos previos

+ Completar la guía de inicio rápido siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
+ Además, completar el tutorial siguiente: [Import and publish your first API](import-and-publish.md) (Importación y publicación de la primera API).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="customize-the-developer-portal"></a>Personalización del portal para desarrolladores

1. Seleccione **Información general**.
2. Haga clic en el botón **Portal para desarrolladores** en la parte superior de la ventana **Información general**. También puede hacer clic en el vínculo de la **dirección URL del portal para desarrolladores**.
3. En el lado superior izquierdo de la pantalla, verá un icono formado por dos pinceles. Mantenga el puntero sobre este icono para abrir el menú de personalización del portal.

    ![Personalizar el estilo](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Seleccione **Estilos** en el menú para abrir el panel de personalización del estilo.

    En la página aparecen todos los elementos que se pueden personalizar con **Estilos**.
5. Escriba "headings-color" en el campo **Change variable values to customize developer portal appearance:** (Cambiar valores de variables para personalizar la apariencia del portal para desarrolladores).

    El elemento **@headings-color** aparece en la página. Esta variable controla el color del texto.

    ![Personalizar el estilo](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Haga clic en el campo correspondiente a la variable **@headings-color**. 
    
    Se abre la lista desplegable del selector de colores.
7. En esta lista, seleccione un nuevo color.

    > [!TIP]
    > Existe una versión preliminar en tiempo real disponible de todos los cambios. Un indicador de progreso aparece en la parte superior del panel de personalización. Al cabo de unos segundos, el color del texto del encabezado cambia al que se acaba de seleccionar.

8. Seleccione **Publicar** en la parte inferior izquierda del menú del panel de personalización.
9. Seleccione **Publish customizations** (Publicar personalizaciones) para que los cambios estén disponibles públicamente.

## <a name="view-your-change"></a>Ver el cambio

1. Desplácese hasta el portal para desarrolladores.
2. Puede ver el cambio que ha realizado.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Personalizar el estilo de los elementos de las páginas del portal para **desarrolladores**
> * Ver el cambio

> [!div class="nextstepaction"]
> [Cómo personalizar el portal para desarrolladores de Azure API Management mediante plantillas](api-management-developer-portal-templates.md)