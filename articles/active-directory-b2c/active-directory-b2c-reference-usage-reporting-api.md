---
title: Ejemplos y definiciones de la API de informes de uso de Azure AD B2C | Microsoft Docs
description: "Instrucciones y ejemplos de cómo obtener informes sobre usuarios, autenticaciones y MFA de inquilinos de B2C."
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: joroja
translationtype: Human Translation
ms.sourcegitcommit: 4b2cbf5f62ee63bada42b2a06506b793d4349fdb
ms.openlocfilehash: 7db9a45a4c80ea8d01937837dfa7a15c171fb66b
ms.lasthandoff: 02/10/2017


---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Acceso a informes de uso en Azure AD B2C a través de la API de informes

Azure Active Directory B2C proporciona autenticación basada en inicio de sesión y en MFA para todos los usuarios finales de la familia de aplicaciones a través de proveedores de identidades.  Conocer el número de usuarios registrados en el inquilino, los proveedores que usan para registrarse y el número de autenticaciones por tipo, responde preguntas como:
* ¿Cuántos usuarios de cada tipo de proveedor de identidades (por ejemplo, cuentas de Microsoft o LinkedIn) se han registrado en los últimos 10 días?
* ¿Cuántas autenticaciones multifactor se han realizado correctamente en el último mes?
* ¿Cuántas autenticaciones basadas en inicios de sesión se han realizado este mes? ¿Por día? ¿Por aplicación?
* ¿Cómo puedo hacer una estimación aproximada del coste mensual previsto de mi actividad de inquilino de B2C?

Este artículo se centra en informes relacionados principalmente con el trabajo de facturación, que se basa en el número de usuarios, el número de autenticaciones basadas en inicios de sesión facturables y el número de autenticaciones multifactor.


## <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Requisitos previos para acceder a la API de generación de informes de Azure AD
Antes de comenzar, debe completar los [requisitos previos para acceder a la API de informes de Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).  Cree una aplicación, obtenga un secreto y concédale derechos de acceso a los informes del inquilino de Azure AD B2C. Aquí también se proporcionan ejemplos de *script de Bash* y *script de Python*.

## <a name="powershell-script"></a>Script de PowerShell
Este script muestra los cuatro informes de uso con el parámetro **TimeStamp** y el filtro **-ApplicationId**.

```
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.windows.net"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
       $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Definiciones de informes de uso
**tenantUserCount**: recuento del número de usuarios en el inquilino por tipo de proveedor de identidades al día en los últimos 30 días. (Si lo desea, un filtro TimeStamp proporciona recuentos de usuarios desde una fecha especificada hasta la fecha actual). El informe proporciona:
 * TotalUserCount = recuento de todos los objetos de usuario
 * OtherUserCount = número de usuarios del directorio de AAD (usuarios que no pertenecen a B2C)
 * LocalUserCount = número de cuentas de usuario de B2C creadas con credenciales locales para el inquilino de B2C

**AlternateIdUserCount** = número de usuarios de B2C registrados con proveedores de identidades externos (por ejemplo, Facebook, cuentas de Microsoft y otros inquilinos de AAD, también conocidos como OrgId)

**b2cAuthenticationCountSummary**: suma del recuento diario de autenticaciones facturables durante los últimos 30 días por día y por tipo de flujo de autenticación

**b2cAuthenticationCount**: recuento del número de autenticaciones dentro de un período de tiempo. Los últimos 30 días son el valor predeterminado.  (Opcional: las marcas de tiempo de inicio y fin definen un período específico de recuentos deseados) El resultado incluye una marca de tiempo de inicio (la fecha más próxima de actividad de este inquilino) y la marca de tiempo de finalización (última actualización)

**b2cMfaRequestCountSummary**: suma de recuentos diarios de autenticaciones multifactor por día y por tipo de MFA (SMS o voz)


## <a name="limitations"></a>Limitaciones
* Datos de recuento de usuarios se actualizan cada 24 a 48 horas.  Las autenticaciones se actualizan varias veces al día.
* Al utilizar el filtro ApplicationId, una respuesta de informe vacío puede deberse a una de las condiciones siguientes:
 * El identificador de la aplicación no existe en el inquilino. Asegúrese de que sea correcto.
 * El identificador de la aplicación existe, pero no se han encontrado datos en el período de informes. Revise los parámetros de fecha y hora.


## <a name="next-steps"></a>Pasos siguientes
### <a name="estimating-your-azure-ad-monthly-bill"></a>Estimación de la factura mensual de Azure AD
Cuando se combina con [los precios disponibles más actualizados de Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/), puede calcular el consumo diario, semanal y mensual de Azure.  Una estimación es especialmente útil cuando planee los cambios de comportamiento del inquilino, lo que puede afectar al coste general.  Puede revisar los costes reales en la [suscripción vinculada de Azure.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing)

### <a name="options-for-other-output-formats"></a>Opciones para otros formatos de salida
```
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    

