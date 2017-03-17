---
title: "PowerShell: Clúster de HDInsight de Azure con Data Lake Store como almacenamiento predeterminado | Microsoft Docs"
description: "Uso de Azure PowerShell para crear y usar clústeres de HDInsight con Azure Data Lake"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 01c75f20909c7334981bf407e775656476972276
ms.lasthandoff: 03/03/2017


---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-default-storage"></a>Uso de Azure PowerShell para crear y usar clústeres de HDInsight con Data Lake Store (como almacenamiento predeterminado)
> [!div class="op_single_selector"]
> * [Uso del Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Uso de PowerShell (para el almacenamiento predeterminado)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Uso de PowerShell (para el almacenamiento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Uso de Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Aprenda a usar Azure PowerShell para configurar un clúster de HDInsight con acceso a Azure Data Lake Store **como almacenamiento predeterminado**. Para obtener instrucciones sobre cómo crear un clúster de HDInsight con Azure Data Lake Store como almacenamiento adicional, consulte [Creación de un clúster de HDInsight con Data Lake Store como almacenamiento adicional](data-lake-store-hdinsight-hadoop-use-powershell.md).

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Uso de Data Lake Store como almacenamiento de clúster de HDInsight

Estas son algunas consideraciones importantes que deben tenerse en cuenta al usar HDInsight con Data Lake Store:

* La opción para crear clústeres de HDInsight con acceso a Data Lake Store como almacenamiento predeterminado está disponible para HDInsight versión 3.5.

* La opción para crear clústeres de HDInsight con acceso a Data Lake Store como almacenamiento predeterminado no está disponible para clústeres de HDInsight Premium.

* En clústeres HBase (Windows y Linux), Azure Data Lake Store **no se admite** como una opción de almacenamiento, ya sea almacenamiento predeterminado o almacenamiento adicional.


La configuración de HDInsight para trabajar con el Almacén de Data Lake mediante PowerShell consta de los pasos siguientes:

* Creación de un Almacén de Azure Data Lake
* Configuración de la autenticación para el acceso basado en roles al Almacén de Data Lake
* Creación de un clúster de HDInsight con autenticación en el Almacén de Data Lake
* Ejecución de un trabajo de prueba en el clúster

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versiones posteriores**. Consulte [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs).
* **Windows SDK**. Puede instalarlo desde [aquí](https://dev.windows.com/en-us/downloads). Úselo para crear un certificado de seguridad.
* **Entidad de servicio de Azure Active Directory** En los pasos de este tutorial se proporcionan instrucciones sobre cómo crear entidades de servicio de Azure AD. Sin embargo, debe ser administrador de Azure AD para poder crearlas. Si ya lo es, puede hacer caso omiso a este requisito previo y continuar con el tutorial.

    **Si no lo es**, no podrá realizar los pasos necesarios para crear una entidad de servicio. En este caso, su administrador de Azure AD debe generar primero una entidad de servicio antes de crear un clúster de HDInsight con Data Lake Store. Además, la entidad de servicio debe crearse con un certificado, tal y como se describe en [Creación de una entidad de servicio](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

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
   > Si recibe un error similar a `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` al registrar el proveedor de recursos del Almacén de Data Lake, es posible que su suscripción no esté en la lista de permitidas del Almacén de Azure Data Lake. Asegúrese de habilitar su suscripción de Azure en la versión preliminar pública de Data Lake Store siguiendo estas [instrucciones](data-lake-store-get-started-portal.md).
   >
   >
2. La cuenta de Almacén de Azure Data Lake se asocia con un grupo de recursos de Azure. Comience creando un grupo de recursos de Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Creación de un grupo de recursos de Azure](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Creación de un grupo de recursos de Azure")
3. Cree una cuenta de Almacén de Azure Data Lake. El nombre de cuenta que especifique debe contener solo letras minúsculas y números.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Creación de una cuenta de Azure Data Lake](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Creación de una cuenta de Azure Data Lake")
4. Compruebe que la cuenta se creó correctamente.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    El resultado debe ser **True**.

5. El uso de Data Lake Store como almacenamiento predeterminado requiere especificar una ruta de acceso raíz en la que se copiarán los archivos específicos del clúster durante su creación. Use los cmdlets siguientes para crear una ruta de acceso raíz, que en el siguiente fragmento es **/clusters/hdiadlcluster**.

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configuración de la autenticación para el acceso basado en roles al Almacén de Data Lake
Cada una de las suscripciones de Azure está asociada a un Azure Active Directory. Los usuarios y los servicios que acceden a los recursos de la suscripción mediante el Portal de Azure clásico o la API de Azure Resource Manager deben autenticarse primero en ese Azure Active Directory. Para conceder acceso a servicios y suscripciones de Azure, se les asigna el rol adecuado en un recurso de Azure.  Para los servicios, una entidad de servicio identifica al servicio en Azure Active Directory (AAD). En esta sección, se muestra cómo conceder a un servicio de aplicación, como HDInsight, acceso a un recurso de Azure (la cuenta de Almacén de Azure Data Lake creada antes) mediante la creación de una entidad de servicio para la aplicación y la asignación de roles a ella a través de Azure PowerShell.

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
3. Conceda a la entidad de servicio acceso a la raíz de Data Lake Store y a todas las carpetas de la ruta de acceso raíz que especificó anteriormente. Use los siguientes cmdlets.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-default-storage"></a>Creación de un clúster de HDInsight en Linux con Data Lake Store como almacenamiento predeterminado

En esta sección, vamos a crear un clúster de Hadoop en HDInsight basado en Linux con Data Lake Store como almacenamiento predeterminado. En esta versión, el clúster de HDInsight y Data Lake Store deben estar en la misma ubicación.

1. Comience recuperando el identificador del inquilino de la suscripción. Lo necesitará más adelante.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
        
2. Cree el clúster de HDInsight. Use los siguientes cmdlets.

        # Set these variables
        
        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                          # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.5" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    Una vez que el cmdlet se complete correctamente, debería ver un resultado con la información del clúster.

        
## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Ejecución de trabajos de prueba en el clúster de HDInsight para usar el Almacén de Data Lake
Después de configurar un clúster de HDInsight, puede ejecutar trabajos de prueba en el clúster para probar que el clúster de HDInsight pueda acceder al Almacén de Data Lake. Para hacerlo, ejecutaremos un trabajo de Hive de ejemplo que crea una tabla con los datos de ejemplo que ya están disponibles en Data Lake Store en **<cluster root>/example/data/sample.log**.

En esta sección, aprenderá a usar SSH en el clúster de HDInsight en Linux que creó y ejecutará una consulta de Hive de ejemplo.

* Si usa un cliente Windows para usar SSH en el clúster, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Si usa un cliente Linux para usar SSH en el clúster, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Una vez conectado, inicie la CLI de Hive con el siguiente comando:

        hive
2. Mediante la CLI, especifique las siguientes instrucciones para crear una tabla nueva denominada **vehicles** con los datos de ejemplo del Almacén de Data Lake:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Debería ver el resultado de la consulta en la consola SSH. 

    > [!NOTE]
       > La ruta de acceso a los datos de ejemplo del comando CREATE TABLE anterior es `adl:///example/data/`, donde `adl:///` es la raíz de clúster. Si tomamos como ejemplo la raíz de clúster especificada en este tutorial, será `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Por lo tanto, puede usar la alternativa más corta o proporcionar la ruta de acceso a la raíz de clúster completa. 
       >
       >



## <a name="access-data-lake-store-using-hdfs-commands"></a>Acceso al Almacén de Data Lake mediante comandos de HDFS
Una vez que configure el clúster de HDInsight para que use el Almacén de Data Lake, puede usar los comandos de shell de HDFS para acceder al almacén.

En esta sección, aprenderá a usar SSH en el clúster de Linux en HDInsight que creó y ejecutará los comandos de HDFS. 

* Si usa un cliente Windows para usar SSH en el clúster, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Si usa un cliente Linux para usar SSH en el clúster, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Una vez conectado, utilice el siguiente comando del sistema de archivos HDFS para enumerar los archivos del Almacén de Data Lake.

    hdfs dfs -ls adl:///

También puede usar el comando `hdfs dfs -put` para cargar algunos archivos en el almacén de Data Lake y después usar `hdfs dfs -ls` para comprobar si los archivos se cargaron correctamente.

## <a name="see-also"></a>Otras referencias
* [Aprovisionamiento de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de vista previa de Azure](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

