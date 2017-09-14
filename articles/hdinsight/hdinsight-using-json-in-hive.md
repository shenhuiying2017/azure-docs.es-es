---
title: "Análisis y procesamiento de documentos JSON con Hive en HDInsight | Microsoft Docs"
description: Aprenda a utilizar documentos JSON y analizarlos mediante Hive en HDInsight.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: ee7d40d2ff0ae1ac10b54f4c1f1dd704a70eb70c
ms.contentlocale: es-es
ms.lasthandoff: 09/06/2017

---
# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>Proceso y análisis de documentos JSON mediante Hive en HDInsight

Aprenda a procesar y a analizar archivos JSON mediante Hive en HDInsight. En el tutorial se usa el siguiente documento JSON:

    {
        "StudentId": "trgfg-5454-fdfdg-4346",
        "Grade": 7,
        "StudentDetails": [
            {
                "FirstName": "Peggy",
                "LastName": "Williams",
                "YearJoined": 2012
            }
        ],
        "StudentClassCollection": [
            {
                "ClassId": "89084343",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "High",
                "Score": 93,
                "PerformedActivity": false
            },
            {
                "ClassId": "78547522",
                "ClassParticipation": "NotSatisfied",
                "ClassParticipationRank": "None",
                "Score": 74,
                "PerformedActivity": false
            },
            {
                "ClassId": "78675563",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "Low",
                "Score": 83,
                "PerformedActivity": true
            }
        ]
    }

El archivo se encuentra en wasb://processjson@hditutorialdata.blob.core.windows.net/. Para obtener más información sobre cómo usar el almacenamiento de blobs de Azure con HDInsight, consulte [Uso del almacenamiento de blobs de Azure compatibles con HDFS con Hadoop en HDInsight](hdinsight-hadoop-use-blob-storage.md). Puede copiar el archivo en el contenedor predeterminado del clúster.

En este tutorial, se utiliza la consola de Hive.  Para obtener instrucciones sobre cómo abrir la consola de Hive, consulte [Uso de Hive con Hadoop en HDInsight con Escritorio remoto](hdinsight-hadoop-use-hive-remote-desktop.md).

## <a name="flatten-json-documents"></a>Acoplamiento de documentos JSON
Los métodos enumerados en la siguiente sección requieren que el documento JSON esté en una sola fila. Por lo tanto, debe acoplar el documento JSON a una cadena. Si el documento JSON ya está acoplado, puede omitir este paso e ir directamente a la sección siguiente sobre Análisis de los datos JSON.

    DROP TABLE IF EXISTS StudentsRaw;
    CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

    DROP TABLE IF EXISTS StudentsOneLine;
    CREATE EXTERNAL TABLE StudentsOneLine
    (
      json_body string
    )
    STORED AS TEXTFILE LOCATION '/json/students';

    INSERT OVERWRITE TABLE StudentsOneLine
    SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
          FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
          GROUP BY INPUT__FILE__NAME;

    SELECT * FROM StudentsOneLine

El archivo sin formato JSON se encuentra en **wasb://processjson@hditutorialdata.blob.core.windows.net/**. La tabla de Hive *StudentsRaw* apunta al documento JSON sin formato y no acoplado.

La tabla de Hive *StudentsOneLine* almacena los datos en el sistema de archivos predeterminado de HDInsight en la ruta de acceso */json/students/*.

La instrucción INSERT rellena la tabla StudentOneLine con los datos de JSON acoplados.

La instrucción SELECT solo devolverá una fila.

Este es el resultado de la instrucción SELECT:

![Acoplamiento del documento JSON.][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Análisis de documentos JSON en Hive
Hive proporciona tres mecanismos distintos para ejecutar consultas en documentos JSON:

* usar la UDF (función definida por el usuario) GET\_JSON\_OBJECT
* usar la UDF JSON_TUPLE
* usar el SerDe personalizado
* escribir la propia UDF con Python u otros lenguajes Consulte [este artículo][hdinsight-python] para obtener información sobre cómo ejecutar su propio código de Python con Hive.

### <a name="use-the-getjsonobject-udf"></a>Uso de la UDF GET\_JSON_OBJECT
Hive proporciona una función definida por el usuario integrada llamada [get json object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object), que puede realizar consultas JSON en tiempo de ejecución. Este método toma dos argumentos: el nombre de la tabla y el nombre del método que tiene el documento JSON acoplado y el campo JSON que debe analizarse. Veamos un ejemplo para ver cómo funciona esta UDF.

Obtener el nombre y el apellido de cada estudiante

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

Este es el resultado cuando se ejecuta esta consulta en la ventana de la consola.

![UDF get_json_object][image-hdi-hivejson-getjsonobject]

Existen algunas limitaciones de la UDF de get-json_object.

* Como cada campo de la consulta requiere volver a analizar la consulta, afecta al rendimiento.
* GET\_JSON_OBJECT() devuelve la representación de cadena de una matriz. Para convertir esta matriz en una matriz de Hive, tiene que utilizar expresiones regulares para reemplazar los corchetes '[' y ']' y luego llamar también a split para obtener la matriz.

Este es el motivo de que el sitio wiki de Hive recomiende el uso de json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Use la UDF JSON_TUPLE.
Otra función definida por el usuario proporcionada por Hive se denomina [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), que es más eficaz que [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Este método toma un conjunto de claves y una cadena JSON y devuelve una tupla de valores mediante una función. La siguiente consulta devuelve el identificador y el curso del estudiante del documento JSON:

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

Salida de este script en la consola de Hive:

![UDF json_tuple][image-hdi-hivejson-jsontuple]

JSON\_TUPLE usa la sintaxis [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) de Hive, que permite que json\_tuple cree una tabla virtual aplicando la función UDT a cada fila de la tabla original.  Los JSON complejos se vuelven demasiado difíciles de manejar debido al uso repetido de LATERAL VIEW. Además, JSON_TUPLE no puede controlar los JSON anidados.

### <a name="use-custom-serde"></a>Usar el SerDe personalizado
SerDe es la mejor opción para analizar documentos JSON anidados ya que le permite definir el esquema JSON y usar el esquema para analizar los documentos. Para obtener instrucciones, consulte este artículo acerca del [uso de un SerDe de JSON personalizado con Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Resumen
En conclusión, el tipo de operador JSON en Hive que elija depende de su escenario. Si tiene un documento JSON sencillo y solo tiene un campo por el que buscar, puede elegir usar la UDF de Hive get\_json\_object. Si tiene varias claves por las que buscar, puede utilizar json_tuple. Si tiene un documento anidado, debe utilizar el SerDe de JSON.

## <a name="next-steps"></a>Pasos siguientes

Para ver otros artículos relacionados, consulte

* [Usar Hive y HiveQL con Hadoop en HDInsight para analizar un archivo log4j de Apache de muestra](hdinsight-use-hive.md)
* [Análisis de datos de retraso de vuelos con Hive en HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Análisis de datos de Twitter con Hive en HDInsight](hdinsight-analyze-twitter-data.md)
* [Ejecución de un trabajo de Hadoop con Azure Cosmos DB y HDInsight](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

