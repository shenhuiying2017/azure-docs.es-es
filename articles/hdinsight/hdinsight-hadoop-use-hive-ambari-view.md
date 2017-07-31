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
ms.date: 05/05/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 418b7bf2c1b2e6f66f6aebef48e2209c6b3ce5fb
ms.contentlocale: es-es
ms.lasthandoff: 06/10/2017


---
# <a name="use-the-hive-view-with-hadoop-in-hdinsight"></a>Uso de Vista de Hive con Hadoop en HDInsight

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Aprenda a ejecutar consultas de Hive mediante Ambari Hive View. Ambari es una utilidad de administración y supervisión proporcionada con los clústeres de HDInsight basados en Linux. Una de las características que se proporcionan a través de Ambari es una interfaz de usuario web que puede usarse para ejecutar consultas de Hive.

> [!NOTE]
> Ambari cuenta con muchas funcionalidades que no se tratan en este documento. Para más información, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de HDInsight basado en Linux Para información sobre la creación de un clúster, consulte [Introducción a HDInsight en Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

> [!IMPORTANT]
> Los pasos descritos en este documento requieren un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

## <a name="open-the-hive-view"></a>Abra la Vista de Hive.

Para ver Vistas Ambari en Azure Portal, seleccione el clúster de HDInsight y **Vistas de Ambari** en la sección **Vínculos rápidos**.

![Sección Vínculos rápidos](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

También puede ir directamente a Ambari accediendo a https://CLUSTERNAME.azurehdinsight.net in a web browser. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight. Para mostrar las vistas disponibles, seleccione el conjunto de cuadrados del menú. Para abrir la vista, seleccione la entrada **Vista de Hive**.

![Selección de vistas de Ambari](./media/hdinsight-hadoop-use-hive-ambari-view/select-hive-view.png).

> [!NOTE]
> Al acceder a Ambari, se le pide autenticarse en el sitio. Escriba el nombre de la cuenta del administrador (de manera predeterminada `admin`) y la contraseña que usó al crear el clúster.

Debería ver una página similar a la siguiente imagen:

![Imagen de la página de Vista de Hive, que contiene una sección de editor de consultas](./media/hdinsight-hadoop-use-hive-ambari-view/ambari-hive-view.png)

## <a name="view-tables"></a>Visualización de tablas

En la sección **Database Explorer** (Explorador de base de datos) de la página, seleccione la entrada **default** (Predeterminada) en la pestaña **Databases** (Bases de datos). Se muestra una lista de tablas en la base de datos predeterminada. HDInsight incluye una tabla denominada **hivesampletable**.

![explorador de base de datos con la base de datos predeterminada expandida](./media/hdinsight-hadoop-use-hive-ambari-view/database-explorer.png)

A medida que se agregan tablas a través de los pasos descritos en este documento, puede usar el icono de actualización que se encuentra en la esquina superior derecha del Explorador de base de datos para actualizar la lista.

## <a name="hivequery"></a>Editor de consultas

Para ejecutar una consulta de Hive, use los pasos siguientes en la vista de Hive.

1. En la sección **Editor de consultas** de la página, pegue las siguientes instrucciones de HiveQL en la hoja de cálculo:

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

2. Para iniciar la consulta, use el botón **Ejecutar** situado en la parte inferior del Editor de consultas. Su color cambia a naranja y el texto a **Detener ejecución**. Debe aparecer la sección **Resultados del proceso de consulta** bajo el Editor de consultas y mostrar información sobre el trabajo.

   > [!IMPORTANT]
   > Es posible que algunos exploradores no puedan actualizar correctamente la información de registro o los resultados. Si se ejecuta un trabajo y parece ejecutarse eternamente sin actualizar el registro o sin devolver resultados, pruebe a usar en su lugar Mozilla FireFox o Google Chrome.

3. Cuando haya finalizado la consulta, la sección **Resultados del proceso de consulta** muestra los resultados de la operación. El botón **Detener ejecución** volverá a cambiar al botón **Ejecutar** de color verde cuando la consulta se complete. La pestaña **Resultados** debe contener la información siguiente:

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

### <a name="hive-settings"></a>Configuración de Hive

Seleccione el icono de **Configuración** a la derecha del editor.

![Icono de configuración para la vista de Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-settings-icon.png)

Se puede usar la configuración para cambiar los diferentes valores de Hive. Por ejemplo, para cambiar el motor de ejecución de Hive de Tez (el valor predeterminado) a MapReduce.

### <a name="visualization"></a>Visualización

Seleccione el icono __Visualización__ a la derecha del editor.

![Icono de visualización de vista de Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization-icon.png)

La interfaz de visualización es donde puede crear visualizaciones de los datos devueltos por la consulta. La imagen siguiente es una visualización de ejemplo en la que se usan datos de `hivesampletable` que se incluye con HDInsight:

![Visualización de ejemplo](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization.png)

### <a name="visual-explain"></a>Explicación visual

Seleccione el icono de **Explicación visual** a la derecha del editor.

![Icono de explicación vista para la vista de Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visual-explain-icon.png)

La vista de **explicación visual** de la consulta puede ser útil para comprender el flujo de las consultas complejas. Puede ver un equivalente textual de esta vista con el botón **Explicar** en el Editor de consultas.

![Imagen de Explicación visual](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

### <a name="tez"></a>Tez

Seleccione el icono de **Tez** a la derecha del editor.

![Icono de Tez para la vista de Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-tez-icon.png)

Si se usó Tez para resolver la consulta, se muestra el gráfico acíclico dirigido (DAG). Si desea ver el DAG para las consultas que se ejecutaron en el pasado, o depurar el proceso Tez, use la [vista de Tez](hdinsight-debug-ambari-tez-view.md) en su lugar.

### <a name="notifications"></a>Notificaciones

Seleccione el icono de **Notificaciones** a la derecha del editor.

![Icono de notificación para la vista de Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-notifications-icon.png)

Las notificaciones son mensajes que se generan al ejecutar las consultas. Por ejemplo, recibirá una notificación cuando se envía una consulta, o cuando se produce un error.

## <a name="saved-queries"></a>Consultas guardadas

1. Desde el Editor de consultas, cree una nueva hoja de cálculo y escriba la siguiente consulta:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Ejecute la consulta para comprobar que funciona. Los resultados son similares a los del ejemplo siguiente:

        errorlogs.t1     errorlogs.t2     errorlogs.t3     errorlogs.t4     errorlogs.t5     errorlogs.t6     errorlogs.t7
        2012-02-03     18:35:34     SampleClass0     [ERROR]     incorrect     id     
        2012-02-03     18:55:54     SampleClass1     [ERROR]     incorrect     id     
        2012-02-03     19:25:27     SampleClass4     [ERROR]     incorrect     id

2. Use el botón **Guardar como** en la parte inferior del editor. Asigne a esta consulta el nombre de **Errorlogs** y seleccione **Aceptar**. El nombre de la hoja de cálculo cambia a **Errorlogs**.

3. Seleccione la pestaña **Consultas guardadas** en la parte superior de la página de Vista de Hive. **Errorlogs** aparece ahora como consulta guardada. Permanecerá en esta lista hasta que lo quite. Si selecciona el nombre, se abrirá la consulta en el Editor de consultas.

## <a name="query-history"></a>Historial de consulta

El botón **Historial** de la parte superior de la vista de Hive le permite ver las consultas ejecutadas anteriormente. Úselo ahora y seleccione una de las consultas que ejecutó previamente. Cuando selecciona una consulta, se abre en el Editor de consultas.

## <a name="user-defined-functions-udf"></a>Funciones definidas por el usuario (UDF)

Hive también puede extenderse a través de **funciones definidas por el usuario (UDF)**. Una UDF le permite implementar la funcionalidad o la lógica que no se modela con facilidad en HiveQL.

La pestaña UDF en la parte superior de la vista de Hive permite declarar y guardar un conjunto de UDF. Estas UDF se pueden usar con el **Editor de consultas**.

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

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información general acerca de Hive en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras formas en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

