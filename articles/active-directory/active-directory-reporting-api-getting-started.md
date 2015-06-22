<properties
   pageTitle="Introducción a la API de informes de Azure AD"
   description="Introducción a la API de informes de Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="yossibanai"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/22/2015"
   ms.author="yossib"/>


# Introducción a la API de informes de Azure AD

Azure Active Directory proporciona una variedad de informes de actividad, seguridad y auditoría. Estos datos se pueden consumir a través del Portal de Azure, pero también pueden ser muy útiles en muchas otras aplicaciones, como los sistemas SIEM y herramientas de auditoría y de inteligencia empresarial.

Las API de informes de Azure AD proporcionan acceso mediante programación a estos datos a través de un conjunto de API basadas en REST al que se puede llamar desde una variedad de lenguajes y herramientas de programación.

Este artículo le guiará a través del proceso de llamada a las API de informes de Azure AD mediante PowerShell. Puede modificar el script de PowerShell de ejemplo para tener acceso a datos desde cualquiera de los informes disponibles en formato JSON, XML o texto, según lo requiera su escenario.

Para usar este ejemplo, será necesario [Azure Active Directory](active-directory-whatis.md)

## Creación de una aplicación de Azure AD para tener acceso a la API

La API de informes utiliza [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar el acceso a las API web. Para tener acceso a información desde su directorio, debe crear una aplicación en Active Directory y concederle los permisos adecuados para tener acceso a los datos de AAD.


### Creación de una aplicación
- Vaya al [Portal de administración de Azure](https://manage.windowsazure.com/).
- Vaya al directorio.
- Vaya a las aplicaciones.
- En la barra inferior, haga clic en "Agregar".
	- Haga clic en "Agregar una aplicación que mi organización está desarrollando".
	- **Nombre**: cualquier nombre es correcto. Se recomienda algo como "Aplicación de API de informes".
	- **Tipo**: seleccione "Aplicación web y/o API web".
	- Haga clic en la flecha para ir a la página siguiente.
	- **URL de inicio de sesión**: ```http://localhost```.
	- **URI de id. de aplicación**: ```http://localhost```.
	- Haga clic en la marca de verificación para terminar de agregar la aplicación.

### Conceder a la aplicación permiso para usar la API
- Vaya a la pestaña Aplicaciones.
- Vaya a la aplicación recién creada.
- Haga clic en la pestaña **Configurar**.
- En la sección "Permisos para otras aplicaciones":
	- En Microsoft Azure Active Directory > Permisos de la aplicación, seleccione **Leer datos de directorio**.
- Haga clic en **Guardar** en la parte inferior.


### Obtención del id. de directorio, el id. de cliente y el secreto de cliente

Los pasos siguientes le guiarán para obtener el id. de cliente y el secreto de cliente de la aplicación. También necesitará saber el nombre del inquilino, que puede ser su *. onmicrosoft.com o un nombre de dominio personalizado. Cópielos en un lugar independiente; los usará para modificar el script.

#### Id. de cliente de la aplicación
- Vaya a la pestaña Aplicaciones.
- Vaya a la aplicación recién creada.
- Vaya a la pestaña **Configurar**.
- El id. de cliente de la aplicación aparece en el campo **Id. de cliente**.

#### Secreto de cliente de la aplicación
- Vaya a la pestaña Aplicaciones.
- Vaya a la aplicación recién creada.
- Vaya a la pestaña Configurar.
- Genere una clave secreta nueva para la aplicación seleccionando una duración en la sección "Claves".
- La clave se mostrará al realizar la operación de guardar. Asegúrese de copiarla y pegarla en una ubicación segura, porque no hay manera de recuperarla más adelante.


## Modificación del script
Para editar el script de PowerShell para trabajar con su directorio, reemplace $ClientID, $ClientSecret y $tenantdomain con los valores correctos de "Delegación de acceso en Azure AD".

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID      = <<YOUR CLIENT ID HERE>>                # Should be a ~35 character string insert your info here
    $ClientSecret  = "<<YOUR CLIENT SECRET HERE>>"          # Should be a ~44 character string insert your info here
    $loginURL      = "https://login.windows.net"
    $tenantdomain  = "<<YOUR TENANT NAME HERE>>"            # For example, contoso.onmicrosoft.com

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
        $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        # Returns a list of all the available reports
        Write-host List of available reports
        Write-host =========================
        $allReports = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports?api-version=beta")
        Write-host $allReports.Content

        Write-host
        Write-host Data from the AccountProvisioningEvents report
        Write-host ====================================================
        Write-host
        # Returns a JSON document for the "accountProvisioningEvents" report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/accountProvisioningEvents?api-version=beta")
        Write-host $myReport.Content

        Write-host
        Write-host Data from the AuditEvents report with datetime filter
        Write-host ====================================================
        Write-host
        # Returns a JSON document for the "auditEvents" report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&$filter=eventTime gt 2015-05-20")
        Write-host $myReport.Content

        # Options for other output formats

        # to output the JSON use following line
        $myReport.Content | Out-File -FilePath accountProvisioningEvents.json -Force

        # to output the content to a name value list
        ($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath accountProvisioningEvents.txt -Force

        # to output the content in XML use the following line
        (($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath accountProvisioningEvents.xml -Force

    } else {
        Write-Host "ERROR: No Access Token"
        }


## Ejecución del script
Una vez que termine de editar el script, ejecútelo y compruebe que se devuelven los datos esperados.

El script devuelve listas de todos los informes disponibles y devuelve los resultados del informe AccountProvisioningEvents en la ventana de PowerShell en formato JSON. También crea archivos con la misma salida en JSON, texto y XML. Puede comentar la experiencia de modificar el script para devolver datos de otros informes y  puede comentar los formatos de salida  que no necesita.


## Pasos siguientes
- ¿Tiene curiosidad sobre qué informes de actividad, auditoría y seguridad están disponibles? Consulte [Informes de actividad, auditoría y seguridad de Azure AD](active-directory-view-access-usage-reports.md).
- Consulte [Eventos del informe de auditoría de Azure AD](active-directory-reporting-audit-events.md) para obtener más detalles sobre el informe de auditoría.
- Consulte [Informes y eventos de Azure AD (vista previa)](https://msdn.microsoft.com/library/azure/mt126081.aspx) para obtener más detalles sobre el servicio REST de API Graph.

<!---HONumber=58--> 