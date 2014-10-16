<properties linkid="manage-services-hdinsight-hbase-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Get started using HBase with Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using HBase with Hadoop in HDInsight. learn how to created HBase tables and query them with Hive." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using HBase with Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="bradsev"></tags>

# Introducción al uso de HBase con Hadoop en HDInsight

HBase es una base de datos NoSQL de baja latencia que permite el procesamiento de transacciones en línea de Big Data. HBase se ofrece como un clúster administrado integrado en el entorno de Azure. Los clústeres están configurados para almacenar datos directamente en el almacenamiento de blobs de Azure, que proporciona una latencia baja y una mayor elasticidad de las opciones de rendimiento o coste. Esto permite que los clientes creen sitios web interactivos que trabajen con grandes conjuntos de datos, creen servicios que almacenen datos del sensor y telemetría desde millones de extremos y analicen estos datos con trabajos de Hadoop.

En este tutorial, aprenderá a crear y consultar tablas de HBase con HDInsight. Se describen los siguientes procedimientos:

-   Aprovisionamiento de un clúster de HBase mediante el Portal de Azure.
-   Habilitación y uso de RDP para acceder al shell de HBase y usar este para crear una tabla de ejemplo de HBase, agregar filas y luego mostrar las filas en la tabla.
-   Creación de una tabla de Hive que se asigna a una tabla de HBase existente y uso de HiveQL para consultar los datos en la tabla de HBase.
-   Uso del SDK de .NET para crear una nueva tabla de HBase y mostrar las tablas de HBase de su cuenta y cómo agregar y recuperar las filas de las tablas.

> [WACOM.NOTE] Actualmente HBase solo está disponible en vista previa para su uso con clústeres de HDInsight 3.0 en HDInsight (basado en Hadoop 2.2.0). Para obtener información de la versión, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight][]

Durante el período de vista previa, se recomienda que la copia de seguridad de la versión original de los datos usados en el clúster de HBase se almacenen fuera de ese clúster. El motivo es que el formato de los archivos de base de datos podría cambiar en las versiones futuras y los formatos actuales de los archivos de datos usados en la versión de vista previa podrían no ser compatibles o actualizables en las sucesivas versiones.

**Requisitos previos:**

Antes de empezar este tutorial, debe contar con lo siguiente:

-   Una suscripción de Azure. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][], [Ofertas para miembros][] o [Evaluación gratuita][].
-   Una cuenta de almacenamiento de Azure. Para obtener instrucciones, consulte [Creación de una cuenta de almacenamiento][].
-   Una copia de Visual Studio.

**Duración aproximada:** 30 minutos

## Apartados de este tutorial

