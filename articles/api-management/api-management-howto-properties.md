---
title: "Uso de propiedades en directivas de Administración de API de Azure"
description: "Aprenda a usar las propiedades en directivas de Administración de API de Azure."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 6f39b00f-cf6e-4cef-9bf2-1f89202c0bc0
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 3b0fe2a300038e13cc488bdb4f50f8be270ea8f4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Uso de propiedades en directivas de Administración de API de Azure
En Administración de API, las directivas constituyen una eficaz funcionalidad del sistema que permite al editor cambiar el comportamiento de la API a través de la configuración. Las directivas son una colección de declaraciones que se ejecutan secuencialmente en la solicitud o respuesta de una API. Las instrucciones de las directivas se pueden crear con valores de texto literal, expresiones de directiva y propiedades. 

Cada instancia del servicio de Administración de API tiene una colección de propiedades de pares clave-valor que son globales para la instancia del servicio. Estas propiedades se pueden utilizar para administrar los valores constantes de la cadena en todas las directivas y la configuración de API. Cada propiedad tiene también los siguientes atributos.

| Atributo | Tipo | Description |
| --- | --- | --- |
| Nombre |string |El nombre de la propiedad. Puede contener solo letras, dígitos, puntos, guiones o caracteres de subrayado. |
| Valor |string |El valor de la propiedad. No puede estar vacío ni contener solo espacios en blanco. |
| Secret |boolean |Determina si el valor es secreto y si se debe cifrar. |
| Etiquetas |matriz de cadena |Etiquetas opcionales que, cuando se proporcionan, pueden usarse para filtrar la lista de propiedades. |

Las propiedades se configuran en el portal del editor, en la pestaña **Properties (Propiedades)** . En el ejemplo siguiente, se configuran tres propiedades.

![Properties (Propiedades)][api-management-properties]

Los valores de propiedad pueden contener cadenas literales y [expresiones de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx). En la siguiente tabla se muestran las tres propiedades de ejemplo anteriores y sus atributos. El valor de `ExpressionProperty` es una expresión de directiva que devuelve una cadena que contiene la fecha y la hora actuales. La propiedad `ContosoHeaderValue` está marcada como secreta, por lo que no se muestra su valor.

| Nombre | Valor | Secret | Etiquetas |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-use-a-property"></a>Uso de una propiedad
Para utilizar una propiedad en una directiva, coloque el nombre de la propiedad dentro de un par doble de llaves (como `{{ContosoHeader}}`), de la misma forma que se muestra en el ejemplo siguiente.

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

En este ejemplo, `ContosoHeader` se utiliza como el nombre de un encabezado en una directiva `set-header`, y `ContosoHeaderValue` se utiliza como valor de ese encabezado. Cuando esta directiva se evalúa durante una solicitud o responde a la pasarela de Administración de API, `{{ContosoHeader}}` y `{{ContosoHeaderValue}}` se reemplazan por sus respectivos valores de propiedad.

Las propiedades se pueden utilizar como atributo completo como o valores de elemento (tal como se muestra en el ejemplo anterior), pero también se pueden insertar o combinar con parte de una expresión de texto literal, como en el ejemplo siguiente: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Las propiedades también pueden contener expresiones de directiva. En el ejemplo siguiente, se utiliza la propiedad `ExpressionProperty`.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Cuando esta directiva se evalúa, se reemplaza `{{ExpressionProperty}}` por su valor: `@(DateTime.Now.ToString())`. Puesto que el valor es una expresión de directiva, la expresión se evalúa y la directiva continúa con su ejecución.

Puede probarlo en el portal para desarrolladores si llama a una operación que tenga dentro de su ámbito una directiva con propiedades. En el ejemplo siguiente, se llama a una operación con las dos directivas `set-header` de ejemplo anteriores con propiedades. Tenga en cuenta que la respuesta contiene dos encabezados personalizados que se han configurado mediante directivas con propiedades.

![portal para desarrolladores][api-management-send-results]

Si busca en el [seguimiento de API Inspector](api-management-howto-api-inspector.md) una llamada que incluya las dos directivas de ejemplo anteriores con propiedades, verá las dos directivas `set-header` con los valores de propiedad insertados, así como la evaluación de la expresión de directiva para la propiedad que la contiene.

