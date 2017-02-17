---
title: "Uso de la interfaz de la línea de comandos como introducción a Azure Data Lake Store | Microsoft Docs"
description: "Uso de la línea de comandos multiplataforma de Azure para crear una cuenta de Almacén de Data Lake y realización de operaciones básicas"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 380788f3-041d-4ae5-b6be-37ca74ca333d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/31/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: f33ccee7dd520adf074856616005c929040116dd
ms.openlocfilehash: 89d1811f3f336f2526ccba3b6be26e90ab7120ad


---
# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Introducción al Almacén de Azure Data Lake mediante la línea de comandos de Azure
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [SDK de Java](data-lake-store-get-started-java-sdk.md)
> * [API DE REST](data-lake-store-get-started-rest-api.md)
> * [CLI de Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

> [!NOTE]
> Para cargar y descargar gran cantidad de datos (archivos grandes, un gran número de archivos o ambos), se recomienda usar el [SDK de Python](data-lake-store-get-started-python.md), el [SDK de .NET](data-lake-store-get-started-net-sdk.md) o [Azure PowerShell](data-lake-store-get-started-powershell.md). Estas opciones tienen mejor rendimiento, ya que utilizan varios subprocesos para realizar el movimiento de datos en paralelo.
> 
>  

Aprenda a usar la interfaz de la línea de comandos de Azure para crear una cuenta del Almacén de Azure Data Lake y realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta, etc. Para más información acerca de Data Lake Store, consulte [Información general de Data Lake Store](data-lake-store-overview.md).

La CLI de Azure se implementa en Node.js y se puede usar en cualquier plataforma compatible con Node.js, entre las que se incluyen Windows, Mac y Linux. La CLI de Azure es código abierto. El código fuente se administra en GitHub en <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Este artículo solo trata del uso de la CLI de Azure con Almacén de Data Lake. Para una guía general de cómo usar la CLI de Azure, consulte [Uso de la CLI de Azure][azure-command-line-tools].

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este artículo, debe tener lo siguiente:

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **CLI de Azure** - Vea [Instalar y configurar la CLI de Azure](../xplat-cli-install.md) para obtener información de instalación y configuración. Asegúrese de reiniciar el equipo después de instalar la CLI.

## <a name="authentication"></a>Autenticación
En este artículo se utiliza un enfoque de autenticación más sencillo con Data Lake Store, donde inicia sesión como usuario final. El nivel de acceso a la cuenta de Data Lake Store y al sistema de archivos está determinado por el nivel de acceso del usuario que ha iniciado sesión. No obstante, existen otros enfoques para realizar la autenticación con Data Lake Store, que son **autenticación de usuario final** o **autenticación de servicio a servicio**. Para instrucciones y más información acerca de cómo realizar la autenticación, consulte [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)(Autenticación con Data Lake Store mediante Azure Active Directory).

## <a name="login-to-your-azure-subscription"></a>Inicio de sesión en la suscripción de Azure
1. Siga los pasos documentados en [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)`azure login` y conéctese a su suscripción mediante el método ](../xplat-cli-connect.md).
2. Enumere las suscripciones que están asociadas a su cuenta mediante el comando `azure account list`.
   
        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false
   
    En la salida anterior, **Azure-sub-1** está habilitada y la otra suscripción es **Azure-sub-2**. 
3. Seleccione la suscripción con la que desea trabajar. Si desea trabajar con la suscripción de Azure-sub-2, utilice el comando `azure account set`.
   
        azure account set Azure-sub-2

## <a name="create-an-azure-data-lake-store-account"></a>Creación de una cuenta de Almacén de Azure Data Lake
Abra un símbolo del sistema, el shell o sesión de Terminal y ejecute los comandos siguientes.

1. Cambie al modo de Administrador de recursos de Azure con el siguiente comando:
   
        azure config mode arm
2. Cree un nuevo grupo de recursos. En el siguiente comando, proporcione los valores de los parámetros que desee usar.
   
        azure group create <resourceGroup> <location>
   
    Si el nombre de la ubicación contiene espacios, escríbalo entre comillas. Por ejemplo "East US 2".
3. Cree una cuenta de Almacén de Data Lake.
   
        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Creación de carpetas en el Almacén de Data Lake
Puede crear carpetas en su cuenta de Almacén de Azure Data Lake para administrar y almacenar datos. Use el siguiente comando para crear una carpeta denominada "miNuevaCarpeta" en la raíz del Almacén de Data Lake.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Por ejemplo:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Carga de datos en el Almacén de Data Lake
Puede cargar los datos en el Almacén de Data Lake directamente en el nivel raíz o en la carpeta que creó en la cuenta. Los fragmentos de código siguientes muestran cómo cargar algunos datos de ejemplo en la carpeta (**miNuevaCarpeta**) que creó en la sección anterior.

Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Descargue el archivo y almacénelo en un directorio local en el equipo, como C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Por ejemplo:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Enumeración de archivos en Almacén de Data Lake
Use el siguiente comando para enumera los archivos de una cuenta de Almacén de Data Lake.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Por ejemplo:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

La salida de este comando debe ser similar a la siguiente:

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Cambio del nombre, descarga y eliminación de los datos del Almacén de Data Lake
* **Para cambiar el nombre de un archivo**, use el comando siguiente:
  
        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>
  
    Por ejemplo:
  
        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv
* **Para descargar un archivo**, use el comando siguiente. Asegúrese de que la ruta de destino especificada ya existe.
  
        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>
  
    Por ejemplo:
  
        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"
* **Para eliminar un archivo**, use el comando siguiente:
  
        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>
  
    Por ejemplo:
  
        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
  
    Cuando se le solicite, escriba **Y** para eliminar el elemento.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Visualización de la lista de control de acceso de una carpeta del Almacén de Data Lake
Use el comando siguiente para ver las ACL en una carpeta del Almacén de Data Lake. En la versión actual, las ACL pueden establecerse solo en la raíz del Almacén de Data Lake. Por lo tanto, el parámetro de la ruta de acceso siguiente siempre será el raíz (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Por ejemplo:

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Eliminación de una cuenta del Almacén de Data Lake
Use el comando siguiente para eliminar la cuenta del Almacén de Data Lake.

    azure datalake store account delete <dataLakeStoreAccountName>

Por ejemplo:

    azure datalake store account delete mynewdatalakestore

Cuando se le solicite, escriba **Y** para eliminar la cuenta.

## <a name="next-steps"></a>Pasos siguientes
* [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
* [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

[azure-command-line-tools]: ../xplat-cli-install.md



<!--HONumber=Feb17_HO1-->


