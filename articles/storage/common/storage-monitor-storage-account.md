---
title: "Supervisión de una cuenta de Azure Storage | Microsoft Docs"
description: Aprenda a supervisar una cuenta de almacenamiento en Azure usando el Portal de Azure.
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: b83cba7b-4627-4ba7-b5d0-f1039fe30e78
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: tamram
ms.openlocfilehash: 20cb425b64bfba06d64b3c6c41f9ef496395ad8e
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Supervisión de una cuenta de almacenamiento en el Portal de Azure

[Análisis de Azure Storage](../storage-analytics.md) proporciona métricas para todos los servicios de almacenamiento y registros para blobs, colas y tablas. Puede usar [Azure Portal](https://portal.azure.com) para configurar qué registros y métricas se registran para su cuenta y configurar gráficos que proporcionen representaciones visuales de los datos de las métricas.

> [!NOTE]
> Existen costos asociados con el análisis de los datos de supervisión en Azure Portal. Para obtener más información, consulte [Facturación y Storage Analytics](/rest/api/storageservices/Storage-Analytics-and-Billing).
>
> Azure Files admite actualmente las métricas de Storage Analytics, pero aún no admite el registro.
>
> Las cuentas de almacenamiento que tienen una replicación de tipo "Almacenamiento con redundancia de zona" (ZRS) son compatibles con las métricas y el registro. Las cuentas anteriores de ZRS cuyo nombre haya cambiado a ZRS Classic no admiten las métricas o el registro. Para obtener más información acerca de ZRS, consulte [Almacenamiento con redundancia de zona](storage-redundancy.md#zone-redundant-storage). 
> 
> Para obtener orientación exhaustiva sobre el uso de análisis de almacenamiento y otras herramientas para identificar, diagnosticar y solucionar problemas relacionados con Azure Storage, consulte [Supervisión, diagnóstico y solución de problemas de Microsoft Azure Storage](../storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>la supervisión para una cuenta de almacenamiento

1. En [Azure Portal](https://portal.azure.com), seleccione **Cuentas de almacenamiento**y, después, seleccione el nombre de la cuenta de almacenamiento para abrir el panel de esta.
1. En la sección **SUPERVISIÓN** de la hoja de menú, seleccione **Diagnósticos**.

    ![OpcionesSupervisión](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)

1. Seleccione el **tipo** de datos de métricas para cada **servicio** que desee supervisar y la **directiva de retención** para los datos. También puede deshabilitar la supervisión estableciendo el **estado** en **Desactivado**.

    ![OpcionesSupervisión](./media/storage-monitor-storage-account/stg-enable-metrics-01.png)

   Hay dos tipos de métricas que puede habilitar para cada servicio. Ambos están habilitados de forma predeterminada en el caso de nuevas cuentas de almacenamiento:

   * **Agregados**: recopila métricas como entrada/salida, disponibilidad, latencia y porcentajes de éxito. Estas métricas se agregan a los servicios Blob, Cola, Tabla y Archivo.
   * **Por API**: además de las métricas de agregados, recopila el mismo conjunto de métricas para cada operación de almacenamiento en la API del servicio Azure Storage.

   Para configurar la directiva de retención de datos, mueva el control deslizante **Retención (en días)** o escriba el número de días que se deben retener los datos, entre 1 y 365. El valor predeterminado para las nuevas cuentas de almacenamiento es de siete días. Si no desea configurar una directiva de retención, escriba un cero. Si no existe una directiva de retención, es posible eliminar los datos de supervisión.

   > [!WARNING]
   > Se le cobrará si elimina manualmente datos de métricas. El sistema eliminará los datos de análisis obsoletos (aquellos anteriores a la directiva de retención de datos) sin costo alguno. Es recomendable configurar una directiva de retención basada en el tiempo que desee retener los datos de análisis de almacenamiento de su cuenta. Consulte [¿Qué se le facturará cuando habilite las métricas de almacenamiento?](../common/storage-enable-and-view-metrics.md#what-charges-do-you-incur-when-you-enable-storage-metrics) para más información.
   >

1. Al finalizar la configuración de la supervisión, seleccione **Guardar**.

Se muestra un conjunto predeterminado de métricas en los gráficos de la hoja de la cuenta de almacenamiento, así como las hojas de cada servicio (Blob, Cola, Tabla y Archivo). Una vez que ha habilitado las métricas de un servicio, puede tardar una hora hasta que los datos aparezcan en los gráficos. Puede seleccionar **Editar** en cualquier gráfico de métricas para [configurar qué métricas](#how-to-customize-metrics-charts) se muestran en el gráfico.

Puede deshabilitar la recopilación y el registro de métricas estableciendo el **estado** en **Desactivado**.

> [!NOTE]
> Azure Storage usa [Table Storage](../common/storage-introduction.md#table-storage) para almacenar las métricas para la cuenta de almacenamiento y almacena las métricas en tablas en su cuenta. Para más información, consulte [Cómo se almacenan las métricas](../common/storage-analytics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Personalización de gráficos de métricas

Utilice el procedimiento siguiente para elegir las métricas de almacenamiento que desea ver en un gráfico de métricas. 

1. Comience por mostrar un gráfico de métricas de almacenamiento en Azure Portal. Puede encontrar los gráficos en la **hoja de la cuenta de almacenamiento** y en la hoja **Métricas** de un servicio individual (Blob, Cola, Tabla, Archivo).

   En este ejemplo, se trabaja con el siguiente gráfico que aparece en la **hoja de la cuenta de almacenamiento**:

   ![Selección de gráfico en Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. A continuación, haga clic en cualquier lugar dentro del gráfico para abrir la hoja **Métricas**. Seleccione **Editar gráfico** para abrir la hoja **Editar gráfico**.

   ![Botón Editar gráfico de la hoja del gráfico](./media/storage-monitor-storage-account/stg-customize-chart-01.png)

1. En la hoja **Editar gráfico**, seleccione el **intervalo de tiempo** de las métricas que se van a mostrar en el gráfico y el **servicio** (Blob, Cola, Tabla, Archivo) cuyas métricas desea mostrar. Aquí hemos seleccionado para mostrar las métricas de la semana anterior para el servicio Blob:

   ![Selección de intervalo de tiempo y servicio en la hoja Editar gráfico](./media/storage-monitor-storage-account/stg-customize-chart-02.png)

1. Seleccione las **métricas** individuales que desea que aparezcan en el gráfico y, a continuación, haga clic en **Aceptar**. Por ejemplo, aquí hemos decidido mostrar las métricas *ContainerCount* y *ObjectCount*:

   ![Selección de métricas individuales en la hoja Editar gráfico](./media/storage-monitor-storage-account/stg-customize-chart-03.png)

Estas configuraciones del gráfico no afectan a la recopilación, agregación ni almacenamiento de los datos de supervisión en la cuenta de almacenamiento, solo afectan a la visualización de los datos de las métricas.

### <a name="metrics-availability-in-charts"></a>Disponibilidad de las métricas en los gráficos

La lista de las métricas disponibles cambia en función de qué servicio haya elegido en la lista desplegable y del tipo de unidad del gráfico que está editando. Por ejemplo, puede seleccionar métricas de porcentaje como *PercentNetworkError* y *PercentThrottlingError* solo si está editando un gráfico que muestra las unidades en porcentajes:

![Solicitud de gráfico de porcentaje de errores en Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Resolución de métricas

Las métricas seleccionadas en Diagnóstico determinan la resolución de las métricas que están disponibles para su cuenta:

* **Agregados**: la supervisión proporciona métricas como entrada/salida, disponibilidad, latencia y porcentajes de éxito. Estas métricas se agregan a los servicios Blob, Cola, Tabla y Archivo.
* **Por API** proporciona una resolución más precisa, con métricas disponibles para operaciones de almacenamiento individuales, además de los agregados de nivel de servicio.

## <a name="configure-metrics-alerts"></a>Configuración de las alertas de métricas

Puede crear alertas para recibir una notificación cuando se alcancen los umbrales para las métricas de los recursos de almacenamiento.

1. Para abrir la **hoja de reglas de alerta**, desplácese hacia abajo hasta la sección **SUPERVISIÓN** de la **hoja de menú** y seleccione **Reglas de alerta**.
1. Seleccione **Agregar alerta** para abrir la hoja **Agregar una regla de alerta**
1. Seleccione **Recurso** (blob, archivo, cola, tabla) en la lista desplegable y escriba un **nombre** y una **descripción** para la nueva regla de alerta.
1. Seleccione la **métrica** para la que desea agregar una alerta, una **condición** de alerta y un **umbral** de alerta. El tipo de unidad de umbral cambiará en función de la métrica que haya elegido. Por ejemplo, "número" es el tipo de unidad para *ContainerCount*, mientras que la unidad para la métrica *PercentNetworkError* es un porcentaje.
1. Seleccione el **período**. Las métricas que alcancen o superen el umbral dentro de ese período de tiempo desencadenarán una alerta.
1. (Opcional) Configure las notificaciones de **Correo electrónico** y **Webhook**. Para más información sobre webhooks, consulte [Configuración de un webhook en una alerta de métrica de Azure](../../monitoring-and-diagnostics/insights-webhooks-alerts.md). Si no configura las notificaciones de correo electrónico o webhook, las alertas solo aparecerán en Azure Portal.

![Hoja "Agregar una regla de alerta" en Azure Portal](./media/storage-monitor-storage-account/stg-alert-rules-01.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Adición de gráficos de métricas al panel del portal

Puede agregar gráficos de métricas de Azure Storage a cualquiera de las cuentas de almacenamiento del panel del portal.

1. Seleccione **Editar panel** mientras lo ve en [Azure Portal](https://portal.azure.com).
1. En la **galería de iconos**, seleccione **Buscar iconos por** > **Tipo**.
1. Seleccione **Tipo** > **Cuentas de almacenamiento**.
1. En **Recursos**, seleccione la cuenta de almacenamiento cuyas métricas desea agregar al panel.
1. Seleccione **Categorías** > **Supervisión**.
1. Arrastre y coloque el icono del gráfico en el panel de la métrica que desea mostrar. Repita este paso para todas las métricas que desee que se muestren en el panel. En la siguiente imagen, se resalta el gráfico "Blobs: Número total de solicitudes" como un ejemplo, pero todos los gráficos están disponibles para su ubicación en el panel.

   ![Galería de iconos de Azure Portal](./media/storage-monitor-storage-account/stg-customize-dashboard-01.png)
1. Seleccione la opción **Personalización finalizada** situada cerca de la parte superior del panel cuando haya terminado de agregar los gráficos.

Una vez que haya agregado los gráficos al panel, los puede personalizar aún más tal y como se describe en [Personalización de gráficos de métricas](#how-to-customize-metrics-charts).

## <a name="configure-logging"></a>registro

También puede indicar a Azure Storage que guarde los registros de diagnóstico de lectura, escritura y eliminación de las solicitudes de los servicios Blob, Tabla y Cola. La directiva de retención de datos que estableció también se aplica a estos registros.

> [!NOTE]
> Azure Files admite actualmente las métricas de Storage Analytics, pero aún no admite el registro.
>

1. En [Azure Portal](https://portal.azure.com), seleccione **Cuentas de almacenamiento**y, después, seleccione el nombre de la cuenta de almacenamiento para abrir la hoja de esta.
1. En la sección **SUPERVISIÓN** de la hoja de menú, seleccione **Diagnósticos**.

    ![Elemento de menú Diagnóstico de la sección SUPERVISIÓN de Azure Portal.](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)
    
1. Asegúrese de que el **estado** está establecido en **Activado** y seleccione los **servicios** para los que desea habilitar el registro.

    ![Configure el registro en Azure Portal.](./media/storage-monitor-storage-account/stg-enable-logging-01.png)
1. Haga clic en **Save**(Guardar).

Los registros de diagnóstico se guardan en un contenedor de blobs denominado $logs en su cuenta de almacenamiento. Puede ver los datos de registro mediante un explorador de almacenamiento como el [Explorador de Microsoft Storage](http://storageexplorer.com), o mediante programación con la biblioteca de cliente de almacenamiento o PowerShell.

Para más información acerca del acceso al contenedor $logs, consulte [Habilitar el registro de almacenamiento y acceso a los datos del registro](/rest/api/storageservices/enabling-storage-logging-and-accessing-log-data).

## <a name="next-steps"></a>pasos siguientes

* Consiga más información acerca de [métricas, registro y facturación](../storage-analytics.md) para Storage Analytics.
* [Habilitar métricas de Azure Storage y visualizar datos de métricas](../storage-enable-and-view-metrics.md) mediante PowerShell y mediante programación con C#.