-   [Aprovisionamiento de un clúster de HBase en el Portal de Azure][]
-   [Creación de una tabla de ejemplo de HBase a partir del shell de HBase][]
-   [Uso de Hive para consultar una tabla de HBase][]
-   [Uso de las API de C# para HBase para crear una tabla de HBase y recuperar los datos de la tabla][]
-   [Resumen][]

## <a name="create-hbase-cluster"></a>Aprovisionamiento de un clúster de HBase en el Portal de Azure

En esta sección se describe cómo aprovisionar un clúster de HBase mediante el Portal de Azure.

**Para aprovisionar un clúster de HBase en el Portal de Azure, siga estos pasos:**

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  Haga clic en **HDInsight** a la izquierda para mostrar el estado de los clústeres de su cuenta y luego el icono **+NUEVO** en la parte inferior izquierda.

    ![][]

3.  Haga clic en el icono de HDInsight en la segunda columna de la izquierda y luego en la opción HBase de la siguiente columna. Especifique los valores para NOMBRE DEL CLÚSTER y TAMAÑO DEL CLÚSTER, el nombre de la cuenta de almacenamiento y una contraseña para el nuevo clúster de HBase.

    ![][1]

4.  Haga clic en el icono de verificación en la parte inferior izquierda para crear el clúster de HBase.

## <a name="create-sample-table"></a>Creación de una tabla de ejemplo de HBase a partir del shell de HBase

En esta sección se describe cómo habilitar y usar el Protocolo de escritorio remoto (RDP) para acceder al shell de HBase y usarlo luego para crear una tabla de ejemplo de HBase, agregar filas y mostrar a continuación las filas en la tabla.

Se supone que ha completado el procedimiento descrito en la primera sección y que, por tanto, ha creado correctamente un clúster de HBase.

**Habilitación de la conexión RDP al clúster de HBase**

1.  Para habilitar una Conexión a Escritorio remoto al clúster de HDInsight, seleccione el clúster de HBase que ha creado y haga clic en la pestaña **CONFIGURACIÓN**. Haga clic en el botón **HABILITAR DE FORMA REMOTA** en la parte inferior de la página para habilitar la conexión RDP al clúster.
2.  Proporcione las credenciales y la fecha de caducidad en el asistente **CONFIGURAR ESCRITORIO REMOTO** y haga clic en el círculo marcado en la parte inferior derecha. (La operación puede tardar unos minutos en completarse).
3.  Para conectarse al clúster de HDInsight, haga clic en el botón **CONECTAR** en la parte inferior de la pestaña **CONFIGURACIÓN**.

**Apertura del shell de HBase**

1.  En la sesión de RDP, haga clic en el acceso directo del **símbolo del sistema de Hadoop** situado en el escritorio.

2.  Cambie la carpeta al directorio principal de HBase:

        cd %HBASE_HOME%\bin

3.  Abra del shell de HBase:

        hbase shell

**Creación de una tabla de ejemplo y adición y recuperación de los datos**

1.  Cree una tabla de ejemplo:

        create 'sampletable', 'cf1'

2.  Agregue una fila a la tabla de ejemplo:

        put 'sampletable', 'row1', 'cf1:col1', 'value1'

3.  Muestre las filas en la tabla de ejemplo:

        scan 'sampletable'

## <a name="hive-query"></a>Uso de Hive para consultar una tabla de HBase

Ahora que tiene un clúster de HBase aprovisionado y que ha creado una tabla, puede consultarla mediante Hive. En esta sección se crea una tabla de Hive que se asigna a la tabla de HBase y se usa para consultar los datos en la tabla de HBase.

**Para abrir el panel del clúster, siga estos pasos:**

1.  Inicie sesión en el [Portal de administración de Azure][].
2.  Haga clic en **HDINSIGHT** en el panel izquierdo. Verá una lista de los clústeres creados, junto con el que acaba de crear en la última sección.
3.  Haga clic en el nombre del clúster donde desea ejecutar el trabajo de Hive.
4.  Haga clic en **ADMINISTRAR CLÚSTER** en la parte inferior de la página para abrir el panel del clúster. Se abre una página web en una pestaña de explorador diferente.
5.  Escriba el nombre de usuario y la contraseña de la cuenta de usuario de Hadoop. El nombre de usuario predeterminado es **admin** y la contraseña es la que ha escrito durante el proceso de aprovisionamiento. El panel tiene este aspecto:

    ![][2]

**Para ejecutar una consulta de Hive, siga estos pasos:**

1.  Para crear una tabla de Hive con una asignación a la tabla de HBase, escriba a continuación el script de Hive en la ventana de la consola de Hive y haga clic en el botón **ENVIAR**. Asegúrese de que ha creado la tabla de ejemplo a la que se hace referencia aquí en HBase mediante el shell de HBase antes de ejecutar esta instrucción.

        SET hbase.zookeeper.quorum=zookeepernode0,zookeepernode1,zookeepernode2;  
        CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
        TBLPROPERTIES ('hbase.table.name' = 'sampletable');

2.  Para ejecutar una consulta de Hive sobre los datos de HBase, escriba a continuación el script de HiveQL en la ventana de la consola de Hive y haga clic en el botón **ENVIAR**.

        SET hbase.zookeeper.quorum=zookeepernode0,zookeepernode1,zookeepernode2;
        SET hive.aux.jars.path=file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hive-hbase-handler-0.12.0.2.0.9.0-1677.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-server-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-protocol-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/htrace-core-2.01.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-client-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/guava-12.0.1.jar;
        SELECT count(*) FROM hbasesampletable;

3.  Para recuperar los resultados de la consulta de Hive, haga clic en el vínculo **Ver detalles** de la ventana **Sesión de trabajo** cuando el trabajo termine de ejecutarse.

Nota: el vínculo del shell de HBase cambia la pestaña por el **shell de HBase**.

**Para examinar el archivo de salida, siga estos pasos:**

1.  En el panel del clúster, haga clic en **Archivos** en la parte superior.
2.  Haga clic en **Templeton-Job-Status**.
3.  Haga clic en el número de GUID que tiene la hora de última modificación un poco después que la hora de inicio del trabajo que anotara anteriormente. Tome nota de este GUID. Lo necesitará en la próxima sesión.
4.  El archivo **stdout** tiene los datos que necesita en la próxima sección. Puede hacer clic en **stdout** para descargar una copia del archivo de datos que desea.

## <a name="hbase-powershell"></a>Uso de las API de C# para HBase para crear una tabla de HBase y recuperar los datos de la tabla

Marlin es una capa delgada encima de la API REST, que se ocupa de la interacción con HBase mediante ProtoBuf en C\#. El proyecto Marlin se debe descargar de github y el proyecto compilado para usar el SDK de .NET para HBase.

1.  Siga los pasos de compilación que se describen en la sección de descarga del proyecto Marlin desde la [página del proyecto Marlin][]. Descomprímalo en un directorio local.

2.  Abra el proyecto en Visual Studio. Abra el asistente del Administrador de paquetes NuGet; para ello, vaya a los menús **HERRAMIENTAS** -\> **Administrador de paquetes de la biblioteca** y seleccione **Administrar paquetes NuGet para la solución ...**

    ![][3]

3.  En el cuadro En línea de la parte superior derecha, busque protobuf-net e instale v2.0.0.68. Compule el proyecto Marlin; para ello, haga clic con el botón secundario en el proyecto **Marlin** en el **Explorador de soluciones** y seleccione **Compilar**.

4.  Recupere el archivo marlin.dll compilado resultante y agréguelo al proyecto de C\#.

5.  Cree una nueva instancia de Marlin mediante las credenciales del clúster y recupere la versión del clúster:

        var credentials = ClusterCredentials.Create("https://yourclustername.azurehdinsight.net/", "user", "password");
            var marlin = new Marlin(credentials);
        // retrieve the version as a test
        var version = marlin.GetVersion();
        Console.WriteLine(version);

6.  Para mostrar las tablas en el clúster, puede usar el siguiente código:

        var tables = marlin.ListTables();
        foreach(var tableName in tables.name) 
                Console.WriteLine(tableName);

7.  Para crear una nueva tabla de HBase, use este código:

        var schema = new TableSchema();
        schema.name = "sampletable";
        schema.columns.Add(new ColumnSchema() { name = "cf1" });
        schema.columns.Add(new ColumnSchema() { name = "cf2" });
        marlin.CreateTable(schema);

8.  Para recuperar una fila por su clave, especifique el nombre de la tabla y una clave de fila para recuperar un valor de fila.

        var cells = marlin.GetCells("sampletable", "row1");
        var row = cells.rows[0];
            foreach(var val in row.values) 
            {
               Console.WriteLine(Encoding.UTF8.GetString(val.data));
            }

9.  Para almacenar una nueva fila de datos, puede usar el siguiente código:

        CellSet set = new CellSet();
        CellSet.Row row = new CellSet.Row() { key = BitConverter.GetBytes(1337) };
            var value = new Cell()
                    {
                        column = Encoding.UTF8.GetBytes("cf1:d"),
                        data = Encoding.UTF8.GetBytes("Hello World!")
                    };
            row.values.Add(value);
            set.rows.Add(row);
        marlin.StoreCells("sampletable", set);

## <a name="summary"></a>Resumen

En este tutorial, ha aprendido a aprovisionar un clúster de HBase, a crear tablas y a ver los datos de esas tablas desde el shell de HBase. También ha aprendido a usar Hive para consultar los datos de las tablas de HBasey a usar las API de C# para HBase para crear una tabla de HBase y recuperar los datos de la tabla.

  [Novedades en las versiones de clústeres proporcionadas por HDInsight]: ../hdinsight-component-versioning/
  [Opciones de compra]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Ofertas para miembros]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Evaluación gratuita]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Creación de una cuenta de almacenamiento]: http://azure.microsoft.com/es-es/documentation/articles/storage-create-storage-account/
  [Aprovisionamiento de un clúster de HBase en el Portal de Azure]: #create-hbase-cluster
  [Creación de una tabla de ejemplo de HBase a partir del shell de HBase]: #create-sample-table
  [Uso de Hive para consultar una tabla de HBase]: #hive-query
  [Uso de las API de C# para HBase para crear una tabla de HBase y recuperar los datos de la tabla]: #hbase-powershell
  [Resumen]: #summary
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  []: http://i.imgur.com/PmGynKZ.jpg
  [1]: http://i.imgur.com/ecxbB9K.jpg
  [2]: http://i.imgur.com/tMwXlj9.jpg
  [página del proyecto Marlin]: https://github.com/thomasjungblut/marlin
  [3]: http://i.imgur.com/hUNoJDJ.jpg
