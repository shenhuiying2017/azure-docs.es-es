---
title: "Uso de Azure Portal para crear clústeres de Azure HDInsight con Data Lake Store | Microsoft Docs"
description: "Uso de Azure Portal para crear y usar clústeres de HDInsight con Azure Data Lake Store"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 4a27ae1402717c91029eda9d635db124f8bb6b8d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>Creación de clústeres de HDInsight con Data Lake Store mediante Azure Portal
> [!div class="op_single_selector"]
> * [Uso de Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Uso de PowerShell (para el almacenamiento predeterminado)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Uso de PowerShell (para almacenamiento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Uso de Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Aprenda a usar Azure Portal para crear un clúster de HDInsight con una cuenta de Azure Data Lake Store como almacenamiento predeterminado o almacenamiento adicional. Aunque el almacenamiento adicional es opcional para los clústeres de HDInsight, se recomienda almacenar los datos empresariales en las cuentas de almacenamiento adicional.

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar este tutorial, asegúrese de que ha cumplido los requisitos siguientes:

* **Una suscripción de Azure**. Vaya a [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Una cuenta de Almacén de Azure Data Lake**. Siga las instrucciones que se describen en [Introducción a Azure Data Lake Store mediante Azure Portal](data-lake-store-get-started-portal.md). También debe crear una carpeta raíz en la cuenta.  En este tutorial se usa una carpeta raíz llamada __/clusters__.
* **Una entidad de servicio de Azure Active Directory**. En este tutorial se proporcionan instrucciones sobre cómo crear entidades de servicio en Azure Active Directory (Azure AD). Sin embargo, para crear una entidad de servicio, debe ser administrador de Azure AD. Si ya lo es, puede hacer caso omiso a este requisito previo y continuar con el tutorial.

    >[!NOTE]
    >Solamente puede crear una entidad de servicio si es administrador de Azure AD. Su administrador de Azure AD debe generar una entidad de servicio antes de crear un clúster de HDInsight con Data Lake Store. Además, la entidad de servicio debe crearse con un certificado, tal y como se describe en [Creación de entidad de servicio con certificado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>Creación de un clúster de HDInsight

En esta sección creará un clúster de HDInsight con cuentas de Data Lake Store como almacenamiento predeterminado o adicional. Este artículo trata únicamente la parte de la configuración de cuentas de Data Lake Store.  Para obtener información general sobre la creación de clústeres y sobre los procedimientos, consulte [Creación de clústeres de Hadoop en HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-store-as-default-storage"></a>Creación de un clúster con Data Lake Store como almacenamiento predeterminado

**Para crear un clúster de HDInsight con Data Lake Store como cuenta de almacenamiento predeterminada**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Siga [Creación de clústeres](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obtener información general sobre cómo crear clústeres de HDInsight.
3. En la hoja **Almacenamiento**, en **Tipo de almacenamiento principal**, seleccione **Data Lake Store** y escriba la siguiente información:

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

    - **Seleccionar la cuenta de Data Lake Store**: seleccione una cuenta existente de Data Lake Store. Se necesita una cuenta existente de Data Lake Store.  Consulte [Requisitos previos](#prereuisites).
    - **Ruta de acceso raíz**: escriba una ruta de acceso en la que se almacenarán los archivos específicos del clúster. En la captura de pantalla, es __/clusters/myhdiadlcluster/__, donde la carpeta __/clusters__ debe existir y el portal crea la carpeta *myhdicluster*.  *myhdicluster* es el nombre del clúster.
    - **Acceso a Data Lake Store**: configure el acceso entre la cuenta de Data Lake Store y el clúster de HDInsight. Para obtener instrucciones, consulte [Configuración del acceso a Data Lake Store](#configure-data-lake-store-access).
    - **Cuentas de almacenamiento adicionales**: agregue cuentas de Azure Storage como cuentas de almacenamiento adicionales para el clúster. Para agregar más almacenes de Data Lake Store, asigne al clúster permisos de datos en más cuentas de Data Lake Store al configurar una cuenta de Data Lake Store como tipo de almacenamiento principal. Consulte [Configuración del acceso a Data Lake Store](#configure-data-lake-store-access).

4. En **Acceso a Data Lake Store**, haga clic en **Seleccionar** y continúe con la creación del clúster, tal y como se describe en [Creación de clústeres de Hadoop en HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-store-as-additional-storage"></a>Creación de un clúster con Data Lake Store como almacenamiento adicional

En las siguientes instrucciones se describe cómo crear un clúster de HDInsight con una cuenta de Azure Storage como almacenamiento predeterminado y una cuenta de Data Lake Store como almacenamiento adicional.
**Para crear un clúster de HDInsight con Data Lake Store como cuenta de almacenamiento predeterminada**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Siga [Creación de clústeres](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obtener información general sobre cómo crear clústeres de HDInsight.
3. En la hoja **Almacenamiento**, en **Tipo de almacenamiento principal**, seleccione **Azure Storage** y escriba la siguiente información:

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

    - **Método de selección**: use una de las siguientes opciones:

        * Para especificar una cuenta de almacenamiento que forma parte de su suscripción de Azure, seleccione **Mis suscripciones** y después seleccione la cuenta de almacenamiento.
        * Para especificar una cuenta de almacenamiento que está fuera de su suscripción de Azure, seleccione **Clave de acceso** y luego proporcione la información de la cuenta de almacenamiento externa.

    - **Contenedor predeterminado**: use el valor predeterminado o especifique su propio nombre.

    - Cuentas de almacenamiento adicionales: agregue más cuentas de Azure Storage como almacenamiento adicional.
    - Acceso a Data Lake Store: configure el acceso entre la cuenta de Data Lake Store y el clúster de HDInsight. Para obtener instrucciones, consulte [Configuración del acceso a Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Configuración del acceso a Data Lake Store 

En esta sección podrá configurar el acceso a Data Lake Store desde los clústeres de HDInsight mediante una entidad de servicio de Azure Active Directory. 

### <a name="specify-a-service-principal"></a>Especificar una entidad de servicio

En Azure Portal puede usar una entidad de servicio existente o crear una.

**Para crear una entidad de servicio desde Azure Portal**

1. Haga clic en **Acceso a Data Lake Store** en la hoja Almacenamiento.
2. En la hoja **Acceso a Data Lake Store**, haga clic en **Crear nuevo**.
3. Haga clic en **Entidad de servicio** y siga las instrucciones para crear una entidad de servicio.
4. Descargue el certificado si decide volver a usarlo en el futuro. La descarga del certificado es útil si desea usar la misma entidad de servicio cuando cree clústeres de HDInsight adicionales.

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

4. Haga clic en **Acceso** para configurar el acceso a la carpeta.  Consulte [Configurar los permisos de los archivos](#configure-file-permissions).


**Para usar una entidad de servicio existente en Azure Portal**

1. Haga clic en **Acceso a Data Lake Store**.
1. En la hoja **Acceso a Data Lake Store**, haga clic en **Usar existente**.
2. Haga clic en **Entidad de servicio** y seleccione una entidad de servicio. 
3. Cargue el certificado (archivo .pfx) asociado a la entidad de servicio seleccionada y especifique la contraseña del certificado.

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

4. Haga clic en **Acceso** para configurar el acceso a la carpeta.  Consulte [Configurar los permisos de los archivos](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Configurar los permisos de los archivos

Las configuraciones son diferentes dependiendo de si la cuenta se usa como almacenamiento predeterminado o como cuenta de almacenamiento adicional:

- Uso como almacenamiento predeterminado

    - Permiso en el nivel raíz de la cuenta de Data Lake Store
    - Permiso en el nivel raíz del almacenamiento de clúster de HDInsight. Por ejemplo, la carpeta __/clusters__ que se ha usado antes en el tutorial.
- Uso como almacenamiento adicional

    - Permiso en las carpetas en las que necesita acceso de archivo.

**Para asignar permisos en el nivel raíz de la cuenta de Data Lake Store**

1. En la hoja **Acceso a Data Lake Store**, haga clic en **Acceso**. Se abrirá la hoja **Seleccionar permisos de archivo**. En ella se muestran todas las cuentas de Data Lake Store de su suscripción.
2. Pase el mouse (sin hacer clic) sobre el nombre de la cuenta de Data Lake Store para que aparezca la casilla y, luego, actívela.

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

  De forma predeterminada, los permisos __LECTURA__, __ESCRITURA__ Y __EJECUCIÓN__ están seleccionados.

3. Haga clic en **Seleccionar** en la parte inferior de la página.
4. Haga clic en **Ejecutar** para asignar el permiso.
5. Haga clic en **Done**(Listo).

**Para asignar permisos en el nivel raíz del clúster de HDInsight**

1. En la hoja **Acceso a Data Lake Store**, haga clic en **Acceso**. Se abrirá la hoja **Seleccionar permisos de archivo**. En ella se muestran todas las cuentas de Data Lake Store de su suscripción.
1. En la hoja **Seleccionar permisos de archivo**, haga clic en el nombre de Data Lake Store para mostrar su contenido.
2. Seleccione la raíz del almacenamiento de clúster de HDInsight activando la casilla situada a la izquierda de la carpeta. Según la captura de pantalla anterior, la raíz de almacenamiento del clúster es la carpeta __/clusters__ que especificó al seleccionar Data Lake Store como almacenamiento predeterminado.
3. Establezca los permisos en la carpeta.  De forma predeterminada, los permisos LECTURA, ESCRITURA Y EJECUCIÓN están seleccionados.
4. Haga clic en **Seleccionar** en la parte inferior de la página.
5. Haga clic en **Ejecutar**.
6. Haga clic en **Done**(Listo).

Si usa Data Lake Store como almacenamiento adicional, debe asignar permisos solo para las carpetas a las que quiere obtener acceso desde el clúster de HDInsight. Por ejemplo, en la siguiente captura de pantalla, se proporciona acceso únicamente a la carpeta **hdiaddonstorage** en una cuenta de Data Lake Store.

![Asignación de permisos de entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Asignación de permisos de entidad de servicio al clúster de HDInsight")


## <a name="verify-cluster-set-up"></a>Comprobación de la configuración del clúster

Una vez concluida la configuración del clúster, en la hoja del clúster, compruebe los resultados siguiendo uno de los siguientes pasos o ambos:

* Para comprobar que el almacenamiento asociado para el clúster es la cuenta de Data Lake Store especificada, haga clic en **Cuentas de almacenamiento** en el panel izquierdo.

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

* Para comprobar que la entidad de servicio está correctamente asociada con el clúster de HDInsight, haga clic en **Acceso a Data Lake Store** en el panel izquierdo.

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Incorporación de una entidad de servicio a un clúster de HDInsight")


## <a name="examples"></a>Ejemplos

Después de haber configurado el clúster con Data Lake Store como almacenamiento, consulte estos ejemplos sobre cómo usar el clúster de HDInsight para analizar los datos que están almacenados en Data Lake Store.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>Ejecución de una consulta de Hive en datos de Data Lake Store (como almacenamiento principal)

Para ejecutar una consulta de Hive, use la interfaz de vistas de Hive en el portal de Ambari. Para instrucciones sobre cómo usar las vistas de Hive de Ambari, consulte [Uso de Vista de Hive con Hadoop en HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Cuando se trabaja con datos de Data Lake Store, hay algunas cadenas que se deben cambiar.

Si utiliza, por ejemplo, el clúster que creó con Data Lake Store como almacenamiento principal, la ruta de acceso a los datos es: *adl://<nombre_cuenta_data_lake_store>/azuredatalakestore.net/path/to/file*. Las consultas de Hive para crear una tabla a partir de los datos de ejemplo almacenados en la cuenta de Data Lake Store tienen un aspecto similar a la siguiente instrucción:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Descripciones:
* `adl://hdiadlstorage.azuredatalakestore.net/` es la raíz de la cuenta de Data Lake Store.
* `/clusters/myhdiadlcluster` es la raíz de los datos de clúster que especificó al crear el clúster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` es la ubicación del archivo de ejemplo que usó en la consulta.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>Ejecución de una consulta de Hive en datos de Data Lake Store (como almacenamiento adicional)

Si el clúster que creó usa Blob Storage como almacenamiento predeterminado, los datos de ejemplo no están contenidos en la cuenta de Azure Data Lake Store que se usa como almacenamiento adicional. En tal caso, transfiera primero los datos desde Blob Storage a Data Lake Store y luego ejecute las consultas tal y como se muestra en el ejemplo anterior.

Para información sobre la copia de datos de Blob Storage a Data Lake Store, consulte los siguientes artículos:

* [Uso de Distcp para copiar datos entre los blobs de Azure Storage y Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Uso de AdlCopy para copiar datos de los blobs de Azure Storage a Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>Uso de Data Lake Store con un clúster de Spark
Puede usar un clúster de Spark para ejecutar trabajos de Spark en los datos que se almacenan en Data Lake Store. Para más información, consulte [Uso de un clúster de HDInsight Spark para analizar los datos en Data Lake Store](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Usar el Almacén de Data Lake en una topología de Storm
El Almacén de Data Lake se puede usar para escribir datos de una topología de Storm. Para obtener instrucciones sobre cómo posibilitar este escenario, consulte [Uso del Almacén de Azure Data Lake con Apache Storm con HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Otras referencias
* [Uso de Data Lake Store con clústeres de Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Aprovisionamiento de un clúster de HDInsight con el Almacén de Data Lake mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
