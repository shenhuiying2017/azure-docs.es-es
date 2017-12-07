---
title: "Uso de la acción de script para instalar Solr en HDInsight basado en Linux (Azure) | Microsoft Docs"
description: "Aprenda a instalar Solr en clústeres de Hadoop para HDInsight basados en Linux mediante acciones de script."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cc93ed5c-a358-456a-91a4-f179185c0e98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: larryfr
ms.openlocfilehash: edee4fbb37744a8ef93c6a0b7e60b0790d1af9c4
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Instalación y uso de Solr en clústeres de Hadoop de HDInsight

Aprenda a instalar Solr en Azure HDInsight con acción de script. Solr es una eficaz plataforma de búsqueda eficaz y proporciona capacidades de búsqueda de nivel empresarial en datos administrados por Hadoop.

> [!IMPORTANT]
    > Los pasos descritos en este documento requieren un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!IMPORTANT]
> El script de ejemplo usado en este documento instala Solr 4.9 con una configuración concreta. Si desea configurar el clúster de Solr con distintas colecciones, particiones, esquemas o réplicas, entre otras, debe modificar el script y los archivos binarios de Solr.

## <a name="whatis"></a>¿Qué es Solr?

[Apache Solr](http://lucene.apache.org/solr/features.html) es una plataforma de búsqueda empresarial que permite una eficaz búsqueda de texto completo en los datos. Si bien Hadoop permite almacenar y administrar grandes cantidades de datos, Apache Solr proporciona las capacidades de búsqueda para recuperar rápidamente los datos.

> [!WARNING]
> Los componentes proporcionados con el clúster de HDInsight son totalmente compatibles con Microsoft.
>
> Los componentes personalizados reciben soporte técnico comercialmente razonable para ayudarlo a solucionar el problema. El soporte técnico de Microsoft puede no ser capaz de resolver problemas con componentes personalizados. Debe ponerse en contacto con las comunidades de código abierto para obtener ayuda. Por ejemplo, hay diversos sitios de la comunidad que se pueden usar, como el [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Los proyectos de Apache también tienen sitios de proyecto en [http://apache.org](http://apache.org) (por ejemplo, [Hadoop](http://hadoop.apache.org/)).

## <a name="what-the-script-does"></a>Funcionamiento del script

Este script realiza los siguientes cambios en el clúster de HDInsight:

* Instala Solr 4.9 en `/usr/hdp/current/solr`
* Crea un usuario, **solruser**, que se usa para ejecutar el servicio Solr
* Establece **solruser** como propietario de `/usr/hdp/current/solr`
* Agrega una configuración [Upstart](http://upstart.ubuntu.com/) que inicia Solr automáticamente.

## <a name="install"></a>Instalación de Solr mediante acciones de script

En la siguiente ubicación se encuentra disponible un script de ejemplo para instalar Solr en un clúster de HDInsight:

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Para crear un clúster que tenga Solr instalado, siga los pasos del documento [Creación de clústeres de HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md). Durante el proceso de creación, siga estos pasos para instalar Solr:

1. En la sección __Resumen del clúster__, seleccione__Configuración avanzada__ y, a continuación, __Acciones de script__. Use la siguiente información para rellenar el cuestionario:

   * **NOMBRE**: escriba un nombre descriptivo para la acción de script.
   * **URI DE SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **PRINCIPAL**: active esta opción.
   * **TRABAJADOR**: active esta opción.
   * **ZOOKEEPER**: active esta opción para instalar en el nodo Zookeeper.
   * **PARÁMETROS**: deje este campo en blanco.

2. En la parte inferior de la sección **Acciones de script**, use el botón **Seleccionar** para guardar la configuración. Por último, use el botón **Siguiente** para regresar a __Resumen del clúster__.

3. En la página __Resumen del clúster__, seleccione __Crear__ para crear el clúster.

## <a name="usesolr"></a>Cómo usar Solr en HDInsight

> [!IMPORTANT]
> Los pasos descritos en esta sección muestran la funcionalidad básica de Solr. Para obtener más información sobre el uso de Solr, consulte el sitio de [Solr Apache](http://lucene.apache.org/solr/).

### <a name="index-data"></a>Datos de índice

Use los pasos siguientes para agregar datos de ejemplo a Solr y luego, consúltelo:

1. Conéctese al clúster de HDInsight con SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

     > [!IMPORTANT]
     > Unos pasos más adelante en este documento, use un túnel SSL para conectarse a la interfaz de usuario web Solr. Para poder seguir estos pasos, tiene que establecer un túnel SSL y, después, configurar el explorador para que lo utilice.
     >
     > Para obtener más información, vea el documento [Uso de un túnel SSH con HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Use los siguientes comandos para tener los datos de ejemplo del índice Solr:

    ```bash
    cd /usr/hdp/current/solr/example/exampledocs
    java -jar post.jar solr.xml monitor.xml
    ```

    Se devuelve el siguiente resultado a la consola:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    La utilidad `post.jar` agrega los documentos **solr.xml** y **monitor.xml** al índice.
  
3. Use el comando siguiente para consultar a la API de REST de Solr:

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    Este comando busca en **collection1** los documentos que coinciden con **\*:\*** (codificado como \*%3A\* en la cadena de consulta). El documento JSON siguiente es un ejemplo de la respuesta:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="using-the-solr-dashboard"></a>Mediante el panel de Solr

El panel de Solr es una interfaz de usuario web que le permite trabajar con Solr mediante el explorador web. El panel de Solr no se expone directamente a Internet desde su clúster de HDInsight. Puede usar un túnel SSH para acceder a él. Para obtener más información sobre el uso del túnel SSH, vea el documento [Uso de un túnel SSH con HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

Una vez establecido un túnel SSH, siga estos pasos para usar el panel de Solr:

1. Determine el nombre del host del nodo principal primario:

   1. Use SSH para conectarse al nodo principal del clúster. Por ejemplo: `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

       Para obtener más información sobre cómo usar SSH, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

   2. Use el comando siguiente para obtener el nombre de host completo:

        ```bash
        hostname -f
        ```

        Este comando devuelve un valor similar al nombre del host siguiente:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        Guarde el valor devuelto porque lo usará más adelante.

2. En el explorador, conéctese a **http://HOSTNAME:8983/solr/#/**, donde **HOSTNAME** es el nombre que determinó en los pasos anteriores.

    La solicitud se enruta a través del túnel SSH a la interfaz de usuario de web de Solr en el clúster. La página que se muestra es similar a la imagen siguiente:

    ![Imagen del panel de Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. En el panel izquierdo, use la lista desplegable **Core Selector** (Selector principal) para seleccionar **collection1**. Deberían aparecer varias entradas debajo de **collection1**.

4. Entre las entradas en **collection1**, seleccione **Query** (Consulta). Use los siguientes valores para rellenar la página de búsqueda:

   * En el cuadro de texto **q**, escriba **\*:**\*. Esta consulta devuelve todos los documentos indizados en Solr. Si desea buscar una cadena específica dentro de los documentos, puede especificar esa cadena aquí.
   * En el cuadro de texto **wt** , seleccione el formato de salida. El valor predeterminado es **json**.

     Por último, seleccione el botón **Ejecutar consulta** que aparece en la parte inferior de la página de búsqueda.

     ![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     El resultado devuelve los dos documentos que agregó anteriormente al índice. El resultado es similar al siguiente documento JSON:

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }

### <a name="starting-and-stopping-solr"></a>Inicio y detención de Solr

Si necesita detener o iniciar Solr manualmente, use los siguientes comandos:

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>Copia de seguridad de los datos indizados

Use los pasos siguientes para realizar copias de seguridad de datos de Solr en el almacenamiento predeterminado del clúster:

1. Conéctese al clúster con SSH y, luego, use el comando siguiente para obtener el nombre del host para el nodo principal:

    ```bash
    hostname -f
    ```

2. Use el siguiente comando para crear una instantánea de los datos indexados. Reemplace **HOSTNAME** por el nombre que devolvió el comando anterior:

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    La respuesta es similar al siguiente XML:

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. Cambie los directorios a `/usr/hdp/current/solr/example/solr`. Hay un subdirectorio aquí para cada colección. Cada directorio de la colección contiene un directorio `data` que contiene a su vez la instantánea de la colección.

4. Para crear un archivo comprimido de la carpeta de instantáneas, use el comando siguiente:

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    Sustituya los valores `snapshot.20150806185338855` con el nombre de la instantánea de su colección.

    Este comando crea un archivo denominado **snapshot.20150806185338855.tgz**, que incluye el contenido del directorio **snapshot.20150806185338855**.

5. A continuación, puede almacenar el archivo en el almacenamiento principal del clúster con el comando siguiente:

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

Para más información sobre cómo trabajar con copia de seguridad y restauraciones de Solr, vea [https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups).

## <a name="next-steps"></a>Pasos siguientes

* [Instalación de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use la personalización del clúster para instalar Giraph en clústeres de Hadoop para HDInsight. Giraph permite realizar un procesamiento gráfico mediante Hadoop, y se puede usar con HDInsight de Azure.

* [Instalación de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md). Use la personalización del clúster para instalar Hue en clústeres de Hadoop para HDInsight. Hue es un conjunto de aplicaciones web que usan para interactuar con un clúster de Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
