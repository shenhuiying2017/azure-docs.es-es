---
title: "Autenticación de usuario final: SDK de .NET con Data Lake Store en Azure Active Directory | Microsoft Docs"
description: "Aprenda a lograr la autenticación del usuario final con Data Lake Store mediante Azure Active Directory con el SDK de .NET"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: a2a9754b462e39713e6580baab84c064237306a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-net-sdk"></a>Autenticación de usuario final con Data Lake Store mediante el SDK de .NET
> [!div class="op_single_selector"]
> * [Uso de Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Uso del SDK de .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Uso de Python](data-lake-store-end-user-authenticate-python.md)
> * [Uso de la API de REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

En este artículo, aprenderá a usar el SDK de .NET para realizar la autenticación de usuario final con Azure Data Lake Store. Para la autenticación entre servicios con Data Lake Store mediante el SDK de .NET, consulte [Service-to-service authentication with Data Lake Store using .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md) (Autenticación entre servicios con Data Lake Store mediante el SDK de .NET).

## <a name="prerequisites"></a>Requisitos previos
* **Visual Studio 2013, 2015 o 2017**. En las instrucciones siguientes se usa Visual Studio 2017.

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Cree una aplicación "nativa" de Azure Active Directory**. Debe haber completado los pasos descritos en [Autenticación de usuario final con Data Lake Store mediante Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

## <a name="end-user-authentication"></a>Autenticación de usuario final
Agregue este fragmento de código a su aplicación cliente .NET. Reemplace los valores de marcador de posición por los valores recuperados de una aplicación nativa de Azure AD (se enumera como requisito previo). Este fragmento de código le permite autenticar la aplicación **de manera interactiva** con Data Lake Store, lo que significa que se le pedirá que escriba sus credenciales de Azure.

Para facilitar su uso, el siguiente fragmento de código emplea valores predeterminados para el identificador de cliente y un URI de redirección que son válidos con cualquier suscripción de Azure. En el siguiente fragmento de código, solo es necesario proporcionar el valor del identificador del inquilino. Puede recuperar el identificador del inquilino siguiendo las instrucciones proporcionadas en [Obtención del identificador de inquilino](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
    
    private static void Main(string[] args)
    {
        // User login via interactive popup
        // Use the client ID of an existing AAD native application.
        SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
        var tenant_id = "<AAD_tenant_id>"; // Replace this string with the user's Azure Active Directory tenant ID
        var nativeClientApp_applicationId = "1950a258-227b-4e31-a9cf-717495945fc2";
        var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_applicationId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
        var creds = UserTokenProvider.LoginWithPromptAsync(tenant_id, activeDirectoryClientSettings).Result;
    }

Dos cosas que conviene saber acerca del fragmento de código anterior:

* Para ayudarle a completar este tutorial más rápido, este fragmento de código usa un identificador de dominio y cliente de Azure AD que está disponible de manera predeterminada para todas las suscripciones de Azure. Por lo tanto, puede **usar este fragmento de código tal cual en la aplicación**.
* Sin embargo, si desea utilizar su propio identificador de cliente de dominio y de aplicación de Azure AD, debe crear una aplicación nativa de Azure AD y, después, utilizar el identificador de inquilino de Azure AD, el identificador de cliente y el identificador URI de redirección para la aplicación que ha creado. Consulte [Autenticación de usuario final con Data Lake Store mediante Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md) para obtener instrucciones.

  
## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido a usar la autenticación de usuario final para autenticarse en Azure Data Lake Store mediante el SDK de .NET. Ahora puede consultar los siguientes artículos, que tratan sobre cómo usar el SDK de .NET con Azure Data Lake Store.

* [Operaciones de administración de cuentas en Azure Data Lake Store con el SDK de .NET](data-lake-store-get-started-net-sdk.md)
* [Operaciones de datos en Azure Data Lake Store con el SDK de .NET](data-lake-store-data-operations-net-sdk.md)

