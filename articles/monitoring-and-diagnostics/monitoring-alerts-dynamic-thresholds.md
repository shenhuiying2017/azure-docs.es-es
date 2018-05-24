---
title: Creación de alertas con umbrales dinámicos en Azure Monitor | Microsoft Docs
description: Creación de alertas con umbrales dinámicos basados en aprendizaje automático
author: antonfrMSFT
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: antonfr;mbullwin
ms.openlocfilehash: 45d2f39ff7fb2e5a22cda74cd0c794db28e351f1
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="alerts-with-dynamic-thresholds-in-azure-monitor-limited-public-preview"></a>Alertas con umbrales dinámicos en Azure Monitor (versión preliminar pública limitada)

Las alertas con umbrales dinámicos constituyen una mejora a alertas de métrica de Azure en Azure Monitor, que aprovechan las funcionalidades avanzadas del aprendizaje automático (ML) para obtener información sobre el comportamiento histórico de las métricas para calcular automáticamente las líneas de base y utilizarlas como umbrales de alerta.

Las ventajas de utilizar umbrales dinámicos son:

- Se evitan las complicaciones asociadas con la configuración de un límite predefinido rígido, ya que el monitor aprende automáticamente el rendimiento histórico de la métrica y aplica a algoritmos de aprendizaje automático para determinar los umbrales de alerta.
- Pueden identificar comportamientos estacionales y alertar solo en caso de desviaciones del comportamiento estacional esperado. Las alertas de métricas con umbrales dinámicos no se activarán si el servicio está inactivo habitualmente los fines de semana y luego tiene picos todos los lunes. Actualmente admite: estacionalidad por hora, diaria o semanalmente.
- Aprende continuamente el rendimiento de las métricas y es adaptable a los cambios de métrica.

Las alertas basadas en umbrales dinámicos están disponibles para todos los orígenes de métricas basadas en Azure Monitor que se incluyen en este [artículo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#what-resources-can-i-create-near-real-time-metric-alerts-for).

## <a name="sign-up-to-access-the-preview"></a>Registro para acceder a la versión preliminar

Para probar esta funcionalidad, [regístrese en la versión preliminar](http://aka.ms/DynamicThresholdMetricAlerts). Como siempre, nos encantaría recibir sus comentarios; envíelos a [azurealertsfeedback@microsoft.com](mailto:azurealertsfeedback@microsoft.com)

## <a name="how-to-configure-alerts-with-dynamic-thresholds"></a>Configuración de alertas con umbrales dinámicos

Las alertas con umbrales dinámicos pueden configurarse a través de Alertas en Azure Monitor.

![Versión preliminar de alertas](./media/monitoring-alerts-dynamic-thresholds/0001.png)

## <a name="creating-an-alert-rule-with-dynamic-thresholds"></a>Creación de una regla de alerta con umbrales dinámicos

1. Del panel Alertas en Monitor, seleccione el botón **Nueva regla de alertas** para crear una alerta en Azure.

   ![Nueva regla de alertas](./media/monitoring-alerts-dynamic-thresholds/002.png)

2. La sección Crear regla se divide en tres partes: _Definir condición de la alerta_, _Definir detalles de la alerta_ y _Definir grupo de acciones_. En primer lugar, comience por la sección _Definir condición de alerta_, use el vínculo **Seleccionar destino** para especificar el destino, seleccionando un recurso. Una vez que elija un recurso adecuado, haga clic en el botón Listo.

   ![Selección del destino](./media/monitoring-alerts-dynamic-thresholds/0003.png)

3. A continuación, use el botón **Agregar criterios** para ver la lista de opciones de señal disponibles para el recurso y elija la opción de **métrica** apropiada de la lista de señales. (Por ejemplo, Porcentaje de CPU).

   ![Agregar criterios](./media/monitoring-alerts-dynamic-thresholds/004.png)

4. En la pantalla Configurar lógica de señal, en la sección Lógica de alerta, tiene la opción para cambiar la condición a un tipo Dinámica, lo que generará automáticamente los umbrales dinámicos (líneas rojas) junto con la métrica (línea azul).

   ![Dinámica](./media/monitoring-alerts-dynamic-thresholds/005.png)

5. Los umbrales que aparecen en el gráfico se calculan según los últimos siete días de datos históricos; una vez que se cree una alerta, los umbrales dinámicos adquirirán datos históricos adicionales que estén disponibles y aprenderá de manera continua en función de nuevos datos para obtener umbrales más precisos.

6. Configuración adicional de Lógica de alerta:
   - Condición: puede elegir que la alerta se active en una de las tres condiciones siguientes:
       - Mayor que el umbral superior o menor que el umbral inferior (valor predeterminado)
       - Mayor que el umbral superior
       - Menor que el umbral inferior
   - Agregación de tiempo: Promedio (valor predeterminado), sum, mín, máx.
   - Sensibilidad de la alerta:
       - Alta: más alertas, la alerta se activará ante la desviación más pequeña.
       - Media: menos sensible que la alta, menos alertas que con la sensibilidad alta (valor predeterminado)
       - Baja: el umbral menos sensible.

    ![Configuración de la lógica de alerta](./media/monitoring-alerts-dynamic-thresholds/00007.png)

7. Se evaluó basándose en:
    -  Durante cuánto tiempo la alerta debería buscar la condición especificada elegida en **Período**.

    ![Se evaluó basándose en](./media/monitoring-alerts-dynamic-thresholds/007.png)

   > [!NOTE]
   > Valores de Período admitidos: 5 minutos, 10 minutos, 30 minutos y 1 hora.

   Para reducir el ruido de alertas generado por picos transitorios, se recomienda utilizar la configuración Number violations to trigger the alert (Número de infracciones que activan la alerta). Esta funcionalidad le permite obtener una alerta solo si se ha infringido el umbral X veces consecutivas o Y veces en los últimos Z períodos. Por ejemplo: 

    Para activar una alerta cuando el problema es continuo durante 15 minutos, 3 veces consecutivas en un período dado de 5 minutos, utilice la siguiente configuración:

   ![Se evaluó basándose en](./media/monitoring-alerts-dynamic-thresholds/0008.png)

    Para activar una alerta cuando se ha producido una infracción de umbral dinámico en 15 minutos de los últimos 30 minutos con un período de 5 minutos, utilice la siguiente configuración:

   ![Se evaluó basándose en](./media/monitoring-alerts-dynamic-thresholds/0009.png)

8. Actualmente los usuarios pueden tener las alertas con criterios de umbral dinámico como único criterio.

   ![Creación de una regla](./media/monitoring-alerts-dynamic-thresholds/010.png)

## <a name="q--a"></a>Preguntas y respuestas

- P: Si la métrica cambia lentamente con el tiempo, ¿activará una alerta con umbrales dinámicos?

- R: Probablemente no. Los umbrales dinámicos son buenos para detectar desviaciones importantes, en lugar de problemas de evolución lenta.

- P: ¿Puedo configurar umbrales de dinámicos a través de una API?

- R: Estamos trabajando en ello.
