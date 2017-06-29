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
ms.date: 03/06/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 1fe2b4b6c84e3c4d96677e3da5a94b9462e2a7bf
ms.contentlocale: es-es
ms.lasthandoff: 06/07/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Introducción al Almacén de Azure Data Lake mediante Azure PowerShell
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [SDK de Java](data-lake-store-get-started-java-sdk.md)
> * [API de REST](data-lake-store-get-started-rest-api.md)
> * [CLI de Azure 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Aprenda a usar Azure PowerShell para crear una cuenta del Almacén de Azure Data Lake y realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta, etc. Para más información acerca de Data Lake Store, consulte [Información general de Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versiones posteriores**. Consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Autenticación
En este artículo se utiliza un enfoque de autenticación más sencillo con Data Lake Store en el que se le solicita que escriba las credenciales de la cuenta de Azure. El nivel de acceso a la cuenta de Data Lake Store y al sistema de archivos está determinado por el nivel de acceso del usuario que ha iniciado sesión. No obstante, existen otros enfoques para realizar la autenticación con Data Lake Store, que son **autenticación de usuario final** o **autenticación de servicio a servicio**. Para obtener instrucciones y más información acerca de cómo realizar la autenticación, consulte [Autenticación de usuario final con Data Lake Store mediante Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md) o [Autenticación entre servicios con Data Lake Store mediante Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Creación de una cuenta de Almacén de Azure Data Lake
1. En el escritorio, abra una nueva ventana de Windows PowerShell y escriba el siguiente fragmento de código para iniciar sesión en su cuenta de Azure, establecer la suscripción y registrar el proveedor de Data Lake Store. Cuando se le solicite iniciar sesión, asegúrese de iniciarla como uno de los administradores o propietario de la suscripción:

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
3. Cree una cuenta del Almacén de Azure Data Lake. El nombre que especifique debe contener solo letras minúsculas y números.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Creación de una cuenta de Azure Data Lake Store](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Creación de una cuenta de Azure Data Lake Store")
4. Compruebe que la cuenta se creó correctamente.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    El resultado debe ser **True**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Creación de estructuras de directorios en el Almacén de Azure Data Lake
Puede crear directorios bajo su cuenta de Almacén de Azure Data Lake para administrar y almacenar datos.

1. Especifique un directorio raíz.

        $myrootdir = "/"
2. Cree un nuevo directorio denominado **mynewdirectory** en la raíz especificada.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Compruebe que el nuevo directorio se creó correctamente.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Debe mostrar un resultado parecido al siguiente:

    ![Comprobación del directorio](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Comprobación del directorio")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Carga de datos en el Almacén de Azure Data Lake
Puede cargar los datos en el Almacén de Data Lake directamente en el nivel raíz o en un directorio que creó en la cuenta. Los fragmentos de código siguientes muestran cómo cargar datos de ejemplo en el directorio (**mynewdirectory**) que creó en la sección anterior.

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

## <a name="performance-guidance-while-using-powershell"></a>Guía de rendimiento al usar PowerShell

A continuación se muestran los valores más importantes que se pueden optimizar para obtener el mejor rendimiento al usar PowerShell para trabajar con Data Lake Store:

| Propiedad            | Valor predeterminado | Descripción |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Este parámetro permite elegir el número de subprocesos paralelos para cargar o descargar cada archivo. Este número representa el número máximo de subprocesos que se pueden asignar por archivo, pero, según el escenario, es posible que obtenga menos subprocesos (por ejemplo, si está cargando un archivo de 1 KB, obtendrá un subproceso, aunque solicite 20).  |
| ConcurrentFileCount | 10      | Este parámetro es específico para cargar o descargar carpetas. Este parámetro determina el número de archivos simultáneos que se pueden cargar o descargar. Este número representa el número máximo de archivos simultáneos que se pueden cargar o descargar al mismo tiempo, pero, según el escenario, es posible que obtenga una menor simultaneidad (por ejemplo, si está cargando dos archivos, obtendrá dos cargas de archivos simultáneos, aunque solicite 15). |

**Ejemplo**

Este comando descarga los archivos de Azure Data Lake Store en la unidad local del usuario con 20 subprocesos por archivo y 100 archivos simultáneos.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

### <a name="how-do-i-determine-the-value-to-set-for-these-parameters"></a>¿Cómo puedo determinar qué valor establecer para estos parámetros?

A continuación hay algunas instrucciones que puede usar.

* **Paso 1: Determinación del número total de subprocesos**: debe empezar por calcular el recuento total de subprocesos que se usará. Como norma general, debe usar seis subprocesos por cada núcleo físico.

        Total thread count = total physical cores * 6

    **Ejemplo**

    Suponga que está ejecutando los comandos de PowerShell desde una máquina virtual D14 que tiene 16 núcleos.

        Total thread count = 16 cores * 6 = 96 threads


* **Paso 2: Cálculo de PerFileThreadCount**: calculamos nuestro PerFileThreadCount en función del tamaño de los archivos. Para archivos menores de 2,5 GB, no hay ninguna necesidad de cambiar este parámetro, ya que el valor predeterminado (10) es suficiente. Para los archivos mayores de 2,5 GB, debe usar diez subprocesos como base para las primeras 2,5 GB y agregar un subproceso por cada aumento de 256 MB en el tamaño del archivo. Si está copiando una carpeta con muchos tamaños de archivo, considere la posibilidad de agruparlos por tamaños de archivo similares. Tener tamaños de archivo diferentes puede provocar un rendimiento no óptimo. Si no es posible agrupar los archivos con un tamaño similar, debe establecer PerFileThreadCount en función del tamaño de archivo más grande.

        PerFileThreadCount = 10 threads for the first 2.5GB + 1 thread for each additional 256MB increase in file size

    **Ejemplo**

    Suponiendo que tenga 100 archivos que oscilan entre 1 GB y 10 GB, usaremos los 10 GB como el tamaño de archivo más grande para la ecuación, que debería ser similar a la siguiente.

        PerFileThreadCount = 10 + ((10GB - 2.5GB) / 256MB) = 40 threads

* **Paso 3: Cálculo de ConcurrentFilecount**: use el número total de subprocesos y PerFileThreadCount para calcular ConcurrentFileCount basado en la siguiente ecuación.

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Ejemplo**

    En función de los valores del ejemplo que hemos usado

        96 = 40 * ConcurrentFileCount

    Por tanto, **ConcurrentFileCount** es **2,4**, que se puede redondear a **2**.

### <a name="further-tuning"></a>Ajuste adicional

Es posible que necesite un ajuste adicional porque se va a trabajar con diversos tamaños de archivo. El cálculo anterior funciona bien si todos o la mayoría de los archivos son más grandes y más cercanos a las 10 GB. Por el contrario, si hay muchos tamaños de archivo diferentes, con muchos archivos pequeños, podría reducir PerFileThreadCount. Al reducir PerFileThreadCount, podemos aumentar ConcurrentFileCount. Por lo tanto, si suponemos que la mayoría de nuestros archivos son pequeños, aproximadamente 5 GB, podemos repetir nuestro cálculo:

    PerFileThreadCount = 10 + ((5GB - 2.5GB) / 256MB) = 20

Por lo tanto, **ConcurrentFileCount** ahora será 96/20, cuyo resultado es 4,8, redondeado a **4**.

Puede continuar ajustando esta configuración aumentando o reduciendo **PerFileThreadCount** en función de la distribución de los tamaños de archivo.

### <a name="limitation"></a>Limitación

* **El número de archivos es menor que ConcurrentFileCount**: si el número de archivos que va a cargar es menor que el valor de **ConcurrentFileCount** que ha calculado, debería reducir **ConcurrentFileCount** para que sea igual al número de archivos. También puede utilizar los subprocesos restantes para aumentar **PerFileThreadCount**.

* **Demasiados subprocesos**: si aumenta demasiado el número de subprocesos sin aumentar el tamaño del clúster, corre el riesgo de degradar el rendimiento. Puede haber problemas de contención al cambiar de contexto en la CPU.

* **Simultaneidad insuficiente**: si la simultaneidad no es suficiente, el clúster puede ser demasiado pequeño. Puede aumentar el número de nodos en el clúster, lo que proporcionará más simultaneidad.

* **Errores de limitación**: es posible que vea errores de limitación si la simultaneidad es demasiado alta. Si ve errores de limitación, reduzca la simultaneidad o póngase en contacto con nosotros.

## <a name="next-steps"></a>Pasos siguientes
* [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
* [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)


