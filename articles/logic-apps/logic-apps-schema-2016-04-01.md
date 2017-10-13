---
title: Actualizaciones de esquema, 1 de junio de 2016 (Azure Logic Apps) | Microsoft Docs
description: "Creación de definiciones de JSON para Azure Logic Apps con la versión de esquema del 1 de junio de 2016"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/25/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 43df04d6478e44c82c88b17d916cfc9fe4afc03e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Actualizaciones de esquema para Azure Logic Apps, 1 de junio de 2016

Esta nueva versión de esquema y API de Azure Logic Apps incluye importantes mejoras que aportan una mayor confiabilidad a las aplicaciones lógicas y facilitan su uso:

* [Ámbitos](#scopes): le permiten agrupar o anidar acciones como una colección de acciones.
* [Condiciones y bucles](#conditions-loops): ahora son acciones de primera clase.
* Ordenación más precisa para ejecutar acciones con la propiedad `runAfter`, sustituyendo `dependsOn`.

Para actualizar las aplicaciones lógicas del esquema de versión preliminar del 1 de agosto de 2015 al esquema del 1 de junio de 2016, [consulte la sección de actualización](##upgrade-your-schema).

<a name="scopes"></a>
## <a name="scopes"></a>Ámbitos

Este esquema incluye ámbitos, que le permiten agrupar acciones o anidar acciones unas dentro de otras. Por ejemplo, una condición puede contener otra condición. Aprenda más sobre la [sintaxis de los ámbitos](../logic-apps/logic-apps-loops-and-scopes.md), o revise este ejemplo básico de ámbitos:

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

<a name="conditions-loops"></a>
## <a name="conditions-and-loops-changes"></a>Cambios de condiciones y bucles

En las versiones anteriores del esquema, las condiciones y los bucles eran parámetros asociados a una sola acción. Este esquema elimina esta limitación, por lo que las condiciones y los bucles aparecen ahora como tipos de acción. Aprenda más sobre [bucles y ámbitos](../logic-apps/logic-apps-loops-and-scopes.md), o revise este ejemplo básico de una acción de condición:

```
{
    "If_trigger_is_some-trigger": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'some-trigger')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_another-trigger": "..."
        }      
    }
}
```

<a name="run-after"></a>
## <a name="runafter-property"></a>Propiedad "runAfter"

La propiedad `runAfter` reemplaza a `dependsOn`, por lo que se proporciona una mayor precisión al especificar el orden de ejecución de las acciones según el estado de acciones anteriores.

La propiedad `dependsOn` era sinónimo de "la acción se ejecutó correctamente", no importa cuántas veces quisiera ejecutar una acción, en función de si la acción anterior era correcta, tenía un error o se había omitido. La propiedad `runAfter` ofrece esa flexibilidad como un objeto que especifica todos los nombres de acciones después de los cuales se ejecuta el objeto. Esta propiedad también define una matriz de estados que son aceptables como desencadenadores. Por ejemplo, si quisiera ejecutar una acción después de que el paso A se realiza correctamente y también después de que el paso B se realiza correctamente o produce error, construiría esta propiedad `runAfter`:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Actualización del esquema

La actualización al nuevo esquema se realiza en solo unos cuantos pasos. El proceso de actualización incluye ejecutar el script de actualización, guardarlo como una nueva aplicación lógica y, posiblemente, sobrescribir la antigua aplicación lógica, si lo desea.

1. Abra la aplicación lógica en Azure Portal.

2. Vaya a **Overview** (Información general). En la barra de herramientas de aplicaciones lógicas, elija **Actualizar esquema**.
   
    ![Selección del esquema de actualización][1]
   
    Se devuelve la definición actualizada, que puede copiar y pegar en una definición de recurso si es necesario. 
    Pero **se recomienda firmemente** elegir **Guardar como** para asegurarse de que todas las referencias de conexión sean válidas en la aplicación lógica actualizada.

3. En la barra de herramientas de la hoja de actualización, elija **Guardar como**.

4. Escriba el nombre y el estado de la aplicación lógica. Para implementar la aplicación lógica actualizada, elija **Crear**.

5. Confirme que la aplicación lógica actualizada funciona según lo previsto.
   
   > [!NOTE]
   > Si usa un desencadenador manual o de solicitud, la dirección URL de devolución de llamada cambia en la nueva aplicación lógica. Pruebe la nueva dirección URL para asegurarse de que funciona completamente. Para conservar las direcciones URL anteriores, puede clonarlas a través de la aplicación lógica existente.

6. *Opcional*: para sobrescribir la aplicación lógica anterior con la nueva versión de esquema, en la barra de herramientas, elija **Clonar**, junto a **Actualizar esquema**. Este paso solo es necesario si quiere mantener el mismo identificador de recurso o dirección URL del desencadenador de solicitud de la aplicación lógica.

### <a name="upgrade-tool-notes"></a>Notas de la herramienta de actualización

#### <a name="mapping-conditions"></a>Condiciones de asignación

En la definición actualizada, la herramienta hace todo lo posible por agrupar las acciones de bifurcación true y false como un ámbito. En concreto, el patrón de diseñador de `@equals(actions('a').status, 'Skipped')` debe aparecer como una acción `else`. Sin embargo, si la herramienta detecta patrones irreconocibles, podría crear condiciones distintas para la bifurcación true y false. Si es necesario, puede volver a asignar acciones después de la actualización.

#### <a name="foreach-loop-with-condition"></a>Bucle "foreach" con condición

En el nuevo esquema, puede usar la acción de filtro para replicar el patrón de un bucle `foreach` con una condición por elemento, pero este cambio debe ocurrir automáticamente al actualizar. La condición se convierte en una acción de filtro delante del bucle foreach para devolver solo una matriz de elementos que coincidan con la condición, y esa matriz se pasa a la acción de foreach. Para ver un ejemplo, consulte [Bucles y ámbitos](../logic-apps/logic-apps-loops-and-scopes.md).

#### <a name="resource-tags"></a>Etiquetas del recurso

Después de actualizar, se quitan las etiquetas del recurso, por lo que debe restablecerlas para el flujo de trabajo actualizado.

## <a name="other-changes"></a>Otros cambios

### <a name="renamed-manual-trigger-to-request-trigger"></a>Cambio de nombre de desencadenador "manual" a desencadenador "request"

El tipo de desencadenador `manual` está en desuso y ahora se llama `request` con el tipo `http`. Este cambio crea una mayor coherencia para la clase de patrón que el desencadenador suele compilar.

### <a name="new-filter-action"></a>Nueva acción 'filtro'

Para filtrar una matriz grande hasta un conjunto de elementos más pequeño, el nuevo tipo `filter` acepta una matriz y una condición, evalúa la condición de cada elemento y devuelve una matriz con los elementos que cumplen la condición.

### <a name="restrictions-for-foreach-and-until-actions"></a>Restricciones de las acciones "foreach" y "until"

Los bucles `foreach` y `until` están limitados a una sola acción.

### <a name="new-trackedproperties-for-actions"></a>Nueva propiedad "trackedProperties" para las acciones

Las acciones pueden tener ahora una propiedad adicional llamada `trackedProperties`, que está relacionada con las propiedades `runAfter` y `type`. Este objeto especifica determinadas entradas o salidas de acciones que desea incluir en la telemetría de Azure Diagnostic, que se emiten como parte de un flujo de trabajo. Por ejemplo:

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
* [Creación de definiciones de flujo de trabajo para aplicaciones lógicas](../logic-apps/logic-apps-author-definitions.md)
* [Creación de plantillas de implementación de aplicaciones lógicas](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
