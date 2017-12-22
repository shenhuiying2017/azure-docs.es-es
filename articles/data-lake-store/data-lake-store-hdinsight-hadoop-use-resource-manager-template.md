---
title: Uso de plantillas de Azure para crear Azure HDInsight y Data Lake Store | Microsoft Docs
description: "Uso de las plantillas de Azure Resource Manager para crear y usar clústeres de HDInsight con Azure Data Lake Store"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 8c5afc96cc8101345f00b5d435e9f393d22672de
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>Creación de un clúster de HDInsight con Data Lake Store mediante las plantillas de Azure Resource Manager
> [!div class="op_single_selector"]
> * [Uso del Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Uso de PowerShell (para el almacenamiento predeterminado)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Uso de PowerShell (para el almacenamiento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Uso de Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Aprenda a usar Azure PowerShell para configurar un clúster de HDInsight con Azure Data Lake Store **como almacenamiento adicional**.

Para los tipos de clúster compatibles, Data Lake Store se puede usar como un almacenamiento predeterminado o una cuenta de almacenamiento adicional. Cuando Data Lake Store se usa como almacenamiento adicional, la cuenta de almacenamiento predeterminada para los clústeres seguirá siendo Azure Storage Blobs (WASB) y los archivos relacionados con clústeres (como registros, etc.) seguirán escribiéndose en el almacenamiento predeterminado, mientras que los datos que quiere procesar pueden almacenarse en una cuenta de Data Lake Store. Utilizar el Almacén de Data Lake como una cuenta de almacenamiento adicional no afecta al rendimiento o la capacidad de lectura y escritura en el almacenamiento del clúster.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Uso de Data Lake Store para el almacenamiento de clústeres de HDInsight

Estas son algunas consideraciones importantes que deben tenerse en cuenta al usar HDInsight con Data Lake Store:

* La opción para crear clústeres de HDInsight con acceso a Data Lake Store como almacenamiento predeterminado está disponible para las versiones 3.5 y 3.6 de HDInsight.

* La opción para crear clústeres de HDInsight con acceso a Data Lake Store como almacenamiento está disponible para las versiones 3.2, 3.4, 3.5 y 3.6. de HDInsight.

En este artículo, aprovisionamos un clúster de Hadoop con el Almacén de Data Lake como almacenamiento adicional. Para instrucciones sobre cómo crear un clúster de Hadoop con Data Lake Store como almacenamiento predeterminado, consulte [Creación de un clúster de HDInsight con Data Lake Store mediante Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versiones posteriores**. Consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).
* **Entidad de servicio de Azure Active Directory** En los pasos de este tutorial se proporcionan instrucciones sobre cómo crear entidades de servicio de Azure AD. Sin embargo, debe ser administrador de Azure AD para poder crearlas. Si ya lo es, puede hacer caso omiso a este requisito previo y continuar con el tutorial.

    **Si no lo es**, no podrá realizar los pasos necesarios para crear una entidad de servicio. En este caso, su administrador de Azure AD debe generar primero una entidad de servicio antes de crear un clúster de HDInsight con Data Lake Store. Además, la entidad de servicio debe crearse con un certificado, tal y como se describe en [Creación de una entidad de servicio](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>Creación de un clúster de HDInsight con Azure Data Lake Store
Las plantillas de Resource Manager y los requisitos previos para usarla están disponibles en GitHub: [Deploy a HDInsight Linux cluster with new Data Lake Store](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) (Implementación de un clúster Linux de HDInsight con la nueva versión de Data Lake Store). Siga las instrucciones de este vínculo para crear un clúster de HDInsight con Azure Data Lake Store como almacenamiento adicional.

Estas instrucciones requieren el uso de PowerShell. Antes de comenzar con las instrucciones, asegúrese de iniciar sesión en su cuenta de Azure. En el escritorio, abra una nueva ventana de Azure PowerShell y escriba los siguientes fragmentos. Cuando se le solicite iniciar sesión, asegúrese de iniciarla como uno de los administradores o propietario de la suscripción:

```
# Log in to your Azure account
Login-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>Carga de datos de ejemplo en Azure Data Lake Store
Las plantilla de Resource Manager crean una cuenta de Data Lake Store y la asocian con el clúster de HDInsight. Ahora, debe cargar algunos datos de ejemplo en Data Lake Store. Necesitará estos datos más adelante en el tutorial para ejecutar trabajos desde un clúster de HDInsight que accedan a datos en el Almacén de Data Lake. Para ver las instrucciones sobre cómo cargar datos, consulte el artículo de [carga de archivos en Data Lake Store](data-lake-store-get-started-portal.md#uploaddata). Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Establecimiento de listas ACL pertinentes en los datos de ejemplo
Para confirmar que los datos de ejemplo que carga están accesibles desde el clúster de HDInsight, debe asegurarse de que la aplicación de Azure AD que utiliza para establecer la identidad entre el clúster de HDInsight y Data Lake Store tiene acceso al archivo o a la carpeta deseados. Para ello, siga estos pasos.

1. Busque el nombre de la aplicación de Azure AD asociada al clúster de HDInsight y a Data Lake Store. Una manera de buscar el nombre es abrir la hoja de clúster de HDInsight que creó mediante la plantilla de Resource Manager, hacer clic en la pestaña **Identidad de AAD del clúster** y buscar el valor del **nombre para mostrar de la entidad de servicio**.
2. Ahora, proporcione acceso a esta aplicación de Azure AD en el archivo o la carpeta a los quiera acceder desde el clúster de HDInsight. Para establecer las listas ACL adecuadas en el archivo o carpeta de Data Lake Store, consulte el artículo sobre cómo [proteger los datos de Data Lake Store](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Ejecución de trabajos de prueba en el clúster de HDInsight para usar el Almacén de Data Lake
Después de configurar un clúster de HDInsight, puede ejecutar trabajos de prueba en el clúster para probar que el clúster de HDInsight pueda acceder al Almacén de Data Lake. Para hacerlo, ejecutaremos un trabajo de Hive de ejemplo que crea una tabla con los datos de ejemplo que cargó antes en el Almacén de Data Lake.

En esta sección, aprenderá a usar SSH en el clúster de Linux en HDInsight y ejecutará una consulta de Hive de ejemplo. Si usa un cliente Windows, se recomienda usar **PuTTY**, que se puede descargar en [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, consulte [Uso de SSH con Hadoop basado en Linux en HDInsight desde Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Una vez conectado, inicie la CLI de Hive con el siguiente comando:

   ```
   hive
   ```
2. Mediante la CLI, especifique las siguientes instrucciones para crear una tabla nueva denominada **vehicles** con los datos de ejemplo del Almacén de Data Lake:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   Debería ver una salida similar a la siguiente:

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-store-using-hdfs-commands"></a>Acceso al Almacén de Data Lake mediante comandos de HDFS
Una vez que configure el clúster de HDInsight para que use el Almacén de Data Lake, puede usar los comandos de shell de HDFS para acceder al almacén.

En esta sección, aprenderá a usar SSH en un clúster de Linux en HDInsight y ejecutará los comandos HDFS. Si usa un cliente Windows, se recomienda usar **PuTTY**, que se puede descargar en [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, consulte [Uso de SSH con Hadoop basado en Linux en HDInsight desde Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Una vez conectado, utilice el siguiente comando del sistema de archivos HDFS para enumerar los archivos del Almacén de Data Lake.

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

Se debería incluir el archivo que cargó antes en el Almacén de Data Lake.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

También puede usar el comando `hdfs dfs -put` para cargar algunos archivos en el almacén de Data Lake y después usar `hdfs dfs -ls` para comprobar si los archivos se cargaron correctamente.


## <a name="next-steps"></a>Pasos siguientes
* [Copiar datos de los blobs de Azure Storage en el Almacén Data Lake](data-lake-store-copy-data-wasb-distcp.md)
* [Uso de Data Lake Store con clústeres de Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
