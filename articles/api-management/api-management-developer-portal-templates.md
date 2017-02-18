---
title: "Personalización del portal para desarrolladores para API Management mediante plantillas - Azure| Microsoft Docs"
description: "Obtenga información sobre cómo personalizar el portal para desarrolladores para Administración de API de Azure mediante plantillas"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 94e13ac6fec09081484a2f7f5d7bc1871822743f
ms.openlocfilehash: 01fb45cc2d2323290427a08e3210fadf27f6cda8


---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Cómo personalizar el portal para desarrolladores de Administración de API de Azure mediante plantillas
Administración de API de Azure proporciona varias características de personalización que permiten a los administradores [personalizar la apariencia del portal para desarrolladores](api-management-customize-portal.md), así como personalizar el contenido de las páginas del portal para desarrolladores mediante un conjunto de plantillas que configuran el contenido de las páginas. Mediante el uso de la sintaxis [DotLiquid](http://dotliquidmarkup.org/) y del conjunto proporcionado de recursos de cadena localizada, iconos y controles de página, dispone de gran flexibilidad para configurar el contenido de las páginas según le convenga con estas plantillas.

## <a name="developer-portal-templates-overview"></a>Información general sobre las plantillas del portal para desarrolladores
Los administradores de la instancia del servicio de Administración de API pueden administrar las plantillas del portal para desarrolladores en el portal. Para administrar las plantillas de desarrollador, vaya a la instancia del servicio API Management en Azure Portal y haga clic en la opción **Portal del desarrollador** de la barra de herramientas.

![portal para desarrolladores][api-management-browse]

Si ya se encuentra en el portal del publicador, puede acceder al portal para desarrolladores haciendo clic en **Portal para desarrolladores**.

![Menú del portal para desarrolladores][api-management-developer-portal-menu]

Para acceder a las plantillas del portal para desarrolladores, haga clic en el icono de personalización de la izquierda para mostrar el menú de personalización y haga clic en **Plantillas**.

![Plantillas del portal para desarrolladores][api-management-customize-menu]

La lista de plantillas muestra varias categorías de plantillas que abarcan las distintas páginas del portal para desarrolladores. Cada plantilla es diferente, pero los pasos para editarlas y publicar los cambios son los mismos. Para editar una plantilla, haga clic en el nombre.

![Plantillas del portal para desarrolladores][api-management-templates-menu]

Cuando hace clic en una plantilla, se abre la página del portal para desarrolladores que se puede personalizar con esa plantilla. En este ejemplo se muestra la plantilla **Lista de productos** . La plantilla **Lista de productos** controla el área de la pantalla que se indica con un rectángulo rojo. 

![Plantilla de lista de productos][api-management-developer-portal-templates-overview]

Algunas plantillas, como las plantillas **Perfil de usuario** , personalizan partes diferentes de la misma página. 

![Plantillas de perfil de usuario][api-management-user-profile-templates]

El editor de cada plantilla del portal para desarrolladores tiene dos secciones que se muestran en la parte inferior de la página. El lado izquierdo muestra el panel de edición de la plantilla y el lado derecho muestra el modelo de datos de la plantilla. 

El panel de edición de plantillas contiene el marcado que controla la apariencia y el comportamiento de la página correspondiente en el portal para desarrolladores. El marcado de la plantilla usa la sintaxis [DotLiquid](http://dotliquidmarkup.org/) . Un editor popular de DotLiquid es [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Todos los cambios que se realizan en la plantilla durante la edición se muestran en tiempo real en el explorador, pero no son visibles para los clientes mientras no se [guarda](#to-save-a-template) y se [publica](#to-publish-a-template) la plantilla.

![Marcado de plantilla][api-management-template]

El panel **Template data** (Datos de plantilla) proporciona indicaciones sobre el modelo de datos de las entidades que están disponibles para su uso en una plantilla determinada. Dichas indicaciones consisten en los datos actuales que se muestran actualmente en el portal para desarrolladores. Para expandir los paneles de plantilla, haga clic en el rectángulo de la esquina superior derecha del panel **Template data** (Datos de plantilla).

![Modelo de datos de plantilla][api-management-template-data]

En el ejemplo anterior, se muestran en el portal para desarrolladores dos productos que se recuperaron de los datos mostrados en el panel **Template data** (Datos de plantilla), tal como se muestra en el ejemplo siguiente.

```json
{
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 2,
        "ShowAll": false,
        "PageCount": 1
    },
    "Filtering": {
        "Pattern": null,
        "Placeholder": "Search products"
    },
    "Products": [
        {
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
            "Title": "Unlimited",
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
            "Terms": null,
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        }
    ]
}
```

El marcado de la plantilla **Lista de productos** procesa los datos para proporcionar el resultado deseado. Para ello, itere a través de la colección de productos para mostrar información y un vínculo a cada producto. Observe los elementos `<search-control>` y `<page-control>` del marcado. Controlan la presentación de la búsqueda y los controles de paginación en la página. `ProductsStrings|PageTitleProducts` es una referencia de cadena localizada que contiene el texto del encabezado `h2` de la página. Para obtener una lista de recursos de cadena, controles de página e iconos disponibles para su uso en plantillas del portal para desarrolladores, consulte la [referencia de plantillas del portal para desarrolladores de API Management](api-management-developer-portal-templates-reference.md).

```html
<search-control></search-control>
<div class="row">
    <div class="col-md-9">
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
    </div>
</div>
<div class="row">
    <div class="col-md-12">
    {% if products.size > 0 %}
    <ul class="list-unstyled">
    {% for product in products %}
        <li>
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
            {{product.description}}
        </li>    
    {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    </div>
</div>
```

## <a name="to-save-a-template"></a>Para guardar una plantilla
Para guardar una plantilla, haga clic en guardar en el editor de plantillas.

![Guardar plantilla][api-management-save-template]

Los cambios guardados no se activan en el portal para desarrolladores mientras no se publiquen.

## <a name="to-publish-a-template"></a>Para publicar una plantilla
Las plantillas guardadas se pueden publicar individualmente o todas juntas. Para publicar una plantilla individual, haga clic en publicar en el editor de plantillas.

![Publicar plantilla][api-management-publish-template]

Haga clic en **Sí** para confirmar y hacer que la plantilla se active en el portal para desarrolladores.

![Confirmar publicación][api-management-publish-template-confirm]

Para publicar todas las versiones de plantillas actualmente sin publicar, haga clic en **Publicar** en la lista de plantillas. Las plantillas no publicadas se indican con un asterisco después del nombre de la plantilla. En este ejemplo, se van a publicar las plantillas **Lista de productos** y **Producto**.

![Publicar plantillas][api-management-publish-templates]

Haga clic en **Publicar personalizaciones** para confirmar.

![Confirmar publicación][api-management-publish-customizations]

Las plantillas recién publicadas entran en vigor de inmediato en el portal para desarrolladores.

## <a name="to-revert-a-template-to-the-previous-version"></a>Para revertir una plantilla a la versión anterior
Para revertir una plantilla a la versión publicada anterior, haga clic en revertir en el editor de plantillas.

![Revertir plantilla][api-management-revert-template]

Haga clic en **Sí** para continuar.

![Confirm][api-management-revert-template-confirm]

La versión de una plantilla publicada anteriormente estará activa en el portal para desarrolladores en cuanto se complete la operación de reversión.

## <a name="to-restore-a-template-to-the-default-version"></a>Para restaurar una plantilla a la versión predeterminada
La restauración de las plantillas a su versión predeterminada es un proceso que consta de dos pasos. Primero deben restaurarse las plantillas y después deben publicarse las versiones restauradas.

Para restaurar una sola plantilla a la versión predeterminada, haga clic en restaurar en el editor de plantillas.

![Revertir plantilla][api-management-reset-template]

Haga clic en **Sí** para continuar.

![Confirm][api-management-reset-template-confirm]

Para restaurar todas las plantillas a las versiones predeterminadas, haga clic en **Restore default templates** (Restaurar plantillas predeterminadas) en la lista de plantillas.

![Restaurar plantillas][api-management-restore-templates]

Las plantillas restauradas deben publicarse individualmente o a la vez siguiendo los pasos descritos en [Para publicar una plantilla](#to-publish-a-template).

## <a name="next-steps"></a>Pasos siguientes
Para obtener información de referencia sobre plantillas del portal para desarrolladores, recursos de cadena, iconos y controles de página, consulte la [referencia de plantillas del portal para desarrolladores de Administración de API](api-management-developer-portal-templates-reference.md).


[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png










<!--HONumber=Jan17_HO5-->


