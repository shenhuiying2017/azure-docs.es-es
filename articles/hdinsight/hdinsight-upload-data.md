---
title: Carga de datos para trabajos de Hadoop en HDInsight | Microsoft Docs
description: "Aprenda a cargar datos en HDInsight y a obtener acceso a ellos para trabajos de Hadoop con la CLI de Azure, el Explorador de Azure Storage, Azure PowerShell, la línea de comandos de Hadoop o Sqoop."
keywords: "extracción, transformación y carga de datos de hadoop, obtención de datos en hadoop, carga de datos de hadoop"
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 56b913ee-0f9a-4e9f-9eaf-c571f8603dd6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jgao
ms.openlocfilehash: cfe1b6bee9bc1f093b239f8f4acc523e47ad5d1a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Carga de datos para trabajos de Hadoop en HDInsight

Azure HDInsight ofrece un sistema de archivos distribuido de Hadoop (HDFS) completo a través de Azure Storage y Azure Data Lake Store. Azure Storage y Data Lake Store están diseñados como una extensión de HDFS para ofrecer una experiencia sin problemas para los clientes. Habilitan el conjunto completo de componentes en el ecosistema de Hadoop para que opere directamente en los datos que administra. Azure Storage y Data Lake Store son sistemas de archivos diferentes que se han optimizado para el almacenamiento de datos y el cálculo en ellos. Para más información sobre las ventajas del uso de Azure Storage, vea [Uso de Azure Storage con HDInsight][hdinsight-storage] y [Uso de Data Lake Store con HDInsight](hdinsight-hadoop-use-data-lake-store.md).

## <a name="prerequisites"></a>requisitos previos

Tenga en cuenta los siguientes requisitos antes de empezar:

* Un clúster de HDInsight de Azure. Para obtener instrucciones, vea [Introducción a Azure HDInsight][hdinsight-get-started] o [Creación de clústeres de HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Conocimiento de los dos artículos siguientes:

    - Consulte [Uso de Azure Storage con HDInsight][hdinsight-storage]
    - [Uso de Data Lake Store con HDInsight](hdinsight-hadoop-use-data-lake-store.md)

## <a name="upload-data-to-azure-storage"></a>Carga de datos en Azure Storage

### <a name="command-line-utilities"></a>Utilidades de la ea de comandos
Microsoft proporciona las utilidades siguientes para trabajar con Azure Storage:

| Herramienta | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Interfaz de la línea de comandos de Azure][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] |✔ | |✔ |
| [Línea de comandos de Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]
> Mientras que la CLI de Azure, Azure PowerShell y AzCopy se pueden usar desde fuera de Azure, el comando de Hadoop solo está disponible en el clúster de HDInsight. Además el comando solo permite cargar datos desde el sistema de archivos local en Azure Storage.
>
>

#### <a id="xplatcli"></a>Azure CLI
La CLI de Azure es una herramienta multiplataforma que le permite administrar los servicios de Azure. Para cargar datos en Azure Storage, siga estos pasos:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Instale y configure la CLI de Azure para Mac, Linux y Windows](../cli-install-nodejs.md).
2. Abra un símbolo del sistema, bash u otro shell y use lo siguiente para autenticarse en su suscripción de Azure.

    ```cli
    azure login
    ```

    Cuando se le solicite, escriba el nombre de usuario y la contraseña de su suscripción.
3. Escriba el comando siguiente para enumerar las cuentas de almacenamiento de su suscripción:

    ```cli
    azure storage account list
    ```

4. Seleccione la cuenta de almacenamiento que contiene el blob con en el que quiere trabajar y use el comando siguiente para recuperar la clave de esta cuenta:

    ```cli
    azure storage account keys list <storage-account-name>
    ```

    Este comando devuelve las claves **Principal** y **Secundaria**. Copie el valor de la clave **Principal** ya que se usará en los pasos siguientes.
5. Use el comando siguiente para recuperar una lista de contenedores de blobs dentro de la cuenta de almacenamiento:

    ```cli
    azure storage container list -a <storage-account-name> -k <primary-key>
    ```

6. Use los comandos siguientes para cargar y descargar archivos en el blob:

   * Para cargar un archivo:

        ```cli
        azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
        ```

   * Para descargar un archivo:

        ```cli
        azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>
        ```
    
> [!NOTE]
> Si siempre trabaja con la misma cuenta de almacenamiento, puede establecer las siguientes variables de entorno en lugar de especificar la cuenta y la clave de cada comando:
>
> * **AZURE\_STORAGE\_ACCOUNT**: el nombre de la cuenta de almacenamiento.
> * **AZURE\_STORAGE\_ACCESS\_KEY**: la clave de la cuenta de almacenamiento.
>
>

