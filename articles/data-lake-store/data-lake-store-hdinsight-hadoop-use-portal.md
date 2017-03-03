---
title: Uso de un explorador web para crear Azure HDInsight y Data Lake Store | Microsoft Docs
description: "Uso del Portal de Azure para crear y uso de clústeres de HDInsight con el Almacén de Azure Data Lake"
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
ms.date: 02/16/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9e480c13f48e93da32ff5a3c8d3064e98fed0265
ms.openlocfilehash: 0ec19832d395547e8ebd3eee0d44dcf466a2ace7
ms.lasthandoff: 02/17/2017


---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-portal"></a>Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure
> [!div class="op_single_selector"]
> * [Uso del Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Uso de PowerShell (como almacenamiento predeterminado)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Uso de PowerShell (como almacenamiento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Uso de Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Aprenda a usar Azure Portal para crear un clúster de HDInsight con acceso a Azure Data Lake Store. Para los tipos de clúster compatibles, Data Lake Store se puede usar como un almacenamiento predeterminado o una cuenta de almacenamiento adicional. Cuando Data Lake Store se usa como almacenamiento adicional, la cuenta de almacenamiento predeterminada para los clústeres seguirá siendo Azure Storage Blobs (WASB) y los archivos relacionados con clústeres (como registros, etc.) seguirán escribiéndose en el almacenamiento predeterminado, mientras que los datos que quiere procesar pueden almacenarse en una cuenta de Data Lake Store. Utilizar el Almacén de Data Lake como una cuenta de almacenamiento adicional no afecta al rendimiento o la capacidad de lectura y escritura en el almacenamiento del clúster.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Uso de Data Lake Store como almacenamiento de clúster de HDInsight

Estas son algunas consideraciones importantes que deben tenerse en cuenta al usar HDInsight con Data Lake Store:

* La opción para crear clústeres de HDInsight con acceso a Data Lake Store como almacenamiento predeterminado está disponible para HDInsight versión 3.5.

* La opción para crear clústeres de HDInsight con acceso a Data Lake Store como almacenamiento está disponible para las versiones 3.2, 3.4 y 3.5. de HDInsight.

* En clústeres HBase (Windows y Linux), Azure Data Lake Store **no se admite** como una opción de almacenamiento, ya sea almacenamiento predeterminado o almacenamiento adicional.

* En clústeres de Storm (Windows y Linux), Data Lake Store se puede usar para escribir datos de una topología de Storm. Almacén de Data Lake también puede utilizarse para almacenar datos de referencia que luego puede leer una topología de Storm. Para obtener más información, consulte [Usar el Almacén de Data Lake en una topología de Storm](#use-data-lake-store-in-a-storm-topology).


## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Cuenta del Almacén de Azure Data Lake**. Siga las instrucciones que se describen en [Introducción al Almacén de Azure Data Lake mediante el Portal de Azure](data-lake-store-get-started-portal.md).

* **Entidad de servicio de Azure Active Directory** En los pasos de este tutorial se proporcionan instrucciones sobre cómo crear entidades de servicio de Azure AD. Sin embargo, debe ser administrador de Azure AD para poder crearlas. Si ya lo es, puede hacer caso omiso a este requisito previo y continuar con el tutorial.

    **Si no lo es**, no podrá realizar los pasos necesarios para crear una entidad de servicio. En este caso, su administrador de Azure AD debe generar primero una entidad de servicio antes de crear un clúster de HDInsight con Data Lake Store. Además, la entidad de servicio debe crearse con un certificado, tal y como se describe en [Creación de una entidad de servicio](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

## <a name="create-an-hdinsight-cluster-with-access-to-azure-data-lake-store"></a>Creación de un clúster de HDInsight con acceso al Almacén de Azure Data Lake
En esta sección, se crea un clúster de Hadoop en HDInsight que usa el Almacén de Data Lake como almacenamiento adicional. En esta versión, para un clúster de Hadoop, el Almacén de Data Lake solo sirve como almacenamiento adicional para el clúster. El almacenamiento predeterminado seguirá siendo los blobs de almacenamiento de Azure (WASB). En primer lugar, crearemos la cuenta de almacenamiento y los contenedores de almacenamiento necesarios para el clúster.

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).

2. Siga los pasos descritos en [Creación de clústeres de Hadoop en HDInsight](../hdinsight/hdinsight-provision-clusters.md) para iniciar el aprovisionamiento de un clúster de HDInsight.

3. En la hoja **Almacenamiento**, especifique si desea Azure Storage (WASB) o Data Lake Store como almacenamiento predeterminado. Si desea usar Azure Data Lake Store como almacenamiento predeterminado, vaya al paso siguiente.

    Si desea utilizar Azure Storage Blobs como almacenamiento predeterminado, para el **tipo de almacenamiento principal**, haga clic en **Azure Storage**. Después de eso, en **Método de selección**, puede elegir **Mis suscripciones** si desea especificar una cuenta de almacenamiento que forma parte de su suscripción de Azure y después seleccione la cuenta de almacenamiento. En caso contrario, haga clic en **Clave de acceso** y proporcione la información de la cuenta de almacenamiento que desea elegir fuera de la suscripción de Azure. En **Contenedor predeterminado**, puede optar por el nombre de contenedor predeterminado que se sugiere en el portal o especificar uno propio. 

    Cuando se usa Azure Storage Blob como almacenamiento predeterminado, puede seguir utilizando Azure Data Lake Store como almacenamiento adicional para el clúster. Para ello, haga clic en **Acceso a Data Lake Store** y después vaya al paso 5.

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Incorporación de una entidad de servicio a un clúster de HDInsight")


4. Si desea utilizar Azure Data Lake Store como almacenamiento predeterminado, para el **tipo de almacenamiento principal**, haga clic en **Data Lake Store**. Seleccione una cuenta existente de Data Lake Store; especifique la ruta de acceso de la carpeta raíz en la que se van a guardan los archivos específicos del clúster; establezca **Ubicación** en **Este de EE. UU. 2**, y haga clic en **Acceso a Data Lake Store**. Puede usar esta opción solo con clústeres de HDInsight 3.5.(Standard Edition). En los clústeres de HDInsight 3.5 esta opción no está disponible para el tipo de clúster de HBase.

    En la siguiente captura de pantalla, la ruta de acceso de la carpeta raíz es /clusters/myhdiadlcluster, donde **myhdiadlcluster** es el nombre del clúster que se está creando. En tal caso, asegúrese de que la carpeta **/clústeres** ya existe en la cuenta Data Lake Store. La carpeta **myhdiadlcluster** se creará durante la creación del clúster. De forma similar, si la ruta de acceso raíz se estableció en /hdinsight/clusters/data/myhdiadlcluter, debe asegurarse de que **/hdinsight/clústeres/datos/** ya existe en la cuenta de Data Lake Store.

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

5. En la hoja **Acceso a Data Lake Store**, puede seleccionar una entidad de servicio existente o crear una nueva. Si desea usar a una entidad de servicio existente, vaya al paso siguiente.

    Si desea crear una entidad de servicio nueva, en la hoja **Acceso a Data Lake Store**, haga clic en **Crear nuevo** y en **Entidad de servicio**. A continuación, en la hoja **Crear entidad de servicio**, especifique los valores correspondientes para crear la nueva entidad de servicio. En este paso, también se crean un certificado y una aplicación de Azure Active Directory. Haga clic en **Crear**.

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

    También puede hacer clic en **Descargar certificado** para descargar el certificado asociado a la entidad de servicio creada. Esto es útil si desea usar la misma entidad de servicio en el futuro, cuando cree más clústeres de HDInsight. Haga clic en **Seleccionar**.

6. Si desea usar una entidad de servicio existente, en la hoja **Acceso a Data Lake Store**, haga clic en **Usar existente** y en **Entidad de servicio**. A continuación, en la hoja **Seleccionar entidad de servicio**, busque una entidad de servicio existente. Haga clic en un nombre de entidad de servicio y después en **Seleccionar**.

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

    En la hoja **Acceso a Data Lake Store**, cargue el certificado (.pfx) asociado a la entidad de servicio seleccionada y especifique la contraseña del certificado.

6. En la hoja **Acceso a Data Lake Store**, haga clic en **Acceso**. En el siguiente panel, la opción **Seleccionar permisos de archivo** ya está seleccionada de forma predeterminada y enumera todas las cuentas de Data Lake Store de la suscripción. Haga clic en la cuenta de Data Lake Store que desea asociar al clúster para enumerar los archivos y carpetas de esa cuenta. A continuación, puede asignar permisos en el nivel de archivo o carpeta. Si desea asociar los permisos en el nivel raíz de la cuenta, active la casilla situada junto al nombre de cuenta.

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

    > [!NOTE]
    > Si está utilizando la cuenta de Data Lake Store como almacenamiento predeterminado para un clúster, **debe** asignar los permisos a la entidad de servicio en el nivel raíz de la cuenta de Data Lake Store.

7. Si desea asignar permisos para archivos o carpetas dentro de una cuenta, seleccione la cuenta de Data Lake Store para ver los archivos o carpetas en el panel siguiente. Seleccione los archivos o carpetas, seleccione los permisos (LECTURA/ESCRITURA/EJECUCIÓN) que desea asignar en ellas, especifique si los permisos se aplican de forma recursiva a los elementos secundarios y luego haga clic en **Seleccionar**.

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

8. En la siguiente pantalla, haga clic en **Ejecutar** para asignar los permisos para la entidad de servicio de Azure Active Directory en la cuenta, el archivo o la carpeta que seleccionó.

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

9. Una vez que el permiso se ha asignado correctamente, haga clic en **Listo** en todas las hojas hasta que regrese a la hoja **Acceso a Data Lake Store**.

4. En **Acceder a Data Lake Store**, haga clic en **Seleccionar** y continúe con la creación del clúster, tal y como se describe en [Creación de clústeres de Hadoop en HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

10. Una vez aprovisionado el clúster, puede comprobar que el almacenamiento que tiene asociado es la cuenta de Data Lake Store especificada. Para ello, puede hacer clic en la pestaña **Cuentas de almacenamiento** de la hoja del clúster. 

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

    También puede comprobar si la entidad de servicio está asociada con el clúster de HDInsight. Para ello, en la hoja del clúster, haga clic en **Acceso a Data Lake Store** para ver la entidad de servicio asociada.

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

## <a name="show-me-some-examples"></a>Estos son algunos ejemplos:

Una vez que aprovisionado el clúster con el Data Lake Store como almacenamiento, estos son algunos ejemplos sobre cómo usar el clúster de HDInsight para analizar los datos almacenados en Data Lake Store.

### <a name="run-a-hive-query-against-data-stored-in-data-lake-store-as-primary-storage"></a>Ejecución de una consulta de Hive en datos almacenados en Data Lake Store (almacenamiento principal)

Para ejecutar una consulta de Hive, puede usar la interfaz de vistas de Hive disponible desde el portal de Ambari. Para instrucciones sobre cómo usar las vistas de Hive de Ambari, consulte [Utilice la vista de Hive con Hadoop en HDInsight](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md). Hay un par de cosas que tendrá que cambiar cuando trabaje con datos de Data Lake Store.

* Si toma el ejemplo del clúster que creó con Data Lake Store como almacenamiento principal, la ruta de acceso a los datos será `adl://<data_lake_store_account_name>/azuredatalakestore.net/path/to/file`. Una consulta de Hive para crear una tabla a partir de los datos de ejemplo almacenados en la cuenta de Data Lake Store tendrá un aspecto similar al siguiente:

        CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

En la consulta anterior,

* `adl://hdiadlstorage.azuredatalakestore.net/` es la raíz de la cuenta de Data Lake Store.
* `/clusters/myhdiadlcluster` es la raíz de los datos de clúster que especificó al crear el clúster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` es la ubicación del archivo de ejemplo que utiliza en la consulta.

### <a name="run-a-hive-query-against-data-stored-in-data-lake-store-as-additional-storage"></a>Ejecución de una consulta de Hive en datos almacenados en Data Lake Store (almacenamiento adicional)

Si el clúster que creó usa Azure Storage (WASB) como almacenamiento predeterminado, los datos de ejemplo no estarán en la cuenta de Azure Data Lake Store de almacenamiento adicional. En tales casos, debe transferir primero los datos de WASB a Azure Data Lake Store y, a continuación, ejecutar las consultas como se indicó anteriormente.

Para obtener información sobre la copia de datos de WASB a Azure Data Lake Store, consulte:

* [Uso de Distcp para copiar datos entre los blobs de Azure Storage y Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Uso de AdlCopy para copiar datos de los blobs de Azure Storage a Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md) 

### <a name="use-data-lake-store-with-spark-cluster"></a>Uso del Almacén de Data Lake con un clúster de Spark
Puede usar un clúster de Spark para ejecutar trabajos de Spark en los datos que se almacenan en Data Lake Store. Para instrucciones al respecto, consulte [Use HDInsight Spark cluster to analyze data in Data Lake Store](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md) (Uso de un clúster de Spark en HDInsight para analizar datos de Data Lake Store).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Usar el Almacén de Data Lake en una topología de Storm
El Almacén de Data Lake se puede usar para escribir datos de una topología de Storm. Para obtener instrucciones sobre cómo posibilitar este escenario, consulte [Uso del Almacén de Azure Data Lake con Apache Storm con HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## <a name="see-also"></a>Consulte también
* [Aprovisionamiento de un clúster de HDInsight con el Almacén de Data Lake mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

