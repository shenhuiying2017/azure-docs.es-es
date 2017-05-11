---
title: "Extend U-SQL scripts with R in Azure Data Lake Analytics (Extensión de los scripts de U-SQL con R en Azure Data Lake Analytics) | Microsoft Docs"
description: "Obtenga información sobre cómo ejecutar código R en scripts de U-SQL"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: ea837a735404d11b087cd552d1fce64184e88e00
ms.contentlocale: es-es
ms.lasthandoff: 05/02/2017


---

# <a name="tutorial-get-started-with-extending-u-sql-with-r"></a>Tutorial: Introducción a la extensión de U-SQL con Python (Tutorial: Introducción a la extensión de U-SQL con R)

En el ejemplo siguiente se muestran los pasos básicos para implementar código R:
* Use la instrucción de REFERENCE ASSEMBLY para habilitar las extensiones de R para el script de U-SQL.
* Use la operación REDUCE para la partición de datos de entrada en una clave.
* Las extensiones de R para U-SQL incluyen un reductor integrado (Extension.R.Reducer) que ejecuta código R en cada vértice asignado al reductor. 
* Uso de tramas de datos con nombre dedicadas llamadas inputFromUSQL y outputToUSQL, respectivamente, para pasar datos entre USQL y R. Los nombres del identificador DataFrame de entrada y salida son fijos (es decir, los usuarios no pueden cambiar estos nombres predefinidos de los identificadores DataFrame de entrada y salida).


--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-r-integrates-with-u-sql"></a>Cómo se integra R con U-SQL

### <a name="datatypes"></a>Tipos de datos
* Las columnas de cadena y numéricas de U-SQL se convierten tal cual entre R DataFrame y U-SQL [tipos admitidos: doble, cadena, booleano, entero y byte].
* El tipo de datos de factor no se admite en U-SQL.
* byte[] debe serializarse como cadena codificada en base64.
* Las cadenas de U-SQL se pueden convertir en factores en el código R, una vez que U-SQL cree una trama de datos de entrada de R o mediante el establecimiento del parámetro reductor stringsAsFactors: true.

### <a name="schemas"></a>Esquemas
* Los conjuntos de datos de U-SQL no pueden tener nombres de columna duplicados.
* Los nombres de columna de conjuntos de datos de U-SQL deben ser cadenas.
* Los nombres de columna deben ser los mismos en los scripts de U-SQL y de R.
* La columna de solo lectura no puede formar parte de la trama de datos de salida, ya que las columnas de solo lectura vuelven a insertarse automáticamente en la tabla de U-SQL si forma parte del esquema de salida de UDO.

## <a name="next-steps"></a>Pasos siguientes
* [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Desarrollo de scripts de U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Uso de funciones de ventana de U-SQL para trabajos de Análisis de Azure Data Lake](data-lake-analytics-use-window-functions.md)

