<properties
   pageTitle="Introducción a la API de informes de Azure AD | Microsoft Azure"
   description="Introducción a la API de informes de Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/18/2016"
   ms.author="dhanyahk"/>

# Introducción a la API de informes de Azure Active Directory

*Esta documentación forma parte de la [guía de informes de Azure Active Directory](active-directory-reporting-guide.md).*

Azure Active Directory (AD) ofrece una amplia variedad de informes de actividad, seguridad y auditoría. Estos datos se pueden consumir a través del Portal de Azure clásico, pero también pueden ser muy útiles en muchas otras aplicaciones, como en el caso de sistemas SIEM y herramientas de auditoría y de inteligencia empresarial.

Las [API de informes de Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) proporcionan acceso mediante programación a estos datos a través de un conjunto de API basadas en REST a las que se puede llamar desde una amplia variedad de lenguajes y herramientas de programación.

Este artículo le guiará a través del proceso de llamada a las API de informes de Azure AD mediante PowerShell. Puede modificar el script de PowerShell de ejemplo para tener acceso a datos desde cualquiera de los informes disponibles en formato JSON, XML o texto, según lo requiera su escenario.

Para usar este ejemplo, necesitará un inquilino de [Azure Active Directory](active-directory-whatis.md).

## Creación de una aplicación de Azure AD para tener acceso a la API