![Seguimiento de API Inspector][api-management-api-inspector-trace]

Tenga en cuenta que, a pesar de que los valores de propiedad pueden contener expresiones de directiva, no pueden contener otras propiedades. Si se utiliza texto que contiene una referencia de propiedad para un valor de propiedad, como `Property value text {{MyProperty}}`, esa referencia de propiedad no se reemplazará y se incluirá como parte del valor de la propiedad.

## <a name="to-create-a-property"></a>Creación de una propiedad
Para crear una propiedad, haga clic en **Agregar propiedad** en la pestaña **Propiedades**.

![Agregar propiedad][api-management-properties-add-property-menu]

Los campos **Nombre** y **Valor** son necesarios. Si el valor de esta propiedad es un secreto, marque la casilla de verificación **This is a secret (Es secreto)** . Escriba una o varias etiquetas opcionales para ayudar a organizar las propiedades y haga clic en **Save (Guardar)**.

![Agregar propiedad][api-management-properties-add-property]

Cuando se guarda una nueva propiedad, el cuadro de texto **Search property (Buscar propiedad)** se rellena con el nombre de la nueva propiedad y esta se muestra. Para mostrar todas las propiedades, borre el cuadro de texto **Search property (Buscar propiedad)** y pulse la tecla Entrar.

![Properties (Propiedades)][api-management-properties-property-saved]

Para obtener información sobre cómo crear una propiedad mediante la API de REST, consulte [Create a property using the REST API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put)(Creación de una propiedad mediante la API de REST).

## <a name="to-edit-a-property"></a>Edición de una propiedad
Para editar una propiedad, haga clic en **Edit (Editar)** junto a la propiedad que se va a editar.

![Editar propiedad][api-management-properties-edit]

Realice los cambios necesarios y haga clic en **Save (Guardar)**. Si cambia el nombre de propiedad, las directivas que hagan referencia a esa propiedad se actualizarán automáticamente para utilizar el nuevo nombre.

![Editar propiedad][api-management-properties-edit-property]

Para obtener información sobre cómo editar una propiedad mediante la API de REST, consulte [Edit a property using the REST API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch)(Edición de una propiedad mediante la API de REST).

## <a name="to-delete-a-property"></a>Eliminación de una propiedad
Para eliminar una propiedad, haga clic en **Delete (Eliminar)** junto a la propiedad que se va a eliminar.

![Eliminar propiedad][api-management-properties-delete]

Haga clic en **Sí, eliminar** para confirmar.

![Confirmar eliminación][api-management-delete-confirm]

> [!IMPORTANT]
> Si se hace referencia a la propiedad mediante alguna directiva, podrá eliminarla correctamente hasta que quite la propiedad de todas las directivas que la utilicen.
> 
> 

Para obtener información sobre cómo eliminar una propiedad mediante la API de REST, consulte [Delete a property using the REST API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete)(Eliminación de una propiedad mediante la API de REST).

## <a name="to-search-and-filter-properties"></a>Búsqueda y filtrado de propiedades
En la pestaña **Properties (Propiedades)** se incluyen las funcionalidades de búsqueda y filtrado para ayudarle a administrar las propiedades. Para filtrar la lista de propiedades por el nombre de la propiedad, escriba un término de búsqueda en el cuadro de texto **Search property (Buscar propiedad)** . Para mostrar todas las propiedades, borre el cuadro de texto **Search property (Buscar propiedad)** y pulse la tecla Entrar.

![Search][api-management-properties-search]

Para filtrar la lista de propiedades por valores de etiqueta, escriba una o varias etiquetas en el cuadro de texto **Filtrar por etiquetas** . Para mostrar todas las propiedades, borre el cuadro de texto **Filter by tags (Filtrar por etiquetas)** y pulse la tecla Entrar.

![Filtrar][api-management-properties-filter]

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre cómo trabajar con directivas
  * [Directivas de Administración de API de Azure](api-management-howto-policies.md)
  * [Referencia de directiva](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Policy expressions (Expresiones de directiva)](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Vea un vídeo de introducción.
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Use-Properties-in-Policies/player]
> 
> 

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

