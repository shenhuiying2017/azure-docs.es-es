---
title: "Solución de problemas de eventos de entrada con formato incorrecto en Azure Stream Analytics | Microsoft Docs"
description: "¿Cómo puedo saber qué evento de mis datos de entrada ocasiona problemas en un trabajo de Stream Analytics?"
keywords: 
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/19/2018
ms.author: sngun
ms.openlocfilehash: 2b4f82bae20c3cb398848a89715bfdc1316e1ba1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2018
---
# <a name="troubleshoot-for-malformed-input-events"></a>Solución de problemas de eventos de entrada con formato incorrecto

Cuando el flujo de entrada del trabajo de Stream Analytics contiene mensajes con un formato incorrecto, se producen problemas de serialización. Los mensajes con un formato incorrecto incluyen una serialización incorrecta, por ejemplo, faltan paréntesis en un objeto JSON o el formato de marca de hora es incorrecto. Cuando un trabajo de Stream Analytics recibe un mensaje con formato incorrecto, descarta el mensaje y lo notifica al usuario con una advertencia. Se muestra un símbolo de advertencia en el icono **Inputs** (Entradas) de su trabajo de Stream Analytics:

![Icono de entradas](media/stream-analytics-malformed-events/inputs_tile.png)

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas

1. Vaya al icono de entradas y haga clic para ver las advertencias.
2. El icono de detalles de entrada muestra un conjunto de advertencias con detalles sobre el problema. A continuación se muestra un ejemplo de un mensaje de advertencia con la partición, el desplazamiento y los números de secuencia donde hay datos JSON con un formato incorrecto. 

   ![Mensaje de advertencia con desplazamiento](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Para obtener los datos JSON que tienen un formato incorrecto, ejecute el siguiente fragmento de código. Este bloque de código lee el identificador de la partición y el desplazamiento e imprime los datos. Puede obtener el ejemplo completo en el [repositorio de ejemplos de GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Una vez leídos los datos, puede analizar y corregir el formato de serialización.

```csharp
static void PrintMessages(string partitionId, long offset, int numberOfEvents)
        {
            EventHubReceiver receiver;
            
            try
            {
                foreach (var e in receiver.Receive(numberOfEvents, TimeSpan.FromMinutes(1)))
                {
                    Console.WriteLine(Encoding.UTF8.GetString(e.GetBytes()));
                    Console.WriteLine("----");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.ToString());
                return;
            }

            receiver.Close();
        }
```
## <a name="next-steps"></a>Pasos siguientes

* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
