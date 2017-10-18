---
title: 'SDK de .NET: operaciones de sistema de archivos en Azure Data Lake Store | Microsoft Docs'
description: Use el SDK de .NET de Azure Data Lake Store para realizar operaciones de sistema de archivos en Data Lake Store, como, por ejemplo, crear carpetas.
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: f525bd81ebbc27a4730bdced5c8778b6f2ac69b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-net-sdk"></a>Operaciones de sistema de archivos en Azure Data Lake Store con el SDK de .NET
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [SDK de Java](data-lake-store-get-started-java-sdk.md)
> * [API DE REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Con este artículo aprenderá a realizar operaciones de sistema de archivos en Data Lake Store con el SDK de .NET. Las operaciones de sistema de archivos incluyen, por ejemplo, la creación de carpetas en una cuenta de Data Lake Store o la carga y descarga de archivos.

Para instrucciones sobre cómo realizar operaciones de administración de cuentas en Data Lake Store con el SDK de .NET, consulte [Account management operations on Data Lake Store using .NET SDK](data-lake-store-get-started-net-sdk.md) (Operaciones de administración de cuentas en Azure Data Lake Store con el SDK de .NET).

## <a name="prerequisites"></a>Requisitos previos
* **Visual Studio 2013, 2015 o 2017**. En las instrucciones siguientes se usa Visual Studio 2017.

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Cuenta del Almacén de Azure Data Lake**. Para obtener instrucciones acerca de cómo crear una cuenta, consulte [Introducción a Azure Data Lake Store mediante Azure Portal](data-lake-store-get-started-portal.md)

## <a name="create-a-net-application"></a>Creación de una aplicación .NET
1. Abra Visual Studio y cree una aplicación de consola.
2. En el menú **Archivo**, haga clic en **Nuevo** y en **Proyecto**.
3. En **Nuevo proyecto**, escriba o seleccione los siguientes valores:

   | Propiedad | Valor |
   | --- | --- |
   | Categoría |Plantillas/Visual C#/Windows |
   | Plantilla |Aplicación de consola |
   | Nombre |CreateADLApplication |
4. Haga clic en **Aceptar** para crear el proyecto.
5. Agregue los paquetes NuGet al proyecto.

   1. Haga clic con el botón derecho en el Explorador de soluciones y haga clic en **Administrar paquetes de NuGet**.
   2. En la pestaña **Administrador de paquetes NuGet**, asegúrese de que la opción **Origen del paquete** esté establecida en **nuget.org** y que esté activada la casilla **Incluir versión preliminar**.
   3. Busque e instale los siguientes paquetes NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` - En este tutorial se usa v2.1.3 (versión preliminar).
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - En este tutorial se usa v2.2.12.

        ![Incorporación de un origen de NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Creación de una cuenta de Azure Data Lake")
   4. Cierre el **Administrador de paquetes NuGet**.
6. Abra **Program.cs**, elimine el código existente e incluya las siguientes instrucciones para agregar referencias a espacios de nombres.

        using System;
        using System.IO;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

7. Declare las variables como se indica a continuación y proporcione los valores de los marcadores de posición. Además, asegúrese de que la ruta de acceso local y el nombre de archivo que proporciona existen en el equipo.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static async void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

En las restantes secciones de este artículo, se puede ver cómo se utilizan los métodos .NET disponibles para realizar operaciones como la autenticación, la carga de archivos, etc.

## <a name="authentication"></a>Autenticación

* Para la autenticación del usuario final para la aplicación, consulte el artículo sobre la [autenticación del usuario final con Data Lake Store mediante el SDK de .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Para la autenticación entre servicios para la aplicación, consulte el artículo sobre la [autenticación entre servicios con Data Lake Store mediante el SDK de .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-objects"></a>Creación de objetos de cliente
El fragmento de código siguiente crea los objetos de cliente del sistema de archivos y cuenta de Data Lake Store, que se usan para emitir solicitudes al servicio.

    // Create client objects
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

## <a name="create-a-directory"></a>Creación de directorios
Agregue a la clase el método siguiente. El fragmento de código muestra un método `CreateDirectory()` que puede utilizar para crear un directorio en una cuenta de Data Lake Store.

    // Create a directory
    public static void CreateDirectory(string path)
    {
            _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

Agregue el siguiente fragmento de código al método `Main()` para invocar el método `CreateDirectory()`.

    CreateDirectory(remoteFolderPath);
    Console.WriteLine("Created a directory in the Data Lake Store account. Press any key to continue ...");
    Console.ReadLine();

## <a name="upload-a-file"></a>Cargar un archivo
Agregue a la clase el método siguiente. El fragmento de código muestra un método `UploadFile()` que puede utilizar para cargar archivos en una cuenta de Data Lake Store. El SDK admite la carga y descarga recursiva entre una ruta de acceso de un archivo local y la ruta de acceso de un archivo de Data Lake Store.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

Agregue el siguiente fragmento de código al método `Main()` para invocar el método `UploadFile()`.

    UploadFile(localFilePath, remoteFilePath, true);
    Console.WriteLine("Uploaded file in the directory. Press any key to continue...");
    Console.ReadLine();
    

## <a name="get-file-or-directory-info"></a>Obtención de información de un archivo o directorio
El siguiente fragmento muestra un método `GetItemInfo()` que puede utilizar para recuperar la información sobre un archivo o directorio disponible en Data Lake Store.

    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

Agregue el siguiente fragmento de código al método `Main()` para invocar el método `GetItemInfo()`.

    
    var fileProperties = GetItemInfo(remoteFilePath);
    Console.WriteLine("The owner of the file at the path is:", fileProperties.Owner);
    Console.WriteLine("Press any key to continue...");
    Console.ReadLine();

## <a name="list-file-or-directories"></a>Enumeración de archivos o directorios
El siguiente fragmento muestra un método `ListItems()` que puede utilizar para enumerar el archivo y los directorios de una cuenta de Data Lake Store.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

Agregue el siguiente fragmento de código al método `Main()` para invocar el método `ListItems()`.

    var itemList = ListItems(remoteFolderPath);
    var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
    Console.WriteLine(String.Join("\r\n", fileMenuItems));
    Console.WriteLine("Files and directories listed. Press any key to continue ...");
    Console.ReadLine();

## <a name="concatenate-files"></a>Concatenación de archivos
El siguiente fragmento muestra un método `ConcatenateFiles()` que se utiliza para concatenar archivos.

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

Agregue el siguiente fragmento de código al método `Main()` para invocar el método `ConcatenateFiles()`. Este fragmento de código supone que ha cargado otro archivo a la cuenta de Data Lake Store y que la ruta de acceso del archivo se proporciona en la cadena *anotherRemoteFilePath*.

    string[] stringOfFiles = new String[] {remoteFilePath, anotherRemoteFilePath};
    string destFilePath = Path.Combine(remoteFolderPath, "Concatfile.txt");
    ConcatenateFiles(stringOfFiles, destFilePath);
    Console.WriteLine("Files concatinated. Press any key to continue ...");
    Console.ReadLine();

## <a name="append-to-a-file"></a>Anexión a un archivo
El siguiente fragmento muestra un método `AppendToFile()` que se utiliza para anexar datos a un archivo que ya está almacenado en una cuenta de Data Lake Store.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

Agregue el siguiente fragmento de código al método `Main()` para invocar el método `AppendToFile()`.

    AppendToFile(remoteFilePath, "123");
    Console.WriteLine("Content appended. Press any key to continue ...");
    Console.ReadLine();

## <a name="download-a-file"></a>Descarga de un archivo
El siguiente fragmento muestra un método `DownloadFile()` que se utiliza para descargar un archivo de una cuenta de Data Lake Store.

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:true);
    }

Agregue el siguiente fragmento de código al método `Main()` para invocar el método `DownloadFile()`.

    DownloadFile(destFilePath, localFilePath);
    Console.WriteLine("File downloaded. Press any key to continue ...");
    Console.ReadLine();

## <a name="see-also"></a>Otras referencias
* [Operaciones de administración de cuentas en Azure Data Lake Store con el SDK de .NET](data-lake-store-get-started-net-sdk.md)
* [Referencia de SDK de .NET de Data Lake Store](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Pasos siguientes
* [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
