---
title: Uso de las Vistas de Ambari para trabajar con Hive en HDInsight (Hadoop) | Microsoft Docs
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
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 511d6dd1933f44cd0cb5ba800972a7c112a24c04
ms.lasthandoff: 04/12/2017


---
# <a name="use-the-hive-view-with-hadoop-in-hdinsight"></a>Uso de Vista de Hive con Hadoop en HDInsight

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Ambari es una utilidad de administración y supervisión proporcionada con los clústeres de HDInsight basados en Linux. Una de las características que se proporcionan a través de Ambari es una interfaz de usuario web que puede usarse para ejecutar consultas de Hive. Se trata de la **vista de Hive**, que forma parte de las Vistas de Ambari proporcionadas con el clúster de HDInsight.

> [!NOTE]
> Ambari cuenta con muchas funcionalidades que no se tratan en este documento. Para más información, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de HDInsight basado en Linux Para información sobre la creación de un clúster, consulte [Introducción a HDInsight en Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

> [!IMPORTANT]
> Los pasos descritos en este documento requieren un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="open-the-hive-view"></a>Abra la Vista de Hive.

Para ver Vistas Ambari en Azure Portal, seleccione el clúster de HDInsight y **Vistas de Ambari** en la sección **Vínculos rápidos**.

![Sección Vínculos rápidos](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

También puede ir directamente a Ambari accediendo a https://CLUSTERNAME.azurehdinsight.net in a web browser. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight. Seleccione el grupo de cuadrados en el menú de la página junto al vínculo **Administrador** para mostrar las vistas disponibles. Seleccione la **vista de Hive**.

![Selección de vistas de Ambari](./media/hdinsight-hadoop-use-hive-ambari-view/selecthiveview.png).

> [!NOTE]
> Al acceder a Ambari, se le pide autenticarse en el sitio. Escriba el nombre de la cuenta del administrador (de manera predeterminada `admin`) y la contraseña que usó al crear el clúster.

Debería ver una página similar a la siguiente:

![Imagen de la página de Vista de Hive, que contiene una sección de editor de consultas](./media/hdinsight-hadoop-use-hive-ambari-view/hiveview.png)

## <a name="view-tables"></a>Visualización de tablas
En la sección **Database Explorer** (Explorador de base de datos) de la página, seleccione la entrada **default** (Predeterminada) en la pestaña **Databases** (Bases de datos). Esto muestra una lista de tablas en la base de datos predeterminada. Para un nuevo clúster de HDInsight, solo debe aparecer una tabla, **hivesampletable**.

![explorador de base de datos con la base de datos predeterminada expandida](./media/hdinsight-hadoop-use-hive-ambari-view/databaseexplorer.png)

A medida que se agregan tablas a través de los pasos descritos en este documento, puede usar el icono de actualización que se encuentra en la esquina superior derecha del Explorador de base de datos para actualizar la lista.

## <a name="hivequery"></a>Editor de consultas

Utilice los pasos siguientes de la vista de Hive para ejecutar una consulta de Hive.

1. En la sección **Editor de consultas** de la página, pegue las siguientes instrucciones de HiveQL en la hoja de cálculo:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
    ```

    Estas instrucciones realizan las acciones siguientes:

   * **DROP TABLE** : elimina la tabla y el archivo de datos si la tabla ya existe.

   * **CREATE EXTERNAL TABLE** : crea una tabla "externa" nueva en Hive.
   Las tablas externas solo almacenan la definición de tabla en Hive. Los datos permanecen en la ubicación original.

   * **ROW FORMAT** : indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.

   * **STORED AS TEXTFILE LOCATION** : indica a Hive dónde se almacenan los datos (el directorio example/data) y que se almacenan como texto.

   * **SELECT** : selecciona un número de todas las filas donde la columna t4 contiene el valor [ERROR].

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
   > El diálogo desplegable **Guardar resultados** situado en la parte superior izquierda de la sección **Resultados del proceso de consulta** le permite descargar o guardar los resultados.

4. Seleccione las cuatro primeras líneas de esta consulta y después elija **Ejecutar**. Observe que no hay ningún resultado cuando finaliza el trabajo. Al usar el botón **Ejecutar** cuando parte de la consulta está seleccionada, solo se ejecutan las instrucciones seleccionadas. En este caso, la selección no incluyó la instrucción final que recupera filas de la tabla. Si selecciona solo esa línea y usa **Ejecutar**, debería ver los resultados esperados.

5. Para agregar una nueva hoja de cálculo, use el botón **Nueva hoja de cálculo** situado en la parte inferior del **Editor de consultas**. En la hoja de cálculo nueva, escriba las siguientes instrucciones de HiveQL:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
    ```

  Estas instrucciones realizan las acciones siguientes:

   * **CREATE TABLE IF NOT EXISTS** : crea una tabla, si todavía no existe. Puesto que la palabra clave **EXTERNAL** no se utiliza, se crea una tabla interna. Una tabla interna se almacena en el almacenamiento de datos de Hive y Hive la administra completamente. A diferencia de las tablas externas , la eliminación de una tabla interna también elimina los datos subyacentes.

   * **STORED AS ORC** : almacena los datos en el formato Optimized Row Columnar (ORC). Se trata de un formato altamente optimizado y eficiente para almacenar datos de Hive.

   * **INSERT OVERWRITE ... SELECT**: selecciona filas de la tabla **log4jLogs** que contienen [ERROR] e inserta los datos en la tabla **errorLogs**.

     Use el botón **Ejecutar** para ejecutar esta consulta. La pestaña **Resultados** no contiene ninguna información cuando la consulta devuelve cero filas. El estado debe aparecer como **CORRECTO** una vez completada la consulta.

### <a name="hive-settings"></a>Configuración de Hive

Seleccione el icono de **Configuración** a la derecha del editor.

![Icono de configuración para la vista de Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-settings-icon.png)

La opción de configuración puede usarse para cambiar varios ajustes de Hive, como cambiar el motor de ejecución de Hive de Tez (predeterminado) a MapReduce.

### <a name="visualization"></a>Visualización

Seleccione el icono __Visualización__ a la derecha del editor.

![Icono de visualización de vista de Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization-icon.png)

Se abrirá la interfaz de visualización, donde puede crear visualizaciones de los datos que devuelve la consulta. A continuación se muestra una visualización de ejemplo que usa datos de `hivesampletable` que se incluye con HDInsight.

![Visualización de ejemplo](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization.png)

### <a name="visual-explain"></a>Explicación visual

Seleccione el icono de **Explicación visual** a la derecha del editor.

![Icono de explicación vista para la vista de Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visual-explain-icon.png)

Se trata de la vista **Explicación visual** de la consulta, que puede ser útil para comprender el flujo de las consultas complejas. Puede ver un equivalente textual de esta vista con el botón **Explicar** en el Editor de consultas.

![Imagen de Explicación visual](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

### <a name="tez"></a>Tez

Seleccione el icono de **Tez** a la derecha del editor.

![Icono de Tez para la vista de Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-tez-icon.png)

Se muestra el grafo acíclico dirigido (DAG) usado por Tez para esta consulta, si está disponible. Si desea ver el DAG para las consultas que se ejecutaron en el pasado, o depurar el proceso Tez, use la [vista de Tez](hdinsight-debug-ambari-tez-view.md) en su lugar.

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

El botón **Historial** en la parte superior de la Vista de Hive le permite ver las consultas que haya ejecutado previamente. Úselo ahora y seleccione algunas de las consultas que ejecutó previamente. Cuando selecciona una consulta, se abre en el Editor de consultas.

## <a name="user-defined-functions-udf"></a>Funciones definidas por el usuario (UDF)

Hive también puede extenderse a través de **funciones definidas por el usuario (UDF)**. Una UDF le permite implementar la funcionalidad o la lógica que no se modela con facilidad en HiveQL.

La pestaña UDF en la parte superior de la Vista de Hive permite declarar y guardar un conjunto de funciones definidas por el usuario que se pueden usar con el **Editor de consultas**.

Una vez que haya agregado una función definida por el usuario a la Vista de Hive, aparecerá un botón **Insertar UDF** en la parte inferior del **Editor de consultas**. Al seleccionar esta opción se muestra una lista desplegable de las funciones definidas por el usuario establecidas en la Vista de Hive. Al seleccionar una función definida por el usuario se agregan instrucciones HiveQL a la consulta para habilitar dicha función.

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

Para obtener más información sobre el uso de las funciones definidas por el usuario con Hive en HDInsight, consulte lo siguiente:

* [Uso de Python con Hive y Pig en HDInsight](hdinsight-python.md)
* [Cómo agregar una UDF de Hive personalizada a HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a id="nextsteps"></a>Pasos siguientes
Para obtener información general acerca de Hive en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras formas en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

