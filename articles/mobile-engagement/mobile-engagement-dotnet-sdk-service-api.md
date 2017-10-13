---
title: Uso del SDK de .NET para acceder a las API del servicio Azure Mobile Engagement
description: "Describe cómo usar el SDK de .NET de Mobile Engagement para acceder a las API del servicio Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: c07728aa-43f2-4238-8b4a-c9eddf9d838b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 6a497189268c5a1b7e269cc57904ebc77c1906fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>Uso del SDK de .NET para acceder a las API del servicio Azure Mobile Engagement
Azure Mobile Engagement expone un conjunto de API para administrar dispositivos, campañas de cobertura/push, etc. Para interactuar con estas API, también proporcionamos un [archivo de Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) que puede usar con las herramientas para generar los SDK para su idioma preferido. Se recomienda usar la herramienta [AutoRest](https://github.com/Azure/AutoRest) para generar el SDK a partir de nuestro archivo de Swagger.

> [!NOTE]
> El servicio Azure Mobile Engagement se retirará en marzo de 2018 y actualmente solo está disponible para los clientes existentes. Para más información, consulte [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

De forma similar, hemos creado un SDK para .NET que permite interactuar con estas API con un contenedor de C# sin tener que realizar la negociación de token de autenticación y la actualización usted mismo.  

En este ejemplo se pasa por el conjunto de pasos que deben seguirse para usar el SDK. NET:

1. En primer lugar, debe configurar la autenticación para las API mediante Azure Active Directory, como se describe [aquí](mobile-engagement-api-authentication.md#authentication). Al final de estos pasos, debe tener unos elementos **SubscriptionId**, **TenantId**, **ApplicationId** y **Secret** válidos. 
2. Usaremos una sencilla aplicación de la consola de Windows para demostrar cómo trabajar con el SDK de .NET con el escenario de creación de una campaña de anuncio. Abra Visual Studio y cree una **aplicación de consola**.   
3. A continuación debe descargar el SDK de .NET que está disponible como **Microsoft Azure Engagement Management Library** (Biblioteca de administración de Microsoft Azure Engagement) en la galería de Nuget [aquí](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).
   Si va a instalar Nuget desde Visual Studio, debe asegurarse de activar la opción **Incluir versión preliminar** al buscar el paquete:
   
    ![][1]
4. En el archivo `Program.cs` , agregue los siguientes espacios de nombres:
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;
5. A continuación debe definir las constantes que usaremos para la autenticación y la interacción con la aplicación de Mobile Engagement en el que está creando la campaña de anuncio:
   
        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";
   
        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";
   
        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";
6. Defina la variable `EngagementManagementClient` que usaremos para llamar a los métodos del SDK de Mobile Engagement:
   
        static EngagementManagementClient engagementClient; 
7. Agregue lo siguiente al método `Main` :
   
        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();
   
                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }
8. Defina el método siguiente que se encarga de inicializar `EngagementManagementClient` ; para ello, primero autentica y luego se asocia a la aplicación de Mobile Engagement en la que va a crear la campaña de anuncio:
   
        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
   
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;
   
            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }
   
   > [!IMPORTANT]
   > Tenga en cuenta que debe utilizar el **nombre de recurso de la aplicación** como se define en el Portal de administración para el parámetro AppName. 
   > 
   > 
9. Por último, defina el método CreateCampaign, que se encargará de usar el elemento EngagementClient antes inicializado para crear una campaña sencilla de tipo **en cualquier momento** &  y **solo notificación** con un título y un mensaje: 
   
        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/en-us/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );
   
            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/en-us/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }
10. Ejecute la aplicación de la consola y verá lo siguiente cuando se cree la campaña correctamente:
    
    **Campaign Id '159' was created successfully and it is in 'draft' state**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
