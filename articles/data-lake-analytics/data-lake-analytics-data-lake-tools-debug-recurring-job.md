---
title: "Solución de problemas de un trabajo periódico anómalo | Microsoft Docs"
description: "Aprenda a usar las herramientas de Azure Data Lake para Visual Studio para la depuración de trabajos periódicos anómalos."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2017
ms.author: yanacai
ms.openlocfilehash: a358f94b117c12511028a875e56b5c9dba8d3382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-an-abnormal-recurring-job"></a>Solución de problemas de un trabajo periódico anómalo

En este documento, se explicará cómo usar las [herramientas de Azure Data Lake para Visual Studio](http://aka.ms/adltoolsvs) para solucionar problemas de trabajos periódicos. [Aquí](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/) puede encontrar más información acerca de la canalización y los trabajos recurrentes.
Normalmente, los trabajos periódicos comparten la misma lógica de consulta y datos de entrada similares. Por ejemplo, tiene un trabajo periódico que se ejecuta todos los lunes por la mañana a las 8 A.M. y que cuenta el usuario activo semanal de la semana pasada, los scripts de estos trabajos comparten una plantilla de script que contiene la lógica de consulta y las entradas de estos trabajos son los datos de uso de la semana anterior. Compartir la misma lógica de consulta y una entrada similar suele significar que el rendimiento de estos trabajos es similar y estable, y si repentinamente el funcionamiento de uno de los trabajos periódicos es anómalo, genera errores y se ralentiza mucho, puede:

1.  Vea los informes de las estadísticas de las ejecuciones de las versiones preliminares del trabajo periódico para ver lo que ha sucedido.
2.  Compare el trabajo anómalo con uno normal para descubrir lo que ha cambiado.

La **vista de trabajos relacionados** de las herramientas de Azure Data Lake para Visual Studio le ayuda a acelerar el progreso de la solución de problemas en ambos casos.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Paso 1: Búsqueda de trabajos periódicos y apertura de la vista de trabajos relacionados

Para usar la vista de trabajos relacionados para solucionar el problema de un trabajo periódico, primero es preciso buscar el trabajo periódico en Visual Studio y, después, abrir la vista de trabajo relacionados.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Caso 1: Tiene la dirección URL del trabajo periódico

A través de **Herramientas > Data Lake > Vista Trabajo**, puede pegar la dirección URL del trabajo para abrir la vista Trabajo en Visual Studio y a través de Ver trabajos relacionados puede la vista de trabajos relacionados.

![Ver trabajos relacionados en las Herramientas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Caso 2: Tiene la canalización del trabajo periódico, pero no la dirección URL

En Visual Studio, puede abrir el explorador de canalizaciones a través de **Explorador de servidores > su cuenta de Data Lake Analytics > Canalizaciones** (si no puede encontrar este nodo en el Explorador de servidores, obtenga la herramienta [aquí](http://aka.ms/adltoolsvs)). En el explorador de canalizaciones, todas las canalizaciones para la cuenta de ADLA se enumeran a la izquierda y puede expandirlas para buscar todos los trabajos periódicos, haga clic en el que tiene problemas, la vista de trabajos relacionados se abre a la derecha.

![Ver trabajos relacionados en las Herramientas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

![Ver trabajos relacionados en las Herramientas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-statistics-report"></a>Paso 2: Análisis del informe de estadísticas

Un resumen y un informe de las estadísticas se muestran en la parte superior de la vista de trabajos relacionados, desde donde puede ver la potencial causa principal de la anomalía. 

1.  En primer lugar, debe buscar el trabajo anómalo en el informe. El eje X muestra la hora de envío de los trabajos, a través de la cual puede localizar el trabajo anómalo.
2.  Con el proceso siguiente puede consultar las estadísticas y obtener la información de la anomalía y las posibles soluciones.

![Ver trabajos relacionados en las Herramientas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-recurring-job-to-a-normal-job"></a>Paso 3: Comparación del trabajo periódico anormal con un trabajo normal

Todos los trabajos periódicos enviados se pueden encontrar en la parte inferior de la vista de trabajos relacionados. Si hace clic con el botón derecho, puede comparar el trabajo anómalo con uno normal anterior para buscar más información y posibles soluciones en la vista Diferencias de trabajo.

![Ver trabajos relacionados en las Herramientas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Normalmente, es preciso prestar atención a las grandes diferencias entre estos 2 trabajos, ya que es probable que sean lo que causa los problemas de rendimiento, y también puede llevar a cabo los pasos siguientes para realizar una comprobación.

![Ver trabajos relacionados en las Herramientas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Pasos siguientes

* [Depuración y resolución de problemas de asimetría de datos](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Depuración de código C# definido por el usuario para trabajos de U-SQL con errores](data-lake-analytics-debug-u-sql-jobs.md)