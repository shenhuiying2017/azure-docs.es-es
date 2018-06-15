---
title: Información sobre las entradas de Azure Stream Analytics
description: En este artículo se describe el concepto de entradas en un trabajo de Azure Stream Analytics y se compara la entrada de streaming con la entrada de datos de referencia.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 926821e2ba9912ae0140f11c9fe9a2d504609a1e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186069"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Información sobre las entradas de Azure Stream Analytics

Los trabajos de Azure Stream Analytics se conectan a una o más entradas de datos. Cada entrada define una conexión a un origen de datos existente. Stream Analytics acepta datos procedentes de varios tipos de orígenes de eventos, entre los que se incluyen Event Hubs, IoT Hub y el almacenamiento de blobs. En la consulta SQL de streaming que se escribe para cada trabajo, se hace referencia a las entradas por su nombre. En la consulta, puede combinar varias entradas para fusionar datos o comparar datos de streaming con una búsqueda de datos de referencia y pasar los resultados a las salidas. 

Stream Analytics presenta una integración de primera clase de tres tipos de recursos como entradas:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Almacenamiento de blobs de Azure](https://azure.microsoft.com/services/storage/blobs/) 

Estos orígenes de entrada pueden proceder de la misma suscripción de Azure que el trabajo de Stream Analytics o de otra suscripción.

Puede usar [Azure Portal](stream-analytics-quick-create-portal.md#configure-input-to-the-job), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), la [API de REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) y [Visual Studio](stream-analytics-tools-for-visual-studio.md) para crear, editar y probar las entradas del trabajo de Stream Analytics.

## <a name="stream-and-reference-inputs"></a>Entradas de flujo y de referencia
A medida que los datos se insertan en un origen de datos, el trabajo de Stream Analytics los consume y los procesa en tiempo real. Las entradas se dividen en dos tipos distintos: entradas de flujo de datos y entradas de datos de referencia.

### <a name="data-stream-input"></a>Entrada de flujo datos
Un flujo de datos es una secuencia ilimitada de eventos a lo largo del tiempo. Los trabajos de Stream Analytics deben incluir, como mínimo, una entrada de flujo de datos. Event Hubs, IoT Hub y and Blob Storage se admiten como orígenes de entrada de flujo de datos. Event Hubs sirve para recopilar flujos de eventos desde varios dispositivos y servicios. Es posible que estos flujos incluyan fuentes de actividades de redes sociales, información bursátil o datos de sensores. Los Centros de IoT están optimizados para recopilar datos de dispositivos conectados en escenarios del Internet de las cosas (IoT).  Blob Storage puede usarse como origen de entrada para la ingesta de conjuntos masivos de datos en forma de flujo, como, por ejemplo, archivos de registro.  

Para obtener más información sobre las entradas de streaming, vea [Stream data as input into Stream Analytics](stream-analytics-define-inputs.md) (Datos de flujo como entrada en Stream Analytics).

### <a name="reference-data-input"></a>Entrada de datos de referencia
Stream Analytics también admite la entrada de *datos de referencia*. Los datos de referencia son completamente estáticos o cambian lentamente. Se utilizan normalmente para realizar la correlación y las búsquedas. Por ejemplo, es posible combinar datos de la entrada de flujo de datos con datos de los datos de referencia, de la misma forma que ejecutaría una instrucción SQL JOIN para buscar valores estáticos. Almacenamiento de blobs de Azure es el único origen de entrada admitido actualmente para los datos de referencia. El tamaño de los blobs de origen de datos de referencia se limita a 100 MB.

Para obtener más información sobre las entradas de datos de referencia, vea [Uso de datos de referencia para las búsquedas en Stream Analytics](stream-analytics-use-reference-data.md)

Este artículo es un paso de la [ruta de aprendizaje de Stream Analytics](/documentation/learning-paths/stream-analytics/).

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Guía de inicio rápido: Creación de un trabajo de Stream Analytics mediante Azure Portal](stream-analytics-quick-create-portal.md)