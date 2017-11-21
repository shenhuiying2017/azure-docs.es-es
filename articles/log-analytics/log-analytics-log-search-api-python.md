---
title: Script de Python para recuperar datos de Azure Log Analytics | Microsoft Docs
description: "La API de búsqueda de registros de Log Analytics permite que cualquier cliente de API de REST recupere datos de un área de trabajo de Log Analytics.  En este artículo se muestra un script de Python de ejemplo con la API de búsqueda de registros."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: bwren
ms.openlocfilehash: a8a4ec7a6ddf2daeca6ead11460fa076a7eb5c94
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2017
---
# <a name="retrieve-data-from-log-analytics-with-a-python-script"></a>Recuperación de datos desde Log Analytics con un script de Python
La [API de búsqueda de registros de Log Analytics](log-analytics-log-search-api.md) permite que cualquier cliente de API de REST recupere datos de un área de trabajo de Log Analytics.  En este artículo se presenta un script de Python de ejemplo que usa la API de búsqueda de registros de Log Analytics.  

>[!NOTE]
> En este artículo se utiliza la API de búsqueda de registros para el lenguaje de consulta heredado en Log Analytics.  Se proporcionará una actualización para este artículo destinada a las áreas de trabajo que se han actualizado al [nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md).

## <a name="authentication"></a>Autenticación
Este script usa una entidad de servicio en Azure Active Directory para autenticarse en el área de trabajo.  Las entidades de servicio permiten que una aplicación cliente solicite que el servicio autentique una cuenta incluso si el cliente no tiene el nombre de la cuenta. Antes de ejecutar este script, debe crear una entidad de servicio mediante el proceso que aparece en [Uso del portal para crear una aplicación de Azure Active Directory y una entidad de servicio con acceso a los recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md).  Deberá proporcionar el identificador de aplicación, el identificador de inquilino y la clave de autenticación en el script. 

> [!NOTE]
> Cuando [cree una cuenta de Azure Automation](../automation/automation-create-standalone-account.md), se crea una entidad de servicio adecuada para usarla con este script.  Si ya tiene una entidad de servicio creada mediante Azure Automation, debería poder usarla en lugar de crear una nueva, a pesar de que es posible que tenga que [crear una clave de autenticación](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) si todavía no tiene una.

## <a name="script"></a>Script
``` python
import adal
import requests
import json
import datetime
from pprint import pprint

# Details of workspace.  Fill in details for your workspace.
resource_group = 'xxxxxxxx'
workspace = 'xxxxxxxx'

# Details of query.  Modify these to your requirements.
query = "Type=Event"
end_time = datetime.datetime.utcnow()
start_time = end_time - datetime.timedelta(hours=24)
num_results = 100  # If not provided, a default of 10 results will be used.

# IDs for authentication.  Fill in values for your service principal.
subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
tenant_id = 'xxxxxxxx-xxxx-xxxx-xxx-xxxxxxxxxxxx'
application_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx'
application_key = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'

# URLs for authentication
authentication_endpoint = 'https://login.microsoftonline.com/'
resource  = 'https://management.core.windows.net/'

# Get access token
context = adal.AuthenticationContext('https://login.microsoftonline.com/' + tenant_id)
token_response = context.acquire_token_with_client_credentials('https://management.core.windows.net/', application_id, application_key)
access_token = token_response.get('accessToken')

# Add token to header
headers = {
    "Authorization": 'Bearer ' + access_token,
    "Content-Type":'application/json'
}

# URLs for retrieving data
uri_base = 'https://management.azure.com'
uri_api = 'api-version=2015-11-01-preview'
uri_subscription = 'https://management.azure.com/subscriptions/' + subscription_id
uri_resourcegroup = uri_subscription + '/resourcegroups/'+ resource_group
uri_workspace = uri_resourcegroup + '/providers/Microsoft.OperationalInsights/workspaces/' + workspace
uri_search = uri_workspace + '/search'

# Build search parameters from query details
search_params = {
        "query": query,
        "top": num_results,
        "start": start_time.strftime('%Y-%m-%dT%H:%M:%S'),
        "end": end_time.strftime('%Y-%m-%dT%H:%M:%S')
        }

# Build URL and send post request
uri = uri_search + '?' + uri_api
response = requests.post(uri,json=search_params,headers=headers)

# Response of 200 if successful
if response.status_code == 200:

    # Parse the response to get the ID and status
    data = response.json()
    search_id = data["id"].split("/")
    id = search_id[len(search_id)-1]
    status = data["__metadata"]["Status"]

    # If status is pending, then keep checking until complete
    while status == "Pending":

        # Build URL to get search from ID and send request
        uri_search = uri_search + '/' + id
        uri = uri_search + '?' + uri_api
        response = requests.get(uri,headers=headers)

        # Parse the response to get the status
        data = response.json()
        status = data["__metadata"]["Status"]

else:

    # Request failed
    print (response.status_code)
    response.raise_for_status()

print ("Total records:" + str(data["__metadata"]["total"]))
print ("Returned top:" + str(data["__metadata"]["top"]))
pprint (data["value"])
```
## <a name="next-steps"></a>Pasos siguientes
- Más información sobre la [API de búsqueda de registros de Log Analytics](log-analytics-log-search-api.md).