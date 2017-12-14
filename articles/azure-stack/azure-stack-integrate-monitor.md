---
title: "Integrar una solución de supervisión externa con Azure Stack | Microsoft Docs"
description: "Obtenga información acerca de cómo integrar Azure Stack con una solución de supervisión externa en el centro de datos."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: mabrigg
ms.openlocfilehash: 76499ac959b77e83494bc4f9593c20a99da5c147
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrar una solución de supervisión externa con Azure Stack

*Se aplica a: sistemas integrados de Azure Stack*

Para la supervisión externa de la infraestructura de Azure Stack, deberá supervisar el software Azure Stack, los equipos físicos y los modificadores de red físicos. Cada una de estas áreas ofrece un método para recuperar información sobre el estado de mantenimiento y las alertas.

- El software Azure Stack ofrece una API basada en REST para recuperar el estado de mantenimiento y las alertas. (Con el uso de tecnologías definidas por software, como Espacios de almacenamiento directo, las alertas y el estado de mantenimiento del almacenamiento forman parte de la supervisión de software).
- Los equipos físicos pueden hacer que la información acerca del estado de mantenimiento y las alertas esté disponible a través de los controladores de administración de placa base (BMC).
- Los dispositivos de red físicos pueden hacer que la información acerca del estado de mantenimiento y las alertas esté disponible a través del protocolo SNMP.

Cada solución de Azure Stack se suministra con un host de ciclo de vida de hardware. Este host ejecuta el software de supervisión del fabricante del hardware OEM para los dispositivos de red y servidores físicos. Si quiere, puede omitir estas soluciones de supervisión e integrar directamente las soluciones de supervisión existentes en su centro de datos.

> [!IMPORTANT]
> La solución de supervisión externa que use debe ser sin agente. No puede instalar agentes de terceros dentro de componentes de Azure Stack.

En el siguiente diagrama se muestra el flujo de tráfico entre un sistema integrado de Azure Stack, el host de ciclo de vida de hardware, una solución de supervisión externa y un sistema externo de recopilación de datos y vales.

