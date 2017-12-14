---
title: "Ejemplos de la API de auditoría de generación de informes de Azure Active Directory | Microsoft Docs"
description: "Introducción a la API de informes de Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b9e0fb21986b82f19d90f999f5d905fbf95d2cc9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Ejemplos de la API de auditoría de generación de informes de Azure Active Directory
Este tema forma parte de una serie de temas sobre la API de informes de Azure Active Directory.  
La característica de generación de informes de Azure AD proporciona una API que permite acceder a los datos de auditoría mediante el uso de código o herramientas relacionadas.
Este tema se centra en proporcionar el código de ejemplo para la **API de actividad de auditoría**.

Consulte:

* [Registros de auditoría](active-directory-reporting-azure-portal.md#activity-reports) para obtener más información
* [Introducción a la API de generación de informes de Azure Active Directory](active-directory-reporting-api-getting-started.md) para obtener más información sobre esta API

Para ver preguntas, problemas o comentarios, póngase en contacto con el equipo de [ayuda de informes de AAD](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Requisitos previos
Para poder usar los ejemplos de este tema, debe completar la [requisitos previos para tener acceso a la API de generación de informes de Azure AD](active-directory-reporting-api-prerequisites.md).  

## <a name="known-issue"></a>Problema conocido
La autenticación de aplicaciones no funcionará si el inquilino está en la región de la UE. Utilice la autenticación de usuario para acceder a la API de auditoría como alternativa hasta que se corrija el problema. 

## <a name="powershell-script"></a>Script de PowerShell
    # This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

    # Constants
    $ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
    $ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
    $loginURL       = "https://login.microsoftonline.com"     # AAD Instance; for example https://login.microsoftonline.com
    $tenantdomain   = "your-tenant-domain.onmicrosoft.com"    # AAD Tenant; for example, contoso.onmicrosoft.com
    $resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
    $7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
    Write-Output "Searching for events starting $7daysago"

    # Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    # Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
    if ($oauth.access_token -ne $null) {   
        $i=0
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
        $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=activityDate gt ' + $7daysago

        # loop through each query page (1 through n)
        Do{
            # display each event on the console window
            Write-Output "Fetching data using Uri: $url"
            $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
            foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
                Write-Output ($event | ConvertTo-Json)
            }

            # save the query page to an output file
            Write-Output "Save the output to a file audit$i.json"
            $myReport.Content | Out-File -FilePath audit$i.json -Force
            $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
            $i = $i+1
        } while($url -ne $null)
    } else {
        Write-Host "ERROR: No Access Token"
        }

    Write-Host "Press any key to continue ..."
    $x = $host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")


### <a name="executing-the-powershell-script"></a>Ejecución del script de PowerShell
Una vez que termine de editar el script, ejecútelo y compruebe que el informe de registros de auditoría devuelve los datos esperados.

El script devuelve la salida del informe de auditoría en formato JSON. También se crea un archivo `audit.json` con la misma salida. Puede experimentar modificando el script para que devuelva datos de otros informes y convertir en comentario los formatos de salida que no necesite.

## <a name="bash-script"></a>Script de Bash
    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.microsoftonline.com"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/activities/audit?api-version=beta&$filter=activityDate%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"

## <a name="python-script"></a>Script de Python
    # Author: Michael McLaughlin (michmcla@microsoft.com)
    # Date: January 20, 2016
    # This requires the Python Requests module: http://docs.python-requests.org

    import requests
    import datetime
    import sys

    client_id = 'your-application-client-id-here'
    client_secret = 'your-application-client-secret-here'
    login_url = 'https://login.microsoftonline.com/'
    tenant_domain = 'your-directory-name-here.onmicrosoft.com'

    # Get an OAuth access token
    bodyvals = {'client_id': client_id,
                'client_secret': client_secret,
                'grant_type': 'client_credentials'}

    request_url = login_url + tenant_domain + '/oauth2/token?api-version=1.0'
    token_response = requests.post(request_url, data=bodyvals)

    access_token = token_response.json().get('access_token')
    token_type = token_response.json().get('token_type')

    if access_token is None or token_type is None:
        print "ERROR: Couldn't get access token"
        sys.exit(1)

    # Use the access token to make the API request
    yesterday = datetime.date.strftime(datetime.date.today() - datetime.timedelta(days=1), '%Y-%m-%d')

    header_params = {'Authorization': token_type + ' ' + access_token}
    request_string = 'https://graph.windows.net/' + tenant_domain + '/activities/audit?api-version=beta&$filter=activityDate%20gt%20' + yesterday   
    response = requests.get(request_string, headers = header_params)

    if response.status_code is 200:
        print response.content
    else:
        print 'ERROR: API request failed'





## <a name="next-steps"></a>Pasos siguientes
* ¿Quiere personalizar los ejemplos de este tema? Consulte la [referencia de la API de auditoría de Azure Active Directory](active-directory-reporting-api-audit-reference.md). 
* Si quiere obtener una descripción completa del uso de la API de generación de informes de Azure Active Directory, consulte el artículo de [introducción a la API de generación de informes de Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Si quiere obtener más información sobre informes de Azure Active Directory, consulte la [guía de generación de informes de Azure Active Directory](active-directory-reporting-guide.md).  

