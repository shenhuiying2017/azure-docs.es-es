---
title: "Adición de la acción de consulta a Logic Apps | Microsoft Docs"
description: "En este artículo se muestra información general sobre las acciones de consulta para realizar acciones como Filter array (Filtrar matriz)."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 05dd4ae3c4ee439d66401a3f5595f9104051f8ee
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-query-action"></a>Introducción a la acción de consulta
Mediante la acción de consulta puede utilizar lotes y matrices para realizar flujos de trabajo como los siguientes:

* Crear una tarea para todos los registros de alta prioridad desde una base de datos.
* Guardar todos los datos adjuntos en PDF de correos electrónicos en un blob de Azure.

Para empezar a usar la acción de consulta en una aplicación lógica, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Uso de la acción de consulta
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](connectors-overview.md).  

En estos momentos, la acción de consulta tiene una operación expuesta en el diseñador que se denomina Filter array (Filtrar matriz). Gracias a ella, podrá consultar una matriz y devolver un conjunto de resultados filtrados.

Este es el procedimiento para agregarlo en una aplicación lógica:

1. Seleccione el botón **Nuevo paso** .
2. Elija **Add an action**(Agregar una acción).
3. En el cuadro de búsqueda de la acción, escriba **Filtrar** para mostrar la acción **Filtrar matriz**.
   
    ![Seleccione la acción de consulta](./media/connectors-native-query/using-action-1.png)
4. Seleccione la matriz que se va a filtrar. (La captura de pantalla siguiente muestra la matriz de resultados de búsqueda de Twitter).
5. Cree una condición para evaluar en cada elemento. (La captura de pantalla siguiente filtra tweets de usuarios que tienen más de 100 seguidores).
   
    ![Complete la acción de consulta](./media/connectors-native-query/using-action-2.png)
   
    La acción generará una nueva matriz que contiene solo los resultados que cumplen los requisitos de filtro.
6. Haga clic en la esquina superior izquierda de la barra de herramientas para guardarla; la aplicación lógica se guardará y se publicará (activará).

\* Si llama a un punto de conexión HTTP y recibe una respuesta JSON, use la acción _Análisis del archivo JSON_ para analizar la respuesta JSON. Sin realizar este paso, _Filtrar matriz_ solo verá Cuerpo y no comprenderá la estructura de la carga JSON.

## <a name="query-action"></a>Acción de consulta
Aquí se muestran los detalles de la acción que admite este conector. El conector tiene una acción posible.

| . | DESCRIPCIÓN |
| --- | --- |
| Filter array |Evalúa una condición de cada elemento de una matriz y devuelve los resultados |

## <a name="action-details"></a>Detalles de la acción
La acción de consulta incluye una acción posible. Las tablas siguientes describen los campos de entrada obligatorios y opcionales para la acción y los detalles de salida correspondientes asociados a su uso.

### <a name="filter-array"></a>Filter array
Los siguientes son los campos de entrada para la acción que realiza una solicitud de salida HTTP.
Un * significa que es un campo obligatorio.

| Nombre para mostrar | Nombre de propiedad | DESCRIPCIÓN |
| --- | --- | --- |
| De* |De |La matriz que se va a filtrar. |
| Condición* |donde |La condición que se va a evaluar en cada elemento. |

<br>

### <a name="output-details"></a>Detalles de salida
Los detalles de la salida de la respuesta HTTP son los siguientes.

| Nombre de propiedad | Tipo de datos | DESCRIPCIÓN |
| --- | --- | --- |
| Matriz filtrada |array |Una matriz que contiene un objeto de cada resultado filtrado |

## <a name="next-steps"></a>pasos siguientes
Ahora, pruebe la plataforma y [cree una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Puede explorar los demás conectores disponibles en aplicaciones lógicas consultando nuestra [lista de API](apis-list.md).

