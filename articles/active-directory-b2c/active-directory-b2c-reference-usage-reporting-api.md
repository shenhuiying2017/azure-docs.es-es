---
title: 'Azure Active Directory B2C: Ejemplos y definiciones de la API de informes de uso | Microsoft Docs'
description: "Guía y ejemplos sobre la obtención de informes en las autenticaciones multifactor, las autenticaciones y los usuarios de inquilino de Azure AD B2C"
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: 52180b760046d273c5a75720df0a73532d0343ad
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Acceso a informes de uso en Azure AD B2C a través de la API de informes

Azure Active Directory B2C (Azure AD B2C) proporciona autenticación basada en el inicio de sesión de los usuarios y en Azure Multi-Factor Authentication. La autenticación se proporciona para los usuarios finales de la familia de aplicaciones en los proveedores de identidades. Cuando conozca el número de usuarios registrados en el inquilino, los proveedores que usan para registrarse y el número de autenticaciones por tipo, puede responder preguntas como:
* ¿Cuántos usuarios de cada tipo de proveedor de identidades (por ejemplo, una cuenta de Microsoft o LinkedIn) se han registrado en los últimos 10 días?
* ¿Cuántas autenticaciones con Multi-Factor Authentication se han realizado correctamente en el último mes?
* ¿Cuántas autenticaciones basadas en inicios de sesión se han realizado este mes? ¿Por día? ¿Por aplicación?
* ¿Cómo puedo estimar el costo mensual previsto de mi actividad de inquilino de Azure AD B2C?

Este artículo se centra en informes relacionados con el trabajo de facturación, que se basa en el número de usuarios, las autenticaciones basadas en inicios de sesión facturables y las autenticaciones multifactor.


## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar, debe completar los pasos de [Requisitos previos para acceder a la API de informes de Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Cree una aplicación, obtenga un secreto y concédale derechos de acceso a los informes del inquilino de Azure AD B2C. Aquí también se proporcionan ejemplos de *script de Bash* y *script de Python*. 

## <a name="powershell-script"></a>Script de PowerShell
Este script muestra la creación de cuatro informes de uso con el parámetro `TimeStamp` y el filtro `ApplicationId`.

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
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
* **tenantUserCount**: el número de usuarios en el inquilino por tipo de proveedor de identidades al día en los últimos 30 días. (De manera opcional, un filtro `TimeStamp` proporciona recuentos de usuarios desde una fecha especificada hasta la fecha actual). El informe proporciona:
  * **TotalUserCount**: el número de todos los objetos de usuario.
  * **OtherUserCount**: el número de usuarios de Azure Active Directory (no de usuarios de Azure AD B2C).
  * **LocalUserCount**: el número de cuentas de usuario de Azure AD B2C creadas con credenciales locales para el inquilino de Azure AD B2C.

* **AlternateIdUserCount**: el número de usuarios de Azure AD B2C registrados con proveedores de identidades externos (por ejemplo, Facebook, una cuenta Microsoft u otro inquilino de Azure Active Directory, al que también se hace referencia como un `OrgId`).

* **b2cAuthenticationCountSummary**: resumen del recuento diario de autenticaciones facturables durante los últimos 30 días por día y por tipo de flujo de autenticación.

* **b2cAuthenticationCount**: el número de autenticaciones dentro de un período de tiempo. El valor predeterminado es los últimos 30 días.  (Opcional: los parámetros `TimeStamp` iniciales y finales definen un período de tiempo específico). La salida incluye `StartTimeStamp` (primera fecha de actividad de este inquilino) y `EndTimeStamp` (última actualización).

* **b2cMfaRequestCountSummary**: resumen del recuento diario de autenticaciones multifactor por día y por tipo (SMS o voz).


## <a name="limitations"></a>Limitaciones
Datos de recuento de usuarios se actualizan cada 24 a 48 horas. Las autenticaciones se actualizan varias veces al día. Al usar el filtro `ApplicationId`, una respuesta de informe vacío puede deberse a una de las condiciones siguientes:
  * El identificador de la aplicación no existe en el inquilino. Asegúrese de que sea correcto.
  * El identificador de la aplicación existe, pero no se han encontrado datos en el período de informes. Revise los parámetros de fecha y hora.


## <a name="next-steps"></a>Pasos siguientes
### <a name="monthly-bill-estimates-for-azure-ad"></a>Estimaciones de las facturas mensuales para Azure AD
Cuando se combina con [los precios disponibles más actualizados de Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/), puede calcular el consumo diario, semanal y mensual de Azure.  Una estimación es especialmente útil cuando planee los cambios de comportamiento del inquilino, lo que puede afectar al costo general. Puede revisar los costos reales en la [suscripción vinculada de Azure](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Opciones para otros formatos de salida
En el código siguiente se muestran ejemplos de envío de la salida a JSON, una lista de valores de nombre y XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
