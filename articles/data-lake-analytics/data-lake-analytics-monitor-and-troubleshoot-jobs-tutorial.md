---
title: Solución de problemas de trabajos de Azure Data Lake Analytics mediante Azure Portal | Microsoft Docs
description: 'Aprenda a usar Azure Portal para solucionar problemas de trabajos de Análisis de Data Lake. '
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: f6168997c449be5354bd223c516d4f929a1bf894
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>Solución de problemas de trabajos de Azure Data Lake Analytics mediante Azure Portal
Aprenda a usar Azure Portal para solucionar problemas de trabajos de Data Lake Analytics.

En este tutorial, configurará un problema con un archivo de origen que falta y usará Azure Portal para solucionar el problema.

## <a name="submit-a-data-lake-analytics-job"></a>Envío de un trabajo de Análisis de Data Lake

Envíe el siguiente trabajo de U-SQL:

```
@searchlog =
   EXTRACT UserId          int,
           Start           DateTime,
           Region          string,
           Query           string,
           Duration        int?,
           Urls            string,
           ClickedUrls     string
   FROM "/Samples/Data/SearchLog.tsv1"
   USING Extractors.Tsv();

OUTPUT @searchlog   
   TO "/output/SearchLog-from-adls.csv"
   USING Outputters.Csv();
```
    
El archivo de origen definido en el script es **/Samples/Data/SearchLog.tsv1**, pero debería ser **/Samples/Data/SearchLog.tsv**.


## <a name="troubleshoot-the-job"></a>Solución de problemas del trabajo

**Para ver todos los trabajos**

1. En el Portal de Azure, haga clic en **Microsoft Azure** en la esquina superior izquierda.
2. Haga clic en el icono con el nombre de la cuenta de Análisis de Data Lake.  Se muestra el resumen del trabajo en el icono **Administración de trabajos** .

    ![Administración de trabajos de Análisis de Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    La administración de trabajos ofrece información general del estado del trabajo. Observe que hay un trabajo con error.
3. Haga clic en el icono **Administración de trabajo** para ver los trabajos. Los trabajos se organizan por las categorías **En ejecución**, **En cola** y **Terminado**. Verá el trabajo con error en el **Terminado** . Deberá ser el primero de la lista. Si tiene una gran cantidad de trabajos, haga clic en **Filtro** para ayudarle a localizar los trabajos.

    ![Trabajos de filtro de Análisis de Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Haga clic en el trabajo con error en la lista para abrir los detalles de dicho trabajo:

    ![Trabajos con error de Análisis de Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Observe el **Reenviar** botón. Después de corregir el problema, puede volver a enviar el trabajo.
5. Haga clic en la parte resaltada de la captura de pantalla anterior para abrir los detalles del error.  Verá algo parecido a lo siguiente:

    ![Detalles de trabajos con error de Análisis de Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Indica que no se encuentra la carpeta de origen.
6. Haga clic en **Duplicar script**.
7. Actualización de la ruta de acceso **DESDE** a:

    "/ Samples/Data/SearchLog.tsv"
8. Haga clic en **Enviar trabajo**.

## <a name="see-also"></a>Otras referencias
* [Información general de Análisis de Azure Data Lake](data-lake-analytics-overview.md)
* [Introducción a Análisis de Azure Data Lake mediante Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introducción a Análisis de Azure Data Lake y a U-SQL mediante Visual Studio](data-lake-analytics-u-sql-get-started.md)
* [Administración de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-manage-use-portal.md)
