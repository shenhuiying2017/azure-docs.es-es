---
title: Uso de propiedades en directivas de Azure API Management
description: Aprenda a usar las propiedades en directivas de Azure API Management.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: apimpm
ms.openlocfilehash: e0559380f6d686a4e559779c4271ea85106558d6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Uso de propiedades en directivas de Azure API Management
En API Management, las directivas constituyen una funcionalidad eficaz del sistema que permite a Azure Portal cambiar el comportamiento de la API mediante la configuración. Las directivas son una colección de declaraciones que se ejecutan secuencialmente en la solicitud o respuesta de una API. Las instrucciones de las directivas se pueden crear con valores de texto literal, expresiones de directiva y propiedades. 

Cada instancia del servicio de API Management tiene una colección de propiedades de pares clave-valor que son globales para la instancia del servicio. Estas propiedades se pueden utilizar para administrar los valores constantes de la cadena en todas las directivas y la configuración de API. Cada propiedad puede tener también los siguientes atributos:

| Atributo | type | DESCRIPCIÓN |
| --- | --- | --- |
| Nombre para mostrar |cadena |Cadena alfanumérica que se usa para hacer referencia a la propiedad en las directivas. |
| Valor |cadena |El valor de la propiedad. No puede estar vacío ni contener solo espacios en blanco. |
|Secret|boolean|Determina si el valor es secreto y si se debe cifrar.|
| Etiquetas |matriz de cadena |Etiquetas opcionales que, cuando se proporcionan, pueden usarse para filtrar la lista de propiedades. |

![Valores con nombre](./media/api-management-howto-properties/named-values.png)

Los valores de propiedad pueden contener cadenas literales y [expresiones de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx). Por ejemplo, el valor de `ExpressionProperty` es una expresión de directiva que devuelve una cadena que contiene la fecha y la hora actuales. La propiedad `ContosoHeaderValue` está marcada como secreta, por lo que no se muestra su valor.

| NOMBRE | Valor | Secret | Etiquetas |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-add-and-edit-a-property"></a>Incorporación y edición de una propiedad

![Incorporación de una propiedad](./media/api-management-howto-properties/add-property.png)

1. Seleccione **API** en **API MANAGEMENT**.
2. Seleccione **Valores con nombre**.
3. Presione **+Agregar**.

  Los campos Nombre y Valor son necesarios. Si el valor de esta propiedad es un secreto, marque la casilla Este valor es un secreto. Escriba una o varias etiquetas opcionales que le ayuden a organizar las propiedades y haga clic en Guardar.
4. Haga clic en **Create**(Crear).

Una vez creada la propiedad, es posible editarla haciendo clic en ella. Si cambia el nombre de propiedad, las directivas que hagan referencia a esa propiedad se actualizarán automáticamente para utilizar el nuevo nombre.

Para obtener información sobre cómo editar una propiedad mediante la API de REST, consulte [Edit a property using the REST API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch)(Edición de una propiedad mediante la API de REST).

## <a name="to-delete-a-property"></a>Eliminación de una propiedad

Para eliminar una propiedad, haga clic en **Delete (Eliminar)** junto a la propiedad que se va a eliminar.

> [!IMPORTANT]
> Si se hace referencia a la propiedad mediante alguna directiva, podrá eliminarla correctamente hasta que quite la propiedad de todas las directivas que la utilicen.
> 
> 

Para obtener información sobre cómo eliminar una propiedad mediante la API de REST, consulte [Delete a property using the REST API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete)(Eliminación de una propiedad mediante la API de REST).

## <a name="to-search-and-filter-properties"></a>Búsqueda y filtrado de propiedades

En la pestaña **Valores con nombre** se incluyen las funcionalidades de búsqueda y filtrado que le ayudan a administrar las propiedades. Para filtrar la lista de propiedades por el nombre de la propiedad, escriba un término de búsqueda en el cuadro de texto **Search property (Buscar propiedad)** . Para mostrar todas las propiedades, borre el cuadro de texto **Search property (Buscar propiedad)** y pulse la tecla Entrar.

Para filtrar la lista de propiedades por valores de etiqueta, escriba una o varias etiquetas en el cuadro de texto **Filtrar por etiquetas** . Para mostrar todas las propiedades, borre el cuadro de texto **Filter by tags (Filtrar por etiquetas)** y pulse la tecla Entrar.

## <a name="to-use-a-property"></a>Uso de una propiedad

Para utilizar una propiedad en una directiva, coloque el nombre de la propiedad dentro de un par doble de llaves (como `{{ContosoHeader}}`), de la misma forma que se muestra en el ejemplo siguiente:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

En este ejemplo, `ContosoHeader` se utiliza como el nombre de un encabezado en una directiva `set-header`, y `ContosoHeaderValue` se utiliza como valor de ese encabezado. Cuando esta directiva se evalúa durante una solicitud o responde a la pasarela de API Management, `{{ContosoHeader}}` y `{{ContosoHeaderValue}}` se reemplazan por sus respectivos valores de propiedad.

Las propiedades se pueden utilizar como atributo completo como o valores de elemento (tal como se muestra en el ejemplo anterior), pero también se pueden insertar o combinar con parte de una expresión de texto literal, como en el ejemplo siguiente: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Las propiedades también pueden contener expresiones de directiva. En el ejemplo siguiente, se utiliza la propiedad `ExpressionProperty`.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Cuando esta directiva se evalúa, se reemplaza `{{ExpressionProperty}}` por su valor: `@(DateTime.Now.ToString())`. Puesto que el valor es una expresión de directiva, la expresión se evalúa y la directiva continúa con su ejecución.

Puede probarlo en el portal para desarrolladores si llama a una operación que tenga dentro de su ámbito una directiva con propiedades. En el ejemplo siguiente, se llama a una operación con las dos directivas `set-header` de ejemplo anteriores con propiedades. Tenga en cuenta que la respuesta contiene dos encabezados personalizados que se han configurado mediante directivas con propiedades.

![Portal para desarrolladores][api-management-send-results]

Si busca en el [seguimiento de API Inspector](api-management-howto-api-inspector.md) una llamada que incluya las dos directivas de ejemplo anteriores con propiedades, verá las dos directivas `set-header` con los valores de propiedad insertados, así como la evaluación de la expresión de directiva para la propiedad que la contiene.

![Seguimiento de API Inspector][api-management-api-inspector-trace]

Aunque los valores de propiedad pueden contener expresiones de directiva, no pueden contener otras propiedades. Si se utiliza texto que contiene una referencia de propiedad para un valor de propiedad, como `Property value text {{MyProperty}}`, esa referencia de propiedad no se reemplazará y se incluirá como parte del valor de la propiedad.

## <a name="next-steps"></a>pasos siguientes
* Obtenga más información sobre cómo trabajar con directivas
  * [Directivas de Azure API Management](api-management-howto-policies.md)
  * [Referencia de directiva](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Expresiones de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

