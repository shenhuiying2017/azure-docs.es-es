---
title: "Creación de clústeres de HDInsight con Data Lake Store como almacenamiento predeterminado mediante PowerShell | Microsoft Docs"
description: "Uso de Azure PowerShell para crear y usar clústeres de HDInsight con Azure Data Lake Store"
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
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 2f1793c2de2b68a8b155ada73044c6bc36882612
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-hdinsight-clusters-with-data-lake-store-as-default-storage-by-using-powershell"></a>Creación de clústeres de HDInsight con Data Lake Store como almacenamiento predeterminado mediante PowerShell
> [!div class="op_single_selector"]
> * [Uso de Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Uso de PowerShell (para el almacenamiento predeterminado)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Uso de PowerShell (para almacenamiento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Uso de Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Obtenga información sobre cómo usar Azure PowerShell para configurar clústeres de Azure HDInsight con Azure Data Lake Store, como almacenamiento predeterminado. Para obtener instrucciones sobre la creación de un clúster de HDInsight con Azure Data Lake Store como almacenamiento adicional, consulte [Uso de Azure PowerShell para crear clústeres de HDInsight con Data Lake Store (como almacenamiento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md).

Estas son algunas consideraciones importantes que deben tenerse en cuenta al usar HDInsight con Data Lake Store:

* La opción para crear clústeres de HDInsight con acceso a Data Lake Store como almacenamiento predeterminado está disponible para la versión 3.5 y 3.6 de HDInsight.

* La opción para crear clústeres de HDInsight con acceso a Data Lake Store como almacenamiento predeterminado *no está disponible* para clústeres de HDInsight Premium.

Para configurar HDInsight para trabajar con Data Lake Store con PowerShell, siga las instrucciones que aparecen en las cinco secciones siguientes.

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar este tutorial, asegúrese de que se cumplen los requisitos siguientes:

* **Suscripción a Azure**: Vaya a [Cree su cuenta gratuita de Azure hoy mismo](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o posterior**: Consulte [How to install and configure PowerShell](/powershell/azure/overview) (Instalación y configuración de PowerShell).
* **Kit de desarrollo de software (SDK) de Windows**: Para instalar el SDK de Windows, vaya a [Descargas y herramientas para Windows 10](https://dev.windows.com/en-us/downloads). El SDK se usa para crear un certificado de seguridad.
* **Entidad de servicio de Azure Active Directory**: Este tutorial describe cómo crear una entidad de servicio en Azure Active Directory (Azure AD). Sin embargo, para crear una entidad de servicio, debe ser administrador de Azure AD. Si ya lo es, puede hacer caso omiso a este requisito previo y continuar con el tutorial.

    >[!NOTE]
    >Solamente puede crear una entidad de servicio si es administrador de Azure AD. Su administrador de Azure AD debe generar una entidad de servicio antes de crear un clúster de HDInsight con Data Lake Store. La entidad de servicio se debe crear con un certificado, tal y como se describe en [Creación de entidad de servicio con certificado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-store-account"></a>Crear una cuenta de Almacén de Data Lake
Para crear una cuenta de Data Lake Store, realice el siguiente procedimiento:

1. En el escritorio, abra una ventana de PowerShell y escriba los siguientes fragmentos de código: Cuando se le pida que inicie sesión, hágalo como uno de los propietarios o administradores de la suscripción. 

        # Sign in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Si registra el proveedor de recursos de Data Lake Store y recibe un error similar a `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, su suscripción podría no estar en la lista de permitidas para Data Lake Store. Para habilitar la suscripción de Azure para la versión preliminar pública de Data Lake Store, siga las instrucciones que se encuentran en [Introducción a Azure Data Lake Store mediante Azure Portal](data-lake-store-get-started-portal.md).
    >

2. Una cuenta de Data Lake Store se asocia con un grupo de recursos de Azure. Comience a crear un grupo de recursos.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    La salida debe ser parecida a la siguiente:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Cree una cuenta de Data Lake Store. El nombre de cuenta que especifique solo debe contener letras minúsculas y números.

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

4. El uso de Data Lake Store como almacenamiento predeterminado requiere especificar una ruta de acceso raíz en la que se copiarán los archivos específicos del clúster durante su creación. Para crear una ruta de acceso raíz, que es **/clusters/hdiadlcluster** en el fragmento de código, use los cmdlets siguientes:

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configuración de la autenticación para el acceso basado en roles al Almacén de Data Lake
Cada una de las suscripciones de Azure está asociada a una entidad de Azure AD. Los usuarios y los servicios que acceden a los recursos de la suscripción Azure Portal o la API de Azure Resource Manager deben autenticarse primero en Azure AD. Para conceder acceso a servicios y suscripciones de Azure, se les asigna el rol adecuado en un recurso de Azure. Para los servicios, una entidad de servicio identifica al servicio en Azure AD.

En esta sección se muestra cómo conceder a un servicio de aplicación, como HDInsight, acceso a un recurso de Azure (la cuenta de Data Lake Store que creó anteriormente). La forma de hacerlo es crear una entidad de servicio para la aplicación y asignarla roles a través de PowerShell.

Para configurar la autenticación de Active Directory para Azure Data Lake, realice las tareas en las dos secciones siguientes.

### <a name="create-a-self-signed-certificate"></a>Creación de un certificado autofirmado
Asegúrese de que tiene [Windows SDK](https://dev.windows.com/en-us/downloads) instalado antes de continuar con los pasos de esta sección. También debe crear un directorio, como *C:\mycertdir*, donde creará el certificado.

1. En la ventana de PowerShell, vaya a la ubicación donde instaló Windows SDK (normalmente, *C:\Archivos de programa (x86)\Windows Kits\10\bin\x86*) y use la utilidad [MakeCert][makecert] para crear un certificado autofirmado y una clave privada. Use los comandos siguientes:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Se le pedirá que escriba la contraseña de la clave privada. Después de que el comando se ejecute correctamente, debería ver los archivos **CertFile.cer** y **mykey.pvk** en el directorio de certificado que especificó.
2. Emplee la utilidad [Pvk2Pfx][pvk2pfx] para convertir los archivos .pvk y .cer que MakeCert creó en un archivo .pfx. Ejecute el siguiente comando:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Cuando se le pida, escriba la contraseña de la clave privada que especificó antes. El valor especificado para el parámetro **-po** es la contraseña asociada al archivo .pfx. Cuando el comando se haya completado correctamente, debería ver un archivo **CertFile.pfx** en el directorio de certificado que especificó.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Creación de una aplicación en Azure AD y una entidad de servicio
En esta sección, creará una entidad de servicio para una aplicación de Azure AD, asignará un rol a la entidad de servicio y la autenticará como la entidad de servicio proporcionando un certificado. Para crear una aplicación en Azure AD, ejecute los comandos siguientes:

1. Pegue los siguientes cmdlets en la ventana de consola de PowerShell. Asegúrese de que el valor especificado para la propiedad **- DisplayName** sea único. Los valores de **-HomePage** e **-IdentiferUris** son valores de marcador de posición y no se comprueban.

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
2. Cree una entidad de servicio mediante el identificador de aplicación.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Conceda a la entidad de servicio acceso a la raíz de Data Lake Store y a todas las carpetas de la ruta de acceso raíz que especificó anteriormente. Use los siguientes cmdlets:

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-the-default-storage"></a>Creación de un clúster de HDInsight en Linux con Data Lake Store como almacenamiento predeterminado

En esta sección, creará un clúster de Hadoop en HDInsight basado en Linux con Data Lake Store como almacenamiento predeterminado. En esta versión, el clúster de HDInsight y Data Lake Store deben estar en la misma ubicación.

1. Recupere el identificador de inquilino de suscripción y almacénelo para usarlo más adelante.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Cree el clúster de HDInsight mediante los cmdlets siguientes:

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                       # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
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
               -Version "3.6" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    Cuando el cmdlet se haya completado correctamente, debería ver una salida con la información del clúster.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-store"></a>Ejecución de trabajos de prueba en el clúster de HDInsight para usar Data Lake Store
Después de configurar un clúster de HDInsight, puede ejecutar trabajos de prueba en él para garantizar que puede acceder a Data Lake Store. Para hacerlo, ejecute un trabajo de Hive de ejemplo para crear una tabla que usa los datos de ejemplo que ya están disponibles en Data Lake Store en *<cluster root>/example/data/sample.log*.

En esta sección, realiza una conexión de Secure Shell (SSH) en el clúster de HDInsight en Linux que creó y luego ejecute una consulta de Hive de ejemplo.

* Si usa un cliente Windows para realizar una conexión SSH en el clúster, consulte [Uso de SSH con clústeres de HDInsight desde PuTTY en Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Si usa un cliente Linux para realizar una conexión SSH en el clúster, consulte [Uso de SSH con HDInsight (Hadoop) desde Bash en Windows 10, Linux, Unix u OS X](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Después de haber realizado la conexión, inicie la interfaz de la línea de comandos (CLI) de Hive con el siguiente comando:

        hive
2. Use la CLI para especificar las siguientes instrucciones para crear una tabla nueva denominada **vehicles** con los datos de ejemplo de Data Lake Store:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Debería ver el resultado de la consulta en la consola SSH.

    >[!NOTE]
    >La ruta de acceso a los datos de ejemplo del comando CREATE TABLE anterior es `adl:///example/data/`, donde `adl:///` es la raíz de clúster. Siguiendo el ejemplo de la raíz de clúster especificada en este tutorial, el comando es `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Puede usar la alternativa más corta o proporcionar la ruta de acceso a la raíz de clúster completa.
    >

## <a name="access-data-lake-store-by-using-hdfs-commands"></a>Acceso a Data Lake Store mediante comandos de HDFS
Después de configurar el clúster de HDInsight para que use Data Lake Store, puede usar los comandos de shell del sistema de archivos distribuido de hadoop (HDFS) para acceder al almacén.

En esta sección, realiza una conexión SSH en el clúster de HDInsight en Linux que creó y luego ejecuta los comandos de HDFS.

* Si usa un cliente Windows para realizar una conexión SSH en el clúster, consulte [Uso de SSH con clústeres de HDInsight desde PuTTY en Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Si usa un cliente Linux para realizar una conexión SSH en el clúster, consulte [Uso de SSH con HDInsight (Hadoop) desde Bash en Windows 10, Linux, Unix u OS X](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Después de realizar la conexión, pasa a enumerar los archivos de Data Lake Store mediante el siguiente comando de sistema de archivos de HDFS.

    hdfs dfs -ls adl:///

También puede usar el comando `hdfs dfs -put` para cargar algunos archivos en Data Lake Store y después usar `hdfs dfs -ls` para comprobar si los archivos se cargaron correctamente.

## <a name="see-also"></a>Otras referencias
* [Uso de Data Lake Store con clústeres de Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Azure Portal: Creación de un clúster de HDInsight para usar Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
