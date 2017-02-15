---
title: "Nueva versión de esquema 2016-06-01 | Microsoft Docs"
description: "Aprenda a escribir la definición de JSON de la versión más reciente de Aplicaciones lógicas"
author: jeffhollan
manager: dwrede
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eec9705688f88cedddedee32c562bdf714e45743


---
# <a name="new-schema-version-2016-06-01"></a>Nueva versión de esquema 2016-06-01
La nueva versión de esquema y API de Aplicaciones lógicas presenta varias mejoras que optimizan la confiabilidad y la facilidad de uso de estas aplicaciones. Hay tres diferencias principales:

1. Adición de ámbitos, que son acciones que contienen una colección de acciones.
2. Las condiciones y los bucles son acciones de primera clase.
3. Orden de ejecución más detallada mediante la propiedad `runAfter` (que reemplaza a `dependsOn`)

Para ver información acerca de cómo actualizar las aplicaciones lógicas del esquema 2015-08-01-preview al esquema 2016-06-01-preview, [consulte la sección de actualización, más adelante](#upgrading-to-2016-06-01-schema)

## <a name="1-scopes"></a>1. Ámbitos
Uno de los cambios más importantes en este esquema es la adición de ámbitos y la capacidad para anidar acciones.  Esto es útil al agrupar un conjunto de acciones o cuando es necesario anidar acciones (por ejemplo, una condición puede contener otra condición).  Puede encontrar más detalles sobre la sintaxis de los ámbitos [aquí](app-service-logic-loops-and-scopes.md), pero se puede encontrar un ejemplo simple de ámbito a continuación:

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

## <a name="2-conditions-and-loops-changes"></a>2. Cambios de condiciones y bucles
En las versiones anteriores del esquema, las condiciones y los bucles eran parámetros asociados a una sola acción.  Esta limitación se ha eliminado soluciona en este esquema y ahora las condiciones y los bucles aparecen como un tipo de acción.  Puede encontrar más información [en este artículo](app-service-logic-loops-and-scopes.md), y un ejemplo sencillo de una acción de condición a continuación:

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
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
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## <a name="3-runafter-property"></a>3. Propiedad RunAfter
La nueva propiedad `runAfter` reemplaza a `dependsOn`, lo que proporciona mayor precisión a las órdenes de ejecución.  `dependsOn` era sinónimo de "la acción se ejecutó correctamente"; sin embargo, muchas veces es necesario ejecutar una acción si la acción anterior es correcta, tiene un error o se omite.  `runAfter` permite dicha flexibilidad.  Es un objeto que especifica todos los nombres de acciones que se ejecutarán después y define una matriz de los estados desde los que se puede desencadenar.  Por ejemplo, si desea ejecutarlo después de que el paso A se haya realizado correctamente y el paso B se haya realizado correctamente o no, cree la siguiente propiedad `runAfter` :

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrading-to-2016-06-01-schema"></a>Actualización al esquema 2016-06-01
La actualización al nuevo esquema 2016-06-01-preview se realiza con solo unos cuantos pasos.  Puede encontrar detalles sobre los cambios del esquema [en este artículo](app-service-logic-schema-2016-04-01.md).  El proceso de actualización incluye ejecutar el script de actualización, guardar como una nueva aplicación lógica y, posiblemente, sobrescribir la antigua aplicación lógica, si es necesario.

1. Abra la aplicación lógica actual.
2. Haga clic en el botón **Actualizar esquema** de la barra de herramientas.
   
    ![][1]
   
    Se devolverá la definición actualizada.  Puede copiarla y pegarla en una definición de recursos si es necesario, pero se **recomienda encarecidamente** utilizar el botón **Guardar como** para asegurarse de que todas las referencias de conexión son válidas en la aplicación lógica actualizada.
3. Haga clic en el botón **Guardar como** de la barra de herramientas de la hoja de la actualización.
4. Rellene el nombre y la el estado de la aplicación lógica y haga clic en **Crear** para implementar la aplicación lógica actualizada.
5. Compruebe que la aplicación lógica actualizada funciona según lo esperado.
   
   > [!NOTE]
   > Si usa un desencadenador manual o de solicitud, la dirección URL de devolución de llamada habrá cambiado en la nueva aplicación lógica.  Use la nueva dirección URL para comprobar que funciona de principio a fin, y puede clonar la aplicación lógica existente para conservar las direcciones URL anteriores.
   > 
   > 
6. *Opcional* Utilice el botón **Clonar** de la barra de herramientas (junto al icono **Actualizar esquema** de la imagen anterior) para sobrescribir la aplicación lógica anterior con la nueva versión del esquema.  Esto solo es necesario si desea mantener el mismo identificador del recurso o la dirección URL del desencadenador de la solicitud de la aplicación lógica.

### <a name="upgrade-tool-notes"></a>Notas de la herramienta de actualización
#### <a name="condition-mapping"></a>Asignación de condiciones
La herramienta hará todo lo posible para agrupar las acciones de las ramas verdaderas y falsas en un ámbito de la definición actualizada.  Específicamente, el patrón del diseñador de `@equals(actions('a').status, 'Skipped')` debe aparecer como una acción `else`.  Sin embargo, si la herramienta detecta patrones que no reconoce, potencialmente creará condiciones independientes para las ramas verdaderas y falsas.  Las acciones pueden reasignarse después de la actualización si es necesario.

#### <a name="foreach-with-condition"></a>ForEach con condición
El patrón anterior de un bucle foreach con una condición por elemento se puede replicar en el nuevo esquema con la acción de filtro.  Esto debería producirse automáticamente durante la actualización.  La condición se convierte en una acción de filtro antes del bucle foreach (para devolver solo una matriz de los elementos que coinciden con la condición), y esa matriz se pasa a la acción de foreach.  Puede ver un ejemplo de esto [en este artículo](app-service-logic-loops-and-scopes.md)

#### <a name="resource-tags"></a>Etiquetas del recurso
Las etiquetas del recurso se quitarán durante la actualización y deberá establecerlas de nuevo para el flujo de trabajo actualizado.

## <a name="other-changes"></a>Otros cambios
### <a name="manual-trigger-renamed-to-request-trigger"></a>Nombre del desencadenador manual cambiado a desencadenador de solicitud
El tipo `manual` está ahora en desuso y su nombre se ha cambiado a `request` con la variante de `http`.  Esto es más coherente con el tipo de patrón de desencadenador que se utiliza para generarlo.

### <a name="new-filter-action"></a>Nueva acción 'filtro'
Si está trabajando con una matriz de gran tamaño y necesita filtrar un conjunto de elementos más pequeño, puede usar el nuevo tipo 'filtro'.  Acepta una matriz y una condición, y evalúa la condición para cada elemento y devuelve una matriz de elementos que cumplen la condición.

### <a name="foreach-and-until-action-restrictions"></a>Restricciones de acción forEach y until
Los bucles foreach y until están restringidos a una sola acción.

### <a name="trackedproperties-on-actions"></a>TrackedProperties en acciones
Las acciones pueden tener una propiedad adicional (del mismo nivel que `runAfter` y `type`) llamada `trackedProperties`.  Se trata de un objeto que especifica ciertas entradas o salidas de acción que se incluirán en la telemetría de diagnóstico de Azure generada como parte de un flujo de trabajo.  Por ejemplo:

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
* [Uso de la definición del flujo de trabajo de la aplicación lógica](app-service-logic-author-definitions.md)
* [Creación de una plantilla de implementación de aplicación lógica](app-service-logic-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png



<!--HONumber=Nov16_HO3-->


