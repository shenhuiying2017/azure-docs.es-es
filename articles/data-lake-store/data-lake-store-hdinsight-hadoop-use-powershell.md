---
title: "PowerShell: clúster de Azure HDInsight con Data Lake Store como almacenamiento complementario | Microsoft Docs"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/01/2017
ms.author: nitinme
ms.openlocfilehash: dc46e3987f3710bc73ef13a2c195b68f78bd104d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-additional-storage"></a>Uso de Azure PowerShell para crear clústeres de HDInsight con Data Lake Store (como almacenamiento adicional)
> [!div class="op_single_selector"]
> * [Uso del Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Uso de PowerShell (para el almacenamiento predeterminado)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Uso de PowerShell (para el almacenamiento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Uso de Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Aprenda a usar Azure PowerShell para configurar un clúster de HDInsight con Azure Data Lake Store **como almacenamiento adicional**. Para obtener instrucciones acerca de cómo crear un clúster de HDInsight con Azure Data Lake Store como almacenamiento predeterminado, consulte [Creación de un clúster de HDInsight con Data Lake Store mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Si va a usar Azure Data Lake Store como almacenamiento adicional para un clúster de HDInsight, se recomienda encarecidamente hacer esto al crear el clúster como se describe en este artículo. La adición de Azure Data Lake Store como almacenamiento adicional a un clúster de HDInsight existente es un proceso complicado y en la que es fácil que se produzcan errores.
>

En el caso de los tipos de clúster compatibles, Data Lake Store se puede usar como almacenamiento predeterminado o como cuenta de almacenamiento adicional. Cuando Data Lake Store se usa como almacenamiento adicional, la cuenta de almacenamiento predeterminada para los clústeres seguirá siendo Azure Storage Blobs (WASB) y los archivos relacionados con clústeres (como registros, etc.) seguirán escribiéndose en el almacenamiento predeterminado, mientras que los datos que quiere procesar pueden almacenarse en una cuenta de Data Lake Store. Utilizar el Almacén de Data Lake como una cuenta de almacenamiento adicional no afecta al rendimiento o la capacidad de lectura y escritura en el almacenamiento del clúster.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Uso de Data Lake Store para el almacenamiento de clústeres de HDInsight

Estas son algunas consideraciones importantes que deben tenerse en cuenta al usar HDInsight con Data Lake Store:

* La opción para crear clústeres de HDInsight con acceso a Data Lake Store como almacenamiento está disponible para las versiones 3.2, 3.4, 3.5 y 3.6 de HDInsight.

La configuración de HDInsight para trabajar con el Almacén de Data Lake mediante PowerShell consta de los pasos siguientes:

* Creación de un Almacén de Azure Data Lake
* Configuración de la autenticación para el acceso basado en roles al Almacén de Data Lake
* Creación de un clúster de HDInsight con autenticación en el Almacén de Data Lake
* Ejecución de un trabajo de prueba en el clúster

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versiones posteriores**. Consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).
* **Windows SDK**. Puede instalarlo desde [aquí](https://dev.windows.com/en-us/downloads). Úselo para crear un certificado de seguridad.
* **Entidad de servicio de Azure Active Directory** En los pasos de este tutorial se proporcionan instrucciones sobre cómo crear entidades de servicio de Azure AD. Sin embargo, debe ser administrador de Azure AD para poder crearlas. Si ya lo es, puede hacer caso omiso a este requisito previo y continuar con el tutorial.

    **Si no lo es**, no podrá realizar los pasos necesarios para crear una entidad de servicio. En este caso, su administrador de Azure AD debe generar primero una entidad de servicio antes de crear un clúster de HDInsight con Data Lake Store. Además, la entidad de servicio debe crearse con un certificado, tal y como se describe en [Creación de una entidad de servicio](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-azure-data-lake-store"></a>Creación de un Almacén de Azure Data Lake
Siga estos pasos para crear un Almacén de Data Lake.

1. En el escritorio, abra una nueva ventana de Azure PowerShell y escriba el siguiente fragmento. Cuando se le solicite iniciar sesión, asegúrese de iniciarla como uno de los administradores o propietario de la suscripción:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Si recibe un error similar a `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` al registrar el proveedor de recursos de Data Lake Store, es posible que su suscripción no esté en la lista de permitidas de Azure Data Lake Store. Asegúrese de habilitar su suscripción de Azure en la versión preliminar pública de Data Lake Store siguiendo estas [instrucciones](data-lake-store-get-started-portal.md).
   >
   >
2. La cuenta de Almacén de Azure Data Lake se asocia con un grupo de recursos de Azure. Comience creando un grupo de recursos de Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    La salida debe ser parecida a la siguiente:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Cree una cuenta de Almacén de Azure Data Lake. El nombre de cuenta que especifique debe contener solo letras minúsculas y números.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    Debe ver algo parecido a lo siguiente:

        ...
        ProvisioningState           : Succeeded
        State                       : Active
        CreationTime                : 5/5/2017 10:53:56 PM
        EncryptionState             : Enabled
        ...
        LastModifiedTime            : 5/5/2017 10:53:56 PM
        Endpoint                    : hdiadlstore.azuredatalakestore.net
        DefaultGroup                :
        Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
        Name                        : hdiadlstore
        Type                        : Microsoft.DataLakeStore/accounts
        Location                    : East US 2
        Tags                        : {}

5. Cargue datos de ejemplo a Azure Data Lake. Los usaremos más adelante en este artículo para comprobar que se puede acceder a los datos desde un clúster de HDInsight. Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configuración de la autenticación para el acceso basado en roles al Almacén de Data Lake
Cada una de las suscripciones de Azure está asociada a un Azure Active Directory. Los usuarios y los servicios que acceden a los recursos de la suscripción mediante Azure Portal o la API de Azure Resource Manager deben autenticarse primero en ese Azure Active Directory. Para conceder acceso a servicios y suscripciones de Azure, se les asigna el rol adecuado en un recurso de Azure.  Para los servicios, una entidad de servicio identifica al servicio en Azure Active Directory (AAD). En esta sección, se muestra cómo conceder a un servicio de aplicación, como HDInsight, acceso a un recurso de Azure (la cuenta de Almacén de Azure Data Lake creada antes) mediante la creación de una entidad de servicio para la aplicación y la asignación de roles a ella a través de Azure PowerShell.

Para configurar la autenticación de Active Directory para Azure Data Lake, debe realizar las siguientes tareas.

* Creación de un certificado autofirmado
* Creación de una aplicación en Azure Active Directory y una entidad de servicio

### <a name="create-a-self-signed-certificate"></a>Creación de un certificado autofirmado
Asegúrese de que tiene [Windows SDK](https://dev.windows.com/en-us/downloads) instalado antes de continuar con los pasos de esta sección. También debe crear un directorio, como **C:\mycertdir**, donde se creará el certificado.

1. En la ventana de PowerShell, vaya a la ubicación donde instaló Windows SDK (normalmente, `C:\Program Files (x86)\Windows Kits\10\bin\x86`) y use la utilidad [MakeCert][makecert] para crear un certificado autofirmado y una clave privada. Use los comandos siguientes.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Se le pedirá que escriba la contraseña de la clave privada. Después de que el comando se ejecute correctamente, debería ver los archivos **CertFile.cer** y **mykey.pvk** en el directorio de certificado especificado.
2. Emplee la utilidad [Pvk2Pfx][pvk2pfx] para convertir los archivos .pvk y .cer que MakeCert creó en un archivo .pfx. Ejecute el siguiente comando.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Cuando se le pida, escriba la contraseña de la clave privada que especificó antes. El valor especificado para el parámetro **-po** es la contraseña asociada al archivo .pfx. Cuando el comando se complete correctamente, debería ver un archivo CertFile.pfx en el directorio de certificado especificado.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Creación de una aplicación en Azure Active Directory y una entidad de servicio
En esta sección, llevará a cabo los pasos para crear una entidad de servicio para una aplicación de Azure Active Directory, asignar un rol a la entidad de servicio y autenticarse como la entidad de servicio proporcionando un certificado. Ejecute los comandos siguientes para crear una aplicación en Azure Active Directory.

1. Pegue los siguientes cmdlets en la ventana de consola de PowerShell. Asegúrese de que el valor especificado para la propiedad **- DisplayName** sea único. Además, los valores de **-HomePage** e **-IdentiferUris** son valores de marcador de posición y no se comprueban.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Cree una entidad de servicio con el identificador de aplicación.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Conceda acceso de entidad de servicio al archivo o la carpeta de Data Lake Store a la que accederá desde el clúster de HDInsight. El fragmento de código siguiente proporciona acceso a la raíz de la cuenta de Data Lake Store (donde copió el archivo de datos de ejemplo) y al propio archivo.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-additional-storage"></a>Creación de un clúster de HDInsight en Linux con Data Lake Store como almacenamiento adicional

En esta sección, se creará un clúster de Hadoop en HDInsight basado en Linux con Data Lake Store como almacenamiento adicional. En esta versión, el clúster de HDInsight y Data Lake Store deben estar en la misma ubicación.

1. Comience recuperando el identificador del inquilino de la suscripción. Lo necesitará más adelante.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
2. En esta versión, para un clúster de Hadoop, el Almacén de Data Lake solo sirve como almacenamiento adicional para el clúster. El almacenamiento predeterminado seguirá siendo los blobs de almacenamiento de Azure (WASB). En primer lugar, crearemos la cuenta de almacenamiento y los contenedores de almacenamiento necesarios para el clúster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext
3. Cree el clúster de HDInsight. Use los siguientes cmdlets.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Una vez que el cmdlet se complete correctamente, debería ver un resultado con la información del clúster.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Ejecución de trabajos de prueba en el clúster de HDInsight para usar el Almacén de Data Lake
Después de configurar un clúster de HDInsight, puede ejecutar trabajos de prueba en el clúster para probar que el clúster de HDInsight pueda acceder al Almacén de Data Lake. Para hacerlo, ejecutaremos un trabajo de Hive de ejemplo que crea una tabla con los datos de ejemplo que cargó antes en el Almacén de Data Lake.

En esta sección, aprenderá a usar SSH en el clúster de Linux en HDInsight que creó y ejecutará una consulta de Hive de ejemplo.

* Si usa un cliente Windows para usar SSH en el clúster, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Si usa un cliente Linux para usar SSH en el clúster, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Una vez conectado, inicie la CLI de Hive con el siguiente comando:

        hive
2. Mediante la CLI, especifique las siguientes instrucciones para crear una tabla nueva denominada **vehicles** con los datos de ejemplo del Almacén de Data Lake:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Debería ver una salida similar a la siguiente:

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

## <a name="access-data-lake-store-using-hdfs-commands"></a>Acceso al Almacén de Data Lake mediante comandos de HDFS
Una vez que configure el clúster de HDInsight para que use el Almacén de Data Lake, puede usar los comandos de shell de HDFS para acceder al almacén.

En esta sección, aprenderá a usar SSH en el clúster de Linux en HDInsight que creó y ejecutará los comandos de HDFS.

* Si usa un cliente Windows para usar SSH en el clúster, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Si usa un cliente Linux para usar SSH en el clúster, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Una vez conectado, utilice el siguiente comando del sistema de archivos HDFS para enumerar los archivos del Almacén de Data Lake.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Se debería incluir el archivo que cargó antes en el Almacén de Data Lake.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

También puede usar el comando `hdfs dfs -put` para cargar algunos archivos en el almacén de Data Lake y después usar `hdfs dfs -ls` para comprobar si los archivos se cargaron correctamente.

## <a name="see-also"></a>Otras referencias
* [Uso de Data Lake Store con clústeres de Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Aprovisionamiento de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de vista previa de Azure](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
