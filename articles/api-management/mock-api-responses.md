---
title: Simulación de respuestas de API con Azure Portal | Microsoft Docs
description: Este tutorial muestra cómo utilizar API Management (APIM) con el fin de establecer una directiva para una API para que devuelva una respuesta simulada. Este método permite a los desarrolladores continuar con la implementación y las pruebas de la instancia de API Management en caso de que el back-end no esté disponible para enviar respuestas reales.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 4383ce3788f6fade5299d69ef99b80221c58d9e7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936990"
---
# <a name="mock-api-responses"></a>Simulación de respuestas de API

Pueden importarse API de back-end en una API de APIM, o bien crearse o administrarse manualmente. Los pasos descritos en este tutorial muestran cómo usar APIM para crear una API en blanco y administrarla de forma manual. El tutorial muestra cómo establecer una directiva en una API para que devuelva una respuesta simulada. Este método permite a los desarrolladores continuar con la implementación y las pruebas de la instancia de APIM en caso de que el back-end no esté disponible para enviar respuestas reales. La funcionalidad de simular respuestas puede resultar útil en una serie de escenarios:

+ Cuando la fachada de API se ha diseñado primero y la implementación de back-end se realiza más tarde. El back-end se está desarrollando en paralelo.
+ Cuando el back-end no está temporalmente operativo o no se puede escalar.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una API de prueba 
> * Adición de una operación a la API de prueba
> * Habilitación de la simulación de respuesta
> * Probar la API simulada

![Respuesta de la operación simulada](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>requisitos previos

Completar la guía de inicio rápido siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Creación de una API de prueba 

Los pasos de esta sección muestran cómo crear una API en blanco sin back-end. También se explica cómo agregar una operación a la API. Al llamar a la operación después de completar los pasos de esta sección, se produce un error. No se devolverá ningún error después de finalizar los pasos descritos en la sección "Habilitación de la simulación de respuesta".

1. Seleccione **API** en **API MANAGEMENT**.
2. En el menú izquierdo, seleccione **+ Agregar API**.
3. Seleccione **API en blanco** en la lista.
4. Escriba "*API de prueba*" en **Nombre para mostrar**.
5. Escriba "*Sin límite*" en **Productos**.
6. Seleccione **Crear**.

## <a name="add-an-operation-to-the-test-api"></a>Adición de una operación a la API de prueba

1. Seleccione la API que creó en los pasos anteriores.
2. Haga clic en **+ Agregar operación**.

    ![Respuesta de la operación simulada](./media/mock-api-responses/mock-api-responses02.png)

    |Configuración|Valor|DESCRIPCIÓN|
    |---|---|---|
    |**URL** (verbo HTTP)|GET|Puede elegir uno de los verbos HTTP predefinidos.|
    |**URL** |*/test*|Una ruta de acceso URL de la API. |
    |**Nombre para mostrar**|*Llamada de prueba*|El nombre se muestra en el **Portal para desarrolladores**.|
    |**Descripción**||Especifique una descripción de la operación que se usa para proporcionar documentación a los desarrolladores que usen esta API en el **Portal para desarrolladores**.|
    |Pestaña **Consulta**||Puede agregar parámetros de consulta. Además de proporcionar un nombre y una descripción, puede especificar valores que se pueden asignar a este parámetro. Uno de los valores se puede marcar como predeterminado (opcional).|
    |Pestaña **Solicitud**||Puede definir esquemas, ejemplos y tipos de contenido de solicitud. |
    |Pestaña **Respuesta**|Consulte los pasos que figuran abajo de esta tabla.|Defina esquemas, ejemplos, tipos de contenido y códigos de estado de respuesta.|

3. Seleccione la pestaña **Respuesta**, que se encuentra bajo los campos URL, Nombre para mostrar y Descripción.
4. Haga clic en **+ Agregar respuesta**.
5. Seleccione **200 OK** en la lista.
6. En el encabezado **Representaciones** de la derecha, seleccione **+ Agregar representación**.
7. Escriba "*application/json*" en el cuadro de búsqueda y seleccione el tipo de contenido **application/json**.
8. En el cuadro de texto **Ejemplo**, escriba "*{'sampleField': 'test'}*".
9. Seleccione **Guardar**.

## <a name="enable-response-mocking"></a>Habilitación de la simulación de respuesta

1. Seleccione la API que creó en el paso "Creación de una API de prueba".
2. Seleccione la operación de prueba que agregó.
2. En la ventana de la derecha, haga clic en **Diseño**.
3. En la ventana **Procesamiento de entrada**, haga clic en el icono del lápiz.
4. En la pestaña **Simulación**, seleccione **Respuestas estáticas** en **Comportamiento de simulación**.
5. En el cuadro de texto **API Management returns the following response** (API Management devuelve la siguiente respuesta), escriba **200 OK, application/json**. Esta selección indica que la API debe devolver la respuesta de ejemplo que definió en la sección anterior.
6. Seleccione **Guardar**.

## <a name="test-the-mocked-api"></a>Probar la API simulada

1. Seleccione la API que creó en el paso "Creación de una API de prueba".
2. Abra la pestaña **Prueba**.
3. Asegúrese de que la API de **Llamada de prueba** está seleccionada.

    > [!TIP]
    > Una barra amarilla con el texto **La simulación de respuesta está habilitada** indica que las respuestas devueltas desde API Management envían una directiva de simulación y no una respuesta de back-end real.

3. Seleccione **Enviar** para realizar una llamada de prueba.
4. **Respuesta HTTP** muestra el JSON especificado como un ejemplo en la primera sección del tutorial.

## <a name="video"></a>Vídeo

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]
> 
> 

## <a name="next-steps"></a>Pasos siguientes
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Creación de una API de prueba
> * Adición de una operación a la API de prueba
> * Habilitación de la simulación de respuesta
> * Probar la API simulada

Avance hasta el siguiente tutorial:

> [!div class="nextstepaction"]
> [Transformación y protección de una API publicada](transform-api.md)
