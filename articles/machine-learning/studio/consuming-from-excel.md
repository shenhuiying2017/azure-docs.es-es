---
title: Consumo de un servicio web Machine Learning de Excel | Microsoft Docs
description: "Consumir un servicio web de Aprendizaje automático de Azure de Excel"
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: cgronlun
ms.assetid: 3f3cdd2f-1816-487e-ab78-530e01e9788f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/13/2017
ms.author: tedway
ms.openlocfilehash: 9a8b39853e1a90815758af1d8fd772db7cc18ac5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="consuming-an-azure-machine-learning-web-service-from-excel"></a>Consumo de un servicio web de Aprendizaje automático de Azure de Excel
 Estudio de aprendizaje automático de Azure facilita la llamada a servicios web directamente desde Excel sin necesidad de escribir ningún código.

Si utiliza Excel 2013 (o posterior) o Excel Online, le recomendamos que use el [complemento de Excel](excel-add-in-for-web-services.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="steps"></a>Pasos
Publicar un servicio web. [esta página](walkthrough-5-publish-web-service.md) se explica cómo hacerlo. Actualmente, la función de libro de Excel solo se admite para los servicios de solicitud/respuesta que tienen una salida única (es decir, una etiqueta de puntuación única). 

Una vez que tenga un servicio web, haga clic en la sección **SERVICIOS WEB** que se encuentra a la izquierda del estudio y, luego, seleccione el servicio web que se va a usar desde Excel.

**Servicio web clásico**

1. En la pestaña **PANEL** del servicio web se encuentra una fila para el servicio **SOLICITUD/RESPUESTA**. Si este servicio tenía una salida única, deberá consultar el vínculo **Descargar el libro de Excel** de esa fila.
   
    ![][1]
2. Haga clic en **Descargar el libro de Excel**.

**Servicio web nuevo**

1. En el portal de servicios web de Aprendizaje automático de Azure, seleccione **Consume**(Consumo).
2. En la página de consumo, en la sección **Web service consumption options** (Opciones de consumo del servicio web), haga clic en el icono de Excel.

**Uso del libro**

1. Abra el libro.
2. Aparecerá una advertencia de seguridad; haga clic en el botón **Habilitar edición** .
   
    ![][2]
3. Aparecerá una advertencia de seguridad. Haga clic en el botón **Habilitar contenido** para ejecutar macros en la hoja de cálculo.
   
    ![][3]
4. Una vez que las macros están habilitadas, se generará una tabla. Las columnas en azul son necesarias como entrada al servicio web RRS, o como **PARÁMETROS**. Tenga en cuenta la salida del servicio RRS, los **VALORES PREDICHOS** en verde. Cuando se llenan todas las columnas de una fila determinada, el libro llama a la API de puntuación automáticamente y muestra los resultados con puntuación.
   
    ![][4]
5. Para puntuar más de una fila, rellene la segunda fila con datos y se generarán los valores predichos. Incluso puede pegar varias filas a la vez.

Puede utilizar cualquiera de las funciones de Excel (gráficos, asignación de energía, formato condicional, etc.) con los valores de predicción como ayuda para visualizar los datos.    

## <a name="sharing-your-workbook"></a>Compartir el libro
Para que las macros funcionen, la clave de API debe formar parte de la hoja de cálculo. Esto significa que debe compartir el libro con las entidades e individuos de su confianza.

## <a name="automatic-updates"></a>Actualizaciones automáticas
En las siguientes dos situaciones se realiza una llamada a RRS:

1. La primera vez que una fila tiene contenido en todos sus **PARÁMETROS**
2. Cada vez que cualquiera de los **PARÁMETROS** cambia en una fila en la que se habían especificado todos sus **PARÁMETROS**.

[1]: ./media/consuming-from-excel/excellink.png
[2]: ./media/consuming-from-excel/enableeditting.png
[3]: ./media/consuming-from-excel/enablecontent.png
[4]: ./media/consuming-from-excel/sampletable.png
