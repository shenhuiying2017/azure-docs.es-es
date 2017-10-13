---
title: "Supervisión de trabajos de Stream Analytics mediante programación | Microsoft Docs"
description: "Obtenga información sobre cómo supervisar los trabajos de Stream Analytics creados a través de las API de REST, el SDK de Azure o PowerShell."
keywords: "supervisión de .net monitor, supervisión de trabajos, aplicación de supervisión"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 2ec02cc9-4ca5-4a25-ae60-c44be9ad4835
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: 7e9d2f6f03fd539c59b105108fb46697bcd60f1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Creación de supervisión de trabajos de Análisis de transmisiones mediante programación

En este artículo se demuestra cómo habilitar la supervisión de un trabajo de Análisis de transmisiones. Los trabajos de Stream Analytics creados a través de las API de REST, el SDK de Azure o PowerShell no tienen habilitada la supervisión de forma predeterminada. Puede habilitarla manualmente en Azure Portal dirigiéndose a la página Supervisión del trabajo y haciendo clic en el botón Habilitar, o bien puede automatizar este proceso siguiendo los pasos que se describen en este artículo. Los datos de supervisión se mostrarán en el área Métricas de Azure Portal para el trabajo de Stream Analytics.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar este proceso, debe tener lo siguiente:

* Visual Studio 2017 o 2015
* [SDK de .NET para Azure](https://azure.microsoft.com/downloads/) descargado e instalado
* Un trabajo de Stream Analytics existente que requiera la habilitación de supervisión

## <a name="create-a-project"></a>Creación de un proyecto

1. Cree una aplicación de consola .NET de Visual Studio C#.
2. En la consola del administrador de paquetes, ejecute los siguientes comandos para instalar los paquetes NuGet. El primero es el SDK de .NET de administración de Análisis de transmisiones de Azure. El segundo es el SDK de Azure Monitor que se usará para habilitar la supervisión. El último es el cliente de Azure Active Directory que se usará para autenticación.
   
   ```
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Agregue la siguiente sección appSettings al archivo App.config:
   
   ```
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   Reemplace los valores para *SubscriptionId* y *ActiveDirectoryTenantId* por sus identificadores de inquilino y de suscripción de Azure. Para obtener estos valores, ejecute el siguiente cmdlet de PowerShell:
   
   ```
   Get-AzureAccount
   ```
4. Agregue las siguientes instrucciones using al archivo de origen (Program.cs) en el proyecto.
   
   ```
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Agregue un método auxiliar de autenticación:
   
     public static string GetAuthorizationHeader()
   
         {
             AuthenticationResult result = null;
             var thread = new Thread(() =>
             {
                 try
                 {
                     var context = new AuthenticationContext(
                         ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                         ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
   
                     result = context.AcquireToken(
                         resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                         clientId: ConfigurationManager.AppSettings["AsaClientId"],
                         redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                         promptBehavior: PromptBehavior.Always);
                 }
                 catch (Exception threadEx)
                 {
                     Console.WriteLine(threadEx.Message);
                 }
             });
   
             thread.SetApartmentState(ApartmentState.STA);
             thread.Name = "AcquireTokenThread";
             thread.Start();
             thread.Join();
   
             if (result != null)
             {
                 return result.AccessToken;
             }
   
             throw new InvalidOperationException("Failed to acquire token");
     }

## <a name="create-management-clients"></a>Creación de clientes de administración

El código siguiente configurará las variables y los clientes de administración necesarios.

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Habilitación de supervisión para un trabajo de Stream Analytics existente

El código siguiente habilitará la supervisión de un trabajo de Stream Analytics **existente**. La primera parte del código realiza una solicitud GET en el servicio Análisis de transmisiones para recuperar información sobre el trabajo de Análisis de transmisiones en concreto. Usa la propiedad *Id* (recuperada de la solicitud GET) como parámetro del método Put en la segunda mitad del código que envía una solicitud PUT al servicio Insights para habilitar la supervisión para el trabajo de Stream Analytics.

>[!WARNING]
>Si previamente ha habilitado la supervisión de otro trabajo de Stream Analytics, a través del Azure Portal o mediante programación con el siguiente código, **es recomendable proporcionar el mismo nombre de cuenta de almacenamiento que usó cuando habilitó anteriormente la supervisión.**
> 
> La cuenta de almacenamiento está vinculada a la región en la que se ha creado el trabajo de Stream Analytics, no específicamente al trabajo.
> 
> Todos los trabajos de Stream Analytics (y todos los demás recursos de Azure) de esa misma región comparten esta cuenta de almacenamiento para almacenar los datos de supervisión. Si proporciona otra cuenta de almacenamiento, puede provocar efectos secundarios no deseados en la supervisión de sus otros trabajos de Stream Analytics u otros recursos de Azure.
> 
> El nombre de la cuenta de almacenamiento utilizado para reemplazar `<YOUR STORAGE ACCOUNT NAME>` en el siguiente código debe ser una cuenta de almacenamiento que esté en la misma suscripción que el trabajo de Stream Analytics para el que está habilitando la supervisión.
> 
> 

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## <a name="get-support"></a>Obtención de soporte técnico

Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes

* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

