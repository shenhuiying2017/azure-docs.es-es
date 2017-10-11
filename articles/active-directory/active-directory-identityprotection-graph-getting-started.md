---
title: "Introducción a Azure Active Directory Identity Protection y Microsoft Graph | Microsoft Docs"
description: "Proporciona una introducción a la consulta de Microsoft Graph para obtener una lista de eventos de riesgo e información asociada desde Azure Active Directory."
services: active-directory
keywords: azure active directory identity protection, evento de riesgo, vulnerabilidad, directiva de seguridad, Microsoft Graph
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 9b01ff86da6a1fd4a439a6ba59ea15ed6480cdad
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introducción a Azure Active Directory Identity Protection y Microsoft Graph
Microsoft Graph es el punto de conexión de API unificada de Microsoft y donde se encuentran las API de [Azure Active Directory Identity Protection](active-directory-identityprotection.md). La primera API, **identityRiskEvents**, le permite consultar una lista de Microsoft Graph de [eventos de riesgo](active-directory-identityprotection-risk-events-types.md) así como información asociada. Este artículo es una introducción a cómo consultar esta API. Para una introducción más detallada, consulte la documentación completa y el acceso al explorador de Graph en [sitio web de Microsoft Graph](https://graph.microsoft.io/).

> [!IMPORTANT]
> Microsoft recomienda administrar Azure AD con el [Centro de administración de Azure AD](https://aad.portal.azure.com) en Azure Portal en lugar de usar el portal de Azure clásico al que se hace referencia en este artículo.

Hay tres pasos para acceder a los datos de Identity Protection a través de Microsoft Graph:

1. Agregar una aplicación con un secreto de cliente. 
2. Use este secreto y otro tipo de información para autenticarse en Microsoft Graph, donde recibirá un token de autenticación. 
3. Utilice este token para realizar solicitudes en el punto de conexión de API y obtener datos de Identity Protection.

Antes de comenzar, necesitará lo siguiente:

* Privilegios de administrador para crear la aplicación en Azure AD
* El nombre de dominio del inquilino (por ejemplo, contoso.onmicrosoft.com)

## <a name="add-an-application-with-a-client-secret"></a>Agregar una aplicación con un secreto de cliente
1. [Inicie sesión](https://manage.windowsazure.com) como administrador en el Portal de Azure clásico. 
2. En el panel de navegación izquierdo,haga clic en **Active Directory**. 
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)
3. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
4. En el menú superior, haga clic en **Aplicaciones**.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)
5. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)
6. En la página de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación que mi organización está desarrollando**.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)
7. En la cuadro de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)
   
    a. En el cuadro de texto **Nombre** , escriba un nombre para la aplicación (por ejemplo: aplicación de API de eventos de riesgo AADIP).
   
    b. Como **Tipo**, seleccione **Aplicación web y/o API web**.
   
    c. Haga clic en **Siguiente**.
8. En el cuadro de diálogo **Agregar propiedades** , realice los pasos siguientes:
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)
   
    a. En el cuadro de texto **URL de inicio de sesión**, escriba `http://localhost`.
   
    b. En el cuadro de texto **URI de id. de aplicación**, escriba `http://localhost`.
   
    c. Haga clic en **Completo**.

Ahora puede configurar la aplicación.

![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)

## <a name="grant-your-application-permission-to-use-the-api"></a>Conceder a la aplicación permiso para usar la API
1. En la página de la aplicación, en el menú de la parte superior, haga clic en **Configurar**. 
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)
2. En la sección **Permisos para otras aplicaciones**, haga clic en **Agregar aplicación**.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)
3. En el cuadro de diálogo **Permisos para otras aplicaciones** , realice los pasos siguientes:
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)
   
    a. Seleccione **Microsoft Graph**.
   
    b. Haga clic en **Complete**.
4. Haga clic en **Permisos de la aplicación: 0** y seleccione **Read all identity risk event information** (Leer toda la información de eventos de riesgo de identidad).
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)
5. Haga clic en **Guardar** en la parte inferior de la página.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

## <a name="get-an-access-key"></a>Obtención de una clave de acceso
1. En la página de la aplicación, en la sección de **claves** , seleccione 1 año como duración.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)
2. Haga clic en **Guardar** en la parte inferior de la página.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)
3. En la sección de claves, copie el valor de la clave recién creada y péguelo en una ubicación segura.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)
   
   > [!NOTE]
   > Si pierde esta clave, tendrá que volver a esta sección y crear una nueva. Guarde esta clave como un secreto: cualquier persona que la tenga accederá a sus datos.
   > 
   > 
4. En la sección **Propiedades**, copie el **identificador de cliente** y péguelo en una ubicación segura. 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Autenticación en Microsoft Graph y consulta a Identity Risk Events API
En este momento, debe tener:

* El identificador de cliente que copió anteriormente
* La clave que copió antes
* El nombre de dominio del inquilino

Para autenticarse, envíe una solicitud post a `https://login.microsoft.com` con los siguientes parámetros en el cuerpo:

* grant_type: “**client_credentials**”
* resource: “**https://graph.microsoft.com**”
* client_id: <your client ID>
* client_secret: <your key>

> [!NOTE]
> Debe proporcionar valores para los parámetros **client_id** y **client_secret**.
> 
> 

Si se realiza correctamente, devuelve un token de autenticación.  
Para llamar a la API, cree un encabezado con el parámetro siguiente:

    `Authorization`=”<token_type> <access_token>"


Al autenticar, puede encontrar el tipo de token y el token de acceso en el token devuelto.

Envíe este encabezado como una solicitud a la siguiente dirección URL de la API: `https://graph.microsoft.com/beta/identityRiskEvents`

La respuesta, si se ha realizado correctamente, es una colección de eventos de riesgo de identidad y datos asociados en formato JSON de OData, que se pueden analizar y tratar como convenga.

Este es el código de ejemplo para autenticar y llamar a la API mediante Powershell.  
Solo tiene que agregar el identificador de cliente, la clave y el dominio del inquilino.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>Pasos siguientes
Enhorabuena, acaba de hacer la primera llamada a Microsoft Graph.  
Ahora puede consultar los eventos de riesgo de identidad y utilizar los datos cuando lo estime necesario.

Para obtener más información sobre Microsoft Graph y cómo crear aplicaciones con la API Graph, consulte la [documentación](https://graph.microsoft.io/docs) y muchos más detalles en el [sitio web de Microsoft Graph](https://graph.microsoft.io/). Además, asegúrese de guardar en sus favoritos la página de las [API de Azure AD Identity Protection](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) , donde se enumeran todas las API de Identity Protection disponibles en Graph. A medida que se agreguen nuevas formas de trabajar con Identity Protection a través de la API, los verá en la página.

## <a name="additional-resources"></a>Recursos adicionales
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
* [Types of risk events detected by Azure Active Directory Identity Protection (Tipos de eventos de riesgo que detecta Azure Active Directory Identity Protection)](active-directory-identityprotection-risk-events-types.md)
* [Microsoft Graph](https://graph.microsoft.io/)
* [Overview of Microsoft Graph (Información general de Microsoft Graph)](https://graph.microsoft.io/docs)
* [Azure AD Identity Protection Service Root (Raíz del servicio de Azure AD Identity Protection)](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

