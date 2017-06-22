---
title: "Introducción a Azure Data Lake Analytics mediante Azure Portal | Microsoft Docs"
description: 'Aprenda a usar Azure Portal para crear una cuenta de Data Lake Analytics, crear un trabajo de Data Lake Analytics mediante U-SQL y enviar el trabajo. '
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: eb85d8ef6b29605d7e26b0d2139a4a95c35141fb
ms.contentlocale: es-es
ms.lasthandoff: 05/31/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Tutorial: Introducción a Azure Data Lake Analytics mediante Azure Portal
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Aprenda a usar Azure Portal para crear cuentas de Azure Data Lake Analytics, definir trabajos en [U-SQL](data-lake-analytics-u-sql-get-started.md) y enviar trabajos al servicio Data Lake Analytics. Para obtener más información acerca de Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Requisitos previos

Para comenzar este tutorial, es preciso tener una **suscripción a Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-data-lake-analytics-account"></a>Creación de una cuenta de Data Lake Analytics

Ahora, creará una cuenta de Data Lake Analytics y una cuenta de Data Lake Store simultáneamente.  Este paso es sencillo y solo tarda unos 60 s en finalizar.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo** >  **Inteligencia y análisis** > **Data Lake Analytics**.
3. Seleccione los valores de los siguientes elementos:
   * **Nombre**: nombre de la cuenta de Data Lake Analytics (solo se permiten letras minúsculas y números).
   * **Suscripción**: seleccione la suscripción de Azure usada para la cuenta de Analytics.
   * **Grupo de recursos**. seleccione un grupo de recursos de Azure existente o cree uno nuevo.
   * **Ubicación**. seleccione un centro de datos de Azure para la cuenta de Análisis de Data Lake.
   * **Data Lake Store**: siga las instrucciones para crear una nueva cuenta de Data Lake Store o seleccione una existente. 
4. Si lo desea, seleccione un plan de tarifa para la cuenta de Data Lake Analytics.
5. Haga clic en **Crear**. 

## <a name="create-and-submit-data-lake-analytics-jobs"></a>Creación y envío de trabajos de Análisis de Data Lake
Después de preparar el origen de datos, puede comenzar a desarrollar un script U-SQL.  

**Para enviar un trabajo**

1. Desde la cuenta de Data Lake Analytics, haga clic en **Nuevo trabajo**.
2. Escriba el **Nombre del trabajo**y el siguiente script U-SQL:

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog   
    TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();
```


Este script de U-SQL lee el archivo de datos de origen mediante **Extractors.Tsv()** y crea un archivo csv con **Outputters.Csv()**.

1. Haga clic en **Enviar trabajo**.   
2. Espere a que el estado del trabajo cambie a **Correcto**.
3. Si se produce un error en el trabajo, consulte el artículo acerca de la [supervisión y la solución de problemas de los trabajos de Data Lake Analytics](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).
4. Haga clic en la pestaña **Salida** y luego en `SearchLog-from-Data-Lake.csv`. 

## <a name="see-also"></a>Consulte también

* Para empezar a desarrollar aplicaciones con U-SQL, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para conocer las tareas de administración, consulte [Administración de Azure Data Lake Analytics mediante el Azure Portal](data-lake-analytics-manage-use-portal.md).

