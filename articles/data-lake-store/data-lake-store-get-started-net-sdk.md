---
title: "SDK de .NET: operaciones de administración de cuentas en Azure Data Lake Store | Microsoft Docs"
description: "Con Azure Data Lake Store y el SDK de .NET se realizan operaciones de administración de cuentas en Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 682e8c9fc9e7f16d6b69cd73535c76e6c53c6b49
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="account-management-operations-on-azure-data-lake-store-using-net-sdk"></a>Operaciones de administración de cuentas en Azure Data Lake Store con el SDK de .NET
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [API DE REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

En este artículo se aprende a realizar operaciones de administración de cuentas en Data Lake Store con el SDK de .NET. Las operaciones de administración de cuentas incluyen, por ejemplo, la creación o eliminación de cuentas de Data Lake Store, o su enumeración en una suscripción de Azure.

Para instrucciones sobre cómo realizar operaciones de administración de datos en Data Lake Store con el SDK de .NET, consulte [Operaciones de sistema de archivos en Azure Data Lake Store con el SDK de .NET](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Requisitos previos
* **Visual Studio 2013, 2015 o 2017**. En las instrucciones siguientes se usa Visual Studio 2017.

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-net-application"></a>Creación de una aplicación .NET
1. Abra Visual Studio y cree una aplicación de consola.
2. En el menú **Archivo**, haga clic en **Nuevo** y en **Proyecto**.
3. En **Nuevo proyecto**, escriba o seleccione los siguientes valores:

   | Propiedad | Valor |
   | --- | --- |
   | Categoría |Plantillas/Visual C#/Windows |
   | Plantilla |Aplicación de consola |
   | NOMBRE |CreateADLApplication |
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
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Declare las variables y proporcione los valores de los marcadores de posición. Además, asegúrese de que la ruta de acceso local y el nombre de archivo que proporcione existen en el equipo.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

En las restantes secciones de este artículo, se puede ver cómo se utilizan los métodos .NET disponibles para realizar operaciones como la autenticación, la carga de archivos, etc.

## <a name="authentication"></a>Autenticación

* Para la autenticación del usuario final para la aplicación, consulte el artículo sobre la [autenticación del usuario final con Data Lake Store mediante el SDK de .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Para la autenticación entre servicios para la aplicación, consulte el artículo sobre la [autenticación entre servicios con Data Lake Store mediante el SDK de .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Creación del objeto de cliente
El fragmento de código siguiente crea el objeto de cliente de la cuenta de Data Lake Store, que se usa para emitir solicitudes de administración de cuentas para el servicio, como, por ejemplo, crear o eliminar una cuenta.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-store-account"></a>Crear una cuenta de Almacén de Data Lake
El fragmento de código siguiente crea una cuenta de Data Lake Store en la suscripción de Azure que proporcionó al crear el objeto de cliente de la cuenta de Data Lake Store.

    // Create Data Lake Store account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Enumeración de todas las cuentas de Data Lake Store de una suscripción
Agregue el método siguiente a la definición de la clase. El siguiente fragmento de código enumera todas las cuentas de Data Lake Store de una suscripción de Azure dada.

    // List all Data Lake Store accounts within the subscription
    public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccountBasic>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="delete-a-data-lake-store-account"></a>Eliminar una cuenta del Almacén de Data Lake
El siguiente fragmento de código elimina la cuenta de Data Lake Store que creó anteriormente.

    // Delete Data Lake Store account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Otras referencias
* [Operaciones de sistema de archivos en Azure Data Lake Store con el SDK de .NET](data-lake-store-data-operations-net-sdk.md)
* [Referencia de SDK de .NET de Data Lake Store](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>pasos siguientes
* [Protección de los datos en Data Lake Store](data-lake-store-secure-data.md)