La API de informes utiliza [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar el acceso a las API web. Para tener acceso a información desde su directorio, debe crear una aplicación en el inquilino de Azure AD y concederle los permisos adecuados para tener acceso a los datos de Azure AD.


### Registro de una aplicación de Azure AD
Para completar el trabajo de registro de aplicación de Azure AD, debe iniciar sesión en el Portal de Azure clásico con una cuenta de administrador de suscripción de Azure que también sea miembro del rol de directorio Administrador global en su inquilino de Azure AD. El motivo es que va a registrar la aplicación de Azure AD con permisos que requieren el registro y consentimiento con una cuenta que tenga privilegios de Administrador global.

> [AZURE.IMPORTANT] Las aplicaciones que se ejecutan con credenciales con privilegios administrativos como este pueden ser muy avanzadas, así que asegúrese de mantener seguras las credenciales del secreto o el identificador de la aplicación.


1. Vaya al [Portal de Azure clásico](https://manage.windowsazure.com/).
2. En la extensión de **Active Directory** que hay junto al panel izquierdo, vaya al inquilino de Azure AD.
3. Vaya a la pestaña **Aplicaciones**.
4. En la barra inferior, haga clic en **Agregar**.
	- Haga clic en "Agregar una aplicación que mi organización está desarrollando".
	- **Nombre**: cualquier nombre es correcto. Se recomienda algo como "Aplicación de API de informes".
	- **Tipo**: seleccione "Aplicación web y/o API web".
	- Haga clic en la flecha para ir a la página siguiente.
	- **URL de inicio de sesión**: ```https://localhost```.
	- **URI de id. de aplicación**: ```https://localhost/ReportingApiApp```.
	- Haga clic en la marca de verificación para terminar de agregar la aplicación.

### Conceder a la aplicación permiso para usar la API
1. Vaya a la pestaña **Aplicaciones**.
2. Vaya a la aplicación recién creada.
3. Haga clic en la pestaña **Configurar**.
4. En la sección "Permisos para otras aplicaciones":
	- Para el recurso "Microsoft Azure Active Directory" (permisos para la API de Azure AD Graph), haga clic en la lista desplegable "Permisos de aplicación" y seleccione **Leer datos de directorio**.

        > [AZURE.IMPORTANT] Asegúrese de especificar aquí los permisos correctos. Aplique permisos de aplicación en lugar de permisos delegados, si no la aplicación no obtendrá el nivel de permiso necesario para acceder a la API de informes y el script recibirá un error *"Unable to check Directory Read access for appId"* (No se puede comprobar el acceso de lectura del directorio para appId).


5. Haga clic en **Guardar** en la parte inferior.

### Obtención del id. de directorio, el id. de cliente y el secreto de cliente

Los pasos siguientes le guiarán para obtener el id. de cliente y el secreto de cliente de la aplicación. También necesitará saber el nombre del inquilino, que puede ser su *.onmicrosoft.com o un nombre de dominio personalizado. Copie los valores en un lugar aparte; más adelante los usará para modificar el script.

#### Obtención del nombre de dominio del inquilino de Azure AD
1. En la extensión de **Active Directory** que hay junto al panel izquierdo, vaya al inquilino de Azure AD.
2. Vaya a la pestaña **Dominios**.
4. Se mostrará el nombre de dominio "<tenant-name>.onmicrosoft.com" del inquilino, junto con los nombres de dominio personalizados, si es que se han configurado.

#### Obtención del id. de cliente de la aplicación
1. Vaya a la pestaña **Aplicaciones**.
2. Vaya a la aplicación recién creada.
3. Vaya a la pestaña **Configurar**.
4. El id. de cliente de la aplicación aparece en el campo **Id. de cliente**.

#### Obtención del secreto de cliente de la aplicación
1. Vaya a la pestaña **Aplicaciones**.
2. Vaya a la aplicación recién creada.
3. Vaya a la pestaña **Configurar**.
4. Genere una clave secreta nueva para la aplicación seleccionando una duración en la sección "Claves".
5. La clave se mostrará al realizar la operación de guardar. Asegúrese de copiarla y pegarla en una ubicación segura, porque no hay manera de recuperarla más adelante.

## Modificación del script
Edite uno de los scripts siguientes para trabajar con su directorio, reemplace $ClientID, $ClientSecret y $tenantdomain por los valores correctos de las secciones anteriores.

### Script de PowerShell
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

    # Parse auditEvents report items, save output to file(s): auditEventsX.json, where X = 0 thru n for number of nextLink pages
    if ($oauth.access_token -ne $null) {   
        $i=0
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
        $url = 'https://graph.windows.net/' + $tenantdomain + '/reports/auditEvents?api-version=beta&`$filter=eventTime gt ' + $7daysago

        # loop through each query page (1 through n)
        Do{
            # display each event on the console window
            Write-Output "Fetching data using Uri: $url"
            $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
            foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
                Write-Output ($event | ConvertTo-Json)
            }
        
            # save the query page to an output file
            Write-Output "Save the output to a file auditEvents$i.json"
            $myReport.Content | Out-File -FilePath auditEvents$i.json -Force
            $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
            $i = $i+1
        } while($url -ne $null)
    } else {
        Write-Host "ERROR: No Access Token"
        }

    Write-Host "Press any key to continue ..."
    $x = $host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")

### Script de Bash

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"

### Python
	# Author: Michael McLaughlin (michmcla@microsoft.com)
	# Date: January 20, 2016
	# This requires the Python Requests module: http://docs.python-requests.org

	import requests
	import datetime
	import sys

	client_id = 'your-application-client-id-here'
	client_secret = 'your-application-client-secret-here'
	login_url = 'https://login.windows.net/'
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
	request_string = 'https://graph.windows.net/' + tenant_domain + '/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20' + yesterday   
	response = requests.get(request_string, headers = header_params)

	if response.status_code is 200:
	    print response.content
	else:
	    print 'ERROR: API request failed'


## Ejecución del script
Una vez que termine de editar el script, ejecútelo y compruebe que el informe AuditEvents devuelve los datos esperados.

El script devuelve la salida del informe auditEvents en formato JSON. También se crea un archivo `auditEvents.json` con la misma salida. Puede experimentar modificando el script para que devuelva datos de otros informes y convertir en comentario los formatos de salida que no necesite.

## Notas

- No hay ningún límite en el número de eventos devueltos por la API de informes de Azure AD (mediante la paginación de OData).
- Para ver los límites de retención de los datos de informes, consulte [Directivas de retención de informes](active-directory-reporting-retention.md).


## Pasos siguientes
- ¿Tiene curiosidad sobre qué informes de seguridad, auditoría y actividad están disponibles? Consulte los [Informes de actividad, auditoría y seguridad de Azure AD](active-directory-view-access-usage-reports.md). También puede ver todos los puntos de conexión disponibles de API de Azure AD Graph en la página [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta), que se documentan en el artículo [Azure AD Reports and Events (Preview)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview) (Informes y eventos de Azure AD [versión preliminar]).
- Consulte [Eventos del informe de auditoría de Azure AD](active-directory-reporting-audit-events.md) para obtener más detalles sobre el informe de auditoría.
- Consulte la página de [informes y eventos de Azure AD (versión preliminar)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) para más información sobre el servicio REST de la API de Azure AD Graph.

<!---HONumber=AcomDC_0720_2016-->