---
title: "Administración de Azure Data Lake Analytics con el SDK de .NET de Azure | Microsoft Docs"
description: "Aprenda a administrar trabajos, orígenes de datos y usuarios de Análisis de Data Lake. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/23/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 63ce27657ecf1c89f5d3dfc163ee655bc15ce0b7


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Administración de Azure Data Lake Analytics con el SDK de .NET para Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Obtenga información sobre cómo administrar cuentas, orígenes de datos, usuarios y trabajos de Azure Data Lake Analytics mediante el SDK de .NET para Azure. Para ver los temas de administración con otras herramientas, haga clic en el selector de pestañas de arriba.

**Requisitos previos**

Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

<!-- ################################ -->
<!-- ################################ -->


## <a name="connect-to-azure-data-lake-analytics"></a>Conexión a Azure Data Lake Analytics
Necesitará los siguientes paquetes de Nuget:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common 
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


El ejemplo de código siguiente muestra cómo conectarse a Azure y ver las cuentas de Data Lake Analytics existentes en la suscripción de Azure.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;

            private static void Main(string[] args)
            {

                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = SUBSCRIPTIONID;

                var adlaAccounts = ListADLAAccounts();

                Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
                for (int i = 0; i < adlaAccounts.Count; i ++)
                {
                    Console.WriteLine(adlaAccounts[i].Name);
                }

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
            }

            public static ServiceClientCredentials AuthenticateAzure(
            string domainName,
            string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }
        }
    }


## <a name="manage-accounts"></a>Administrar cuentas
Antes de ejecutar un trabajo de Análisis de Data Lake, debe tener una cuenta de Análisis de Data Lake. A diferencia de HDInsight de Azure, no se paga por una cuenta de Análisis cuando no está ejecutando un trabajo.  Solo se paga por el tiempo en que se ejecuta un trabajo.  Para obtener más información, consulte la página con [información general sobre Análisis con Azure Data Lake](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Creación de cuentas
Debe tener un grupo de Azure Resource Management y una cuenta de Data Lake Store antes de poder ejecutar el ejemplo siguiente.

El código siguiente muestra cómo crear un grupo de recursos:

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

El código siguiente muestra cómo crear una cuenta de Data Lake Store:

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

El código siguiente muestra cómo crear una cuenta de Data Lake Analytics:

    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
    var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

### <a name="list-accounts"></a>Enumeración de cuentas
Consulte [Conexión a Azure Data Lake Analytics](#connect_to_azure_data_lake_analytics).

### <a name="find-an-account"></a>Búsqueda de una cuenta
Una vez que obtenga un objeto de una lista de cuentas de Data Lake Analytics, puede usar lo siguiente para buscar una cuenta:

    Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
    var myAdlaAccount = adlaAccounts.Find(accountFinder);

### <a name="delete-data-lake-analytics-accounts"></a>Eliminación de cuentas de Análisis de Data Lake
El fragmento de código siguiente elimina una cuenta de Data Lake Analytics:

    _adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Administración de orígenes de datos de cuenta
Actualmente, Análisis de Data Lake admite los siguientes orígenes de datos:

* [Almacén de Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Almacenamiento de Azure](../storage/storage-introduction.md)

Cuando crea una cuenta de Análisis, debe designar una cuenta de Almacén de Azure Data Lake para que sea la cuenta de almacenamiento predeterminada. La cuenta predeterminada de Almacén de Data Lake sirve para almacenar los registros de auditoría y de metadatos de trabajos. Una vez creada la cuenta de Análisis, puede agregar más cuentas de Almacén de Data Lake y/o cuentas de Almacenamiento de Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Búsqueda de la cuenta predeterminada de Almacén de Data Lake
Consulte Búsqueda de una cuenta en este artículo para buscar la cuenta de Data Lake Analytics. Luego, use lo siguiente:

    string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## <a name="use-azure-resource-manager-groups"></a>Uso de grupos del Administrador de recursos de Azure
Las aplicaciones normalmente se componen de muchos componentes,por ejemplo una aplicación web, base de datos, servidor de base de datos, almacenamiento y servicios de terceros. Azure Resource Manager le permite trabajar con los recursos de la aplicación como un grupo al que se hace referencia como un Azure Resource Group. Puede implementar, actualizar, supervisar o eliminar todos los recursos de la aplicación en una operación única y coordinada. Para la implementación se utiliza una plantilla, y esta plantilla puede trabajar en diferentes entornos, como pruebas, ensayo y producción. Puede aclarar la facturación de la organización consultando los costes acumulados de todo el grupo. Para obtener más información, consulte [Información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md). 

Un servicio Análisis de Data Lake puede incluir los siguientes componentes:

* Cuenta de Análisis de Azure Data Lake
* Cuenta predeterminada y necesaria de Almacén de Azure Data Lake
* Cuentas adicionales de Almacén de Azure Data Lake
* Cuentas adicionales de Almacenamiento de Azure

Puede crear todos estos componentes en un grupo de Resource Manager para que sea más fáciles de administrar.

![Cuenta y almacenamiento de Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

La cuenta de Análisis de Data Lake y las cuentas de almacenamiento dependientes deben ubicarse en el mismo centro de datos de Azure.
Sin embargo, el grupo de Resource Manager se puede ubicar en un centro de datos distinto.  

## <a name="see-also"></a>Consulte también
* [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Introducción a Data Lake Analytics mediante el portal de Azure](data-lake-analytics-get-started-portal.md)
* [Administración de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-manage-use-portal.md)
* [Supervisión y solución de problemas de trabajos de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)




<!--HONumber=Nov16_HO3-->


