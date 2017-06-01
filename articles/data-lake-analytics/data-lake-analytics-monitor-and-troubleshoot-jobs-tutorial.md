---
title: "Solución de problemas de trabajos de Azure Data Lake Analytics mediante Azure Portal | Microsoft Docs"
description: "Aprenda a usar el Portal de Azure para solucionar problemas de trabajos de Análisis de Data Lake. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: b2b19a6f2ea20c414119e9dfbf84fda92dd93402
ms.contentlocale: es-es
ms.lasthandoff: 05/26/2017


---
# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>Solución de problemas de trabajos de Análisis de Azure Data Lake mediante el Portal de Azure
Aprenda a usar el Portal de Azure para solucionar problemas de trabajos de Análisis de Data Lake.

En este tutorial, configurará un problema con un archivo de origen que falta y usará el Portal de Azure para solucionar el problema.

**Requisitos previos**

Antes de empezar este tutorial, debe contar con lo siguiente:

* **Conocimientos básicos del proceso de trabajo de Análisis de Data Lake**. Consulte [Introducción a Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md).
* **Una cuenta de Análisis de Data Lake**. Vea [Introducción a Azure Data Lake Analytics con Azure Portal](data-lake-analytics-get-started-portal.md#create-data-lake-analytics-account).
* **Copiar los datos de ejemplo en la cuenta predeterminada de Almacén de Data Lake**.  Consulte [Preparación de los datos de origen](data-lake-analytics-get-started-portal.md)

## <a name="submit-a-data-lake-analytics-job"></a>Envío de un trabajo de Análisis de Data Lake
Ahora creará un trabajo U-SQL con un nombre de archivo de origen incorrecto.  

**Para enviar el trabajo**

1. En el Portal de Azure, haga clic en **Microsoft Azure** en la esquina superior izquierda.
2. Haga clic en el icono con el nombre de la cuenta de Análisis de Data Lake.  Se ancló aquí cuando se creó la cuenta.
   Si la cuenta no está anclada ahí, consulte [Apertura de una cuenta de Análisis desde el portal](data-lake-analytics-manage-use-portal.md#manage-data-sources).
3. Haga clic en **Nuevo trabajo** en el menú superior.
4. Escriba el nombre del trabajo y el siguiente script U-SQL:

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

    El archivo de origen definido en el script es **/Samples/Data/SearchLog.tsv1**, pero debería ser **/Samples/Data/SearchLog.tsv**.
5. Haga clic en **Enviar trabajo** en la parte superior. Se abre un nuevo panel llamado Detalles del trabajo. En la barra de título, se muestra el estado del trabajo. Tarda unos minutos en finalizar. Puede hacer clic en **Actualizar** para obtener el estado más reciente.
6. Espere a que el estado del trabajo cambie a **Error**.  Si el estado del trabajo es **Correcto**, es porque no quitó la carpeta /Samples. Consulte la sección **Requisitos previos** que se encuentra al principio del tutorial.

Tal vez se pregunte: ¿por qué tarda tanto tiempo para trabajo pequeño.  Recuerde que Análisis de Data Lake está diseñado para procesar macrodatos.  Destaca al procesar una gran cantidad de datos mediante su sistema distribuido.

Ahora suponga que envió el trabajo y cierra el portal.  En la sección siguiente, aprenderá a solucionar el problema del trabajo.

## <a name="troubleshoot-the-job"></a>Solución de problemas del trabajo
En la última sección, ha enviado un trabajo y este dio error.  

**Para ver todos los trabajos**

1. En el Portal de Azure, haga clic en **Microsoft Azure** en la esquina superior izquierda.
2. Haga clic en el icono con el nombre de la cuenta de Análisis de Data Lake.  Se muestra el resumen del trabajo en el icono **Administración de trabajos** .

    ![Administración de trabajos de Análisis de Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    La administración de trabajos ofrece información general del estado del trabajo. Observe que hay un trabajo con error.
3. Haga clic en el icono **Administración de trabajo** para ver los trabajos. Los trabajos se organizan por las categorías **En ejecución**, **En cola** y **Terminado**. Verá el trabajo con error en el **Terminado** . Deberá ser el primero de la lista. Si tiene una gran cantidad de trabajos, haga clic en **Filtro** para ayudarle a localizar los trabajos.

    ![Trabajos de filtro de Análisis de Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Haga clic en el trabajo con error en la lista para abrir los detalles de dicho trabajo en una nueva hoja:

    ![Trabajos con error de Análisis de Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Observe el **Reenviar** botón. Después de corregir el problema, puede volver a enviar el trabajo.
5. Haga clic en la parte resaltada de la captura de pantalla anterior para abrir los detalles del error.  Verá algo parecido a lo siguiente:

    ![Detalles de trabajos con error de Análisis de Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Indica que no se encuentra la carpeta de origen.
6. Haga clic en **Duplicar script**.
7. Actualización de la ruta de acceso **DESDE** a lo siguiente:

    "/ Samples/Data/SearchLog.tsv"
8. Haga clic en **Enviar trabajo**.

## <a name="see-also"></a>Otras referencias
* [Información general de Análisis de Azure Data Lake](data-lake-analytics-overview.md)
* [Introducción a Análisis de Azure Data Lake mediante Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introducción a Análisis de Azure Data Lake y a U-SQL mediante Visual Studio](data-lake-analytics-u-sql-get-started.md)
* [Administración de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-manage-use-portal.md)

