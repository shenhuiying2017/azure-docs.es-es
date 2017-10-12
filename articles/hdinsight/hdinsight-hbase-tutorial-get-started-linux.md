---
title: "Introducción a un ejemplo de HBase en HDInsight - Azure | Microsoft Docs"
description: "Siga este ejemplo de Apache HBase para empezar a usar Hadoop en HDInsight. Cree tablas desde el shell de HBase y consúltelas mediante Hive."
keywords: hbasecommand, ejemplo de hbase
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 4d6a2658-6b19-4268-95ee-822890f5a33a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: jgao
ms.openlocfilehash: 2f3cb99c832b6e17ac932112c1d397fa0c8afeca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-an-apache-hbase-example-in-hdinsight"></a>Introducción a un ejemplo de Apache HBase en HDInsight

Aprenda a crear un clúster de HBase en HDInsight, a crear tablas de HBase y a consultar tablas mediante Hive. Para obtener información general de HBase, consulte [Información general de HBase de HDInsight][hdinsight-hbase-overview].

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar a probar este ejemplo de HBase, debe tener los siguientes elementos:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Secure Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md). 
* [curl](http://curl.haxx.se/download.html).

## <a name="create-hbase-cluster"></a>Creación del clúster de HBase.
El siguiente procedimiento usa una plantilla de Azure Resource Manager para crear un clúster de HBase basado en Linux versión 3.4, y la cuenta de Azure Storage predeterminada dependiente. Para comprender los parámetros utilizados en el procedimiento y otros métodos de creación del clúster, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Haga clic en la imagen siguiente para abrir la plantilla en el Portal de Azure. La plantilla se encuentra en un contenedor de blobs público. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. En la hoja **Implementación personalizada**, escriba los valores siguientes:
   
   * **Suscripción**: seleccione la suscripción de Azure que usa para crear este clúster.
   * **Grupo de recursos**: cree un grupo de administración de recursos de Azure o use uno existente.
   * **Ubicación**: especifique la ubicación del grupo de recursos. 
   * **Nombre del clúster**: escriba el nombre del clúster de HBase.
   * **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es **admin**.
   * **Nombre de usuario y contraseña de SSH**: el nombre de usuario predeterminado es **sshuser**.  Puede cambiarlo.
     
     Otros parámetros son opcionales.  
     
     Cada clúster tiene una dependencia de cuenta de Azure Storage. Después de eliminar un clúster, los datos permanecen en la cuenta de almacenamiento. El nombre de cuenta de almacenamiento de clúster predeterminado es el nombre del clúster con "store" anexado. Está codificado en la sección de variables de plantilla.
3. Seleccione **Acepto los términos y condiciones indicadas anteriormente** y, después, haga clic en **Comprar**. Se tarda aproximadamente 20 minutos en crear un clúster.

> [!NOTE]
> Después de que se elimine un clúster de HBase, puede crear otro clúster de HBase mediante el mismo contenedor de blobs predeterminado. El nuevo clúster selecciona las tablas de HBase que creó en el clúster original. Para evitar incoherencias, recomendamos deshabilitar las tablas de HBase antes de eliminar el clúster.
> 
> 

## <a name="create-tables-and-insert-data"></a>Creación de tablas e inserción de datos
Puede usar SSH para conectarse a los clústeres de HBase y, después, usar el shell de HBase para crear tablas de HBase e insertar y consultar datos. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Para la mayoría de las personas, los datos aparecen en formato tabular:

![Datos tabulares de HBase de HDInsight][img-hbase-sample-data-tabular]

En HBase (una implementación de BigTable), los mismos datos tienen un aspecto similar al siguiente:

![Datos de HDInsight HBase BigTable][img-hbase-sample-data-bigtable]


**Para usar el shell de HBase, siga estos pasos:**

1. Desde SSH ejecute el siguiente comando de HBase:
   
    ```bash
    hbase shell
    ```

2. Cree una tabla HBase con dos familias de columnas:

    ```hbaseshell   
    create 'Contacts', 'Personal', 'Office'
    list
    ```
3. Inserte algunos datos:
    
    ```hbaseshell   
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    scan 'Contacts'
    ```
   
    ![Shell de HDInsight Hadoop HBase][img-hbase-shell]
4. Obtenga una sola fila
   
    ```hbaseshell
    get 'Contacts', '1000'
    ```
   
    Verá los mismos resultados que con el comando de examen porque solo hay una fila.
   
    Para más información acerca del esquema de tabla de Hbase, consulte [Introducción al diseño de esquema de HBase][hbase-schema]. Para ver más comandos de HBase, consulte [Guía de referencia de Apache HBase][hbase-quick-start].
5. Salga del shell
   
    ```hbaseshell
    exit
    ```

**Para cargar datos de forma masiva en la tabla HBase de contactos**

HBase incluye varios métodos de carga de datos en las tablas.  Para obtener más información, vea [Carga masiva](http://hbase.apache.org/book.html#arch.bulk.load).

Se puede encontrar un archivo de datos de ejemplo en un contenedor de blobs público, *wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  El contenido del archivo de datos es:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Opcionalmente, puede crear un archivo de texto y cargarlo en su propia cuenta de almacenamiento. Para obtener instrucciones, consulte [Carga de datos para trabajos de Hadoop en HDInsight][hdinsight-upload-data].

> [!NOTE]
> Este procedimiento usa la tabla HBase de contactos que ha creado en el último procedimiento.
> 

1. Desde SSH ejecute el siguiente comando para transformar el archivo de datos en StoreFiles y almacene en una ruta de acceso relativa especificada por Dimporttsv.bulk.output.  Si está en el shell de HBase, utilice el comando de salida para salir.

    ```bash   
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Ejecute el siguiente comando para cargar los datos desde /example/data/storeDataFileOutput en la tabla de HBase:
   
    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Puede abrir el shell de HBase y usar el comando de análisis para mostrar el contenido de la tabla.

## <a name="use-hive-to-query-hbase"></a>Utilización de Hive para consultar HBase

Puede consultar datos en tablas de HBase mediante el uso de Hive. En esta sección, creará una tabla de Hive que se asigna a la tabla de HBase y la usará para consultar los datos de la tabla de HBase.

1. Abra **PuTTY**y conéctese al clúster.  Consulte las instrucciones del procedimiento anterior.
2. En la sesión SSH, use el siguiente comando para iniciar Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Para más información sobre Beeline, consulte [Uso de Hive con Hadoop en HDInsight con Beeline](hdinsight-hadoop-use-hive-beeline.md).
       
3. Ejecute el siguiente script de HiveQL para crear una tabla de Hive que se asigne a la tabla de HBase. Antes de ejecutar esta instrucción, asegúrese de haber creado la tabla de ejemplo a la que se hace referencia aquí en HBase mediante el shell de HBase.

    ```hiveql   
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

4. Ejecute el siguiente script de HiveQL para consultar los datos de la tabla de HBase:

    ```hiveql   
    SELECT count(rowkey) FROM hbasecontacts;
    ```

## <a name="use-hbase-rest-apis-using-curl"></a>Usar las API de REST de HBase con Curl

La API de REST se protege con la [autenticación básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre debe crear solicitudes usando HTTP segura (HTTPS) para así garantizar que las credenciales se envían de manera segura al servidor.

2. Use el siguiente comando para enumerar las tablas de HBase existentes:

    ```bash
    curl -u <UserName>:<Password> \
    -G https://<ClusterName>.azurehdinsight.net/hbaserest/
    ```

3. Use el siguiente comando para crear una nueva tabla de HBase con dos familias de columnas:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    El esquema se ofrece con el formato JSon.
4. Use el siguiente comando para instalar algunos datos:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```
   
    Debe codificar en base64 los valores especificados en el modificador -d. En el ejemplo:
   
   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: John Dole
     
     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) permite insertar varios valores (por lotes).
5. Use el siguiente comando para obtener una fila:
   
    ```bash 
    curl -u <UserName>:<Password> \
    -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Para más información sobre Rest de HBase, consulte la [guía de referencia de Apache HBase](https://hbase.apache.org/book.html#_rest).

> [!NOTE]
> Thrift no es compatible con HBase en HDInsight.
>
> Al usar Curl o cualquier otra comunicación REST con WebHCat, debe proporcionar el nombre de usuario y la contraseña del administrador del clúster de HDInsight para autenticar las solicitudes. También debe usar el nombre del clúster como parte del identificador uniforme de recursos (URI) que se utiliza para enviar las solicitudes al servidor:
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    Recibirá una respuesta similar a la siguiente:
>   
>        {"status":"ok","version":"v1"}
   


## <a name="check-cluster-status"></a>Comprobar el estado del clúster
HBase en HDInsight se incluye con una interfaz de usuario web para la supervisión de clústeres. Mediante la interfaz de usuario web, puede solicitar estadísticas o información acerca de las regiones.

**Para acceder a la interfaz de usuario maestra de HBase**

1. Inicie sesión en la interfaz de usuario web de Ambari en https://&lt;nombre_de_cluster>.azurehdinsight.net.
2. Haga clic en **HBase** en el menú izquierdo.
3. Haga clic en **Quick links** (Vínculos rápidos) en la parte superior de la página, seleccione el vínculo del nodo Zookeeper activo y, después, haga clic en **HBase Master UI** (Interfaz de usuario maestra de HBase).  La interfaz de usuario se abre en otra pestaña del explorador:

  ![Interfaz de usuario maestra de HBase de HDInsight](./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

  La interfaz de usuario maestra de HBase contiene las siguientes secciones:

  - servidores regionales
  - maestros de copia de seguridad
  - tables
  - tareas
  - atributos de software

## <a name="delete-the-cluster"></a>Eliminación del clúster
Para evitar incoherencias, recomendamos deshabilitar las tablas de HBase antes de eliminar el clúster.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido a crear un clúster de HBase, a crear tablas y a ver los datos de esas tablas en el shell de HBase. También ha aprendido a usar una consulta de datos de Hive en las tablas de HBase y a usar las API de REST de C# para HBase para crear una tabla de HBase y recuperar los datos de la tabla.

Para obtener más información, consulte:

* [Información general de HBase de HDInsight][hdinsight-hbase-overview]: HBase es una base de datos NoSQL de código abierto Apache basada en Hadoop que proporciona acceso aleatorio y una coherencia sólida para grandes cantidades de datos no estructurados y semiestructurados.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
