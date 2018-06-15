---
title: Probar un trabajo en Azure Stream Analytics con datos de ejemplo
description: Cómo probar consultas en trabajos de Stream Analytics.
keywords: En este artículo se describe cómo usar Azure Portal para probar un trabajo de Azure Stream Analytics, crear entradas de ejemplo y cargar datos de ejemplo.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 3dc9091934f3db8ededc13f74d2f302eccace4d6
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312997"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Probar una consulta de Stream Analytics con datos de ejemplo

Si usa Azure Stream Analytics, puede cargar datos de ejemplo y probar las consultas en Azure Portal sin iniciar ni detener ningún trabajo.

## <a name="upload-sample-data-and-test-the-query"></a>Carga de los datos de ejemplo y prueba de la consulta

1. Inicie sesión en el Portal de Azure. 

2. Busque el trabajo de Stream Analytics existente y selecciónelo.

3. En la página del trabajo de Stream Analytics, en el encabezado **Topología del trabajo**, seleccione **Consulta** para abrir la ventana del editor de consultas. 

4. Para probar la consulta con datos de entrada de ejemplo, haga clic con el botón derecho en cualquiera de las entradas.  A continuación, seleccione **Cargar datos de ejemplo desde un archivo**.

   Los datos deben ser datos con formato JSON. Si los datos tienen un formato distinto, como CSV, deberá convertirlos al formato JSON antes de cargarlos. Puede usar cualquier herramienta de conversión de código abierto, como este [convertidor de CSV a JSON](http://www.convertcsv.com/csv-to-json.htm), para convertir los datos al formato JSON.

    ![consulta de prueba del editor de consultas de Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Una vez finalizada la carga, seleccione **Probar** para probar esta consulta con los datos de ejemplo que ha proporcionado.

    ![datos de prueba de ejemplo del editor de consultas de Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Si desea guardar la salida de prueba para un uso posterior, el resultado de la consulta se muestra en el explorador mediante un vínculo a los resultados de la descarga. 

7. Ahora puede modificar iterativamente la consulta y probarla repetidamente para ver cómo cambia la salida.

   ![Salida de ejemplo del editor de consultas de Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Cuando use varias salidas en una consulta, podrá ver los resultados en fichas separadas y alternar fácilmente entre ellas.

8. Después de comprobar los resultados mostrados en el explorador, **guarde** la consulta. A continuación, **inicie** el trabajo y deje que procese los eventos de entrada.

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
