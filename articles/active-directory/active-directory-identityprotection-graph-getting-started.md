---
title: Microsoft Graph para Azure Active Directory Identity Protection | Microsoft Docs
description: "Obtenga información sobre cómo realizar una consulta a Microsoft Graph para obtener una lista de eventos de riesgo e información asociada desde Azure Active Directory."
services: active-directory
keywords: azure active directory identity protection, evento de riesgo, vulnerabilidad, directiva de seguridad, Microsoft Graph
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.openlocfilehash: df0d89fc93f1b9c19d669c29306398a8b25ee425
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introducción a Azure Active Directory Identity Protection y Microsoft Graph
Microsoft Graph es el punto de conexión de API unificada de Microsoft y donde se encuentran las API de [Azure Active Directory Identity Protection](active-directory-identityprotection.md). La primera API, **identityRiskEvents**, le permite consultar una lista de Microsoft Graph de [eventos de riesgo](active-directory-identityprotection-risk-events-types.md) así como información asociada. Este artículo es una introducción a cómo consultar esta API. Para una introducción más detallada, consulte la documentación completa y el acceso al explorador de Graph en [sitio web de Microsoft Graph](https://graph.microsoft.io/).


Hay cuatro pasos para acceder a los datos de Identity Protection a través de Microsoft Graph:

1. Recupere el nombre de dominio.
2. Cree un nuevo registro de aplicaciones. 
2. Use este secreto y otro tipo de información para autenticarse en Microsoft Graph, donde recibirá un token de autenticación. 
3. Utilice este token para realizar solicitudes en el punto de conexión de API y obtener datos de Identity Protection.

Antes de comenzar, necesitará lo siguiente:

* Privilegios de administrador para crear la aplicación en Azure AD
* El nombre de dominio del inquilino (por ejemplo, contoso.onmicrosoft.com)


## <a name="retrieve-your-domain-name"></a>Recuperación del nombre de dominio 

1. [Inicie sesión](https://portal.azure.com) en Azure Portal como administrador. 

2. En el panel de navegación izquierdo,haga clic en **Active Directory**. 
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/41.png)


3. En la sección **Administrar**, haga clic en **Propiedades**.

    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/42.png)

4. Copie su nombre de dominio.


## <a name="create-a-new-app-registration"></a>Creación de un registro de aplicaciones

1. En la página **Active Directory**, en la sección **Administrar**, haga clic en **Registros de aplicaciones**.

    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/42.png)


2. En el menú de la parte superior, haga clic en **Nuevo registro de aplicaciones**.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/43.png)

3. En la página **Crear**, realice los pasos siguientes:
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/44.png)

    a. En el cuadro de texto **Nombre** , escriba un nombre para la aplicación (por ejemplo: aplicación de API de eventos de riesgo AADIP).
   
    b. Como **Tipo**, seleccione **Aplicación web y/o API web**.
   
    c. En el cuadro de texto **URL de inicio de sesión**, escriba `http://localhost`.

    d. Haga clic en **Create**(Crear).

4. Para abrir la página **Configuración**, en la lista de aplicaciones, haga clic en el registro de aplicación recién creado. 

5. Copie el **id. de la aplicación**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Conceder a la aplicación permiso para usar la API

1. En la página **Configuración**, haga clic en **Permisos necesarios**.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/15.png)

2. En la página **Permisos necesarios**, en la barra de herramientas de la parte superior, haga clic en **Agregar**.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/16.png)
   
3. En la página **Agregar acceso de API**, haga clic en **Seleccionar una API**.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/17.png)

4. En la página **Seleccionar una API**, seleccione **Microsoft Graph** y, a continuación, haga clic en **Seleccionar**.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/18.png)

5. En la página **Agregar acceso de API**, haga clic en **Seleccionar permisos**.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/19.png)

6. En la página **Habilitar acceso**, haga clic en **Read all identity risk information** (Leer toda la información de riesgo de identidad) y, a continuación, haga clic en **Seleccionar**.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/20.png)

7. En la página **Agregar acceso de API**, haga clic en **Listo**.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/21.png)

8. En la página **Permisos necesarios**, haga clic en **Conceder permisos** y en **Sí**.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/22.png)



## <a name="get-an-access-key"></a>Obtención de una clave de acceso

1. En la página **Configuración**, haga clic en **Claves**.
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/23.png)

2. En la página **Claves**, siga estos pasos:
   
    ![Creación de una aplicación](./media/active-directory-identityprotection-graph-getting-started/24.png)

    a. En el cuadro de texto **Descripción de la clave**, escriba una descripción (por ejemplo, *Evento de riesgo de AADIP*).
    
    b. Como **Duración**, seleccione **1 año**.

    c. Haga clic en **Save**(Guardar).
   
    d. Copie el valor de clave y, a continuación, péguelo en una ubicación segura.   
   
   > [!NOTE]
   > Si pierde esta clave, tendrá que volver a esta sección y crear una nueva. Guarde esta clave como un secreto: cualquier persona que la tenga accederá a sus datos.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Autenticación en Microsoft Graph y consulta a Identity Risk Events API
En este momento, debe tener:

- El nombre de dominio del inquilino

- El id. de cliente 

- La clave 


Para autenticarse, envíe una solicitud post a `https://login.microsoft.com` con los siguientes parámetros en el cuerpo:

- grant_type: “**client_credentials**”

-  resource: “**https://graph.microsoft.com**”

- client_id: \<id. de cliente\>

- client_secret: \<su clave\>


Si se realiza correctamente, devuelve un token de autenticación.  
Para llamar a la API, cree un encabezado con el parámetro siguiente:

    `Authorization`=”<token_type> <access_token>"


Al autenticar, puede encontrar el tipo de token y el token de acceso en el token devuelto.

Envíe este encabezado como una solicitud a la siguiente dirección URL de la API: `https://graph.microsoft.com/beta/identityRiskEvents`

La respuesta, si se ha realizado correctamente, es una colección de eventos de riesgo de identidad y datos asociados en formato JSON de OData, que se pueden analizar y tratar como convenga.

Este es el código de ejemplo para autenticar y llamar a la API mediante PowerShell.  
Solo tiene que agregar el identificador de cliente, la clave secreta y el dominio del inquilino.

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


## <a name="next-steps"></a>pasos siguientes

Enhorabuena, acaba de hacer la primera llamada a Microsoft Graph.  
Ahora puede consultar los eventos de riesgo de identidad y utilizar los datos cuando lo estime necesario.

Para obtener más información sobre Microsoft Graph y cómo crear aplicaciones con Graph API, consulte la [documentación](https://graph.microsoft.io/docs) y muchos más detalles en el [sitio web de Microsoft Graph](https://graph.microsoft.io/). Además, asegúrese de guardar en sus favoritos la página de las [API de Azure AD Identity Protection](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) , donde se enumeran todas las API de Identity Protection disponibles en Graph. A medida que se agreguen nuevas formas de trabajar con Identity Protection a través de la API, los verá en la página.

Para obtener información relacionada, consulte:

-  [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

-  [Types of risk events detected by Azure Active Directory Identity Protection (Tipos de eventos de riesgo que detecta Azure Active Directory Identity Protection)](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Overview of Microsoft Graph (Información general de Microsoft Graph)](https://graph.microsoft.io/docs)

- [Azure AD Identity Protection Service Root (Raíz del servicio de Azure AD Identity Protection)](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

