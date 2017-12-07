---
title: "Autenticación y autorización mediante API en Azure Time Series Insights"
description: "En este artículo se describe cómo configurar la autenticación y la autorización para una aplicación personalizada que llama a la API de Azure Time Series Insights."
services: time-series-insights
ms.service: time-series-insights
author: dmdenmsft
ms.author: dmden
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: dd78e1e726029aaceef5aff0e0eed84acac646cf
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticación y autorización para la API de Azure Time Series Insights

En este artículo se explica cómo configurar la autenticación y la autorización utilizadas en una aplicación personalizada que llama a la API de Azure Time Series Insights.

## <a name="service-principal"></a>Entidad de servicio

En esta sección se explica cómo configurar una aplicación para acceder a la API de Time Series Insights en nombre de la aplicación. Después la aplicación puede consultar datos o publicar datos de referencia en el entorno de Time Series Insights con las credenciales de la aplicación en lugar de las del usuario.

Si tiene una aplicación que necesita acceder a Time Series Insights, debe configurar una aplicación de Azure Active Directory y asignar las directivas de acceso a datos en el entorno de Time Series Insights. Este enfoque es preferible a ejecutar la aplicación con sus propias credenciales por los siguientes motivos:

* Puede asignar a la identidad de la aplicación unos permisos distintos a los suyos propios. Normalmente, estos permisos están restringidos a exactamente aquello que la aplicación requiere. Por ejemplo, puede permitir que la aplicación solo lea datos en un entorno de Time Series Insights determinado.
* No es necesario cambiar las credenciales de la aplicación si las responsabilidades cambian.
* Puede usar un certificado o una clave de aplicación para automatizar la autenticación cuando vaya a ejecutar un script desatendido.

En este artículo se muestra cómo realizar esos pasos a través de Azure Portal. Se centra en una aplicación de un único inquilino diseñada para ejecutarse en una sola organización. El usuario normalmente usa aplicaciones de un único inquilino para aplicaciones de línea de negocio que se ejecutan en la organización.

El flujo de configuración consta de tres pasos de alto nivel:

1. Creación de una aplicación en Azure Active Directory.
2. Autorización a esta aplicación a acceder al entorno de Time Series Insights.
3. Uso del identificador y la clave de la aplicación para conseguir un token para la audiencia o el recurso de `"https://api.timeseries.azure.com/"`. Luego el token puede usarse para llamar a la API de Time Series Insights.

Estos son los pasos detallados:

1. En Azure Portal, seleccione **Azure Active Directory** > **Registros de aplicaciones** > **Nuevo registro de aplicaciones**.

   ![Nuevo registro de aplicaciones en Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Asigne un nombre a la aplicación, seleccione **Aplicación web o API** como tipo, seleccione cualquier URI válido para **URL de inicio de sesión** y haga clic en **Crear**.

   ![Creación de la aplicación en Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Seleccione la aplicación recién creada y copie su identificador en el editor de texto que prefiera.

   ![Copia del identificador de la aplicación](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Seleccione **Claves**, escriba el nombre de la clave, seleccione la expiración y haga clic en **Guardar**.

   ![Selección de las claves de aplicación](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Especificación y almacenamiento del nombre y la fecha de expiración de la clave](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. Copie la clave en el editor de texto que prefiera.

   ![Copia de la clave de la aplicación](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Para el entorno de Time Series Insights, seleccione **Directivas de acceso a datos** y haga clic en **Agregar**.

   ![Adición de nueva directiva de acceso a datos al entorno de Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. En el cuadro de diálogo **Seleccionar usuario**, pegue el nombre de la aplicación (del paso 2) o el identificador (del paso 3).

   ![Búsqueda de una aplicación en el cuadro de diálogo Seleccionar usuario](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Seleccione el rol (**Lector** para consultar datos, **Colaborador** para consultar datos y cambiar datos de referencia) y haga clic en **Aceptar**.

   ![Selección de Lector o Colaborador en el cuadro de diálogo Seleccionar rol](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. Haga clic en **Aceptar** para guardar la directiva.

10. Use el identificador de la aplicación (del paso 3) y la clave (del paso 5) para conseguir el token en nombre de la aplicación. Luego el token se puede pasar en el encabezado `Authorization` cuando la aplicación llame a la API de Time Series Insights.

    Si está usando C#, puede emplear el siguiente código para conseguir el token en nombre de la aplicación. Para obtener un ejemplo completo, vea [Consulta de datos mediante C#](time-series-insights-query-data-csharp.md).

    ```csharp
    var authenticationContext = new AuthenticationContext(
        "https://login.microsoftonline.com/common",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/", 
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

Use el identificador y la clave de la aplicación para autenticarse en Azure Time Series Insights. 

## <a name="next-steps"></a>Pasos siguientes
- Para consultar código de ejemplo que llama a la API de Time Series Insights, vea [Consulta de datos mediante C#](time-series-insights-query-data-csharp.md).
- Para obtener información de referencia de la API, vea la [referencia sobre la API de consulta](/rest/api/time-series-insights/time-series-insights-reference-queryapi).

> [!div class="nextstepaction"]
> [Creación de una entidad de servicio](../azure-resource-manager/resource-group-create-service-principal-portal.md)