#### <a id="powershell"></a>Azure PowerShell
Azure PowerShell es un eficaz entorno de scripting que se puede usar para controlar y automatizar la implementación y la administración de cargas de trabajo en Azure. Para obtener información sobre cómo configurar su estación de trabajo para que ejecute Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Para cargar un archivo local en Azure Storage**

1. Abra la consola de Azure PowerShell como se indica en [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).
2. Configure los valores de las cinco primeras variables del script siguiente:

    ```powershell
    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $fileName ="<LocalFileName>"
    $blobName = "<BlobName>"

    # Get the storage account key
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    # Create the storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    # Copy the file from local workstation to the Blob container
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
    ```

3. Pegue el script en la consola de Azure PowerShell para ejecutarlo para copiar el archivo.

Por ejemplo los scripts de PowerShell creados para trabajar con HDInsight, consulte [HDInsight tools (Herramientas de HDInsight)](https://github.com/blackmist/hdinsight-tools).

#### <a id="azcopy"></a>AzCopy
AzCopy es una herramienta de la línea de comandos diseñada para simplificar la tarea de transferir datos dentro y fuera de una cuenta de Azure Storage. Puede usarla como una herramienta independiente o incorporarla a una aplicación existente. [Descarga de AzCopy][azure-azcopy-download]

La sintaxis de AzCopy es la siguiente:

```command
AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]
```

Para obtener más información, consulte [AzCopy: carga y descarga de archivos para blobs de Azure][azure-azcopy].

La versión preliminar de AzCopy en Linux está disponible.  Vea [Announcing AzCopy on Linux Preview (Anuncio de la versión preliminar de AzCopy en Linux)](https://blogs.msdn.microsoft.com/windowsazurestorage/2017/05/16/announcing-azcopy-on-linux-preview/).

#### <a id="commandline"></a>Línea de comandos de Hadoop
La línea de comandos de Hadoop solo es útil para almacenar datos en Azure Storage Blob cuando los datos ya están presentes en el nodo principal del clúster.

Para usar la línea de comando de Hadoop, primero es preciso conectarse al nodo principal a través de uno de los métodos siguientes:

* **HDInsight para Windows**: [conectar mediante Escritorio remoto](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **HDInsight basado en Linux**: conexión mediante [SSH o PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Una vez conectado, puede utilizar la siguiente sintaxis para cargar un archivo al almacenamiento.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Por ejemplo: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Como el sistema de archivos predeterminado de HDInsight está en Azure Storage, /example/data.txt está en realidad en Azure Storage. También puede referirse al archivo como:

    wasb:///example/data/data.txt

o

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para obtener una lista de otros comandos de Hadoop que funcionan con archivos, consulte [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]
> En los clústeres de HBase, el tamaño de bloque predeterminado al escribir datos es de 256 KB. Aunque esto funciona bien cuando se usan API de REST o API de HBase, el uso de los comandos `hadoop` o `hdfs dfs` para escribir más de ~ 12 GB de datos genera un error. Para más información, vea la sección [Excepción de almacenamiento para escritura en blob](#storageexception) de este artículo.
>
>

### <a name="graphical-clients"></a>Clientes gráficos
También hay varias aplicaciones que proporcionan una interfaz gráfica para trabajar con Azure Storage. La siguiente tabla es una lista de algunas de estas aplicaciones:

| Cliente | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools para HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) |✔ |✔ |✔ |
| [Explorador de Azure Storage](http://storageexplorer.com/) |✔ |✔ |✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

#### <a name="visual-studio-tools-for-hdinsight"></a>Visual Studio Tools para HDInsight
Para obtener más información, consulte [Navegación por los recursos vinculados](hadoop/apache-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources).

#### <a id="storageexplorer"></a>Explorador de Azure Storage
*Explorador de Azure Storage* es una práctica herramienta para inspeccionar y modificar los datos de blobs. Se trata de una herramienta gratuita que se puede descargar de [http://storageexplorer.com/](http://storageexplorer.com/). El código fuente también está disponible en este vínculo.

Antes de usar la herramienta, debe saber el nombre y la clave de la cuenta de almacenamiento de Azure. Para obtener instrucciones para lograr esta información, vea la sección "Vista, copia y regeneración de las claves de acceso de almacenamiento" de [Creación, administración o eliminación de una cuenta de almacenamiento][azure-create-storage-account].

1. Ejecute el explorador de Azure Storage. Si es la primera vez que ejecuta el Explorador de Storage, se le pide **Nombre de cuenta de almacenamiento** y **Clave de cuenta de almacenamiento**. Si ya lo ejecutó antes, use el botón **Agregar** para agregar un nombre y una clave de cuenta de almacenamiento nuevos.

    Escriba el nombre y la clave para la cuenta de almacenamiento que usa el clúster de HDInsight y seleccione **GUARDAR Y ABRIR**.

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. En la lista de contenedores de la izquierda de la interfaz, haga clic en el nombre del contenedor que esté asociado al clúster de HDInsight. De forma predeterminada, es el nombre del clúster de HDInsight, pero puede ser diferente si ha especificado un nombre concreto al crear el clúster.
3. En la barra de herramientas, seleccione el icono de carga.

    ![Barra de herramientas con el icono de carga resaltado](./media/hdinsight-upload-data/toolbar.png)
4. Especifique el archivo que quiere cargar y haga clic en **Abrir**. Cuando se le pida, seleccione **Cargar** para cargar el archivo en la raíz del contenedor de almacenamiento. Si quiere cargar el archivo en una ruta de acceso específica, escriba la ruta de acceso en el campo **Destino** y seleccione **Cargar**.

    ![Cuadro de diálogo de carga de archivos](./media/hdinsight-upload-data/fileupload.png)

    Cuando termine de cargar el archivo, puede usarlo desde trabajos en el clúster de HDInsight.

### <a name="mount-azure-storage-as-local-drive"></a>Montaje de Azure Storage como unidad local
Vea [Montaje de Azure Storage como unidad local](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

### <a name="upload-using-services"></a>Carga mediante servicios
#### <a name="azure-data-factory"></a>Azure Data Factory
El servicio Azure Data Factory es un servicio completamente administrado para crear servicios de almacenamiento de datos, procesamiento de datos y movimiento en canalizaciones de producción de datos confiable, escalable y simplificado.

Azure Data Factory puede usarse para mover datos a Azure Storage o para crear canalizaciones de datos que usen directamente características de HDInsight como Hive y Pig.

Para obtener más información, consulte [Documentación de Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

#### <a id="sqoop"></a>Apache Sqoop
Sqoop es una herramienta diseñada para transferir datos entre Hadoop y las bases de datos relacionales. Puede usarla para importar datos desde un sistema de administración de bases de datos relacionales (RDBMS), como SQL Server, MySQL u Oracle en el sistema de archivos distribuidos de Hadoop (HDFS), transformar los datos de Hadoop con MapReduce o Hive y, a continuación, exportar los datos en un RDBMS.

Para obtener más información, consulte [Use Sqoop with Hadoop in HDInsight (Uso de Sqoop con HDInsight)][hdinsight-use-sqoop].

### <a name="development-sdks"></a>SDK de desarrollo
También se puede acceder a Azure Storage mediante un SDK de Azure desde los siguientes lenguajes de programación:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Para obtener más información acerca de cómo instalar los SDK de Azure, consulte [Descargas de Azure](https://azure.microsoft.com/downloads/)

### <a name="troubleshooting"></a>solución de problemas
#### <a id="storageexception"></a>Excepción de almacenamiento para escritura en blob
**Síntomas**: al usar los comandos `hadoop` o `hdfs dfs` para escribir archivos de ~12 GB o mayores en un clúster de HBase, puede experimentarse el siguiente error:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Causa**: HBase en clústeres de HDInsight toma como valor predeterminado un tamaño de bloque de 256 KB al escribir en Azure Storage. Si bien esto funciona para API de HBase o API de REST, se produce un error al usar las utilidades de línea de comandos `hadoop` o `hdfs dfs`.

**Solución**: use `fs.azure.write.request.size` para especificar un tamaño de bloque más grande. Puede hacerlo mediante un sistema por uso con el parámetro `-D`. El siguiente comando es un ejemplo de uso de este parámetro con el comando `hadoop`:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

También puede aumentar el valor de `fs.azure.write.request.size` globalmente mediante Ambari. Los siguientes pasos se pueden usar para cambiar el valor en la IU web de Ambari:

1. En el explorador, vaya a la IU web de Ambari para el clúster. La puede encontrar en https://CLUSTERNAME.azurehdinsight.net, donde **CLUSTERNAME** es el nombre del clúster.

    Cuando se le solicite, escriba el nombre de usuario y la contraseña de administrador para el clúster.
2. En el lado izquierdo de la pantalla, seleccione **HDFS** y luego seleccione la pestaña **Configs** (Configuraciones).
3. En el campo **Filter...** (Filtro...), escriba `fs.azure.write.request.size`. Esto muestra el campo y el valor actual en el centro de la página.
4. Cambie el valor de 262144 (256 KB) al nuevo valor. Por ejemplo, 4194304 (4 MB).

![Imagen de cambiar el valor en la IU web de Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Para más información sobre el uso de Ambari, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>pasos siguientes
Ahora que ya sabe cómo enviar datos a HDInsight, consulte los artículos siguientes para aprender a realizar el análisis:

* [Introducción a Azure HDInsight][hdinsight-get-started]
* [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Uso de Pig con HDInsight][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]:../storage/common/storage-create-storage-account.md
[azure-azcopy-download]:../storage/common/storage-use-azcopy.md
[azure-azcopy]:../storage/common/storage-use-azcopy.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