![Diagrama que muestra el tráfico entre Azure Stack y las soluciones de vales y de supervisión.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

En este artículo se explica cómo integrar Azure Stack con soluciones de supervisión externas, como System Center Operations Manager y Nagios. También se incluye cómo trabajar con alertas mediante programación con PowerShell o a través de llamadas a la API de REST.

## <a name="integrate-with-operations-manager"></a>Integración con Operations Manager

Puede usar Operations Manager para realizar una supervisión externa de Azure Stack. El Módulo de administración de System Center para Microsoft Azure Stack le permite supervisar varias implementaciones de Azure Stack con una única instancia de Operations Manager. El módulo de administración usa las API de REST del proveedor de recursos de mantenimiento y actualización para comunicarse con Azure Stack. Si tiene pensado omitir el software de supervisión de OEM que se ejecuta en el host de ciclo de vida de hardware, puede instalar los módulos de administración del proveedor para supervisar los servidores físicos. También puede utilizar la detección de dispositivos de red de Operations Manager para supervisar los modificadores de red.

El módulo de administración de Azure Stack proporciona las siguientes funcionalidades:

- Puede administrar varias implementaciones de Azure Stack.
- Es compatible con Azure Active Directory (Azure AD) o Servicios de federación de Active Directory (AD FS).
- Puede recuperar y cerrar alertas.
- Dispone de un panel de estado de mantenimiento y de un panel de capacidad.
- Incluye la detección del modo de mantenimiento automático para cuando hay revisiones y actualizaciones en curso.
- Incluye tareas de Forzar actualización para implementaciones y regiones.
- Puede agregar información personalizada a una región.
- Admite notificaciones e informes.

Puede descargar el Módulo de administración de System Center para Microsoft Azure Stack y la guía de usuario asociada [aquí](https://www.microsoft.com/en-us/download/details.aspx?id=55184), o directamente desde Operations Manager.

Para obtener una solución de vales, puede integrar Operations Manager con System Center Service Manager. El conector de producto integrado habilita la comunicación bidireccional que permite cerrar una alerta de Azure Stack y Operations Manager después de resolver una solicitud de servicio en Service Manager.

En el diagrama siguiente se muestra la integración de Azure Stack con una implementación de System Center existente. Puede automatizar Service Manager aún más con System Center Orchestrator o Service Management Automation (SMA) para ejecutar las operaciones en Azure Stack.

![Diagrama que muestra la integración con OM, Service Manager y SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integración con Nagios

Se ha desarrollado un complemento de supervisión de Nagios junto con las soluciones del asociado Cloudbase, que está disponible bajo la permisiva licencia de software gratuito – MIT (Instituto Tecnológico de Massachusetts).

Este complemento se escribe en Python y aprovecha la API de REST del proveedor de recursos de mantenimiento. Ofrece la funcionalidad básica para recuperar y cerrar alertas de Azure Stack. Al igual que el Módulo de administración de System Center, le permite agregar varias implementaciones de Azure Stack y enviar notificaciones.

El complemento funciona con Nagios Enterprise y Nagios Core. Puede descargarlas [aquí](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). El sitio de descarga también incluye detalles de instalación y configuración.

### <a name="plugin-parameters"></a>Parámetros del complemento

Configure el archivo de complemento "Azurestack_plugin.py" con los siguientes parámetros:

| Parámetro | Description | Ejemplo |
|---------|---------|---------|
| *arm_endpoint* | Punto de conexión de Azure Resource Manager (administrador) |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Punto de conexión de Azure Resource Manager (administrador)  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | Identificador de suscripción de administrador | Recuperar a través del portal de administrador o PowerShell |
| *User_name* | Nombre de usuario de la suscripción del operador | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Contraseña de la suscripción del operador | mypassword |
| *Client_id* | Cliente | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Nombre de la región de Azure Stack | local |
|  |  |

*El GUID de PowerShell que se proporciona es universal. Puede utilizarlo para todas las implementaciones.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Usar PowerShell para supervisar el estado de mantenimiento y las alertas

Si no está utilizando una solución basada en Nagios, Nagios o Operations Manager, puede usar PowerShell para habilitar una amplia gama de soluciones de supervisión que pueden integrarse con Azure Stack.
 
1. Para usar PowerShell, asegúrese de tener [PowerShell instalado y configurado](azure-stack-powershell-configure-quickstart.md) para un entorno de operador de Azure Stack. Instale PowerShell en un equipo local que alcance el punto de conexión de Resource Manager (administrador) (https://adminmanagement.[region].[External_FQDN]).

2. Ejecute los comandos siguientes para conectarse con el entorno de Azure Stack como un operador de Azure Stack:

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. Cambie al directorio donde instaló las [herramientas de Azure Stack](https://github.com/Azure/AzureStack-Tools) como parte de la instalación de PowerShell, por ejemplo, c:\azurestack-tools-master. A continuación, cambie al directorio de la infraestructura y ejecute el siguiente comando para importar el módulo de la infraestructura:

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. Utilice comandos como los que se indican en los ejemplos siguientes para trabajar con alertas:
   ```PowerShell
   #Retrieve all alerts
   Get-AzsAlert -location [Region]

   #Filter for active alerts
   $Active=Get-AzsAlert -location [Region] | Where {$_.State -eq "active"}
   $Active

   #Close alert
   Close-AzsAlert -location [Region] -AlertID "ID"

   #Retrieve resource provider health
   Get-AzsResourceProviderHealths -location [Region]

   #Retrieve infrastructure role instance health
   Get-AzsInfrastructureRoleHealths -location [Region]
   ```

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>Usar la API de REST para supervisar el estado de mantenimiento y las alertas

Puede utilizar llamadas a la API de REST para obtener y cerrar alertas, y obtener el estado de mantenimiento de los proveedores de recursos.

### <a name="get-alert"></a>Obtener alertas

**Solicitud**

La solicitud obtiene todas las alertas activas y cerradas de la suscripción de proveedor predeterminada. No hay ningún cuerpo de solicitud.


|Método  |URI de solicitud  |
|---------|---------|
|GET     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01"      |
|     |         |

**Argumentos**

|Argumento  |Descripción  |
|---------|---------|
|armendpoint     |  Punto de conexión de Azure Resource Manager del entorno de Azure Stack, en el formato siguiente https://adminmanagement.{RegionName}.{External FQDN}. Por ejemplo, si el FQDN externo es *azurestack.external* y el nombre de la región es *local*, el punto de conexión de Resource Manager es https://adminmanagement.local.azurestack.external.       |
|subid     |   Identificador de suscripción del usuario que realiza la llamada. Puede utilizar esta API para hacer consultas únicamente con un usuario que tenga permiso para la suscripción de proveedor predeterminada.      |
|RegionName     |    Nombre de la región de la implementación de Azure Stack.     |
|api-version     |  Versión del protocolo que se usa para realizar esta solicitud. Debe usar 2016-05-01.      |
|     |         |

**Respuesta**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/Alerts?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Active",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},

…
```

**Detalles de la respuesta**


|  Argumento  |Descripción  |
|---------|---------|
|*id*     |      Identificador exclusivo de la alerta.   |
|*name*     |     Nombre interno de la alerta.   |
|*type*     |     Definición de recursos.    |
|*ubicación*     |       Nombre de la región.     |
|*etiquetas*     |   Etiquetas del recurso.     |
|*closedtimestamp*    |  Hora UTC a la que se cerró la alerta.    |
|*createdtimestamp*     |     Hora UTC a la que se creó la alerta.   |
|*descripción*     |    Descripción de la alerta     |
|*faultid*     | Componente afectado.        |
|*alertid*     |  Identificador exclusivo de la alerta.       |
|*faulttypeid*     |  Tipo exclusivo del componente defectuoso.       |
|*lastupdatedtimestamp*     |   Hora UTC a la que se actualizó por última vez la información sobre la alerta.    |
|*healthstate*     | Estado general de mantenimiento.        |
|*name*     |   Nombre de la alerta específica.      |
|*fabricname*     |    Nombre del tejido registrado del componente defectuoso.   |
|*descripción*     |  Descripción del componente de tejido registrado.   |
|*servicetype*     |   Tipo del servicio de tejido registrado.   |
|*remediation*     |   Pasos de corrección recomendados.    |
|*type*     |   Tipo de alerta.    |
|*resourceRegistrationid*    |     Identificador del recurso registrado afectado.    |
|*resourceProviderRegistrationID*   |    Identificador del proveedor de recursos registrado del componente afectado.  |
|*serviceregistrationid*     |    Identificador del servicio registrado.   |
|*severity*     |     Gravedad de la alerta.  |
|*state*     |    Estado de la alerta.   |
|*title*     |    Título de la alerta.   |
|*impactedresourceid*     |     Identificador del recurso afectado.    |
|*ImpactedresourceDisplayName*     |     Nombre del recurso afectado.  |
|*closedByUserAlias*     |   Usuario que cerró la alerta.      |

### <a name="close-alert"></a>Cerrar alerta

**Solicitud**

La solicitud cierra una alerta por su identificador único.

|Método    |URI de solicitud  |
|---------|---------|
|PUT     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01"    |

**Argumentos**


|Argumento  |Descripción  |
|---------|---------|
|*armendpoint*     |   Punto de conexión de Resource Manager del entorno de Azure Stack, en el formato siguiente https://adminmanagement.{RegionName}.{External FQDN}. Por ejemplo, si el FQDN externo es *azurestack.external* y el nombre de la región es *local*, el punto de conexión de Resource Manager es https://adminmanagement.local.azurestack.external.      |
|*subid*     |    Identificador de suscripción del usuario que realiza la llamada. Puede utilizar esta API para hacer consultas únicamente con un usuario que tenga permiso para la suscripción de proveedor predeterminada.     |
|*RegionName*     |   Nombre de la región de la implementación de Azure Stack.      |
|*api-version*     |    Versión del protocolo que se usa para realizar esta solicitud. Debe usar 2016-05-01.     |
|*alertid*     |    Identificador exclusivo de la alerta.     |

**Cuerpo**

```json

{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"2017-08-10T20:18:58.1584868Z",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```
**Respuesta**

```http
PUT https://adminmanagement.local.azurestack.external//subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```

**Detalles de la respuesta**


|  Argumento  |Descripción  |
|---------|---------|
|*id*     |      Identificador exclusivo de la alerta.   |
|*name*     |     Nombre interno de la alerta.   |
|*type*     |     Definición de recursos.    |
|*ubicación*     |       Nombre de la región.     |
|*etiquetas*     |   Etiquetas del recurso.     |
|*closedtimestamp*    |  Hora UTC a la que se cerró la alerta.    |
|*createdtimestamp*     |     Hora UTC a la que se creó la alerta.   |
|*Descripción*     |    Descripción de la alerta     |
|*faultid*     | Componente afectado.        |
|*alertid*     |  Identificador exclusivo de la alerta.       |
|*faulttypeid*     |  Tipo exclusivo del componente defectuoso.       |
|*lastupdatedtimestamp*     |   Hora UTC a la que se actualizó por última vez la información sobre la alerta.    |
|*healthstate*     | Estado general de mantenimiento.        |
|*name*     |   Nombre de la alerta específica.      |
|*fabricname*     |    Nombre del tejido registrado del componente defectuoso.   |
|*descripción*     |  Descripción del componente de tejido registrado.   |
|*servicetype*     |   Tipo del servicio de tejido registrado.   |
|*remediation*     |   Pasos de corrección recomendados.    |
|*type*     |   Tipo de alerta.    |
|*resourceRegistrationid*    |     Identificador del recurso registrado afectado.    |
|*resourceProviderRegistrationID*   |    Identificador del proveedor de recursos registrado del componente afectado.  |
|*serviceregistrationid*     |    Identificador del servicio registrado.   |
|*severity*     |     Gravedad de la alerta.  |
|*state*     |    Estado de la alerta.   |
|*title*     |    Título de la alerta.   |
|*impactedresourceid*     |     Identificador del recurso afectado.    |
|*ImpactedresourceDisplayName*     |     Nombre del recurso afectado.  |
|*closedByUserAlias*     |   Usuario que cerró la alerta.      |

### <a name="get-resource-provider-health"></a>Obtener el estado de mantenimiento del proveedor de recursos

**Solicitud**

La solicitud obtiene el estado de mantenimiento de todos los proveedores de recursos registrados.


|Método  |URI de solicitud  |
|---------|---------|
|GET    |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01"   |


**Argumentos**


|Argumentos  |Descripción  |
|---------|---------|
|*armendpoint*     |    Punto de conexión de Resource Manager del entorno de Azure Stack, en el formato siguiente https://adminmanagement.{RegionName}.{External FQDN}. Por ejemplo, si el FQDN externo es azurestack.external y el nombre de la región es local, el punto de conexión de Resource Manager es https://adminmanagement.local.azurestack.external.     |
|*subid*     |     Identificador de suscripción del usuario que realiza la llamada. Puede utilizar esta API para hacer consultas únicamente con un usuario que tenga permiso para la suscripción de proveedor predeterminada.    |
|*RegionName*     |     Nombre de la región de la implementación de Azure Stack.    |
|*api-version*     |   Versión del protocolo que se usa para realizar esta solicitud. Debe usar 2016-05-01.      |


**Respuesta**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths?api-version=2016-05-01
```

```json
{
"value":[
{
"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"name":"03ccf38f-f6b1-4540-9dc8ec7b6389ecca",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths",
"location":"local",
"tags":{},
"properties":{
"registrationId":"03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"displayName":"Key Vault",
"namespace":"Microsoft.KeyVault.Admin",
"routePrefix":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local",
"serviceLocation":"local",
"infraURI":"/subscriptions/4aa97de3-6b83-4582-86e1-65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local/infraRoles/Key Vault",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},
"healthState":"Healthy"
}
}

…
```
**Detalles de la respuesta**


|Argumento  |Descripción  |
|---------|---------|
|*Id*     |   Identificador exclusivo de la alerta.      |
|*name*     |  Nombre interno de la alerta.       |
|*type*     |  Definición de recursos.       |
|*ubicación*     |  Nombre de la región.       |
|*etiquetas*     |     Etiquetas del recurso.    |
|*registrationId*     |   Registro único para el proveedor de recursos.      |
|*displayName*     |Nombre para mostrar del proveedor de recursos.        |
|*namespace*     |   Espacio de nombres de API que implementa el proveedor de recursos.       |
|*routePrefix*     |    URI que interactúa con el proveedor de recursos.     |
|*serviceLocation*     |   Región con la que se registra este proveedor de recursos.      |
|*infraURI*     |   URI del proveedor de recursos que aparece como un rol de infraestructura.      |
|*alertSummary*     |   Resumen de alerta crítica y de advertencia que se asocia con el proveedor de recursos.      |
|*healthState*     |    Estado de mantenimiento del proveedor de recursos.     |


### <a name="get-resource-health"></a>Obtener estado de mantenimiento del recurso

La solicitud obtiene el estado de mantenimiento de todos los proveedores de recursos registrados.

**Solicitud**

|Método  |URI de solicitud  |
|---------|---------|
|GET     |     https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01"    |

**Argumentos**

|Argumentos  |Descripción  |
|---------|---------|
|*armendpoint*     |    Punto de conexión de Resource Manager del entorno de Azure Stack, en el formato siguiente https://adminmanagement.{RegionName}.{External FQDN}. Por ejemplo, si el FQDN externo es azurestack.external y el nombre de la región es local, el punto de conexión de Resource Manager es https://adminmanagement.local.azurestack.external.     |
|*subid*     |Identificador de suscripción del usuario que realiza la llamada. Puede utilizar esta API para hacer consultas únicamente con un usuario que tenga permiso para la suscripción de proveedor predeterminada.         |
|*RegionName*     |  Nombre de la región de la implementación de Azure Stack.       |
|*api-version*     |  Versión del protocolo que se usa para realizar esta solicitud. Debe usar 2016-05-01.       |
|*RegistrationID* |Identificador del registro para un proveedor de recursos específico. |

**Respuesta**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca /resourceHealths?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":
[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/472aaaa6-3f63-43fa-a489-4fd9094e235f/resourceHealths/028c3916-ab86-4e7f-b5c2-0468e607915c",
"name":"028c3916-ab86-4e7f-b5c2-0468e607915c",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths/resourceHealths",
"location":"local",
"tags":{},
"properties":
{"registrationId":"028c3916-ab86-4e7f-b5c2 0468e607915c","namespace":"Microsoft.Fabric.Admin","routePrefix":"/subscriptions/4aa97de3-6b83-4582-86e1 65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local",
"resourceType":"infraRoles",
"resourceName":"Privileged endpoint",
"usageMetrics":[],
"resourceLocation":"local",
"resourceURI":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/Privileged endpoint",
"rpRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},"healthState":"Unknown"
}
}
…
```

**Detalles de la respuesta**

|Argumento  |Descripción  |
|---------|---------|
|*Id*     |   Identificador exclusivo de la alerta.      |
|*name*     |  Nombre interno de la alerta.       |
|*type*     |  Definición de recursos.       |
|*ubicación*     |  Nombre de la región.       |
|*etiquetas*     |     Etiquetas del recurso.    |
|*registrationId*     |   Registro único para el proveedor de recursos.      |
|*resourceType*     |Tipo de recurso.        |
|*resourceName*     |   Nombre del recurso.   |
|*usageMetrics*     |    Métrica de uso del recurso.     |
|*resourceLocation*     |   Nombre de la región donde se implementa.      |
|*resourceURI*     |   URI del recurso.   |
|*alertSummary*     |   Resumen de alertas críticas y de advertencia, estado de mantenimiento.     |

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información acerca de la supervisión de estado de mantenimiento integrada, consulte [Supervisar el estado y las alertas en Azure Stack](azure-stack-monitor-health.md).


