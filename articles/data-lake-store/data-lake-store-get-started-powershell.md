---
title: "Uso de PowerShell como introducción a Azure Data Lake Store | Microsoft Docs"
description: "Uso de Azure PowerShell para crear una cuenta de Almacén de Data Lake y realización de operaciones básicas"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 31c98b930ccb8203316e3a3b13c3c0dc7d74dd9c
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Introducción al Almacén de Azure Data Lake mediante Azure PowerShell
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI de Azure 2.0](data-lake-store-get-started-cli-2.0.md)
>
>

Aprenda a usar Azure PowerShell para crear una cuenta del Almacén de Azure Data Lake y realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta, etc. Para más información acerca de Data Lake Store, consulte [Información general de Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versiones posteriores**. Consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Autenticación
En este artículo se utiliza un enfoque de autenticación más sencillo con Data Lake Store en el que se le solicita que escriba las credenciales de la cuenta de Azure. El nivel de acceso a la cuenta de Data Lake Store y al sistema de archivos está determinado por el nivel de acceso del usuario que ha iniciado sesión. No obstante, existen otros enfoques para realizar la autenticación con Data Lake Store, que son **autenticación de usuario final** o **autenticación de servicio a servicio**. Para obtener instrucciones y más información acerca de cómo realizar la autenticación, consulte [Autenticación de usuario final con Data Lake Store mediante Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md) o [Autenticación entre servicios con Data Lake Store mediante Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Creación de una cuenta de Almacén de Azure Data Lake
1. Desde el escritorio, abra una nueva ventana de Windows PowerShell. Escriba el siguiente fragmento de código para iniciar sesión en su cuenta de Azure, establecer la suscripción y registrar el proveedor de Data Lake Store. Cuando se le solicite iniciar sesión, asegúrese de iniciarla como uno de los administradores o propietario de la suscripción:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. La cuenta de Almacén de Azure Data Lake se asocia con un grupo de recursos de Azure. Comience creando un grupo de recursos de Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Creación de un grupo de recursos de Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Creación de un grupo de recursos de Azure")
3. Cree una cuenta de Almacén de Azure Data Lake. El nombre que especifique debe contener solo letras minúsculas y números.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Creación de una cuenta de Azure Data Lake Store](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Creación de una cuenta de Azure Data Lake Store")
4. Compruebe que la cuenta se creó correctamente.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    La salida del cmdlet debe ser **True**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Creación de estructuras de directorios en el Almacén de Azure Data Lake
Puede crear directorios bajo su cuenta de Almacén de Azure Data Lake para administrar y almacenar datos.

1. Especifique un directorio raíz.

        $myrootdir = "/"
2. Cree un nuevo directorio denominado **mynewdirectory** en la raíz especificada.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Compruebe que el nuevo directorio se creó correctamente.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    La salida debe parecerse a la siguiente captura de pantalla:

    ![Comprobación del directorio](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Comprobación del directorio")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Carga de datos en el Almacén de Azure Data Lake
Puede cargar los datos en Data Lake Store directamente en el nivel raíz o en un directorio que creara en la cuenta. Los fragmentos de código de esta sección muestran cómo cargar datos de ejemplo en el directorio (**mynewdirectory**) que creó en la sección anterior.

Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Descargue el archivo y almacénelo en un directorio local del equipo, como C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Cambio del nombre, descarga y eliminación de los datos del Almacén de Data Lake
Para cambiar el nombre de un archivo, use el comando siguiente:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Para descargar un archivo, use el comando siguiente:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Para eliminar un archivo, use el comando siguiente:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Cuando se le solicite, escriba **Y** para eliminar el elemento. Si tiene más de un archivo para eliminar, puede proporcionar todas las rutas de acceso separadas por comas.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Eliminación de una cuenta del Almacén de Azure Data Lake
Use el siguiente comando para eliminar la cuenta del Almacén de Data Lake.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Cuando se le solicite, escriba **Y** para eliminar la cuenta.

## <a name="next-steps"></a>pasos siguientes
* [Guía sobre la optimización del rendimiento para poder usar PowerShell con Azure Data Lake Store](data-lake-store-performance-tuning-powershell.md)
* [Uso de Azure Data Lake Store para requisitos de macrodatos](data-lake-store-data-scenarios.md) 
* [Protección de los datos en Data Lake Store](data-lake-store-secure-data.md)
* [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)