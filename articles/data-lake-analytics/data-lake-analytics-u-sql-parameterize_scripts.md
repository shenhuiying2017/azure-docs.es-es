---
title: "Parametrización de scripts de U-SQL en Azure Data Lake Analytics | Microsoft Docs"
description: Aprenda a parametrizar scripts de U-SQL en Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/10/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 6318fa8b14d8bb7d650522e2d96a5b1417afe3df
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="parameterizing-u-sql-scripts"></a>Parametrización de scripts de U-SQL

Al trabajar con scripts de U-SQL, es conveniente parametrizarlos. Los parámetros permiten que el cuerpo principal permanezca igual y seguir controlando la ejecución del script al pasar valores de parámetro independientes. Un escenario típico implica que un script procese datos a lo largo de cierto intervalo de tiempo y un desarrollador quiera parametrizar el script en una fecha de inicio y una fecha de finalización. 

Hay dos maneras de parametrizar scripts:
* El script permite la adición segura de parámetros al principio del script. U-SQL admite este método.
* Una API de envío de trabajos permite a los autores de llamadas enviar parámetros. U-SQL no admite este método.

## <a name="example-scripts"></a>Scripts de ejemplo

Este es un sencillo script inicial:

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
    TO "/output/data.csv"
    USING Outputters.Csv();
```    
   
En este escenario, se parametriza la ``Region`` agregando una instrucción `DECLARE EXTERNAL`.

```
DECLARE EXTERNAL @TargetRegion = "en-us";

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
    TO "/output/data.csv"
    USING Outputters.Csv();
```    

Al ejecutarse, este script adopta como valor predeterminado filtrar solo las filas de la región `en-us`. Hasta ahora, se ha definido un parámetro en el script. La palabra clave `EXTERNAL` indica que un usuario puede redefinir el valor `@TargetRegion` anteponiendo una instrucción DECLARE que defina el parámetro `@TargetRegion`.

El siguiente script muestra el aspecto del script parametrizado cuando un usuario ha especificado un valor para el parámetro. El script filtra ahora las filas que tienen una región de `en-gb`.

```
DECLARE @TargetRegion = "en-gb";

DECLARE EXTERNAL @TargetRegion = "en-us";

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
    TO "/output/data.csv"
    USING Outputters.Csv();
```    


## <a name="next-steps"></a>Pasos siguientes
* [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Desarrollo de scripts de U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Supervisión y solución de problemas de trabajos de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

