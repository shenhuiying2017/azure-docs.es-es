---
title: "Creación de tablas de Hive y carga de datos desde Azure Blob Storage | Microsoft Docs"
description: "Crear tablas de subárbol y cargar datos de blob en tablas de subárbol"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: cff9280d-18ce-4b66-a54f-19f358d1ad90
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
ms.openlocfilehash: e879ab2874cb3298de4b0929b286482d824e8309
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Creación de tablas de Hive y carga de datos desde Azure Blob Storage
En este tema se presentan consultas genéricas de Hive que crean tablas de Hive y cargan datos desde Azure Blob Storage. También se ofrecen algunas instrucciones acerca de las particiones de tablas de subárbol y de cómo utilizar el formato ORC para mejorar el rendimiento de las consultas.

Este **menú** siguiente redirige a temas en los que se describe cómo introducir datos en entornos de destino en que se pueden almacenar y procesar datos durante el proceso de ciencia de datos en equipos (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que ha:

* Creado una cuenta de almacenamiento de Azure. Para obtener instrucciones, vea [Acerca de las cuentas de almacenamiento de Azure](../../storage/common/storage-create-storage-account.md).
* Aprovisionado un clúster de Hadoop personalizado con el servicio HDInsight.  Si necesita instrucciones, consulte [Personalización de clústeres de Hadoop de HDInsight de Azure para análisis avanzado](customize-hadoop-cluster.md).
* Habilitado el acceso remoto para el clúster, iniciado sesión y abierto la consola de la línea de comandos de Hadoop. Si necesita instrucciones, consulte [Acceso al nodo principal del clúster Hadoop](customize-hadoop-cluster.md#headnode).

## <a name="upload-data-to-azure-blob-storage"></a>Carga de datos en el almacenamiento de blobs de Azure
Si creó una máquina virtual de Azure siguiendo las instrucciones dadas en [Configuración de una máquina virtual de Azure para análisis avanzado](../data-science-virtual-machine/setup-virtual-machine.md), este archivo de script debió descargarse en el directorio *C:\\Users\\\<nombre de usuario\>\\Documents\\Data Science Scripts* de la máquina virtual. Estas consultas de subárbol solo requieren que conecte sus propios esquemas de datos y la configuración de almacenamiento de blobs de Azure en los campos adecuados para estar preparado para su envío.

Se supone que los datos de las tablas de Hive están en un formato tabular **sin comprimir** y que se han cargado los datos en el contenedor predeterminado (o en otro adicional) de la cuenta de almacenamiento que usa el clúster Hadoop.

Si desea practicar con el grupo **NYC Taxi Trip Data**, necesita hacer lo siguiente:

* **Descargue** los 24 archivos de [NYC Taxi Trip Data](http://www.andresmh.com/nyctaxitrips) (12 archivos de carreras y 12 archivos de tarifas).
* **Descomprima** todos los archivos en archivos .csv.
* **cargue** los archivos en el contenedor predeterminado (o el contenedor pertinente) de la cuenta de Azure Storage que se creó mediante el procedimiento descrito en el tema [Personalización de los clústeres de Hadoop de HDInsight de Azure para el proceso de ciencia de datos en equipos](customize-hadoop-cluster.md) . En esta [página](hive-walkthrough.md#upload)encontrará el proceso de cargar los archivos .csv en el contenedor predeterminado de la cuenta de almacenamiento.

## <a name="submit"></a>Envío de consultas de Hive
Las consultas de subárbol se pueden enviar mediante:

1. [Enviar consultas de Hive a través de línea de comandos de Hadoop en el nodo principal del clúster de Hadoop](#headnode)
2. [Enviar consultas de Hive con el Editor de Hive](#hive-editor)
3. [Enviar consultas de Hive con los comandos de Azure PowerShell](#ps)

Las consultas de Hive son similares a SQL. Los usuarios familiarizados con SQL pueden encontrar la [hoja de referencia rápida Hive para usuarios de SQL](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) de gran utilidad.

Al enviar una consulta de subárbol, también puede controlar el destino del resultado de las consultas de subárbol, ya sea en la pantalla o en un archivo local del nodo principal o en un blob de Azure.

### <a name="headnode"></a> 1. Enviar consultas de Hive a través de línea de comandos de Hadoop en el nodo principal del clúster de Hadoop
Si la consulta es compleja, enviar consultas de Hive directamente en el nodo principal del clúster de Hadoop normalmente permite obtener respuestas más rápidas que si se efectúa el envío mediante un editor de Hive o scripts de Azure PowerShell.

Inicie sesión en el nodo principal del clúster de Hadoop, abra la línea de comandos de Hadoop en el escritorio del nodo principal y escriba el comando `cd %hive_home%\bin`.

Los usuarios disponen de tres maneras de enviar consultas de Hive en la línea de comandos de Hadoop:

* Directamente
* usando archivos .hql
* Con la consola de comandos de Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Envíe consultas de subárbol directamente en la línea de comandos de Hadoop.
Los usuarios pueden ejecutar el comando como `hive -e "<your hive query>;` para enviar consultas de Hive sencillas directamente en la línea de comandos de Hadoop. Este es un ejemplo, donde el cuadro rojo muestra el comando que envía la consulta de subárbol y el cuadro verde muestra el resultado de la consulta de subárbol.

![Creación del espacio de trabajo](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Enviar consultas de subárbol en archivos .hql
Cuando la consulta de subárbol es más complicada y tiene varias líneas, no resulta práctico modificar consultas en la línea de comandos o la consola de comandos de subárbol. Una alternativa es usar un editor de texto en el nodo principal del clúster de Hadoop y guardar las consultas de subárbol en un archivo .hql de un directorio local del nodo principal. A continuación, la consulta de Hive del archivo .hql puede enviarse mediante el argumento `-f` del modo indicado a continuación:

    hive -f "<path to the .hql file>"

![Creación del espacio de trabajo](./media/move-hive-tables/run-hive-queries-3.png)

**Suprimir la impresión de pantalla del estado de progreso de las consultas de subárbol**

De forma predeterminada, después de enviar la consulta de Hive en la línea de comandos de Hadoop, el progreso del trabajo de asignación/reducción se imprimirá en pantalla. Para suprimir la impresión de la pantalla del progreso del trabajo de asignación/reducción, puede usar un argumento `-S` ("S" en mayúsculas) en la línea de comandos del modo indicado a continuación:

    hive -S -f "<path to the .hql file>"
.    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Envíe consultas de subárbol en la consola de comandos de subárbol.
Los usuarios también pueden especificar en primer lugar la consola de comandos de Hive al ejecutar el comando `hive` en la línea de comandos de Hadoop y, a continuación, enviar consultas de Hive en la consola de comandos de Hive. Aquí tiene un ejemplo. En este ejemplo, los dos cuadros de color rojo resaltan los comandos que se utilizan para escribir en la consola de comandos de subárbol y la consulta de subárbol enviada en la consola de comandos de subárbol, respectivamente. El cuadro verde resalta el resultado de la consulta de subárbol.

![Creación del espacio de trabajo](./media/move-hive-tables/run-hive-queries-2.png)

Los ejemplos anteriores generan directamente los resultados de la consulta de subárbol en pantalla. Los usuarios también pueden escribir la salida en un archivo local del nodo principal o en un blob de Azure. A continuación, los usuarios pueden utilizar otras herramientas para analizar más el resultado de las consultas de Hive.

**Genere los resultados de consulta de subárbol en un archivo local.**
Para generar los resultados de consultas de Hive en un directorio local del nodo principal, los usuarios tienen que enviar la consulta de Hive de la línea de comandos de Hadoop de la siguiente manera:

    hive -e "<hive query>" > <local path in the head node>

En el ejemplo siguiente, el resultado de la consulta de Hive se escribe en un archivo `hivequeryoutput.txt` del directorio `C:\apps\temp`.

![Creación del espacio de trabajo](./media/move-hive-tables/output-hive-results-1.png)

**Generar los resultados de consulta de subárbol en un blob de Azure**

Los usuarios también pueden generar resultados de consulta de Hive en un blob de Azure, dentro del contenedor predeterminado del clúster de Hadoop. La consulta de Hive para esto es la siguiente:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

En el ejemplo siguiente, el resultado de la consulta de Hive se escribe en un directorio de blob `queryoutputdir` dentro del contenedor predeterminado del clúster de Hadoop. En este caso, solo necesita proporcionar el nombre del directorio, sin el nombre del blob. Se produce un error si proporciona los nombres de directorio y de blob, como `wasb:///queryoutputdir/queryoutput.txt`.

![Creación del espacio de trabajo](./media/move-hive-tables/output-hive-results-2.png)

Si abre el contenedor predeterminado del clúster de Hadoop mediante herramientas como el Explorador de Azure Storage, verá el resultado de la consulta de Hive como se muestra en la ilustración siguiente. Puede aplicar el filtro (resaltado mediante un cuadro rojo) para recuperar solo el blob con letras especificadas en los nombres.

![Creación del espacio de trabajo](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2. Enviar consultas de Hive con el Editor de Hive
También puede utilizar la consola de consultas (Editor de Hive) escribiendo una dirección URL del formulario *https://&#60;Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor* en un explorador web. Debe haber iniciado sesión para ver esta consola; así pues, tiene que escribir sus credenciales de Hadoop aquí.

### <a name="ps"></a> 3. Enviar consultas de Hive con los comandos de Azure PowerShell
Los usuarios pueden también usar PowerShell para enviar consultas de Hive. Para obtener instrucciones, consulte [Envío de trabajos de Hive mediante PowerShell](../../hdinsight/hdinsight-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Creación de tablas y base de datos de Hive
Las consultas de Hive se comparten en el [repositorio de GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) y se pueden descargar desde allí.

Esta es la consulta de subárbol que crea una tabla de subárbol.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Estas son las descripciones de los campos que los usuarios necesitan para conectar y otras configuraciones:

* **&amp;#60;nombre de base de datos&gt;**: nombre de la base de datos que los usuarios desean crear. Si los usuarios solo desean usar la base de datos predeterminada, se puede omitir la consulta *crear base de datos...*
* **&amp;#60;nombre de tabla&gt;**: nombre de la tabla que los usuarios quieren crear en la base de datos especificada. Si los usuarios desean usar la base de datos predeterminada, puede hacer referencia directamente a la tabla *&#60;nombre de tabla>* sin &#60;nombre de base de datos>.
* **&amp;#60;separador de campos&gt;**: separador que delimita los campos del archivo de datos que se cargará en la tabla de Hive.
* **&amp;#60;line separator&gt;**: separador que delimita las líneas del archivo de datos.
* **&amp;#60;storage location&gt;**: la ubicación de Azure Storage para guardar los datos de tablas de Hive. Si los usuarios no especifican *LOCATION &#60;ubicación de almacenamiento>*, la base de datos y las tablas se almacenan de forma predeterminada en el directorio *hive/warehouse/* del contenedor predeterminado del clúster Hive. Si un usuario desea especificar la ubicación de almacenamiento, esta debe estar dentro del contenedor predeterminado para la base de datos y las tablas. A esta ubicación tiene que hacerse referencia como ubicación relativa al contenedor predeterminado del clúster con el formato *'wasb:///&#60;directory 1>/'* o *'wasb:///&#60;directory 1>/&#60;directory 2>/'*, etc. Después de ejecutar la consulta, se crean los directorios relativos dentro del contenedor predeterminado.
* **TBLPROPERTIES("skip.header.line.count"="1")**: si el archivo de datos tiene una línea de encabezado, los usuarios deben agregar esta propiedad **al final** de la consulta *create table*. De lo contrario, se carga la línea de encabezado como registro en la tabla. Si el archivo de datos no tiene una línea de encabezado, se puede omitir esta configuración en la consulta.

## <a name="load-data"></a>Carga de datos en tablas de Hive
Esta es la consulta de subárbol que carga datos en una tabla de subárbol.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **&amp;#60;ruta de acceso a datos de blob&gt;**: si el archivo de blob que se va a cargar en la tabla de Hive se encuentra en el contenedor predeterminado del clúster Hadoop de HDInsight, *&amp;#60;ruta de acceso a datos de blob&gt;* debe tener el formato "*wasb:///&amp;#60;directorio de este contenedor&gt;/&amp;#60;nombre del archivo de blob&gt;"*. El archivo blob también puede estar en un contenedor adicional del clúster de Hadoop de HDInsight. En este caso, *&#60;ruta de acceso a datos de blob>* debería tener el formato *"wasb://&#60;nombre del contenedor>@&#60;nombre de cuenta de almacenamiento>.blob.core.windows.net/&#60;nombre de archivo de blob>"*.

  > [!NOTE]
  > Los datos blob que se van a cargar en la tabla de subárbol tienen que estar en el contenedor adicional o predeterminado de la cuenta de almacenamiento para el clúster de Hadoop. De lo contrario, la consulta *LOAD DATA* genera un error indicando que no puede obtener acceso a los datos.
  >
  >

## <a name="partition-orc"></a>Temas avanzados: tabla con particiones y datos de Hive de almacén en formato ORC
Si los datos son grandes, crear particiones de la tabla será beneficioso para las consultas que solo necesitan examinar algunas particiones de la tabla. Por ejemplo, es razonable crear particiones de los datos de registro de un sitio web por fechas.

Además de crear particiones de tablas de subárbol, también es beneficioso almacenar los datos de subárbol en el formato ORC. Para obtener más información acerca de la aplicación de formato ORC, consulte <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">El uso de archivos ORC mejora el rendimiento cuando Hive está leyendo, escribiendo y procesando datos</a>.

### <a name="partitioned-table"></a>Tabla con particiones
Esta es la consulta de subárbol que crea una tabla con particiones y carga datos en ella.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Al consultar tablas con particiones, se recomienda agregar la condición de partición al **comienzo** de la cláusula `where`, ya que esto mejora la eficacia de la búsqueda de manera significativa.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Almacenamiento de datos de Hive en formato ORC
Los usuarios no pueden cargar datos directamente desde Blob Storage en tablas de Hive que se almacenan en el formato ORC. Estos son los pasos que los usuarios deben seguir para cargar datos desde blobs de Azure a tablas de Hive almacenadas en formato ORC.

Cree una tabla externa **STORED AS TEXTFILE** y cargue datos del almacenamiento de blobs en la tabla.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Cree una tabla interna con el mismo esquema que la tabla externa del paso 1, con el mismo delimitador de campo, y almacene los datos de subárbol en el formato ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Seleccionar datos desde la tabla externa del paso 1 e insertarlos en la tabla de ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Si la tabla TEXTFILE *&amp;#60;nombre de base de datos&gt;.&amp;#60;nombre de la tabla de archivo de texto externo&gt;`SELECT * FROM <database name>.<external textfile table name>` tiene particiones, en el PASO 3, el comando* selecciona la variable de partición como un campo en el conjunto de datos devuelto. Si se inserta en *&#60;nombre de base de datos>.&#60;nombre de la tabla ORC>* se producirá un error, ya que *&#60;nombre de base de datos>.&#60;nombre de la tabla ORC>* no tiene la variable de partición como un campo en el esquema de tabla. En este caso, los usuarios deben seleccionar específicamente los campos que se van a insertar en *&#60;nombre de base de datos>.&#60;nombre de la tabla ORC>* de la manera siguiente:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Es seguro quitar *&#60;nombre de la tabla de archivo de texto externo>* cuando use la consulta siguiente una vez insertados todos los datos en *&#60;nombre de base de datos>.&#60;nombre de la tabla ORC>*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Después de seguir este procedimiento, debe tener una tabla con datos en el formato ORC lista para su uso.  
