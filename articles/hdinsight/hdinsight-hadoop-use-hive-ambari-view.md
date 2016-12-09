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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4cdc1f0a8958edd23f8df02c4d16d3f60fe648bd


---
# <a name="use-the-hive-view-with-hadoop-in-hdinsight"></a>Uso de Vista de Hive con Hadoop en HDInsight
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Ambari es una utilidad de administración y supervisión proporcionada con los clústeres de HDInsight basados en Linux. Una de las características que se proporcionan a través de Ambari es una interfaz de usuario web que puede usarse para ejecutar consultas de Hive. Se trata de la **vista de Hive**, que forma parte de las Vistas de Ambari proporcionadas con el clúster de HDInsight.

> [!NOTE]
> Ambari cuenta con muchas funcionalidades que no se tratan en este documento. Para más información, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).
> 
> 

## <a name="prerequisites"></a>Requisitos previos
* Un clúster de HDInsight basado en Linux Para información sobre la creación de un clúster, consulte [Introducción a HDInsight en Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

## <a name="open-the-hive-view"></a>Abra la Vista de Hive.
Para ver Vistas Ambari en Azure Portal, seleccione el clúster de HDInsight y **Vistas de Ambari** en la sección **Vínculos rápidos**.

![Sección Vínculos rápidos](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

También puede ir directamente a Ambari si usa la dirección https://NOMBRE_DE_CLUSTER.azurehdinsight.net en un explorador web (donde **NOMBRE_DE_CLUSTER** es el nombre del clúster de HDInsight) y después selecciona el conjunto de cuadrados en el menú de página (junto al vínculo y botón **Admin** de la izquierda de la página) para mostrar las vistas disponibles. Seleccione la **vista de Hive**.

![Selección de vistas de Ambari](./media/hdinsight-hadoop-use-hive-ambari-view/selecthiveview.png).

> [!NOTE]
> Al acceder a Ambari, se le pedirá autenticarse en el sitio. Escriba el nombre de la cuenta del administrador (de manera predeterminada `admin`) y la contraseña que usó al crear el clúster.
> 
> 

Debería ver una página similar a la siguiente:

![Imagen de la página de Vista de Hive, que contiene una sección de editor de consultas](./media/hdinsight-hadoop-use-hive-ambari-view/hiveview.png)

## <a name="view-tables"></a>Visualización de tablas
En la sección **Database Explorer** (Explorador de base de datos) de la página, seleccione la entrada **default** (Predeterminada) en la pestaña **Databases** (Bases de datos). Esto mostrará una lista de tablas en la base de datos predeterminada. Para un nuevo clúster de HDInsight, solo debe aparecer una tabla, **hivesampletable**.

![explorador de base de datos con la base de datos predeterminada expandida](./media/hdinsight-hadoop-use-hive-ambari-view/databaseexplorer.png)

A medida que se agregan nuevas tablas a través de los pasos descritos en este documento, puede usar el icono de actualización en la esquina superior derecha del explorador de base de datos para actualizar la lista de tablas disponibles.

## <a name="a-namehivequeryaquery-editor"></a><a name="hivequery"></a>Editor de consultas
Use los pasos siguientes desde la Vista de Hive para ejecutar una consulta de Hive en los datos que se incluyen con el clúster.

1. En la sección **Editor de consultas** de la página, pegue las siguientes instrucciones de HiveQL en la hoja de cálculo:
   
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
   
    Estas instrucciones realizan las acciones siguientes:
   
   * **DROP TABLE** : elimina la tabla y el archivo de datos si la tabla ya existe.
   * **CREATE EXTERNAL TABLE** : crea una tabla "externa" nueva en Hive. Las tablas externas solo almacenan la definición de tabla en Hive; los datos quedan en la ubicación original.
   * **ROW FORMAT** : indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.
   * **STORED AS TEXTFILE LOCATION** : indica a Hive dónde se almacenan los datos (el directorio example/data) y que se almacenan como texto.
   * **SELECT** : selecciona un número de todas las filas donde la columna t4 contiene el valor [ERROR].
     
     > [!NOTE]
     > Las tablas externas se deben usar cuando espera que un origen externo, como por ejemplo un proceso de carga de datos automático, u otra operación MapReduce, actualice los datos subyacentes, pero siempre desea que las consultas de Hive usen los datos más recientes. La eliminación de una tabla externa *no* elimina los datos, solamente la definición de tabla.
     > 
     > 
2. Use el botón **Ejecutar**, situado en la parte inferior del Editor de consultas, para iniciar la consulta. Debe volverse de color naranja y el texto cambiará a **Detener ejecución**. Debe aparecer la sección **Resultados del proceso de consulta** bajo el Editor de consultas y mostrar información sobre el trabajo.
   
   > [!IMPORTANT]
   > Es posible que algunos exploradores no puedan actualizar correctamente la información de registro o los resultados. Si se ejecuta un trabajo y parece ejecutarse eternamente sin actualizar el registro o sin devolver resultados, pruebe a usar en su lugar Mozilla FireFox o Google Chrome.
   > 
   > 
3. Cuando haya finalizado la consulta, la sección **Resultados del proceso de consulta** mostrará los resultados de la operación. El botón **Detener ejecución** volverá a cambiar al botón **Ejecutar** de color verde. La pestaña **Resultados** debe contener la información siguiente:
   
        sev       cnt
        [ERROR]   3
   
    La pestaña **Registros** puede usarse para ver la información de registro creada por el trabajo. Puede usarlo para solucionar problemas si se produjera alguno con una consulta.
   
   > [!TIP]
   > Observe el elemento desplegable **Guardar resultados** en la esquina superior izquierda de la sección **Resultados del proceso de consulta**; úselo para descargar los resultados o guardarlos en el almacenamiento de HDInsight como archivo CSV.
   > 
   > 
4. Seleccione las cuatro primeras líneas de esta consulta y después elija **Ejecutar**. Observe que no hay ningún resultado cuando finaliza el trabajo. Esto se debe a que, al usar el botón **Ejecutar** cuando parte de la consulta está seleccionada, solo se ejecutarán las instrucciones seleccionadas. En este caso, la selección no incluyó la instrucción final que recupera filas de la tabla. Si selecciona solo esa línea y usa **Ejecutar**, debería ver los resultados esperados.
5. Use el botón **Nueva hoja de cálculo** situado en la parte inferior del **Editor de consultas** para crear una hoja de cálculo. En la hoja de cálculo nueva, escriba las siguientes instrucciones de HiveQL:
   
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
   
    Estas instrucciones realizan las acciones siguientes:
   
   * **CREATE TABLE IF NOT EXISTS** : crea una tabla, si todavía no existe. Dado que la palabra clave **EXTERNAL** no es usa, se trata de una tabla interna, la que se almacena en el almacenamiento de datos de Hive y es administrada completamente por Hive. A diferencia de las tablas externas, la eliminación de una tabla interna también eliminará los datos subyacentes.
   * **STORED AS ORC** : almacena los datos en el formato Optimized Row Columnar (ORC). Se trata de un formato altamente optimizado y eficiente para almacenar datos de Hive.
   * **INSERT OVERWRITE ... SELECT**: selecciona filas de la tabla **log4jLogs** que contienen [ERROR] e inserta los datos en la tabla **errorLogs**.
     
     Use el botón **Ejecutar** para ejecutar esta consulta. La pestaña **Resultados** no contendrá ninguna información ya que esta consulta no devuelve filas, pero el estado debe aparecer como **CORRECTO**.

### <a name="hive-settings"></a>Configuración de Hive
Seleccione el icono de **Configuración** a la derecha del editor.

![iconos](./media/hdinsight-hadoop-use-hive-ambari-view/settings.png)

La opción de configuración puede usarse para cambiar varios ajustes de Hive, como cambiar el motor de ejecución de Hive de Tez (predeterminado), a MapReduce.

### <a name="visual-explain"></a>Explicación visual
Seleccione el icono de **Explicación visual** a la derecha del editor.

![iconos](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplainicon.png)

Se trata de la vista **Explicación visual** de la consulta, que puede ser útil para comprender el flujo de las consultas complejas. Puede ver un equivalente textual de esta vista con el botón **Explicar** en el Editor de consultas.

![Imagen de Explicación visual](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

### <a name="tez"></a>Tez
Seleccione el icono de **Tez** a la derecha del editor.

![iconos](./media/hdinsight-hadoop-use-hive-ambari-view/tez.png)

Se mostrará el grafo acíclico dirigido (DAG) usado por Tez para esta consulta, si está disponible. Si desea ver el DAG para las consultas que se ejecutaron en el pasado, o depurar el proceso Tez, use la [vista de Tez](hdinsight-debug-ambari-tez-view.md) en su lugar.

### <a name="notifications"></a>Notificaciones
Seleccione el icono de **Notificaciones** a la derecha del editor.

![iconos](./media/hdinsight-hadoop-use-hive-ambari-view/notifications.png)

Las notificaciones son mensajes que se generan al ejecutar las consultas. Por ejemplo, recibirá una notificación cuando se envía una consulta, o cuando se produce un error.

## <a name="saved-queries"></a>Consultas guardadas
1. Desde el Editor de consultas, cree una nueva hoja de cálculo y escriba la siguiente consulta:
   
        SELECT * from errorLogs;
   
    Ejecute la consulta para comprobar que funciona. El resultado será como sigue:
   
        errorlogs.t1     errorlogs.t2     errorlogs.t3     errorlogs.t4     errorlogs.t5     errorlogs.t6     errorlogs.t7
        2012-02-03     18:35:34     SampleClass0     [ERROR]     incorrect     id     
        2012-02-03     18:55:54     SampleClass1     [ERROR]     incorrect     id     
        2012-02-03     19:25:27     SampleClass4     [ERROR]     incorrect     id
2. Use el botón **Guardar como** en la parte inferior del editor. Asigne a esta consulta el nombre de **Errorlogs** y seleccione **Aceptar**. Observe que el nombre de la hoja de cálculo cambia a **Errorlogs**.
3. Seleccione la pestaña **Consultas guardadas** en la parte superior de la página de Vista de Hive. Observe que **Errorlogs** aparece ahora como consulta guardada. Permanecerá en esta lista hasta que lo quite. Si selecciona el nombre, se abrirá la consulta en el Editor de consultas.

## <a name="query-history"></a>Historial de consulta
El botón **Historial** en la parte superior de la Vista de Hive le permite ver las consultas que haya ejecutado previamente. Úselo ahora y seleccione algunas de las consultas que ejecutó previamente. Cuando selecciona una consulta, se abre en el Editor de consultas.

## <a name="user-defined-functions-udf"></a>Funciones definidas por el usuario (UDF)
Hive también puede extenderse a través de **funciones definidas por el usuario (UDF)**. Una UDF le permite implementar la funcionalidad o la lógica que no se modela con facilidad en HiveQL.

Aunque puede agregar una función definida por el usuario como parte de las instrucciones de HiveQL en la consulta, la pestaña UDF en la parte superior de la Vista de Hive permite declarar y guardar un conjunto de funciones definidas por el usuario que se pueden usar con el **Editor de consultas**.

Una vez que haya agregado una función definida por el usuario a la Vista de Hive, aparecerá un botón **Insertar UDF** en la parte inferior del **Editor de consultas**. Seleccionar esta opción, mostrará una lista desplegable de las funciones definidas por el usuario establecidas en la Vista de Hive. La selección de una función definida por el usuario agregará instrucciones HiveQL a su consulta para habilitar esta función.

Por ejemplo, si definió una función definida por el usuario con las siguientes propiedades:

* Nombre de recurso: myudfs
* Ruta de acceso de recurso: wasbs:///myudfs.jar
* Nombre de la UDF: myawesomeudf
* Nombre de la clase UDF: com.myudfs.Awesome

Si usa el botón **Insertar UDF**, aparecerá una entrada llamada **myudfs**, con otra lista desplegable para cada función definida por el usuario establecida para ese recurso. En este caso, **myawesomeudf**. Al seleccionar esta entrada se agrega lo siguiente al principio de la consulta:

    add jar wasbs:///myudfs.jar;

    create temporary function myawesomeudf as 'com.myudfs.Awesome';

A continuación, puede usar la función definida por el usuario en la consulta. Por ejemplo: `SELECT myawesomeudf(name) FROM people;`.

Para obtener más información sobre el uso de las funciones definidas por el usuario con Hive en HDInsight, consulte lo siguiente:

* [Uso de Python con Hive y Pig en HDInsight](hdinsight-python.md)
* [Cómo agregar una UDF de Hive personalizada a HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Pasos siguientes
Para obtener información general acerca de Hive en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras formas en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)




<!--HONumber=Nov16_HO3-->


