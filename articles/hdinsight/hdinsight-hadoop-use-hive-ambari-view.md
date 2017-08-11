---
title: 'Uso de las Vistas de Ambari para trabajar con Hive en HDInsight (Hadoop): Azure | Microsoft Docs'
description: "Obtenga información acerca de cómo usar la Vista de Hive desde el explorador web para enviar consultas de Hive. La Vista de Hive es parte de la interfaz de usuario web Ambari que se proporciona con el clúster de HDInsight basado en Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/31/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 80df3da4d62feb814ea2dd97c96e57954093c5c5
ms.contentlocale: es-es
ms.lasthandoff: 08/02/2017

---
# <a name="use-the-hive-view-with-hadoop-in-hdinsight"></a>Uso de Vista de Hive con Hadoop en HDInsight

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Aprenda a ejecutar consultas de Hive mediante Ambari Hive View. Ambari es una utilidad de administración y supervisión proporcionada con los clústeres de HDInsight basados en Linux. Una de las características que se proporcionan a través de Ambari es una interfaz de usuario web que puede usarse para ejecutar consultas de Hive.

> [!NOTE]
> Ambari cuenta con muchas funcionalidades que no se tratan en este documento. Para más información, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de HDInsight basado en Linux Para información sobre la creación de un clúster, consulte [Introducción a HDInsight en Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

> [!IMPORTANT]
> Los pasos descritos en este documento requieren un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="open-the-hive-view"></a>Abra la Vista de Hive.

Para ver Vistas Ambari en Azure Portal, seleccione el clúster de HDInsight y **Vistas de Ambari** en la sección **Vínculos rápidos**.

![sección de vínculos rápidos del portal](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

En la lista de vistas, seleccione __Vista de Hive__.

![La vista de Hive seleccionada](./media/hdinsight-hadoop-use-hive-ambari-view/select-hive-view.png)

> [!NOTE]
> Al acceder a Ambari, se le pide autenticarse en el sitio. Escriba el nombre de la cuenta del administrador (de manera predeterminada `admin`) y la contraseña que usó al crear el clúster.

Debería ver una página similar a la siguiente imagen:

![Imagen de la hoja de cálculo de consulta de la vista de Hive](./media/hdinsight-hadoop-use-hive-ambari-view/ambari-hive-view.png)

## <a name="hivequery"></a>Ejecución de una consulta

Para ejecutar una consulta de Hive, use los pasos siguientes en la vista de Hive.

1. En la pestaña __Consulta__, pegue las instrucciones HiveQL siguientes en la hoja de cálculo:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
    ```

    Estas instrucciones realizan las acciones siguientes:

   * `DROP TABLE`: elimina la tabla y el archivo de datos, en caso de que ya exista la tabla.

   * `CREATE EXTERNAL TABLE`: crea una nueva tabla "externa" en Hive.
   Las tablas externas solo almacenan la definición de tabla en Hive. Los datos permanecen en la ubicación original.

   * `ROW FORMAT`: indica el formato de los datos. En este caso, los campos de cada registro se separan mediante un espacio.

   * `STORED AS TEXTFILE LOCATION`: indica dónde se almacenan los datos y que se almacenan como texto.

   * `SELECT`: selecciona un recuento de todas las filas donde la columna t4 contiene el valor [ERROR].

     > [!NOTE]
     > Las tablas externas se deben utilizar cuando se espera que un origen externo actualice los datos subyacentes. Por ejemplo, un proceso de carga de datos automatizado u otra operación de MapReduce. La eliminación de una tabla externa *no* elimina los datos, solamente la definición de tabla.

    > [!IMPORTANT]
    > Deje la selección de __base de datos__ en el __valor predeterminado__. Los ejemplos de este documento usan la base de datos predeterminada que se incluye en HDInsight.

2. Para iniciar la consulta, use el botón **Ejecutar** que se encuentra debajo de la hoja de cálculo. Su color cambia a naranja y el texto a **Detener**.

3. Cuando haya finalizado la consulta, la pestaña **Resultados** muestra los resultados de la operación. El texto siguiente es el resultado de la consulta:

        sev       cnt
        [ERROR]   3

    La pestaña **Registros** puede usarse para ver la información de registro creada por el trabajo.

   > [!TIP]
   > El cuadro de diálogo desplegable **Guardar resultados** situado en la parte superior izquierda de la sección **Resultados del proceso de consulta** le permite descargar o guardar los resultados.

4. Seleccione las cuatro primeras líneas de esta consulta y después elija **Ejecutar**. Observe que no hay ningún resultado cuando finaliza el trabajo. Al usar el botón **Ejecutar** cuando parte de la consulta está seleccionada, solo se ejecutan las instrucciones seleccionadas. En este caso, la selección no incluyó la instrucción final que recupera filas de la tabla. Si selecciona solo esa línea y usa **Ejecutar**, debería ver los resultados esperados.

5. Para agregar una hoja de cálculo, use el botón **Nueva hoja de cálculo** situado en la parte inferior del **Editor de consultas**. En la hoja de cálculo nueva, escriba las siguientes instrucciones de HiveQL:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
    ```

  Estas instrucciones realizan las acciones siguientes:

   * **CREATE TABLE IF NOT EXISTS** : crea una tabla, si todavía no existe. Puesto que la palabra clave **EXTERNAL** no se utiliza, se crea una tabla interna. Una tabla interna se almacena en el almacenamiento de datos de Hive y Hive la administra completamente. A diferencia de las tablas externas , la eliminación de una tabla interna también elimina los datos subyacentes.

   * **STORED AS ORC** : almacena los datos en el formato Optimized Row Columnar (ORC). ORC es un formato altamente optimizado y eficiente para almacenar datos de Hive.

   * **INSERT OVERWRITE ... SELECT**: selecciona filas de la tabla **log4jLogs** que contienen `[ERROR]` e inserta los datos en la tabla **errorLogs**.

     Use el botón **Ejecutar** para ejecutar esta consulta. La pestaña **Resultados** no contiene ninguna información cuando la consulta devuelve cero filas. El estado debe aparecer como **CORRECTO** una vez completada la consulta.

### <a name="visual-explain"></a>Explicación visual

Para mostrar una visualización del plan de consulta, seleccione la pestaña **Explicación visual** que se encuentra debajo de la hoja de cálculo.

La vista de **explicación visual** de la consulta puede ser útil para comprender el flujo de las consultas complejas. Puede ver un equivalente textual de esta vista con el botón **Explicar** en el Editor de consultas.

### <a name="tez-ui"></a>Interfaz de usuario de Tez

Para mostrar la interfaz de usuario de Tez, seleccione la pestaña **Tez** que se encuentra debajo de la hoja de cálculo.

> [!IMPORTANT]
> Tez no se usa para resolver todas las consultas. Muchas consultas pueden resolverse sin necesidad de usar Tez. 

Si se usó Tez para resolver la consulta, se muestra el gráfico acíclico dirigido (DAG). Si desea ver el DAG para las consultas que se ejecutaron en el pasado, o depurar el proceso Tez, use la [vista de Tez](hdinsight-debug-ambari-tez-view.md) en su lugar.

## <a name="view-job-history"></a>Visualización del historial de trabajos

La pestaña __Trabajos__ muestra un historial de las consultas de Hive.

![Imagen del historial de trabajos](./media/hdinsight-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Tablas de la base de datos

Puede usar la pestaña __Tablas__ para trabajar con tablas dentro de una base de datos de Hive.

![Imagen de la pestaña de tablas](./media/hdinsight-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Consultas guardadas

En la pestaña Consulta, también puede guardar consultas si lo desea. Una vez que guarda la consulta, puede volver a usarla en la pestaña __Consultas guardadas__.

![Imagen de la pestaña de consultas guardadas](./media/hdinsight-hadoop-use-hive-ambari-view/saved-queries.png)

## <a name="user-defined-functions"></a>Funciones definidas por el usuario

Hive también puede extenderse a través de funciones definidas por el usuario (UDF). Una UDF le permite implementar la funcionalidad o la lógica que no se modela con facilidad en HiveQL.

La pestaña UDF en la parte superior de la vista de Hive permite declarar y guardar un conjunto de UDF. Estas UDF se pueden usar con el **Editor de consultas**.

![Imagen de la pestaña UDF](./media/hdinsight-hadoop-use-hive-ambari-view/user-defined-functions.png)

Una vez que haya agregado una función definida por el usuario a la Vista de Hive, aparecerá un botón **Insertar UDF** en la parte inferior del **Editor de consultas**. Al seleccionar esta entrada se muestra una lista desplegable de UDF definidas en la vista de Hive. Al seleccionar una función definida por el usuario se agregan instrucciones HiveQL a la consulta para habilitar dicha función.

Por ejemplo, si definió una función definida por el usuario con las siguientes propiedades:

* Nombre de recurso: myudfs

* Ruta de acceso de recurso: /myudfs.jar

* Nombre de la UDF: myawesomeudf

* Nombre de la clase UDF: com.myudfs.Awesome

Si usa el botón **Insertar UDF**, aparecerá una entrada llamada **myudfs**, con otra lista desplegable para cada función definida por el usuario establecida para ese recurso. En este caso, **myawesomeudf**. Al seleccionar esta entrada se agrega lo siguiente al principio de la consulta:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

A continuación, puede usar la función definida por el usuario en la consulta. Por ejemplo: `SELECT myawesomeudf(name) FROM people;`.

Para más información sobre el uso de UDF con Hive en HDInsight, consulte los siguientes documentos:

* [Uso de Python con Hive y Pig en HDInsight](hdinsight-python.md)
* [Cómo agregar una UDF de Hive personalizada a HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Configuración de Hive

Se puede usar la configuración para cambiar los diferentes valores de Hive. Por ejemplo, para cambiar el motor de ejecución de Hive de Tez (el valor predeterminado) a MapReduce.

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información general acerca de Hive en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras formas en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

