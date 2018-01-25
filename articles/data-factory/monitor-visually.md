---
title: "Supervisión visual de las factorías de datos de Azure | Microsoft Docs"
description: "Aprenda a supervisar visualmente las factorías de datos de Azure"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: 97a8fa1779cd11830781528527ddfe998e0548b6
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="visually-monitor-azure-data-factories"></a>Supervise visualmente las factorías de datos de Azure
Azure Data Factory es un servicio de integración de datos basado en la nube que le permite crear flujos de trabajo basados en datos en la nube a fin de coordinar y automatizar el movimiento y la transformación de datos. Mediante Azure Data Factory, puede crear y programar flujos de trabajo orientados a datos (llamados canalizaciones) que pueden ingerir datos de almacenes de datos dispares, procesar o transformar los datos mediante servicios de proceso, como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics y Azure Machine Learning, y publicar datos de salida en almacenes de datos, como Azure SQL Data Warehouse para que los consuman las aplicaciones de inteligencia empresarial (BI).
En esta guía de inicio rápido, aprenderá a supervisar visualmente las canalizaciones de factorías de datos v2 sin escribir una sola línea de código.
Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Supervisión y administración de canalizaciones en Data Factory, versión 1](v1/data-factory-monitor-manage-app.md).

## <a name="monitor-data-factory-v2-pipelines"></a>Supervisión de las canalizaciones de factorías de datos v2

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Vaya a la hoja de factoría de datos creada en Azure Portal y haga clic en el icono 'Supervisar & Administrar'. Se iniciará la experiencia de supervisión de ADF v2 visual.

## <a name="list-view-monitoring"></a>Supervisión de la vista de lista

Supervise las ejecuciones de actividad y canalización con una simple interfaz de vista de lista. Se muestran todas las ejecuciones en la zona horaria local del explorador. Puede cambiar la zona horaria y todos los campos de fecha y hora se ajustarán a la seleccionada.  

#### <a name="monitoring-pipeline-runs"></a>Supervisión de las ejecuciones de canalización
Vista de lista que muestra cada ejecución de canalización para las canalizaciones de la factoría de datos v2. Columnas incluidas:

| **Nombre de la columna** | **Descripción** |
| --- | --- |
| Nombre de la canalización | Nombre de la canalización. |
| Acciones | Acción única disponible para ver ejecuciones de la actividad. |
| Hora de inicio de la ejecución | Fecha y hora de inicio de la ejecución de canalización (MM/DD/AAAA HH: mm: SS a.m./p.m.) |
| Duration | Duración de ejecución (HH) |
| Desencadenado por | Desencadenador manual, desencadenador de programación |
| Status | No se pudo, Se realizó correctamente, En curso |
| Parámetros | Canalización de parámetros de ejecución (nombre, pares de valor) |
| Error | Error de ejecución de canalización (if/any) |
| Ejecutar identificador | Identificador de la ejecución de canalización |

![La supervisión de la canalización se ejecuta](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>Supervisión de las ejecuciones de actividad
Vista de lista muestra ejecuciones de actividad correspondiente a cada ejecución de canalización. Haga clic en el icono **'Se ejecuta la actividad'** bajo la columna **"Acciones"** para ver que la actividad se ejecuta para cada ejecución de canalización. Columnas incluidas:

| **Nombre de la columna** | **Descripción** |
| --- | --- |
| Nombre de actividad | Nombre de la actividad dentro de la canalización. |
| Tipo de actividad | Tipo de la actividad es decir, copia, HDInsightSpark, HDInsightHive, etcetera. |
| Hora de inicio de la ejecución | Hora y fecha de inicio de ejecución de actividad (MM/DD/AAAA HH:MM:SS a.m./p.m.) |
| Duration | Duración de ejecución (HH) |
| Status | No se pudo, Se realizó correctamente, En curso |
| Entrada | Matriz JSON que describe las entradas de actividad |
| Salida | Matriz JSON que describe las salidas de actividad |
| Error | Error de ejecución de actividad (if/any) |

![Supervisión de las ejecuciones de actividad](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Debe hacer clic en el icono **"Actualizar"** en la parte superior para actualizar la lista de canalizaciones y ejecuciones de actividad. La actualización automática no se admite actualmente.
>

![Actualizar](media/monitor-visually/refresh.png)

## <a name="features"></a>Características

#### <a name="rich-ordering-and-filtering"></a>Orden y filtrado enriquecidos

Las ejecuciones de canalización con orden se ejecutan de forma ascendente o descendente con Inicio de ejecución y las ejecuciones de las canalizaciones con filtro, por las columnas siguientes:

| **Nombre de la columna** | **Descripción** |
| --- | --- |
| Nombre de la canalización | Nombre de la canalización. Las opciones incluyen filtros rápidos para las 'últimas 24 horas', la 'última semana' y los 'últimos 30 días', o bien puede seleccionar una fecha y hora personalizadas. |
| Hora de inicio de la ejecución | Fecha y hora de inicio de la ejecución de canalización |
| Estado de ejecución | Ejecuciones con filtro por estado, es decir, correcto, con error, en curso |

![Filtrar](media/monitor-visually/filter.png)

#### <a name="addremove-columns-to-list-view"></a>Agregar o quitar columnas a la vista de lista
Haga clic con el botón derecho en el encabezado de la vista de lista y elija las columnas que desea que aparezca en la vista de lista

![Columnas](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>Reordenar los anchos de columna en la vista de lista
Aumentar y disminuir los anchos de columna en la vista de lista simplemente manteniendo el puntero sobre el encabezado de columna

#### <a name="select-data-factory"></a>Selección de la factoría de datos
Mantenga el mouse sobre el icono 'Factoría de datos' en la parte superior izquierda. Haga clic en el icono de 'Flecha' para ver una lista de factorías de datos y suscripciones de Azure que puede supervisar.

![Selección de la factoría de datos](media/monitor-visually/select-datafactory.png)

#### <a name="guided-tours"></a>Visitas guiadas
Haga clic en el 'Icono de información' en la parte inferior izquierda y haga clic en 'Visitas guiadas' para obtener instrucciones paso a paso sobre cómo supervisar las ejecuciones de actividad y canalización.

![Visitas guiadas](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>Comentarios
Haga clic en el icono "Comentarios" para enviarnos comentarios de varias características o de los problemas a los que quizás se enfrente.

![Comentarios](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>pasos siguientes

Consulte el artículo [Supervisión y administración de canalizaciones mediante programación](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) para obtener información sobre la supervisión y administración de canalizaciones
