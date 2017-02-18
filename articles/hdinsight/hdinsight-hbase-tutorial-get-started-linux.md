---
title: "Introducción a HBase en Azure HDInsight | Microsoft Docs"
description: "Siga este tutorial de HBase para empezar a usar Apache HBase con Hadoop en HDInsight. Cree tablas desde el shell de HBase y consúltelas mediante Hive."
keywords: apache hbase,hbase,shell de hbase,tutorial de hbase
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 4d6a2658-6b19-4268-95ee-822890f5a33a
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 0a09f1511778623b21a26042a752009ae2208ba6
ms.openlocfilehash: 415f6f71642726aeb8477f067bd406a57717ff2a


---
# <a name="hbase-tutorial-get-started-using-apache-hbase-in-hdinsight"></a>Tutorial de HBase: Introducción al uso de Apache HBase en HDInsight

Aprenda a crear un clúster de HBase en HDInsight, a crear tablas de HBase y a consultar tablas mediante Hive. Para obtener información general de HBase, consulte [Información general de HBase de HDInsight][hdinsight-hbase-overview].

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial de HBase, debe contar con lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Secure Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md). 
* [curl](http://curl.haxx.se/download.html).

### <a name="access-control-requirements"></a>Requisitos de control de acceso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Creación del clúster de HBase.
El siguiente procedimiento usa una plantilla de Azure Resource Manager para crear un clúster de HBase basado en Linux versión 3.4, y la cuenta de Azure Storage predeterminada dependiente. Para comprender los parámetros utilizados en el procedimiento y otros métodos de creación del clúster, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Haga clic en la imagen siguiente para abrir la plantilla en el Portal de Azure. La plantilla se encuentra en un contenedor de blobs público. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. En la hoja **Implementación personalizada**, escriba lo siguiente:
   
   * **Suscripción**: seleccione la suscripción de Azure que se usará para crear este clúster.
   * **Grupo de recursos**: cree un nuevo grupo de recursos de Azure o seleccione uno existente.
   * **Ubicación**: especifique la ubicación del grupo de recursos. 
   * **ClusterName**: escriba un nombre para el clúster de HBase que va a crear.
   * **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es **admin**.
   * **Nombre de usuario y contraseña de SSH**: el nombre de usuario predeterminado es **sshuser**.  Puede cambiarlo.
     
     Otros parámetros son opcionales.  
     
     Cada clúster tiene una dependencia de cuenta de Almacenamiento de blobs de Azure. Después de eliminar un clúster, los datos permanecen en la cuenta de almacenamiento. El nombre de cuenta de almacenamiento de clúster predeterminado es el nombre del clúster con "store" anexado. Está codificado en la sección de variables de plantilla.
3. Seleccione **Acepto los términos y condiciones indicadas anteriormente** y, después, haga clic en **Comprar**. Se tarda aproximadamente 20 minutos en crear un clúster.

> [!NOTE]
> Después de que se elimine un clúster de HBase, puede crear otro clúster de HBase mediante el mismo contenedor de blobs predeterminado. El nuevo clúster seleccionará las tablas de HBase que creó en el clúster original. Para evitar incoherencias, recomendamos deshabilitar las tablas de HBase antes de eliminar el clúster.
> 
> 

## <a name="create-tables-and-insert-data"></a>Creación de tablas e inserción de datos
Puede usar SSH para conectarse a los clústeres de HBase y, después, usar el Shell de HBase para crear tablas de HBase e insertar y consultar datos. Para más información sobre el uso de SSH, consulte [Usar SSH con Hadoop basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md) y [Usar SSH con Hadoop basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

Para la mayoría de las personas, los datos aparecen en formato tabular:

![Datos tabulares de HBase de HDInsight][img-hbase-sample-data-tabular]

En HBase, que es una implementación de BigTable, los mismos datos tienen un aspecto similar al siguiente:

![Datos de HDInsight HBase BigTable][img-hbase-sample-data-bigtable]

Tendrá más sentido cuando termine el siguiente procedimiento.  

**Para usar el shell de HBase, siga estos pasos:**

1. Desde SSH ejecute el siguiente comando:
   
        hbase shell
2. Cree una tabla HBase con dos familias de columnas:
   
        create 'Contacts', 'Personal', 'Office'
        list
3. Inserte algunos datos:
   
        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'
   
    ![Shell de HDInsight Hadoop HBase][img-hbase-shell]
4. Obtenga una sola fila
   
        get 'Contacts', '1000'
   
    Verá los mismos resultados que con el comando de análisis porque solo hay una fila.
   
    Para más información acerca del esquema de tabla de Hbase, consulte [Introducción al diseño de esquema de HBase][hbase-schema]. Para ver más comandos de HBase, consulte [Guía de referencia de Apache HBase][hbase-quick-start].
5. Salga del shell
   
        exit

**Para cargar datos de forma masiva en la tabla HBase de contactos**

HBase incluye varios métodos de carga de datos en las tablas.  Para obtener más información, vea [Carga masiva](http://hbase.apache.org/book.html#arch.bulk.load).

Se ha cargado un archivo de datos de ejemplo en un contenedor de blobs público, *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  El contenido del archivo de datos es:

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

Puede crear un archivo de texto y cargar el archivo en su propia cuenta de almacenamiento si lo desea. Para obtener instrucciones, consulte [Carga de datos para trabajos de Hadoop en HDInsight][hdinsight-upload-data].

> [!NOTE]
> Este procedimiento usa la tabla HBase de contactos que ha creado en el último procedimiento.
> 
> 

1. Desde SSH ejecute el siguiente comando para transformar el archivo de datos en StoreFiles y almacene en una ruta de acceso relativa especificada por Dimporttsv.bulk.output:.  Si está en el shell de HBase, utilice el comando de salida para salir.
   
        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
2. Ejecute el siguiente comando para cargar los datos desde /example/data/storeDataFileOutput en la tabla de HBase:
   
        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
3. Puede abrir el shell de HBase y usar el comando de análisis para mostrar el contenido de la tabla.

## <a name="use-hive-to-query-hbase"></a>Utilización de Hive para consultar HBase
Puede consultar datos en tablas de HBase mediante el uso de Hive. En esta sección se crea una tabla de Hive que se asigna a la tabla de HBase y se usa para consultar los datos en la tabla de HBase.

> [!NOTE]
> Si Hive y HBase están en clústeres diferentes de la misma red virtual, es necesario usar cuórum de Zookeeper al invocar la shell de Hive:
>
>       hive --hiveconf hbase.zookeeper.quorum=zk0-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net,zk1-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net,zk2-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net --hiveconf zookeeper.znode.parent=/hbase-unsecure  
>
>

1. Abra **PuTTY**y conéctese al clúster.  Consulte las instrucciones del procedimiento anterior.
2. Abra el shell de Hive.
   
       hive
       
3. Ejecute el siguiente script de HiveQL para crear una tabla de Hive que se asigne a la tabla de HBase. Antes de ejecutar esta instrucción, asegúrese de haber creado la tabla de ejemplo a la que se hace referencia aquí en HBase mediante el shell de HBase.
   
        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');
4. Ejecute el siguiente script de HiveQL para consultar los datos de la tabla de HBase:
   
         SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>Usar las API de REST de HBase con Curl
> [!NOTE]
> Al usar Curl o cualquier otra comunicación REST con WebHCat, debe proporcionar el nombre de usuario y la contraseña del administrador del clúster de HDInsight para autenticar las solicitudes. También debe usar el nombre del clúster como parte del identificador uniforme de recursos (URI) que se usa para enviar las solicitudes al servidor.
> 
> En el caso de los comandos que aparecen en esta sección, reemplace **USERNAME** por el usuario para autenticación en el clúster y **PASSWORD** por la contraseña de la cuenta de usuario. Reemplace **CLUSTERNAME** por el nombre del clúster.
> 
> La API de REST se protege con la [autenticación básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre debe crear solicitudes usando HTTP segura (HTTPS) para así garantizar que las credenciales se envían de manera segura al servidor.
> 
> 

1. Desde una línea de comandos, utilice el siguiente comando para comprobar que puede conectarse al clúster de HDInsight.
   
        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
   
    Debería recibir una respuesta similar a la siguiente:
   
        {"status":"ok","version":"v1"}
   
    Los parámetros que se utilizan en este comando son los siguientes:
   
   * **-u** : el nombre de usuario y la contraseña que se utilizan para autenticar la solicitud.
   * **-G** : indica que esta es una solicitud GET.
2. Use el siguiente comando para enumerar las tablas de HBase existentes:
   
        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/
3. Use el siguiente comando para crear una nueva tabla de HBase con dos familias de columnas:
   
        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v
   
    El esquema se ofrece con el formato JSon.
4. Use el siguiente comando para instalar algunos datos:
   
        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
        -v
   
    Debe codificar en base64 los valores especificados en el modificador -d.  En el ejemplo:
   
   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: John Dole
     
     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) permite insertar varios valores (por lotes).
5. Use el siguiente comando para obtener una fila:
   
        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

Para más información sobre Rest de HBase, consulte la [guía de referencia de Apache HBase](https://hbase.apache.org/book.html#_rest).

## <a name="check-cluster-status"></a>Comprobar el estado del clúster
HBase en HDInsight se incluye con una interfaz de usuario web para la supervisión de clústeres. Mediante la interfaz de usuario web, puede solicitar estadísticas o información acerca de las regiones.

**Para acceder a la interfaz de usuario maestra de HBase**

1. Abra la interfaz de usuario web de Ambari en https://&lt;nombre_de_cluster>.azurehdinsight.net.
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

## <a name="next-steps"></a>Pasos siguientes
En este tutorial de HBase para HDInsight, ha aprendido a aprovisionar un clúster HBase, a crear tablas y a ver los datos de esas tablas en el shell de HBase. También ha aprendido a usar una consulta de datos de Hive en las tablas de HBase y a usar las API de REST de C# para HBase para crear una tabla de HBase y recuperar los datos de la tabla.

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
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
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



<!--HONumber=Feb17_HO2-->


