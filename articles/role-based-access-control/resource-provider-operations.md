---
title: Operaciones del proveedor de recursos de Azure Resource Manager | Microsoft Docs
description: Enumera las operaciones están disponibles en los proveedores de recursos de Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: rolyon
ms.openlocfilehash: 08059da95baa9e70b8dba2dd847d0b28669778b7
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161216"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operaciones del proveedor de recursos de Azure Resource Manager

En este artículo se enumeran las operaciones que están disponibles para cada proveedor de recursos de Azure Resource Manager. Dichas operaciones se pueden usar en [roles personalizados](custom-roles.md) para proporcionar un [control de acceso basado en rol (RBAC)](overview.md) pormenorizado a los recursos en Azure. Las cadenas de las operaciones tienen el siguiente formato: `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

Las operaciones del proveedor de recursos están en constante evolución. Para obtener las operaciones más recientes, use [Get AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) o [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.AAD/domainServices/delete | Elimina los servicios de dominio. |
> | . | Microsoft.AAD/domainServices/read | Lee los servicios de dominio. |
> | . | Microsoft.AAD/domainServices/write | Escribe en los servicios de dominio. |
> | . | Microsoft.AAD/locations/operationresults/read | Lee el estado de una operación asincrónica. |
> | . | Microsoft.AAD/Operations/read | Descripción localizada de la operación, tal y como se debería mostrar al usuario. |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | microsoft.aadiam/diagnosticsettings/delete | Eliminar una configuración de diagnóstico. |
> | . | microsoft.aadiam/diagnosticsettings/read | Leer una configuración de diagnóstico. |
> | . | microsoft.aadiam/diagnosticsettings/write | Escribir en una configuración de diagnóstico. |
> | . | microsoft.aadiam/diagnosticsettingscategories/read | Leer las categorías de la configuración de diagnóstico. |
> | . | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | microsoft.aadiam/tenants/providers/Microsoft.Insights/logDefinitions/read | Obtiene los registros disponibles de los inquilinos. |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Addons/operations/read | Obtiene operaciones de RP compatibles. |
> | . | Microsoft.Addons/register/action | Registra la suscripción especificada en Microsoft.Addons |
> | . | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Enumera la información del plan de soporte actual de la suscripción especificada. |
> | . | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Quita el plan de soporte Canonical especificado. |
> | . | Microsoft.Addons/supportProviders/supportPlanTypes/read | Obtiene el plan de soporte Canonical especificado. |
> | . | Microsoft.Addons/supportProviders/supportPlanTypes/write | Agrega el tipo de plan de soporte Canonical especificado. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.ADHybridHealthService/addsservices/action | Crea un nuevo bosque para el inquilino. |
> | . | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Obtiene todos los servidores del nombre de servicio especificado. |
> | . | Microsoft.ADHybridHealthService/addsservices/alerts/read | Obtiene los detalles de las alertas del bosque, como el identificador de la alerta, la última alerta detectada, la descripción de la alerta, la última actualización, el nivel de la alerta, el estado de la alerta, los vínculos para la solución del problema de la alerta, etc. |
> | . | Microsoft.ADHybridHealthService/addsservices/configuration/read | Obtiene la configuración del servicio del bosque. Ejemplo: nombre de bosque, nivel funcional, rol principal FSMO de nomenclatura de dominio, rol principal FSMO de esquema, etc. |
> | . | Microsoft.ADHybridHealthService/addsservices/delete | Elimina un servicio y su servidores, junto con los datos de mantenimiento. |
> | . | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Obtiene los detalles de los dominios y sitios del bosque. Ejemplo: estado del mantenimiento, alertas activas, alertas resueltas, propiedades como el nivel funcional del dominio, el bosque, el maestro de infraestructura, el PDC, el maestro de RID.  |
> | . | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Obtiene el valor de preferencia del usuario del bosque.<br>Ejemplo: MetricCounterName como ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Configuración de los gráficos de la interfaz de usuario, etc. |
> | . | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Obtiene el resumen del bosque para el bosque dado como el nombre de bosque, el número de dominios de este bosque, el número y los detalles de los sitios, etc. |
> | . | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Obtiene la lista de métricas admitidas de un servicio determinado.<br>Por ejemplo, Bloqueos de cuenta de extranet, Solicitudes erróneas totales, Solicitudes de token pendientes (proxy), Solicitudes de token/segundo etc. del servicio ADFS.<br>Autenticaciones NTLM/segundo, Nº de enlaces LDAP correctos/s, Duración de enlace LDAP, Nº de subprocesos LDAP activos, Autenticaciones Kerberos/s, Nº total de subprocesos ATQ, etc. para el servicio ADDomain.<br>Latencia de perfil de ejecución, Conexiones TCP establecidas, Bytes privados de agente de Insights, Exportación de estadísticas a Azure AD para el servicio ADSync. |
> | . | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Dado un servicio, esta API obtiene la información de las métricas.<br>Por ejemplo, esta API se puede usar para obtener información relacionada con: Bloqueos de cuenta de extranet, 
Solicitudes erróneas totales, Solicitudes de token pendientes (proxy), Solicitudes de token/segundo etc. del servicio ADFS.<br>Autenticaciones NTLM/segundo, Nº de enlaces LDAP correctos/s, Duración de enlace LDAP, Nº de subprocesos LDAP activos, Autenticaciones Kerberos/s, Nº total de subprocesos ATQ, etc. para el servicio ADDomain.<br>Latencia de perfil de ejecución, Conexiones TCP establecidas, Bytes privados de agente de Insights, Exportación de estadísticas a Azure AD para el servicio Sync. |
> | . | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Esta API obtiene la lista de todos los servicios ADDomain incorporados para un inquilino Premium. |
> | . | Microsoft.ADHybridHealthService/addsservices/read | Obtiene los detalles del servicio del nombre de servicio especificado. |
> | . | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Obtiene los detalles de la replicación de todos los servidores del nombre de servicio especificado. |
> | . | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Obtiene el número de controladores de dominio y sus errores de replicación, si los hay. |
> | . | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Obtiene la lista completa de controladores de dominio, junto con los detalles de la replicación del bosque dado. |
> | . | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Agrega una instancia de servidor al servicio. |
> | . | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Durante el registro del servidor de ADDomainService, se llama a esta API para obtener las credenciales para la incorporación de nuevos servidores. |
> | . | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Elimina un servidor de un servicio e inquilino dados. |
> | . | Microsoft.ADHybridHealthService/addsservices/write | Crea o actualiza la instancia de ADDomainService para el inquilino. |
> | . | Microsoft.ADHybridHealthService/configuration/action | Actualiza la configuración del inquilino. |
> | . | Microsoft.ADHybridHealthService/configuration/read | Lee la configuración de inquilino. |
> | . | Microsoft.ADHybridHealthService/configuration/write | Crea una configuración de inquilino. |
> | . | Microsoft.ADHybridHealthService/logs/contents/read | Obtiene el contenido de los registros de instalación y registro del agente almacenados en el blob. |
> | . | Microsoft.ADHybridHealthService/logs/read | Obtiene los registros de instalación y registro del agente para el inquilino. |
> | . | Microsoft.ADHybridHealthService/operations/read | Obtiene la lista de operaciones admitidas por el sistema. |
> | . | Microsoft.ADHybridHealthService/register/action | Registra el proveedor de recursos del servicio de mantenimiento de ADHybrid y permite la creación del recurso del servicio de mantenimiento de ADHybrid. |
> | . | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Obtiene la lista de regiones disponibles que utiliza DevOps para dar soporte a los incidentes del cliente. |
> | . | Microsoft.ADHybridHealthService/reports/badpassword/read | Obtiene la lista de intentos de contraseña incorrectos de todos los usuarios en el Servicio de federación de Active Directory. |
> | . | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Obtiene el URI de SAS del blob que contiene el estado y el resultado final del trabajo de informes que acaba de ponerse en cola para la frecuencia de intentos de nombre de usuario/contraseña incorrectos por Id. de usuario, dirección IP y día para un inquilino dado. |
> | . | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Obtiene la lista de inquilinos consentidos de DevOps. Se utiliza normalmente para el soporte técnico para clientes. |
> | . | Microsoft.ADHybridHealthService/reports/isdevops/read | Obtiene un valor que indica si DevOps consiente el inquilino, o no. |
> | . | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Actualiza el valor de userid(objectid) para el inquilino de DevOps seleccionado. |
> | . | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Obtiene la implementación seleccionada para el inquilino dado. |
> | . | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Dado un identificador de inquilino obtiene la ubicación del almacenamiento del inquilino. |
> | . | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Obtiene la ubicación geográfica desde la que se accederá a los datos. |
> | . | Microsoft.ADHybridHealthService/services/action | Actualiza una instancia de servicio en el inquilino. |
> | . | Microsoft.ADHybridHealthService/services/alerts/read | Lee las alertas de un servicio. |
> | . | Microsoft.ADHybridHealthService/services/alerts/read | Lee las alertas de un servicio. |
> | . | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Dado un nombre de característica comprueba si un servicio tiene todo lo necesario para usar dicha característica. |
> | . | Microsoft.ADHybridHealthService/services/delete | Elimina una instancia de servicio del inquilino. |
> | . | Microsoft.ADHybridHealthService/services/exporterrors/read | Obtiene los errores de exportación de un servicio de sincronización dado. |
> | . | Microsoft.ADHybridHealthService/services/exportstatus/read | Obtiene el estado de exportación de un servicio dado. |
> | . | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Obtiene los comentarios de las alertas de un servicio y servidor dados. |
> | . | Microsoft.ADHybridHealthService/services/metricmetadata/read | Obtiene la lista de métricas admitidas de un servicio determinado.<br>Por ejemplo, Bloqueos de cuenta de extranet, Solicitudes erróneas totales, Solicitudes de token pendientes (proxy), Solicitudes de token/segundo etc. del servicio ADFS.<br>Autenticaciones NTLM/segundo, Nº de enlaces LDAP correctos/s, Duración de enlace LDAP, Nº de subprocesos LDAP activos, Autenticaciones Kerberos/s, Nº total de subprocesos ATQ, etc. para el servicio ADDomain.<br>Latencia de perfil de ejecución, Conexiones TCP establecidas, Bytes privados de agente de Insights, Exportación de estadísticas a Azure AD para el servicio ADSync. |
> | . | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Dado un servicio, esta API obtiene el promedio de las métricas para un servicio dado.<br>Por ejemplo, esta API se puede usar para obtener información relacionada con: Bloqueos de cuenta de extranet, 
Solicitudes erróneas totales, Solicitudes de token pendientes (proxy), Solicitudes de token/segundo etc. del servicio ADFS.<br>Autenticaciones NTLM/segundo, Nº de enlaces LDAP correctos/s, Duración de enlace LDAP, Nº de subprocesos LDAP activos, Autenticaciones Kerberos/s, Nº total de subprocesos ATQ, etc. para el servicio ADDomain.<br>Latencia de perfil de ejecución, Conexiones TCP establecidas, Bytes privados de agente de Insights, Exportación de estadísticas a Azure AD para el servicio Sync. |
> | . | Microsoft.ADHybridHealthService/services/metrics/groups/read | Dado un servicio, esta API obtiene la información de las métricas.<br>Por ejemplo, esta API se puede usar para obtener información relacionada con: Bloqueos de cuenta de extranet, 
Solicitudes erróneas totales, Solicitudes de token pendientes (proxy), Solicitudes de token/segundo etc. del servicio ADFS.<br>Autenticaciones NTLM/segundo, Nº de enlaces LDAP correctos/s, Duración de enlace LDAP, Nº de subprocesos LDAP activos, Autenticaciones Kerberos/s, Nº total de subprocesos ATQ, etc. para el servicio ADDomain.<br>Latencia de perfil de ejecución, Conexiones TCP establecidas, Bytes privados de agente de Insights, Exportación de estadísticas a Azure AD para el servicio Sync. |
> | . | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Dado un servicio, esta API obtiene la vista agregada de las métricas de un servicio dado.<br>Por ejemplo, esta API se puede usar para obtener información relacionada con: Bloqueos de cuenta de extranet, 
Solicitudes erróneas totales, Solicitudes de token pendientes (proxy), Solicitudes de token/segundo etc. del servicio ADFS.<br>Autenticaciones NTLM/segundo, Nº de enlaces LDAP correctos/s, Duración de enlace LDAP, Nº de subprocesos LDAP activos, Autenticaciones Kerberos/s, Nº total de subprocesos ATQ, etc. para el servicio ADDomain.<br>Latencia de perfil de ejecución, Conexiones TCP establecidas, Bytes privados de agente de Insights, Exportación de estadísticas a Azure AD para el servicio Sync. |
> | . | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Agrega o actualiza la configuración de supervisión de un servicio. |
> | . | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Obtiene las configuraciones de supervisión de un servicio dado. |
> | . | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Agrega o actualiza las configuraciones de supervisión de un servicio. |
> | . | Microsoft.ADHybridHealthService/services/premiumcheck/read | Esta API obtiene la lista de todos los servicios incorporados para un inquilino Premium. |
> | . | Microsoft.ADHybridHealthService/services/read | Lee las instancias de servicio del inquilino. |
> | . | Microsoft.ADHybridHealthService/services/reports/details/read | Obtiene un informe de los 50 usuarios con más errores de contraseña incorrecta en los últimos 7 días |
> | . | Microsoft.ADHybridHealthService/services/servicemembers/action | Crea una instancia de servidor en el servicio. |
> | . | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Lee las alertas de un servidor. |
> | . | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Durante el registro del servidor, se llama a esta API para obtener las credenciales para la incorporación de nuevos servidores. |
> | . | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | En un servidor determinado, esta API obtiene una lista de los tipos de datos que cargan los servidores y la hora más reciente en que se realizó cada carga. |
> | . | Microsoft.ADHybridHealthService/services/servicemembers/delete | Elimina una instancia de servidor en el servicio. |
> | . | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Obtiene los detalles de los errores de exportación de sincronización de un servicio de sincronización dado. |
> | . | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Dado un servicio, esta API obtiene la información de las métricas.<br>Por ejemplo, esta API se puede usar para obtener información relacionada con: Bloqueos de cuenta de extranet, 
Solicitudes erróneas totales, Solicitudes de token pendientes (proxy), Solicitudes de token/segundo etc. del servicio ADFS.<br>Autenticaciones NTLM/segundo, Nº de enlaces LDAP correctos/s, Duración de enlace LDAP, Nº de subprocesos LDAP activos, Autenticaciones Kerberos/s, Nº total de subprocesos ATQ, etc. para el servicio ADDomain.<br>Latencia de perfil de ejecución, Conexiones TCP establecidas, Bytes privados de agente de Insights, Exportación de estadísticas a Azure AD para el servicio Sync. |
> | . | Microsoft.ADHybridHealthService/services/servicemembers/read | Lee la instancia de servidor en el servicio. |
> | . | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Obtiene la configuración del servicio de un inquilino dado. |
> | . | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Obtiene el estado de la listas blanca de características de un inquilino dado. |
> | . | Microsoft.ADHybridHealthService/services/write | Crea una instancia de servicio del inquilino. |
> | . | Microsoft.ADHybridHealthService/unregister/action | Anula el registro de la suscripción del proveedor de recursos del servicio de mantenimiento de ADHybrid. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Advisor/configurations/read | Obtener configuraciones |
> | . | Microsoft.Advisor/configurations/write | Crea o actualiza la configuración. |
> | . | Microsoft.Advisor/generateRecommendations/action | Genera recomendaciones |
> | . | Microsoft.Advisor/generateRecommendations/read | Obtiene el estado de las recomendaciones generadas |
> | . | Microsoft.Advisor/operations/read | Obtiene las operaciones de Microsoft Advisor. |
> | . | Microsoft.Advisor/recommendations/read | Lee las recomendaciones |
> | . | Microsoft.Advisor/recommendations/suppressions/delete | Elimina las supresiones |
> | . | Microsoft.Advisor/recommendations/suppressions/read | Obtiene las supresiones |
> | . | Microsoft.Advisor/recommendations/suppressions/write | Crea o actualiza supresiones |
> | . | Microsoft.Advisor/register/action | Registra la suscripción de Microsoft Advisor |
> | . | Microsoft.Advisor/suppressions/delete | Elimina las supresiones |
> | . | Microsoft.Advisor/suppressions/read | Obtiene las supresiones |
> | . | Microsoft.Advisor/suppressions/write | Crea o actualiza supresiones |
> | . | Microsoft.Advisor/unregister/action | Anula el registro de la suscripción de Microsoft Advisor. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.AlertsManagement/alerts/changestate/action | Cambia el estado de la alerta. |
> | . | Microsoft.AlertsManagement/alerts/read | Obtiene todas las alertas de los filtros de entrada. |
> | . | Microsoft.AlertsManagement/alertsSummary/read | Obtiene el resumen de las alertas. |
> | . | Microsoft.AlertsManagement/Operations/read | Lee las operaciones proporcionadas. |
> | . | Microsoft.AlertsManagement/smartGroups/changestate/action | Para cambiar el estado del grupo inteligente |
> | . | Microsoft.AlertsManagement/smartGroups/read | Obtiene todos los grupos inteligentes de los filtros de entrada. |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Comprueba que el nombre dado de la instancia de Analysis Server es válido y no está en uso. |
> | . | Microsoft.AnalysisServices/locations/operationresults/read | Recupera la información del resultado de la operación especificada. |
> | . | Microsoft.AnalysisServices/locations/operationstatuses/read | Recupera la información del estado de la operación especificada. |
> | . | Microsoft.AnalysisServices/operations/read | Recupera la información de las operaciones. |
> | . | Microsoft.AnalysisServices/register/action | Registra el proveedor de recursos de Analysis Services. |
> | . | Microsoft.AnalysisServices/servers/delete | Elimina la instancia de Analysis Server. |
> | . | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Enumera el estado de la puerta de enlace asociada con el servidor. |
> | . | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico de Analysis Server. |
> | . | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de Analysis Server. |
> | . | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/logDefinitions/read | Obtiene los registros disponibles de los servidores. |
> | . | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de Analysis Server. |
> | . | Microsoft.AnalysisServices/servers/read | Recupera la información de la instancia de Analysis Server especificada. |
> | . | Microsoft.AnalysisServices/servers/resume/action | Reanuda la instancia de Analysis Server. |
> | . | Microsoft.AnalysisServices/servers/skus/read | Recupera la información de SKU disponible del servidor. |
> | . | Microsoft.AnalysisServices/servers/suspend/action | Suspende la instancia de Analysis Server. |
> | . | Microsoft.AnalysisServices/servers/write | Crea o actualiza la instancia de Analysis Server especificada. |
> | . | Microsoft.AnalysisServices/skus/read | Recupera la información de las SKU. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.ApiManagement/checkNameAvailability/read | Comprueba si el nombre del servicio proporcionado está disponible |
> | . | Microsoft.ApiManagement/operations/read | Lee todas las operaciones de API disponibles del recurso Microsoft.ApiManagement. |
> | . | Microsoft.ApiManagement/register/action | Registra la suscripción para el proveedor de recursos de Microsoft.ApiManagement |
> | . | Microsoft.ApiManagement/reports/read | Obtiene informes agregados según los períodos de tiempo, la región geográfica, los desarrolladores, los productos, las API, las operaciones, la suscripción y el elemento byRequest. |
> | . | Microsoft.ApiManagement/service/apis/delete | Elimina una API ya existente |
> | . | Microsoft.ApiManagement/service/apis/diagnostics/delete | Elimina un diagnóstico ya existente. |
> | . | Microsoft.ApiManagement/service/apis/diagnostics/loggers/delete | Quita la asignación de un registrador con una configuración de diagnóstico. |
> | . | Microsoft.ApiManagement/service/apis/diagnostics/loggers/read | Obtiene una lista de registradores de diagnóstico. |
> | . | Microsoft.ApiManagement/service/apis/diagnostics/loggers/write | Asigna un registrador a una configuración de diagnóstico. |
> | . | Microsoft.ApiManagement/service/apis/diagnostics/read | Obtiene una lista de diagnósticos u obtiene detalles del diagnóstico. |
> | . | Microsoft.ApiManagement/service/apis/diagnostics/write | Agrega un nuevo diagnóstico o actualiza los detalles de los diagnóstico existentes. |
> | . | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Quita los datos adjuntos existentes. |
> | . | Microsoft.ApiManagement/service/apis/issues/attachments/read | Obtiene los datos adjuntos de los problemas u obtiene los detalles de los datos adjuntos de los problemas de API Management  |
> | . | Microsoft.ApiManagement/service/apis/issues/attachments/write | Agrega datos adjuntos de problema de API |
> | . | Microsoft.ApiManagement/service/apis/issues/comments/delete | Elimina un comentario existente. |
> | . | Microsoft.ApiManagement/service/apis/issues/comments/read | Obtiene comentarios de un problema u obtiene los detalles de los comentarios de los problemas de API Management |
> | . | Microsoft.ApiManagement/service/apis/issues/comments/write | Agrega un comentario de un problema de una API |
> | . | Microsoft.ApiManagement/service/apis/issues/delete | Elimina un problema existente |
> | . | Microsoft.ApiManagement/service/apis/issues/read | Obtiene los problemas asociados con una API u obtiene los detalles de los problemas de API Management |
> | . | Microsoft.ApiManagement/service/apis/issues/write | Agrega un problema de una API o actualiza un problema de una API |
> | . | Microsoft.ApiManagement/service/apis/operations/delete | Elimina una operación de API ya existente |
> | . | Microsoft.ApiManagement/service/apis/operations/policies/delete | Elimina la configuración de directivas de las directivas de API Operation. |
> | . | Microsoft.ApiManagement/service/apis/operations/policies/read | Obtiene las directivas de API Operation u obtiene los detalles de configuración de directivas de API Operation. |
> | . | Microsoft.ApiManagement/service/apis/operations/policies/write | Establece los detalles de configuración de directivas de API Operation. |
> | . | Microsoft.ApiManagement/service/apis/operations/policy/delete | Elimina la configuración de directivas de una operación |
> | . | Microsoft.ApiManagement/service/apis/operations/policy/read | Obtiene los detalles de configuración de directivas de una operación |
> | . | Microsoft.ApiManagement/service/apis/operations/policy/write | Establece los detalles de configuración de directivas de una operación |
> | . | Microsoft.ApiManagement/service/apis/operations/read | Obtiene una lista de las operaciones de API existentes o detalles de una operación de API |
> | . | Microsoft.ApiManagement/service/apis/operations/tags/delete | Elimina la asociación de una etiqueta existente con la operación existente. |
> | . | Microsoft.ApiManagement/service/apis/operations/tags/read | Obtiene etiquetas asociadas con la operación u obtiene los detalles de la etiqueta. |
> | . | Microsoft.ApiManagement/service/apis/operations/tags/write | Asocia una etiqueta existente con la operación existente. |
> | . | Microsoft.ApiManagement/service/apis/operations/write | Crea una nueva operación de API o actualiza una operación de API ya existente |
> | . | Microsoft.ApiManagement/service/apis/operationsByTags/read | Obtiene una lista de asociaciones de operaciones o etiquetas. |
> | . | Microsoft.ApiManagement/service/apis/policies/delete | Elimina la configuración de directiva de las directivas de API. |
> | . | Microsoft.ApiManagement/service/apis/policies/read | Obtiene las directivas de API u obtiene los detalles de configuración de directivas de API. |
> | . | Microsoft.ApiManagement/service/apis/policies/write | Establece los detalles de configuración de directivas de una API |
> | . | Microsoft.ApiManagement/service/apis/policy/delete | Elimina la configuración de directivas de la API |
> | . | Microsoft.ApiManagement/service/apis/policy/read | Obtiene los detalles de configuración de directivas de una API |
> | . | Microsoft.ApiManagement/service/apis/policy/write | Establece los detalles de configuración de directivas de una API |
> | . | Microsoft.ApiManagement/service/apis/products/read | Obtiene todos los productos que forman parte de la API. |
> | . | Microsoft.ApiManagement/service/apis/read | Obtiene una lista de todas las API registradas o detalles de una API |
> | . | Microsoft.ApiManagement/service/apis/releases/delete | Quita todas las versiones de la API o quita la versión de la API. |
> | . | Microsoft.ApiManagement/service/apis/releases/read | Obtiene versiones de una API u obtiene detalles de la versión de la API. |
> | . | Microsoft.ApiManagement/service/apis/releases/write | Crea una nueva versión de API o actualiza la versión de API ya existente. |
> | . | Microsoft.ApiManagement/service/apis/revisions/delete | Quita todas las revisiones de una API. |
> | . | Microsoft.ApiManagement/service/apis/revisions/read | Obtiene las revisiones que pertenecen a una API. |
> | . | Microsoft.ApiManagement/service/apis/schemas/delete | Elimina un esquema ya existente. |
> | . | Microsoft.ApiManagement/service/apis/schemas/document/read | Obtiene el documento que describe el esquema. |
> | . | Microsoft.ApiManagement/service/apis/schemas/document/write | Actualiza el documento que describe el esquema. |
> | . | Microsoft.ApiManagement/service/apis/schemas/read | Obtiene todos los esquemas de una API determinada u obtiene los esquemas utilizados por la API. |
> | . | Microsoft.ApiManagement/service/apis/schemas/write | Establece los esquemas que usa la API. |
> | . | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Quita la descripción de la etiqueta de la API. |
> | . | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Obtiene descripciones de las etiquetas en el ámbito de la API u obtiene una descripción de las etiquetas en el ámbito de la API. |
> | . | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Crea o cambia la descripción de la etiqueta en el ámbito de la API. |
> | . | Microsoft.ApiManagement/service/apis/tags/delete | Quita la asociación existente entre API y etiquetas. |
> | . | Microsoft.ApiManagement/service/apis/tags/read | Obtiene todas las asociaciones entre API y etiquetas de la API u obtiene los detalles de la asociación entre API y etiquetas. |
> | . | Microsoft.ApiManagement/service/apis/tags/write | Agrega una nueva asociación entre API y etiquetas. |
> | . | Microsoft.ApiManagement/service/apis/write | Crea una nueva API o actualiza los detalles de una API ya existente |
> | . | Microsoft.ApiManagement/service/apisByTags/read | Obtiene una lista de asociaciones entre API y etiquetas. |
> | . | Microsoft.ApiManagement/service/api-version-sets/delete | Quita un elemento VersionSet ya existente. |
> | . | Microsoft.ApiManagement/service/api-version-sets/read | Obtiene la lista de entidades del grupo de versiones u obtiene los detalles de un elemento VersionSet. |
> | . | Microsoft.ApiManagement/service/api-version-sets/versions/read | Obtiene la lista de entidades de versión. |
> | . | Microsoft.ApiManagement/service/api-version-sets/write | Crea un nuevo elemento VersionSet o actualiza los detalles existentes de VersionSet. |
> | . | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Actualiza los recursos de Microsoft.ApiManagement que se ejecutan en una instancia de Virtual Network para seleccionar la configuración de red actualizada. |
> | . | Microsoft.ApiManagement/service/authorizationServers/delete | Quita un servidor de autorización ya existente |
> | . | Microsoft.ApiManagement/service/authorizationServers/read | Obtiene una lista de servidores de autorización o detalles de un servidor de autorización |
> | . | Microsoft.ApiManagement/service/authorizationServers/write | Crea un nuevo servidor de autorización o actualiza los detalles de un servidor de autorización ya existente |
> | . | Microsoft.ApiManagement/service/backends/delete | Elimina un backend existente |
> | . | Microsoft.ApiManagement/service/backends/read | Obtiene una lista de back-ends o detalles de un back-end |
> | . | Microsoft.ApiManagement/service/backends/reconnect/action | Crea una solicitud de reconexión. |
> | . | Microsoft.ApiManagement/service/backends/write | Agregar un nuevo back-end o actualiza los detalles de un back-end ya existente |
> | . | Microsoft.ApiManagement/service/backup/action | Realiza una copia de seguridad del servicio API Management en el contenedor especificado de una cuenta de almacenamiento proporcionada por el usuario |
> | . | Microsoft.ApiManagement/service/certificates/delete | Elimina un certificado existente |
> | . | Microsoft.ApiManagement/service/certificates/read | Obtiene una lista de certificados o detalles de un certificado |
> | . | Microsoft.ApiManagement/service/certificates/write | Agrega un nuevo certificado |
> | . | Microsoft.ApiManagement/service/delete | Elimina una instancia del servicio API Management |
> | . | Microsoft.ApiManagement/service/diagnostics/delete | Elimina un diagnóstico ya existente. |
> | . | Microsoft.ApiManagement/service/diagnostics/loggers/delete | Quita la asignación de un registrador con una configuración de diagnóstico. |
> | . | Microsoft.ApiManagement/service/diagnostics/loggers/read | Obtiene una lista de registradores de diagnóstico. |
> | . | Microsoft.ApiManagement/service/diagnostics/loggers/write | Asigna un registrador a una configuración de diagnóstico. |
> | . | Microsoft.ApiManagement/service/diagnostics/read | Obtiene una lista de diagnósticos u obtiene detalles del diagnóstico. |
> | . | Microsoft.ApiManagement/service/diagnostics/write | Agrega un nuevo diagnóstico o actualiza los detalles de los diagnóstico existentes. |
> | . | Microsoft.ApiManagement/service/getssotoken/action | Obtiene el token de SSO que se puede usar para iniciar sesión en el portal heredado del servicio API Management como administrador |
> | . | Microsoft.ApiManagement/service/groups/delete | Elimina un grupo ya existente |
> | . | Microsoft.ApiManagement/service/groups/read | Obtiene una lista de grupos o detalles de un grupo |
> | . | Microsoft.ApiManagement/service/groups/users/delete | Elimina un usuario existente de un grupo que ya existe |
> | . | Microsoft.ApiManagement/service/groups/users/read | Obtiene una lista de los usuarios del grupo |
> | . | Microsoft.ApiManagement/service/groups/users/write | Agrega un usuario existente a un grupo que ya existe |
> | . | Microsoft.ApiManagement/service/groups/write | Crea un nuevo grupo o actualiza los detalles de un grupo ya existente |
> | . | Microsoft.ApiManagement/service/identityProviders/delete | Elimina el proveedor de identidades existente |
> | . | Microsoft.ApiManagement/service/identityProviders/read | Obtiene una lista de proveedores de identidades o detalles del proveedor de identidades |
> | . | Microsoft.ApiManagement/service/identityProviders/write | Crea un nuevo proveedor de identidades o actualiza los detalles de un proveedor ya existente |
> | . | Microsoft.ApiManagement/service/locations/networkstatus/read | Obtiene el estado de acceso de red de los recursos de los que depende el servicio en la ubicación. |
> | . | Microsoft.ApiManagement/service/loggers/delete | Quita un registrador existente |
> | . | Microsoft.ApiManagement/service/loggers/read | Obtiene una lista de registradores o los detalles de uno de ellos |
> | . | Microsoft.ApiManagement/service/loggers/write | Agrega un nuevo registrador o actualiza los detalles de los registradores existentes |
> | . | Microsoft.ApiManagement/service/managedeployments/action | Cambia SKU y unidades, y agrega o quita las implementaciones regionales del servicio API Management |
> | . | Microsoft.ApiManagement/service/networkstatus/read | Obtiene el estado de acceso de red de los recursos de los que depende el servicio. |
> | . | Microsoft.ApiManagement/service/notifications/action | Envía una notificación a un usuario especificado. |
> | . | Microsoft.ApiManagement/service/notifications/read | Obtiene todas las notificaciones del editor de API Management u obtiene los detalles de la notificación del editor de API Management. |
> | . | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Quita el correo electrónico existente asociado con una notificación. |
> | . | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Obtiene los destinatarios de correo electrónico asociados con la notificación del editor de API Management. |
> | . | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Crea un nuevo destinatario de correo electrónico de la notificación. |
> | . | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Quita el usuario asociado con los destinatarios de la notificación. |
> | . | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Obtiene los usuarios de destinatario asociados con la notificación. |
> | . | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Agrega usuarios a los destinatarios de la notificación. |
> | . | Microsoft.ApiManagement/service/notifications/write | Crea o actualiza la notificación del editor de API Management. |
> | . | Microsoft.ApiManagement/service/openidConnectProviders/delete | Elimina un proveedor de OpenID Connect ya existente |
> | . | Microsoft.ApiManagement/service/openidConnectProviders/read | Obtiene una lista de proveedores de OpenID Connect o detalles del proveedor de OpenID Connect |
> | . | Microsoft.ApiManagement/service/openidConnectProviders/write | Crea un nuevo proveedor de OpenID Connect o actualiza los detalles de un proveedor de OpenID Connect ya existente |
> | . | Microsoft.ApiManagement/service/operationresults/read | Obtiene el estado actual de una operación de larga duración |
> | . | Microsoft.ApiManagement/service/policies/delete | Quita la configuración de directiva de las directivas de inquilino. |
> | . | Microsoft.ApiManagement/service/policies/read | Obtiene las directivas del inquilino u obtiene los detalles de configuración de directivas del inquilino. |
> | . | Microsoft.ApiManagement/service/policies/write | Establece los detalles de configuración de directivas del inquilino. |
> | . | Microsoft.ApiManagement/service/policySnippets/read | Obtiene todos los fragmentos de código de la directiva. |
> | . | Microsoft.ApiManagement/service/portalsettings/read | Obtiene la configuración para registrarse en el portal u obtiene la configuración de inicio de sesión del portal u obtiene la configuración de delegación del portal. |
> | . | Microsoft.ApiManagement/service/portalsettings/write | Actualiza la configuración para registrarse o actualiza la configuración de inicio de sesión o actualiza la configuración de delegación. |
> | . | Microsoft.ApiManagement/service/products/apis/delete | Elimina una API existente de un producto que ya existe |
> | . | Microsoft.ApiManagement/service/products/apis/read | Obtiene una lista de las API que se agregan a un producto existente |
> | . | Microsoft.ApiManagement/service/products/apis/write | Agrega una API existente a un producto que ya existe |
> | . | Microsoft.ApiManagement/service/products/delete | Elimina un producto ya existente |
> | . | Microsoft.ApiManagement/service/products/groups/delete | Elimina la asociación de un grupo de desarrolladores ya existente con un producto que ya existe |
> | . | Microsoft.ApiManagement/service/products/groups/read | Obtiene una lista de grupos de desarrolladores asociados con el producto |
> | . | Microsoft.ApiManagement/service/products/groups/write | Asocia un grupo de desarrolladores ya existente con un producto que ya existe |
> | . | Microsoft.ApiManagement/service/products/policies/delete | Quita la configuración de directiva de las directivas del producto. |
> | . | Microsoft.ApiManagement/service/products/policies/read | Obtiene las directivas del producto u obtiene los detalles de configuración de directivas del producto. |
> | . | Microsoft.ApiManagement/service/products/policies/write | Establece los detalles de configuración de directivas del producto. |
> | . | Microsoft.ApiManagement/service/products/policy/delete | Elimina la configuración de directivas de un producto ya existente |
> | . | Microsoft.ApiManagement/service/products/policy/read | Obtiene la configuración de directivas de un producto ya existente |
> | . | Microsoft.ApiManagement/service/products/policy/write | Establece la configuración de directivas de un producto ya existente |
> | . | Microsoft.ApiManagement/service/products/read | Obtiene una lista de productos o detalles de un producto |
> | . | Microsoft.ApiManagement/service/products/subscriptions/read | Obtiene una lista de suscripciones de productos |
> | . | Microsoft.ApiManagement/service/products/tags/delete | Elimina la asociación de una etiqueta existente con el producto existente. |
> | . | Microsoft.ApiManagement/service/products/tags/read | Obtiene etiquetas asociadas con el producto u obtiene los detalles de la etiqueta. |
> | . | Microsoft.ApiManagement/service/products/tags/write | Asocia una etiqueta existente con el producto existente. |
> | . | Microsoft.ApiManagement/service/products/write | Crea un nuevo producto o actualiza los detalles de un producto existente |
> | . | Microsoft.ApiManagement/service/properties/delete | Elimina una propiedad ya existente |
> | . | Microsoft.ApiManagement/service/properties/read | Obtiene una lista de todas las propiedades o detalles de una propiedad especificada |
> | . | Microsoft.ApiManagement/service/properties/write | Crea una nueva propiedad o actualiza el valor de una propiedad especificada |
> | . | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del servicio de API Management. |
> | . | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del servicio de API Management. |
> | . | Microsoft.ApiManagement/service/providers/Microsoft.Insights/logDefinitions/read | Obtiene los registros disponibles del servicio de API Management. |
> | . | Microsoft.ApiManagement/service/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles del servicio de API Management. |
> | . | Microsoft.ApiManagement/service/quotas/periods/read | Obtener el valor del contador de cuota del período. |
> | . | Microsoft.ApiManagement/service/quotas/periods/write | Establece el valor actual del contador de cuota. |
> | . | Microsoft.ApiManagement/service/quotas/read | Obtiene los valores de cuota. |
> | . | Microsoft.ApiManagement/service/quotas/write | Establece el valor actual del contador de cuota. |
> | . | Microsoft.ApiManagement/service/read | Lectura de los metadatos de una instancia del servicio API Management |
> | . | Microsoft.ApiManagement/service/reports/read | Obtiene el informe agregado según el período de tiempo u obtiene el informe agregado según la región geográfica u obtiene el informe agregado según los desarrolladores.<br>u obtiene el informe agregado según los productos.<br>u obtiene el informe agregado según las API u obtiene el informe agregado según las operaciones u obtiene el informe agregado según la suscripción.<br>u obtiene los datos de los informes de las solicitudes. |
> | . | Microsoft.ApiManagement/service/restore/action | Restauración del servicio API Management desde el contenedor especificado de una cuenta de almacenamiento proporcionada por el usuario |
> | . | Microsoft.ApiManagement/service/subscriptions/delete | Elimina la suscripción. Esta operación se puede usar para eliminar la suscripción |
> | . | Microsoft.ApiManagement/service/subscriptions/read | Obtiene una lista de suscripciones de productos o detalles de una suscripción de producto |
> | . | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Regenera la clave principal de suscripción |
> | . | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Regenera la clave secundaria de suscripción |
> | . | Microsoft.ApiManagement/service/subscriptions/write | Suscribe un usuario existente a un producto existente o actualiza los detalles de la suscripción existente. Esta operación se puede usar para renovar la suscripción |
> | . | Microsoft.ApiManagement/service/tagResources/read | Obtiene una lista de etiquetas con recursos asociados. |
> | . | Microsoft.ApiManagement/service/tags/delete | Quita una etiqueta ya existente. |
> | . | Microsoft.ApiManagement/service/tags/read | Obtiene una lista de etiquetas u obtiene los detalles de una etiqueta. |
> | . | Microsoft.ApiManagement/service/tags/write | Agrega una nueva etiqueta o actualiza los detalles de una etiqueta existente. |
> | . | Microsoft.ApiManagement/service/templates/delete | Restablece la plantilla de correo electrónico predeterminada de API Management. |
> | . | Microsoft.ApiManagement/service/templates/read | Obtiene todas las plantillas de correo electrónico u obtiene los detalles de la plantilla de correo electrónico de API Management. |
> | . | Microsoft.ApiManagement/service/templates/write | Crea o actualiza la plantilla de correo electrónico de API Management o actualiza la plantilla de correo electrónico de API Management. |
> | . | Microsoft.ApiManagement/service/tenant/delete | Elimina la configuración de directivas del inquilino |
> | . | Microsoft.ApiManagement/service/tenant/deploy/action | Ejecuta una tarea de implementación para aplicar los cambios de la bifurcación git especificada en la configuración de la base de datos. |
> | . | Microsoft.ApiManagement/service/tenant/operationResults/read | Obtiene una lista de resultados de operaciones o el resultado de una operación específica |
> | . | Microsoft.ApiManagement/service/tenant/read | Obtiene la configuración de directiva del inquilino u obtiene los detalles de la información de acceso del inquilino. |
> | . | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Regenera la clave de acceso principal |
> | . | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Regenera la clave de acceso secundaria |
> | . | Microsoft.ApiManagement/service/tenant/save/action | Crea una confirmación con la instantánea de configuración en la bifurcación especificada del repositorio |
> | . | Microsoft.ApiManagement/service/tenant/syncState/read | Obtiene el estado de la última sincronización de git |
> | . | Microsoft.ApiManagement/service/tenant/validate/action | Valida los cambios de la bifurcación git especificada |
> | . | Microsoft.ApiManagement/service/tenant/write | Establece la configuración de directiva del inquilino o actualiza los detalles de la información de acceso del inquilino. |
> | . | Microsoft.ApiManagement/service/updatecertificate/action | Carga el certificado SSL de un servicio API Management |
> | . | Microsoft.ApiManagement/service/updatehostname/action | Configura, actualiza o elimina los nombres de dominio personalizado de un servicio API Management |
> | . | Microsoft.ApiManagement/service/users/action | Registra un nuevo usuario. |
> | . | Microsoft.ApiManagement/service/users/applications/attachments/delete | Quita los datos adjuntos. |
> | . | Microsoft.ApiManagement/service/users/applications/attachments/read | Obtiene los datos adjuntos de una aplicación u obtiene los datos adjuntos. |
> | . | Microsoft.ApiManagement/service/users/applications/attachments/write | Agrega los datos adjuntos a la aplicación. |
> | . | Microsoft.ApiManagement/service/users/applications/delete | Quita la aplicación existente. |
> | . | Microsoft.ApiManagement/service/users/applications/read | Obtiene una lista de todas las aplicaciones de usuario u obtiene los detalles de la aplicación de API Management. |
> | . | Microsoft.ApiManagement/service/users/applications/write | Registra una aplicación en API Management o actualiza los detalles de la aplicación. |
> | . | Microsoft.ApiManagement/service/users/delete | Quita una cuenta de usuario |
> | . | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Genera la dirección URL de SSO. La dirección URL se puede utilizar para acceder al portal de administración |
> | . | Microsoft.ApiManagement/service/users/groups/read | Obtiene una lista de grupos de usuario |
> | . | Microsoft.ApiManagement/service/users/keys/read | Obtiene una lista de claves de usuario |
> | . | Microsoft.ApiManagement/service/users/read | Obtiene una lista de usuarios registrados o los detalles de la cuenta de un usuario |
> | . | Microsoft.ApiManagement/service/users/subscriptions/read | Obtiene una lista de suscripciones de usuario |
> | . | Microsoft.ApiManagement/service/users/token/action | Obtiene el token de acceso al token de un usuario. |
> | . | Microsoft.ApiManagement/service/users/write | Registra un nuevo usuario o actualiza los detalles de la cuenta de un usuario existente |
> | . | Microsoft.ApiManagement/service/write | Creación de una nueva instancia del servicio API Management |
> | . | Microsoft.ApiManagement/unregister/action | Anula el registro de la suscripción para el proveedor de recursos de Microsoft.ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Authorization/checkAccess/action | Comprueba si el autor de la llamada tiene permiso para realizar una acción específica. |
> | . | Microsoft.Authorization/classicAdministrators/delete | Quita al administrador de la suscripción. |
> | . | Microsoft.Authorization/classicAdministrators/read | Lee los administradores para la suscripción. |
> | . | Microsoft.Authorization/classicAdministrators/write | Agrega o modifica el administrador para una suscripción. |
> | . | Microsoft.Authorization/elevateAccess/action | Concede al llamador acceso de administrador de acceso de usuario en el ámbito de inquilinos |
> | . | Microsoft.Authorization/locks/delete | Elimina bloqueos en el ámbito especificado. |
> | . | Microsoft.Authorization/locks/read | Obtiene bloqueos en el ámbito especificado. |
> | . | Microsoft.Authorization/locks/write | Agrega bloqueos en el ámbito especificado. |
> | . | Microsoft.Authorization/permissions/read | Enumera todos los permisos que tiene el llamador en un ámbito concreto. |
> | . | Microsoft.Authorization/policyAssignments/delete | Elimina una asignación de directiva en el ámbito especificado. |
> | . | Microsoft.Authorization/policyAssignments/read | Obtiene información sobre una asignación de directiva. |
> | . | Microsoft.Authorization/policyAssignments/write | Crea una asignación de directiva en el ámbito especificado. |
> | . | Microsoft.Authorization/policyDefinitions/delete | Elimina una definición de directiva. |
> | . | Microsoft.Authorization/policyDefinitions/read | Obtiene información sobre una definición de directiva. |
> | . | Microsoft.Authorization/policyDefinitions/write | Crea una definición de directiva personalizada. |
> | . | Microsoft.Authorization/policySetDefinitions/delete | Elimina una definición de un conjunto de directivas. |
> | . | Microsoft.Authorization/policySetDefinitions/read | Obtiene información sobre una definición de un conjunto de directivas. |
> | . | Microsoft.Authorization/policySetDefinitions/write | Crea una definición personalizada de un conjunto de directivas. |
> | . | Microsoft.Authorization/providerOperations/read | Obtiene operaciones para todos los proveedores de recursos que pueden usarse en definiciones de roles. |
> | . | Microsoft.Authorization/roleAssignments/delete | Elimine una asignación de roles en el ámbito especificado. |
> | . | Microsoft.Authorization/roleAssignments/read | Obtiene información sobre una asignación de roles. |
> | . | Microsoft.Authorization/roleAssignments/write | Crea una asignación de roles en el ámbito especificado. |
> | . | Microsoft.Authorization/roleDefinitions/delete | Elimina la definición de roles personalizada especificada. |
> | . | Microsoft.Authorization/roleDefinitions/read | Obtiene información sobre una definición de roles. |
> | . | Microsoft.Authorization/roleDefinitions/write | Crea o actualiza una definición de roles personalizada con permisos especificados y ámbitos asignables. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Lee la información de registro de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Escribe una solicitud para regenerar las claves de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/certificates/delete | Elimina un recurso de certificado de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/certificates/getCount/action | Lee el número de certificados |
> | . | Microsoft.Automation/automationAccounts/certificates/read | Obtiene un recurso de credencial de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/certificates/write | Crea o actualiza un recurso de certificado de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/compilationjobs/read | Lee una compilación de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/compilationjobs/read | Lee una compilación de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/compilationjobs/write | Escribe una compilación de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/compilationjobs/write | Escribe una compilación de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/configurations/content/read | Lee el contenido multimedia de la configuración |
> | . | Microsoft.Automation/automationAccounts/configurations/delete | Elimina contenido de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/configurations/getCount/action | Lee el recuento de contenido de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/configurations/read | Obtiene contenidos de DSC de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/configurations/write | Escribe contenido de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/connections/delete | Elimina un recurso de conexión de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/connections/getCount/action | Lee el número de conexiones |
> | . | Microsoft.Automation/automationAccounts/connections/read | Obtiene un recurso de conexión de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/connections/write | Crea o actualiza un recurso de conexión de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/connectionTypes/delete | Elimina un recurso de tipo de conexión de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/connectionTypes/read | Obtiene un recurso de tipo de conexión de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/connectionTypes/write | Crea un recurso de tipo de conexión de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/credentials/delete | Elimina un recurso de credencial de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/credentials/getCount/action | Lee el número de credenciales |
> | . | Microsoft.Automation/automationAccounts/credentials/read | Obtiene un recurso de credencial de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/credentials/write | Crea o actualiza un recurso de credencial de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/delete | Elimina una cuenta de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.Automation/automationAccounts/diagnosticSettings/write | Establece la configuración de diagnóstico del recurso. |
> | . | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Elimina los recursos de Hybrid Runbook Worker |
> | . | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lee los recursos de Hybrid Runbook Worker |
> | . | Microsoft.Automation/automationAccounts/jobs/output/read | Obtiene la salida de un trabajo |
> | . | Microsoft.Automation/automationAccounts/jobs/read | Obtiene un trabajo de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/jobs/resume/action | Reanuda un trabajo de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Obtiene el contenido del runbook de Azure Automation en el momento de la ejecución del trabajo |
> | . | Microsoft.Automation/automationAccounts/jobs/stop/action | Detiene un trabajo de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtiene un flujo de trabajos de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtiene un flujo de trabajos de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende un trabajo de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/jobs/write | Crea un trabajo de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/jobSchedules/delete | Elimina una programación de trabajos de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/jobSchedules/read | Obtiene una programación de trabajos de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/jobSchedules/write | Crea una programación de trabajos de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtiene el área de trabajo vinculada a la cuenta de Automation. |
> | . | Microsoft.Automation/automationAccounts/listKeys/action | Lee las claves de la cuenta de Automation. |
> | . | Microsoft.Automation/automationAccounts/logDefinitions/read | Obtiene los registros disponibles en la cuenta de Automation. |
> | . | Microsoft.Automation/automationAccounts/modules/activities/read | Obtiene las actividades de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/modules/delete | Elimina un módulo de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/modules/getCount/action | Obtiene el número de módulos de la cuenta de Automation |
> | . | Microsoft.Automation/automationAccounts/modules/read | Obtiene un módulo de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/modules/write | Crea o actualiza un módulo de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Elimina la configuración de nodo de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Lee el contenido de la configuración de nodo de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Lee la configuración de nodo de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Escribe la configuración de nodo de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/nodecounts/read | Lee el resumen del número de nodos del tipo especificado |
> | . | Microsoft.Automation/automationAccounts/nodes/delete | Elimina los nodos de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/nodes/read | Lee los nodos de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Lee el contenido de los informes de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/nodes/reports/read | Lee los informes de DSC de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/nodes/write | Crea o actualiza nodos de DSC de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Obtiene el elemento TypeFields de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las definiciones de las métricas de Automation. |
> | . | Microsoft.Automation/automationAccounts/read | Obtiene una cuenta de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/content/read | Obtiene el contenido de un runbook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/delete | Elimina un runbook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Crea el contenido de un borrador de runbook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Obtiene el resultado de la operación del borrador de un runbook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/draft/publish/action | Publica un borrador de runbook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/draft/read | Obtiene un borrador de runbook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Obtiene un trabajo de prueba del borrador de runbook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Reanuda un trabajo de prueba del borrador de runbook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Detiene un trabajo de prueba del borrador de runbook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Suspende un trabajo de prueba del borrador de runbook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Crea un trabajo de prueba del borrador de runbook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Deshace las ediciones de un borrador de runbook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Obtiene el número de runbooks de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/read | Obtiene un runbook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/runbooks/write | Crea o actualiza un runbook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/schedules/delete | Elimina un recurso de programación de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/schedules/getCount/action | Obtiene el número de programaciones de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/schedules/read | Obtiene un recurso de programación de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/schedules/write | Crea o actualiza un recurso de programación de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/statistics/read | Obtiene las estadísticas de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Obtiene un equipo de implementación de actualizaciones de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Obtiene un trabajo de revisión de la administración de actualizaciones de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/usages/read | Obtiene el uso de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/variables/delete | Elimina un recurso de variable de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/variables/read | Lee un recurso de variable en Azure Automation |
> | . | Microsoft.Automation/automationAccounts/variables/write | Crea o actualiza un recurso de variable de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/watchers/delete | Elimina un trabajo de monitor de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/watchers/read | Obtiene un trabajo de monitor de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/watchers/start/action | Inicia un trabajo de monitor de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/watchers/stop/action | Detiene un trabajo de monitor de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/watchers/streams/read | Obtiene un flujo de trabajos de monitor de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Elimina acciones de un trabajo de monitor de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Obtiene acciones de un trabajo de monitor de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Crea acciones de un trabajo de monitor de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/watchers/write | Crea un trabajo de monitor de Azure Automation. |
> | . | Microsoft.Automation/automationAccounts/webhooks/action | Genera un identificador URI para un webhook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/webhooks/delete | Elimina un webhook de Azure Automation  |
> | . | Microsoft.Automation/automationAccounts/webhooks/read | Lee un webhook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/webhooks/write | Crea o actualiza un webhook de Azure Automation |
> | . | Microsoft.Automation/automationAccounts/write | Crea o actualiza una cuenta de Azure Automation |
> | . | Microsoft.Automation/operations/read | Obtiene las operaciones disponibles para los recursos de Azure Automation |
> | . | Microsoft.Automation/register/action | Registra la suscripción a Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Elimina el recurso del directorio B2C. |
> | . | Microsoft.AzureActiveDirectory/b2cDirectories/read | Comprueba el recurso del directorio B2C. |
> | . | Microsoft.AzureActiveDirectory/b2cDirectories/write | Crea o actualiza el recurso del directorio B2C. |
> | . | Microsoft.AzureActiveDirectory/operations/read | Lee todas las operaciones de API disponibles para el proveedor de recursos Microsoft.AzureActiveDirectory. |
> | . | Microsoft.AzureActiveDirectory/register/action | Registra la suscripción para el proveedor de recursos Microsoft.AzureActiveDirectory. |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.AzureStack/Operations/read | Obtiene las propiedades de una operación del proveedor de recursos. |
> | . | Microsoft.AzureStack/register/action | Registra la suscripción con el proveedor de recursos Microsoft.AzureStack. |
> | . | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Elimina una suscripción de cliente de Azure Stack. |
> | . | Microsoft.AzureStack/registrations/customerSubscriptions/read | Obtiene las propiedades de una suscripción de cliente de Azure Stack. |
> | . | Microsoft.AzureStack/registrations/customerSubscriptions/write | Crea o actualiza una suscripción de cliente de Azure Stack. |
> | . | Microsoft.AzureStack/registrations/delete | Elimina un registro de Azure Stack. |
> | . | Microsoft.AzureStack/registrations/getActivationKey/action | Obtiene la clave de activación de Azure Stack más reciente. |
> | . | Microsoft.AzureStack/registrations/products/listDetails/action | Recupera detalles ampliados de un producto de Marketplace de Azure Stack. |
> | . | Microsoft.AzureStack/registrations/products/read | Obtiene las propiedades de un producto de Marketplace de Azure Stack. |
> | . | Microsoft.AzureStack/registrations/read | Obtiene las propiedades de un registro de Azure Stack. |
> | . | Microsoft.AzureStack/registrations/write | Crea o actualiza un registro de Azure Stack. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Batch/batchAccounts/applications/delete | Elimina una aplicación |
> | . | Microsoft.Batch/batchAccounts/applications/read | Enumera las aplicaciones u obtiene las propiedades de una aplicación |
> | . | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Activa un paquete de aplicación |
> | . | Microsoft.Batch/batchAccounts/applications/versions/delete | Elimina un paquete de aplicación |
> | . | Microsoft.Batch/batchAccounts/applications/versions/read | Obtiene las propiedades de un paquete de aplicación |
> | . | Microsoft.Batch/batchAccounts/applications/versions/write | Crea un nuevo paquete de aplicación o actualiza uno ya existente |
> | . | Microsoft.Batch/batchAccounts/applications/write | Crea una nueva aplicación o actualiza una aplicación ya existente |
> | . | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Obtiene los resultados de una operación de certificado de larga ejecución en una cuenta de Batch. |
> | . | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Cancela el error al eliminar un certificado en una cuenta de Batch. |
> | . | Microsoft.Batch/batchAccounts/certificates/delete | Elimina un certificado de una cuenta de Batch. |
> | . | Microsoft.Batch/batchAccounts/certificates/read | Enumera los certificados de una cuenta de Batch u obtiene las propiedades de un certificado. |
> | . | Microsoft.Batch/batchAccounts/certificates/write | Crea un nuevo certificado en una cuenta de Batch o actualiza un certificado existente. |
> | . | Microsoft.Batch/batchAccounts/delete | Elimina una cuenta de Batch |
> | . | Microsoft.Batch/batchAccounts/listkeys/action | Enumera las claves de acceso de una cuenta de Batch |
> | . | Microsoft.Batch/batchAccounts/operationResults/read | Obtiene los resultados de una operación de cuenta de Batch de larga ejecución. |
> | . | Microsoft.Batch/batchAccounts/poolOperationResults/read | Obtiene los resultados de una operación de grupo de larga ejecución en una cuenta de Batch. |
> | . | Microsoft.Batch/batchAccounts/pools/delete | Elimina un grupo de una cuenta de Batch. |
> | . | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Deshabilita el escalado automático de un grupo de una cuenta de Batch. |
> | . | Microsoft.Batch/batchAccounts/pools/read | Enumera los grupos de una cuentas de Batch u obtiene las propiedades de un grupo. |
> | . | Microsoft.Batch/batchAccounts/pools/stopResize/action | Detiene un operación en curso de cambio de tamaño en un grupo de una cuenta de Batch. |
> | . | Microsoft.Batch/batchAccounts/pools/upgradeOs/action | Actualiza el sistema operativo de un grupo de una cuenta de Batch. |
> | . | Microsoft.Batch/batchAccounts/pools/write | Crea un nuevo grupo en una cuenta de Batch o actualiza un grupo ya existente. |
> | . | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/logDefinitions/read | Obtiene los registros disponibles del servicio de Batch. |
> | . | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles del servicio de Batch. |
> | . | Microsoft.Batch/batchAccounts/read | Enumera las cuentas de Batch u obtiene las propiedades de una de ellas |
> | . | Microsoft.Batch/batchAccounts/regeneratekeys/action | Regenera las claves de acceso de una cuenta de Batch |
> | . | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Sincroniza las teclas de acceso para la cuenta de almacenamiento automática configurada para una cuenta de Batch |
> | . | Microsoft.Batch/batchAccounts/write | Crea una nueva cuenta de Batch o actualiza una cuenta de Batch ya existente |
> | . | Microsoft.Batch/locations/checkNameAvailability/action | Comprueba que el nombre de la cuenta sea válido y que no esté en uso. |
> | . | Microsoft.Batch/locations/quotas/read | Obtiene las cuotas de Batch de la suscripción especificada en la región especificada de Azure |
> | . | Microsoft.Batch/register/action | Registra la suscripción para el proveedor de recursos de Batch y habilita la creación de cuentas de Batch |
> | . | Microsoft.Batch/unregister/action | Quita el registro de la suscripción para el proveedor de recursos de Batch evitando así la creación de cuentas de Batch. |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.BatchAI/clusters/delete | Elimina un clúster de Batch AI. |
> | . | Microsoft.BatchAI/clusters/read | Enumera los clúster de Batch AI u obtiene las propiedades de un clúster de Batch AI. |
> | . | Microsoft.BatchAI/clusters/remoteLoginInformation/action | Enumera la información de inicio de sesión remoto de un clúster de Batch AI. |
> | . | Microsoft.BatchAI/clusters/write | Crea un nuevo clúster de Batch AI o actualiza un clúster de Batch AI ya existente. |
> | . | Microsoft.BatchAI/fileservers/delete | Elimina un servidor de archivos de Batch AI. |
> | . | Microsoft.BatchAI/fileservers/read | Enumera los servidores de archivos de Batch AI u obtiene las propiedades de servidor de archivos de Batch AI. |
> | . | Microsoft.BatchAI/fileservers/resume/action | Reanuda un servidor de archivos de Batch AI. |
> | . | Microsoft.BatchAI/fileservers/suspend/action | Suspende un servidor de archivos de Batch AI. |
> | . | Microsoft.BatchAI/fileservers/write | Crea un nuevo servidor de archivos de Batch AI o actualiza un servidor de archivos de Batch AI ya existente. |
> | . | Microsoft.BatchAI/jobs/delete | Elimina un trabajo de Batch AI. |
> | . | Microsoft.BatchAI/jobs/read | Enumera los trabajos de Batch AI u obtiene las propiedades de un trabajo de Batch AI. |
> | . | Microsoft.BatchAI/jobs/remoteLoginInformation/action | Enumera la información de inicio de sesión remoto de un trabajo de Batch AI. |
> | . | Microsoft.BatchAI/jobs/terminate/action | Finaliza un trabajo de Batch AI. |
> | . | Microsoft.BatchAI/jobs/write | Crea un nuevo trabajo de Batch AI o actualiza un trabajo de Batch AI ya existente |
> | . | Microsoft.BatchAI/register/action | Registra la suscripción del proveedor de recursos de Batch AI y habilita la creación de recursos de Batch AI. |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Billing/billingPeriods/read | Enumera los períodos de facturación disponibles. |
> | . | Microsoft.Billing/invoices/read | Enumera las facturas disponibles |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.BingMaps/mapApis/Delete | Elimina una operación |
> | . | Microsoft.BingMaps/mapApis/listSecrets/action | Enumera los secretos |
> | . | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Lee el token de autorización de inicio de sesión único del recurso |
> | . | Microsoft.BingMaps/mapApis/Read | Lee una operación |
> | . | Microsoft.BingMaps/mapApis/regenerateKey/action | Regenera la clave |
> | . | Microsoft.BingMaps/mapApis/Write | Escribe una operación |
> | . | Microsoft.BingMaps/Operations/read | Descripción de la operación. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Cache/checknameavailability/action | Comprueba si un nombre está disponible para su uso con una nueva instancia de Redis Cache |
> | . | Microsoft.Cache/locations/operationresults/read | Obtiene el resultado de una operación de larga ejecución para la cual el encabezado "Location" se devolvió previamente al cliente. |
> | . | Microsoft.Cache/operations/read | Enumera las operaciones que admite el proveedor "Microsoft.Cache". |
> | . | Microsoft.Cache/redis/delete | Elimina la instancia completa de Redis Cache |
> | . | Microsoft.Cache/redis/export/action | Exporta datos de Redis a blobs de almacenamiento prefijados en un formato especificado |
> | . | Microsoft.Cache/redis/firewallRules/delete | Elimina las reglas de firewall IP de una instancia de Redis Cache |
> | . | Microsoft.Cache/redis/firewallRules/read | Obtiene las reglas de firewall IP de una instancia de Redis Cache |
> | . | Microsoft.Cache/redis/firewallRules/write | Edita las reglas de firewall IP de una instancia de Redis Cache |
> | . | Microsoft.Cache/redis/forceReboot/action | Fuerza el reinicio de una instancia de memoria caché, posiblemente con pérdida de datos. |
> | . | Microsoft.Cache/redis/import/action | Importa datos de un formato especificado desde varios blobs en Redis |
> | . | Microsoft.Cache/redis/linkedservers/delete | Elimina un servidor vinculado de una instancia de Redis Cache |
> | . | Microsoft.Cache/redis/linkedservers/read | Obtiene los servidores vinculados asociados con una instancia de Redis Cache. |
> | . | Microsoft.Cache/redis/linkedservers/write | Agrega un servidor vinculado a una instancia de Redis Cache |
> | . | Microsoft.Cache/redis/listKeys/action | Visualiza el valor de las claves de acceso de Redis Cache en el portal de administración |
> | . | Microsoft.Cache/redis/listUpgradeNotifications/read | Enumera las notificaciones de actualización más recientes para el inquilino de la caché. |
> | . | Microsoft.Cache/redis/metricDefinitions/read | Obtiene las métricas disponibles para una instancia de Redis Cache |
> | . | Microsoft.Cache/redis/patchSchedules/delete | Elimina la programación de una aplicación de revisiones de una instancia de Redis Cache |
> | . | Microsoft.Cache/redis/patchSchedules/read | Obtiene la programación de una aplicación de revisiones de una instancia de Redis Cache |
> | . | Microsoft.Cache/redis/patchSchedules/write | Modifica la programación de una aplicación de revisiones de una instancia de Redis Cache |
> | . | Microsoft.Cache/redis/read | Visualiza la configuración de Redis Cache en el portal de administración |
> | . | Microsoft.Cache/redis/regenerateKey/action | Cambia el valor de las claves de acceso de Redis Cache en el portal de administración |
> | . | Microsoft.Cache/redis/start/action | Inicia una instancia de la memoria caché. |
> | . | Microsoft.Cache/redis/stop/action | Detiene una instancia de la memoria caché. |
> | . | Microsoft.Cache/redis/write | Modifica la configuración de Redis Cache en el portal de administración |
> | . | Microsoft.Cache/register/action | Registra el proveedor de recursos "Microsoft.Cache" con una suscripción |
> | . | Microsoft.Cache/unregister/action | Anula el registro del proveedor de recursos "Microsoft.Cache" con una suscripción |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Capacity/register/action | Registra al proveedor de recursos de Capacity y habilita la creación de recursos de tipo Capacity. |
> | . | Microsoft.Capacity/reservationorders/action | Actualiza cualquier reserva. |
> | . | Microsoft.Capacity/reservationorders/delete | Elimina cualquier reserva. |
> | . | Microsoft.Capacity/reservationorders/read | Lee todas las reservas. |
> | . | Microsoft.Capacity/reservationorders/reservations/action | Actualiza cualquier reserva. |
> | . | Microsoft.Capacity/reservationorders/reservations/delete | Elimina cualquier reserva. |
> | . | Microsoft.Capacity/reservationorders/reservations/read | Lee todas las reservas. |
> | . | Microsoft.Capacity/reservationorders/reservations/revisions/read | Lee todas las reservas. |
> | . | Microsoft.Capacity/reservationorders/reservations/write | Crea cualquier reserva. |
> | . | Microsoft.Capacity/reservationorders/write | Crea cualquier reserva. |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Cdn/CheckNameAvailability/action |  |
> | . | Microsoft.Cdn/CheckResourceUsage/action |  |
> | . | Microsoft.Cdn/edgenodes/delete |  |
> | . | Microsoft.Cdn/edgenodes/read |  |
> | . | Microsoft.Cdn/edgenodes/write |  |
> | . | Microsoft.Cdn/operationresults/delete |  |
> | . | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | . | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | . | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | . | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | . | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | . | Microsoft.Cdn/operationresults/profileresults/read |  |
> | . | Microsoft.Cdn/operationresults/profileresults/write |  |
> | . | Microsoft.Cdn/operationresults/read |  |
> | . | Microsoft.Cdn/operationresults/write |  |
> | . | Microsoft.Cdn/operations/read |  |
> | . | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | . | Microsoft.Cdn/profiles/delete |  |
> | . | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | . | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | . | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | . | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | . | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | . | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | . | Microsoft.Cdn/profiles/endpoints/delete |  |
> | . | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | . | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | . | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | . | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | . | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso. |
> | . | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso. |
> | . | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read | Obtiene los registros disponibles de Microsoft.Cdn. |
> | . | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | . | Microsoft.Cdn/profiles/endpoints/read |  |
> | . | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | . | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | . | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | . | Microsoft.Cdn/profiles/endpoints/write |  |
> | . | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | . | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | . | Microsoft.Cdn/profiles/read |  |
> | . | Microsoft.Cdn/profiles/write |  |
> | . | Microsoft.Cdn/register/action | Registra la suscripción del proveedor de recursos de CDN y habilita la creación de perfiles de CDN. |
> | . | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Elimina un certificado existente |
> | . | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Obtiene la lista de certificados |
> | . | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Agrega un nuevo certificado o actualiza uno existente |
> | . | Microsoft.CertificateRegistration/certificateOrders/Delete | Elimina un AppServiceCertificate existente |
> | . | Microsoft.CertificateRegistration/certificateOrders/Read | Obtiene la lista de CertificateOrders |
> | . | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Vuelve a emitir un certificateorder existente |
> | . | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Renueva un certificateorder existente |
> | . | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Vuelve a enviar el correo electrónico de certificado |
> | . | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Vuelve a enviar los mensajes de correo electrónico de solicitud a otra dirección de correo electrónico |
> | . | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Recupera el sello del sitio de un certificado emitido de App Service |
> | . | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Recupera la lista de acciones de certificado |
> | . | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Recupera el historial de correos electrónicos del certificado |
> | . | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Comprobar la propiedad del dominio |
> | . | Microsoft.CertificateRegistration/certificateOrders/Write | Agrega un nuevo certificateOrder o actualiza uno existente |
> | . | Microsoft.CertificateRegistration/operations/Read | Enumera todas las operaciones del registro de certificados del servicio de la aplicación. |
> | . | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Aprovisiona una entidad de servicio para entidad de aplicación de servicio |
> | . | Microsoft.CertificateRegistration/register/action | Registra el proveedor de recursos de Microsoft Certificates de la suscripción |
> | . | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Valida un objeto de compra de certificado sin enviarlo |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.ClassicCompute/capabilities/read | Muestra las funcionalidades |
> | . | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Comprueba la disponibilidad de un nombre de dominio determinado. |
> | . | Microsoft.ClassicCompute/domainNames/active/write | Establece el nombre de dominio activo. |
> | . | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Muestra el conjunto de disponibilidad para el recurso. |
> | . | Microsoft.ClassicCompute/domainNames/capabilities/read | Muestra las funcionalidades del nombre de dominio |
> | . | Microsoft.ClassicCompute/domainNames/delete | Quita los nombres de dominio de los recursos. |
> | . | Microsoft.ClassicCompute/domainNames/extensions/delete | Quita las extensiones del nombre de dominio. |
> | . | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Lee el estado de la operación de las extensiones de los nombres de dominio. |
> | . | Microsoft.ClassicCompute/domainNames/extensions/read | Devuelve las extensiones del nombre de dominio. |
> | . | Microsoft.ClassicCompute/domainNames/extensions/write | Agrega las extensiones del nombre de dominio. |
> | . | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Quita un nuevo equilibrio de carga interno. |
> | . | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Lee el estado de la operación de los equilibradores de carga internos de los nombres de dominio. |
> | . | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Obtiene los equilibradores de carga internos. |
> | . | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Crea un nuevo equilibrio de carga interno. |
> | . | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Lee el estado de la operación de los conjuntos de puntos de conexión de carga equilibrada de los nombres de dominio. |
> | . | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Muestra los conjuntos de puntos de conexión de carga equilibrada |
> | . | Microsoft.ClassicCompute/domainNames/read | Devuelve los nombres de dominio de los recursos. |
> | . | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Elimina los certificados de servicio usados. |
> | . | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Lee el estado de la operación de los certificados de servicio de los nombres de dominio. |
> | . | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Devuelve los certificados de servicio usados. |
> | . | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Agrega o modifica los certificados de servicio usados. |
> | . | Microsoft.ClassicCompute/domainNames/slots/delete | Elimina una ranura de implementación concreta. |
> | . | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Lee el estado de la operación de las ranuras de los nombres de dominio. |
> | . | Microsoft.ClassicCompute/domainNames/slots/read | Muestra las ranuras de implementación. |
> | . | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Quita la referencia de extensión para el rol de la ranura de implementación. |
> | . | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Lee el estado de la operación de las referencias de extensiones de los roles de las ranuras de nombres de dominio. |
> | . | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Devuelve la referencia de extensión para el rol de la ranura de implementación. |
> | . | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Agrega o modifica la referencia de extensión para el rol de la ranura de implementación. |
> | . | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico. |
> | . | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Agrega o modifica la configuración de diagnóstico. |
> | . | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las definiciones de métricas. |
> | . | Microsoft.ClassicCompute/domainNames/slots/roles/read | Obtiene el rol de la ranura de implementación. |
> | . | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Lee el estado de la operación de las instancias de rol de los roles de las ranuras de nombres de dominio. |
> | . | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Obtiene la instancia de rol. |
> | . | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Recompila la instancia de rol. |
> | . | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Restablece la imagen inicial de la instancia de rol. |
> | . | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Reinicia las instancias de rol. |
> | . | Microsoft.ClassicCompute/domainNames/slots/start/action | Inicia una ranura de implementación. |
> | . | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Cambia el estado de la ranura de implementación a detenido. |
> | . | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Cambia el estado de la ranura de implementación a iniciado. |
> | . | Microsoft.ClassicCompute/domainNames/slots/stop/action | Suspende la ranura de implementación. |
> | . | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Recorre el dominio de actualización. |
> | . | Microsoft.ClassicCompute/domainNames/slots/write | Crea o actualiza la implementación. |
> | . | Microsoft.ClassicCompute/domainNames/swap/action | Cambia la ranura de ensayo a la de producción. |
> | . | Microsoft.ClassicCompute/domainNames/write | Agrega o modifica los nombres de dominio de los recursos. |
> | . | Microsoft.ClassicCompute/moveSubscriptionResources/action | Mueve todos los recursos clásicos a una suscripción diferente. |
> | . | Microsoft.ClassicCompute/operatingSystemFamilies/read | Enumera las familias del sistema operativo invitado que están disponibles en Microsoft Azure, así como las versiones del sistema operativo disponibles para cada familia. |
> | . | Microsoft.ClassicCompute/operatingSystems/read | Enumera las versiones del sistema operativo invitado que están actualmente disponibles en Microsoft Azure. |
> | . | Microsoft.ClassicCompute/quotas/read | Obtiene la cuota de la suscripción. |
> | . | Microsoft.ClassicCompute/register/action | Realiza el registro en un proceso clásico |
> | . | Microsoft.ClassicCompute/resourceTypes/skus/read | Obtiene la lista de Sku para los tipos de recursos admitidos. |
> | . | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Valida la disponibilidad de la suscripción para la operación de movimiento clásico. |
> | . | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Elimina el grupo de seguridad de red asociado con la máquina virtual. |
> | . | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Lee el estado de la operación de los grupos de seguridad de red asociados a las máquinas virtuales. |
> | . | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Obtiene el grupo de seguridad de red asociado con la máquina virtual. |
> | . | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Agrega un grupo de seguridad de red asociado con la máquina virtual. |
> | . | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Obtiene las operaciones asincrónicas posibles |
> | . | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Conecta un disco de datos a una máquina virtual. |
> | . | Microsoft.ClassicCompute/virtualMachines/delete | Quita máquinas virtuales. |
> | . | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Desconecta un disco de datos de una máquina virtual. |
> | . | Microsoft.ClassicCompute/virtualMachines/disks/read | Recupera la lista de discos de datos |
> | . | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Descarga el archivo RDP para la máquina virtual. |
> | . | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Lee el estado de la operación de las extensiones de máquinas virtuales. |
> | . | Microsoft.ClassicCompute/virtualMachines/extensions/read | Obtiene la extensión de máquina virtual. |
> | . | Microsoft.ClassicCompute/virtualMachines/extensions/write | Pone la extensión de máquina virtual. |
> | . | Microsoft.ClassicCompute/virtualMachines/metrics/read | Obtiene las métricas. |
> | . | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Elimina el grupo de seguridad de red asociado con la interfaz de red. |
> | . | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Lee el estado de la operación de los grupos de seguridad de red asociados a las máquinas virtuales. |
> | . | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Obtiene el grupo de seguridad de red asociado con la interfaz de red. |
> | . | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Agrega un grupo de seguridad de red asociado con la interfaz de red. |
> | . | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Lee el estado de la operación de las máquinas virtuales. |
> | . | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Realiza el mantenimiento en la máquina virtual. |
> | . | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico. |
> | . | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Agrega o modifica la configuración de diagnóstico. |
> | . | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las definiciones de métricas. |
> | . | Microsoft.ClassicCompute/virtualMachines/read | Recupera una lista de máquinas virtuales. |
> | . | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Vuelve a implementar la máquina virtual. |
> | . | Microsoft.ClassicCompute/virtualMachines/restart/action | Reinicia las máquinas virtuales. |
> | . | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Apaga la máquina virtual. |
> | . | Microsoft.ClassicCompute/virtualMachines/start/action | Inicie la máquina virtual. |
> | . | Microsoft.ClassicCompute/virtualMachines/stop/action | Detiene la máquina virtual. |
> | . | Microsoft.ClassicCompute/virtualMachines/write | Agrega o modifica máquinas virtuales. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Recupera la lista de dispositivos compatibles. |
> | . | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Elimina el grupo de seguridad de red. |
> | . | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Lee el estado de la operación del grupo de seguridad de red. |
> | . | Microsoft.ClassicNetwork/networkSecurityGroups/read | Obtiene el grupo de seguridad de red. |
> | . | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Elimina la regla de seguridad. |
> | . | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Lee el estado de la operación de las reglas de seguridad del grupo de seguridad de red. |
> | . | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Obtiene la regla de seguridad. |
> | . | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Agrega o actualiza una regla de seguridad. |
> | . | Microsoft.ClassicNetwork/networkSecurityGroups/write | Agrega un nuevo grupo de seguridad de red. |
> | . | Microsoft.ClassicNetwork/quotas/read | Obtiene la cuota de la suscripción. |
> | . | Microsoft.ClassicNetwork/register/action | Realiza el registro en una red clásica |
> | . | Microsoft.ClassicNetwork/reservedIps/delete | Elimina una IP reservada. |
> | . | Microsoft.ClassicNetwork/reservedIps/join/action | Une una IP reservada |
> | . | Microsoft.ClassicNetwork/reservedIps/link/action | Vincula una IP reservada |
> | . | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Lee el estado de la operación de las IP reservadas. |
> | . | Microsoft.ClassicNetwork/reservedIps/read | Obtiene las IP reservadas |
> | . | Microsoft.ClassicNetwork/reservedIps/write | Agregar una nueva IP reservada |
> | . | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Muestra las funcionalidades |
> | . | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Comprueba la disponibilidad de una dirección IP determinada en una red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/delete | Elimina la red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Anula la revocación de un certificado de cliente. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Lee los certificados de cliente revocados. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Revoca un certificado de cliente. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Elimina el certificado de cliente de la puerta de enlace de red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Descarga el certificado con huella digital. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Muestra el paquete de certificado de la puerta de enlace de red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Busca los certificados raíz de cliente. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Carga un nuevo certificado raíz de cliente. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Realiza una conexión de puerta de enlace de sitio a sitio. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Desconecta una conexión de puerta de enlace de sitio a sitio. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Recupera la lista de conexiones. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Prueba una conexión de puerta de enlace de sitio a sitio. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Elimina la puerta de enlace de red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Descarga el script de configuración del dispositivo. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Descarga los diagnósticos de la puerta de enlace. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Recupera la clave de servicio del circuito. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Muestra el paquete de la puerta de enlace de red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Lee el estado de la operación de las puertas de enlace de redes virtuales. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Obtiene el paquete de la puerta de enlace de red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Obtiene las puertas de enlace de red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Inicia el diagnóstico de la puerta de enlace de red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Detiene el diagnóstico de la puerta de enlace de red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Agrega una puerta de enlace de red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/join/action | Une la red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Lee el estado de la operación de las redes virtuales. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Empareja una red virtual con otra red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/read | Obtiene la red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Elimina el grupo de seguridad de red asociado con la subred. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Lee el estado de la operación del grupo de seguridad de red asociado a la subred de red virtual. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Obtiene el grupo de seguridad de red asociado con la subred. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Agrega un grupo de seguridad de red asociado con la subred. |
> | . | Microsoft.ClassicNetwork/virtualNetworks/write | Agrega una nueva red virtual. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.ClassicStorage/capabilities/read | Muestra las funcionalidades |
> | . | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Comprueba la disponibilidad de una cuenta de almacenamiento. |
> | . | Microsoft.ClassicStorage/disks/read | Devuelve el disco de la cuenta de almacenamiento. |
> | . | Microsoft.ClassicStorage/images/read | Devuelve la imagen. |
> | . | Microsoft.ClassicStorage/osImages/read | Devuelve la imagen de sistema operativo. |
> | . | Microsoft.ClassicStorage/publicImages/read | Obtiene la imagen de máquina virtual pública. |
> | . | Microsoft.ClassicStorage/quotas/read | Obtiene la cuota de la suscripción. |
> | . | Microsoft.ClassicStorage/register/action | Registra elementos en el almacenamiento clásico |
> | . | Microsoft.ClassicStorage/storageAccounts/delete | Elimina la cuenta de almacenamiento. |
> | . | Microsoft.ClassicStorage/storageAccounts/disks/delete | Elimina un disco de cuenta de almacenamiento determinado. |
> | . | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Lee el estado de la operación de los recursos. |
> | . | Microsoft.ClassicStorage/storageAccounts/disks/read | Devuelve el disco de la cuenta de almacenamiento. |
> | . | Microsoft.ClassicStorage/storageAccounts/disks/write | Agrega un disco de cuenta de almacenamiento. |
> | . | Microsoft.ClassicStorage/storageAccounts/images/delete | Elimina una imagen determinada de la cuenta de almacenamiento. |
> | . | Microsoft.ClassicStorage/storageAccounts/images/read | Devuelve la imagen de la cuenta de almacenamiento. |
> | . | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Enumera las claves de acceso de las cuentas de almacenamiento. |
> | . | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Lee el estado de la operación de los recursos. |
> | . | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Elimina una imagen del sistema operativo de la cuenta de almacenamiento. |
> | . | Microsoft.ClassicStorage/storageAccounts/osImages/read | Devuelve la imagen del sistema operativo de la cuenta de almacenamiento. |
> | . | Microsoft.ClassicStorage/storageAccounts/read | Devuelve la cuenta de almacenamiento con la cuenta especificada. |
> | . | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Regenera las claves de acceso existentes de la cuenta de almacenamiento. |
> | . | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Obtiene la configuración de diagnóstico. |
> | . | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Agrega o modifica la configuración de diagnóstico. |
> | . | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Obtiene las definiciones de métricas. |
> | . | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Obtiene las métricas. |
> | . | Microsoft.ClassicStorage/storageAccounts/services/read | Obtiene los servicios disponibles. |
> | . | Microsoft.ClassicStorage/storageAccounts/write | Agrega una nueva cuenta de almacenamiento. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.CognitiveServices/accounts/delete | Elimina las cuentas de la API |
> | . | Microsoft.CognitiveServices/accounts/listKeys/action | Enumera las claves |
> | . | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso. |
> | . | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso. |
> | . | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles para Cognitive Services. |
> | . | Microsoft.CognitiveServices/accounts/read | Lee las cuentas de la API. |
> | . | Microsoft.CognitiveServices/accounts/regenerateKey/action | Regenera una clave |
> | . | Microsoft.CognitiveServices/accounts/skus/read | Lee las SKU disponibles para un recurso existente. |
> | . | Microsoft.CognitiveServices/accounts/usages/read | Obtiene el uso de cuota para un recurso existente. |
> | . | Microsoft.CognitiveServices/accounts/write | Escribe las cuentas de la API. |
> | . | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Lee las SKU disponibles en una suscripción. |
> | . | Microsoft.CognitiveServices/Operations/read | Enumera todas las operaciones disponibles. |
> | . | Microsoft.CognitiveServices/register/action | Registra la suscripción de Cognitive Services. |
> | . | Microsoft.CognitiveServices/skus/read | Lee las SKU disponibles en Cognitive Services. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Commerce/RateCard/read | Devuelve datos de oferta, metadatos de recurso o medidor, y las tarifas de las suscripciones dadas. |
> | . | Microsoft.Commerce/UsageAggregates/read | Recupera el consumo de Microsoft Azure por parte de una suscripción. El resultado contiene datos de uso de agregados, información relacionada con la suscripción y los recursos, en un determinado intervalo de tiempo. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Compute/availabilitySets/delete | Elimina el conjunto de disponibilidad |
> | . | Microsoft.Compute/availabilitySets/read | Obtiene las propiedades de un conjunto de disponibilidad |
> | . | Microsoft.Compute/availabilitySets/vmSizes/read | Enumera los tamaños disponibles para crear o actualizar una máquina virtual del conjunto de disponibilidad |
> | . | Microsoft.Compute/availabilitySets/write | Crea un nuevo conjunto de disponibilidad o actualiza uno ya existente |
> | . | Microsoft.Compute/disks/beginGetAccess/action | Obtiene el URI de SAS del disco de acceso de blob |
> | . | Microsoft.Compute/disks/delete | Elimina el disco |
> | . | Microsoft.Compute/disks/endGetAccess/action | Revoca el URI de SAS del disco |
> | . | Microsoft.Compute/disks/read | Obtiene las propiedades de un disco |
> | . | Microsoft.Compute/disks/write | Crea un nuevo disco o actualiza uno ya existente |
> | . | Microsoft.Compute/images/delete | Elimina la imagen |
> | . | Microsoft.Compute/images/read | Obtiene las propiedades de la imagen |
> | . | Microsoft.Compute/images/write | Crea una nueva imagen o actualiza una ya existente |
> | . | Microsoft.Compute/locations/capsOperations/read | Obtiene el estado de una operación Caps asincrónica. |
> | . | Microsoft.Compute/locations/diskOperations/read | Obtiene el estado de una operación Disk asincrónica. |
> | . | Microsoft.Compute/locations/operations/read | Obtiene el estado de una operación asincrónica |
> | . | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Obtiene las propiedades de una oferta de imagen de plataforma. |
> | . | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Obtiene las propiedades de una SKU de imagen de plataforma. |
> | . | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Obtiene las propiedades de una versión de imagen de plataforma. |
> | . | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Obtiene las propiedades de un tipo de VMExtension. |
> | . | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Obtiene las propiedades de una versión de VMExtension. |
> | . | Microsoft.Compute/locations/publishers/read | Obtiene las propiedades de un editor. |
> | . | Microsoft.Compute/locations/runCommands/read | Enumera los comandos de ejecución disponibles en la ubicación. |
> | . | Microsoft.Compute/locations/usages/read | Obtiene los límites de servicio y las cantidades de uso actuales de los recursos de proceso de la suscripción en una ubicación |
> | . | Microsoft.Compute/locations/vmSizes/read | Enumera los tamaños disponibles de máquina virtual en una ubicación |
> | . | Microsoft.Compute/operations/read | Enumera las operaciones disponibles en el proveedor de recursos de Microsoft.Compute |
> | . | Microsoft.Compute/register/action | Registra la suscripción con el proveedor de recursos de Microsoft.Compute |
> | . | Microsoft.Compute/restorePointCollections/delete | Elimina la colección de puntos de restauración y los puntos de restauración que contiene |
> | . | Microsoft.Compute/restorePointCollections/read | Obtiene las propiedades de una colección de puntos de restauración |
> | . | Microsoft.Compute/restorePointCollections/restorePoints/delete | Elimina el punto de restauración |
> | . | Microsoft.Compute/restorePointCollections/restorePoints/read | Obtiene las propiedades de un punto de restauración |
> | . | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Obtiene las propiedades de un punto de restauración junto con los identificadores URI de SAS de blob |
> | . | Microsoft.Compute/restorePointCollections/restorePoints/write | Crea un nuevo punto de restauración |
> | . | Microsoft.Compute/restorePointCollections/write | Crea una nueva colección de puntos de restauración o actualiza una ya existente |
> | . | Microsoft.Compute/sharedVMImages/delete | Elimina SharedVMImage. |
> | . | Microsoft.Compute/sharedVMImages/read | Obtiene las propiedades de SharedVMImage. |
> | . | Microsoft.Compute/sharedVMImages/versions/delete | Elimina SharedVMImageVersion. |
> | . | Microsoft.Compute/sharedVMImages/versions/read | Obtiene las propiedades de SharedVMImageVersion. |
> | . | Microsoft.Compute/sharedVMImages/versions/replicate/action | Replica SharedVMImageVersion en las regiones de destino. |
> | . | Microsoft.Compute/sharedVMImages/versions/write | Crea un nuevo elemento SharedVMImageVersion o actualiza uno existente. |
> | . | Microsoft.Compute/sharedVMImages/write | Crea un nuevo elemento SharedVMImage o actualiza uno existente. |
> | . | Microsoft.Compute/skus/read | Obtiene la lista de SKU de Microsoft.Compute disponibles para la suscripción. |
> | . | Microsoft.Compute/snapshots/beginGetAccess/action | Obtiene el URI de SAS de la instantánea para el acceso de blob. |
> | . | Microsoft.Compute/snapshots/delete | Elimina una instantánea |
> | . | Microsoft.Compute/snapshots/endGetAccess/action | Revoca el URI de SAS de la instantánea. |
> | . | Microsoft.Compute/snapshots/read | Obtiene las propiedades de una instantánea |
> | . | Microsoft.Compute/snapshots/write | Crea una nueva instantánea o actualiza una ya existente |
> | . | Microsoft.Compute/virtualMachines/capture/action | Captura la máquina virtual mediante la copia de los discos duros virtuales y genera una plantilla que se puede usar para crear máquinas virtuales similares |
> | . | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Convierte los discos basados en blobs de la máquina virtual en instancias de Managed Disks |
> | . | Microsoft.Compute/virtualMachines/deallocate/action | Apaga la máquina virtual y libera los recursos de proceso |
> | . | Microsoft.Compute/virtualMachines/delete | Elimina la máquina virtual |
> | . | Microsoft.Compute/virtualMachines/extensions/delete | Elimina la extensión de máquina virtual |
> | . | Microsoft.Compute/virtualMachines/extensions/read | Obtiene las propiedades de una extensión de máquina virtual |
> | . | Microsoft.Compute/virtualMachines/extensions/write | Crea una nueva extensión de máquina virtual o actualiza una ya existente |
> | . | Microsoft.Compute/virtualMachines/generalize/action | Establece el estado de la máquina virtual en Generalizado y prepara la máquina virtual para la captura |
> | . | Microsoft.Compute/virtualMachines/instanceView/read | Obtiene el estado detallado en tiempo de ejecución de la máquina virtual y sus recursos |
> | . | Microsoft.Compute/virtualMachines/performMaintenance/action | Realiza la operación de mantenimiento en la máquina virtual. |
> | . | Microsoft.Compute/virtualMachines/powerOff/action | Apaga la máquina virtual. Tenga en cuenta que se seguirá facturando la máquina virtual. |
> | . | Microsoft.Compute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Lee las definiciones de métricas de las máquinas virtuales |
> | . | Microsoft.Compute/virtualMachines/read | Obtiene las propiedades de una máquina virtual |
> | . | Microsoft.Compute/virtualMachines/redeploy/action | Vuelve a implementar la máquina virtual |
> | . | Microsoft.Compute/virtualMachines/reimage/action | Restablece la imagen inicial de la máquina virtual que está usando el disco de comparación. |
> | . | Microsoft.Compute/virtualMachines/restart/action | Reinicia la máquina virtual |
> | . | Microsoft.Compute/virtualMachines/runCommand/action | Ejecuta un script predefinido en la máquina virtual. |
> | . | Microsoft.Compute/virtualMachines/start/action | Inicia la máquina virtual |
> | . | Microsoft.Compute/virtualMachines/vmSizes/read | Enumera los tamaños disponibles a los que se puede actualizar la máquina virtual |
> | . | Microsoft.Compute/virtualMachines/write | Crea una nueva máquina virtual o actualiza una ya existente |
> | . | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Apaga y libera los recursos de proceso de las instancias del conjunto de escalado de máquinas virtuales.  |
> | . | Microsoft.Compute/virtualMachineScaleSets/delete | Elimina el conjunto de escalado de las máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/delete/action | Elimina las instancias del conjunto de escalado de las máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Elimina la extensión del conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Obtiene las propiedades de una extensión de un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Crea una extensión nueva del conjunto de escalado de máquinas virtuales o actualiza una existente. |
> | . | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Recorre manualmente los dominios de actualización de plataformas de un conjunto de escalado de máquinas virtuales de Service Fabric para finalizar una actualización pendiente que se bloqueó. |
> | . | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Obtiene la vista de instancias del conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Actualiza manualmente las instancias al último modelo del conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Obtiene las propiedades de todas las interfaces de red de un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Obtiene el historial de las actualizaciones del sistema operativo de un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Realiza el mantenimiento planeado en las instancias del conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Apaga las instancias del conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read | Lee las definiciones de métricas del conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Obtiene las propiedades de todas las direcciones IP públicas de un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/read | Obtiene las propiedades de un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Vuelve a implementar las instancias del conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Restablece la imagen inicial de las instancias del conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/restart/action | Reinicia las instancias del conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Cancela la actualización gradual de un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Obtiene el estado de actualización gradual más reciente de un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/scale/action | Permite verificar si un conjunto de escalado de máquinas virtuales existente puede reducirse o escalarse horizontalmente al número de instancias especificado. |
> | . | Microsoft.Compute/virtualMachineScaleSets/skus/read | Enumera las SKU válidas de un conjunto de escalado de máquinas virtuales ya existente. |
> | . | Microsoft.Compute/virtualMachineScaleSets/start/action | Inicia las instancias del conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Apaga y libera los recursos de proceso de una máquina virtual del conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Elimina una máquina virtual específica de un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Recupera la vista de instancias de una máquina virtual de un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Obtiene las propiedades de las direcciones IP públicas creadas mediante un conjunto de escalado de máquinas virtuales. El conjunto de escalado de máquinas virtuales puede crear a lo sumo una IP pública por ipconfiguration (IP privada). |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Obtiene las propiedades de una o todas las configuraciones de IP de una interfaz de red creada mediante el conjunto de escalado de máquinas virtuales. Las configuraciones de IP representan direcciones IP privadas. |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Obtiene las propiedades de una o todas las interfaces de red de una máquina virtual creada mediante el conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Realiza el mantenimiento planeado en una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Apaga una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Lee la máquina virtual en las definiciones de métricas del conjunto de escalado. |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Recupera las propiedades de una máquina virtual de un conjunto de escalado de máquinas virtuales |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Vuelve a implementar una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Restablece la imagen inicial de una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Reinicia una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Inicia una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Actualiza las propiedades de una máquina virtual en un conjunto de escalado de máquinas virtuales. |
> | . | Microsoft.Compute/virtualMachineScaleSets/write | Crea un nuevo conjunto de escalado de máquinas virtuales o actualiza uno ya existente. |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Consumption/balances/read | Enumera el uso del resumen durante el período de facturación de un grupo de administración. |
> | . | Microsoft.Consumption/budgets/read | Enumera los presupuestos con una suscripción o un grupo de administración. |
> | . | Microsoft.Consumption/budgets/write | Crea, actualiza y eliminar los presupuestos mediante una suscripción o un grupo de administración. |
> | . | Microsoft.Consumption/marketplaces/read | Enumera los detalles de uso de los recursos de Marketplace referentes a un ámbito de las suscripciones EA y WebDirect. |
> | . | Microsoft.Consumption/operations/read | Enumera todas las operaciones que admite el proveedor de recursos de Microsoft.Consumption. |
> | . | Microsoft.Consumption/pricesheets/read | Enumera los datos de las hojas de precios de una suscripción o un grupo de administración. |
> | . | Microsoft.Consumption/reservationDetails/read | Enumera los detalles de uso de las instancias que se reservaron mediante una solicitud de reserva o los grupos de administración. Los datos de los detalles se distribuyen por instancia y día. |
> | . | Microsoft.Consumption/reservationRecommendations/read | Enumera recomendaciones individuales o compartidas de las instancias reservadas de una suscripción. |
> | . | Microsoft.Consumption/reservationSummaries/read | Enumera el resumen de uso de las instancias que se reservaron mediante una solicitud de reserva o los grupos de administración. Los datos de resumen son mensuales o diarios. |
> | . | Microsoft.Consumption/reservationTransactions/read | Enumera el historial de transacciones de las instancias que reservaron grupos de administración. |
> | . | Microsoft.Consumption/terms/read | Enumera los términos de una suscripción o un grupo de administración. |
> | . | Microsoft.Consumption/usageDetails/read | Enumera los detalles de uso referentes a un ámbito de las suscripciones EA y WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Obtiene los registros de un contenedor específico. |
> | . | Microsoft.ContainerInstance/containerGroups/delete | Elimina el grupo de contenedores específico. |
> | . | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del grupo de contenedores. |
> | . | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del grupo de contenedores. |
> | . | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles del grupo de contenedores. |
> | . | Microsoft.ContainerInstance/containerGroups/read | Obtiene todos los grupos de contenedores. |
> | . | Microsoft.ContainerInstance/containerGroups/write | Crea o actualiza un grupo de contenedores específico. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.ContainerRegistry/checkNameAvailability/read | Comprueba si el nombre del registro de contenedor está disponible para su uso. |
> | . | Microsoft.ContainerRegistry/locations/operationResults/read | Obtiene el resultado de una operación asincrónica. |
> | . | Microsoft.ContainerRegistry/operations/read | Enumera todas las operaciones de API de REST disponibles de Azure Container Registry. |
> | . | Microsoft.ContainerRegistry/register/action | Registra la suscripción para el proveedor de recursos del registro de contenedor y habilita la creación de registros de contenedor. |
> | . | Microsoft.ContainerRegistry/registries/delete | Elimina un registro de contenedor. |
> | . | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Elimina un filtro de la cuadrícula de eventos de un registro de contenedor. |
> | . | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Obtiene las propiedades de un filtro específico de la cuadrícula de eventos o enumera todos los filtros de la cuadrícula de eventos del registro de contenedor especificado. |
> | . | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Crea o actualiza un filtro de la cuadrícula de eventos para un registro de contenedor con los parámetros especificados. |
> | . | Microsoft.ContainerRegistry/registries/importImage/action | Importa una imagen en un registro de contenedor con los parámetros especificados. |
> | . | Microsoft.ContainerRegistry/registries/listCredentials/action | Enumera las credenciales de inicio de sesión del registro de contenedor especificado. |
> | . | Microsoft.ContainerRegistry/registries/listUsages/read | Enumera los usos de cuota del registro de contenedor especificado. |
> | . | Microsoft.ContainerRegistry/registries/operationStatuses/read | Obtiene el estado de la operación asincrónica del registro. |
> | . | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles para Microsoft Container Registry |
> | . | Microsoft.ContainerRegistry/registries/read | Obtiene las propiedades del registro de contenedor especificado o enumera todos los registros de contenedores en la suscripción o el grupo de recursos especificado. |
> | . | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Regenera una de las credenciales de inicio de sesión del registro de contenedor especificado. |
> | . | Microsoft.ContainerRegistry/registries/replications/delete | Elimina una réplica de un registro de contenedor. |
> | . | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Obtiene el estado de la operación asincrónica de replicación. |
> | . | Microsoft.ContainerRegistry/registries/replications/read | Obtiene las propiedades de una replicación específica o enumera todas las replicaciones del registro de contenedor especificado. |
> | . | Microsoft.ContainerRegistry/registries/replications/write | Crea o actualiza una replicación de un registro de contenedor con los parámetros especificados. |
> | . | Microsoft.ContainerRegistry/registries/webhooks/delete | Elimina un webhook de un registro de contenedor. |
> | . | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Obtiene la configuración del servicio de URI y los encabezados personalizados del webhook. |
> | . | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Enumera los eventos recientes del webhook especificado. |
> | . | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Obtiene el estado de la operación asincrónica del webhook. |
> | . | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Desencadena el evento de ping que se enviará al webhook. |
> | . | Microsoft.ContainerRegistry/registries/webhooks/read | Obtiene las propiedades de un webhook específico o enumera todos los webhooks del registro de contenedor especificado. |
> | . | Microsoft.ContainerRegistry/registries/webhooks/write | Crea o actualiza un webhook de un registro de contenedor con los parámetros especificados. |
> | . | Microsoft.ContainerRegistry/registries/write | Crea o actualiza un registro de contenedor con los parámetros especificados. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.ContainerService/containerServices/delete | Elimina un servicio de contenedor |
> | . | Microsoft.ContainerService/containerServices/read | Obtiene un servicio de contenedor |
> | . | Microsoft.ContainerService/containerServices/write | Crea un nuevo servicio de contenedor o actualiza uno existente. |
> | . | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Obtiene el perfil de acceso de un clúster administrados por nombre de rol mediante las credenciales de la lista |
> | . | Microsoft.ContainerService/managedClusters/accessProfiles/read | Obtiene el perfil de acceso de un clúster administrados por nombre de rol |
> | . | Microsoft.ContainerService/managedClusters/delete | Elimina un clúster administrado |
> | . | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene el valor de diagnóstico del recurso de un clúster administrado |
> | . | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza el valor de diagnóstico del recurso de un clúster administrado |
> | . | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de Managed Cluster |
> | . | Microsoft.ContainerService/managedClusters/read | Obtiene un clúster administrado |
> | . | Microsoft.ContainerService/managedClusters/write | Crea un nuevo clúster administrado o actualiza uno existente. |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.ContentModerator/applications/delete | Elimina una operación |
> | . | Microsoft.ContentModerator/applications/listSecrets/action | Enumera secretos |
> | . | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Lee tokens de inicio de sesión único |
> | . | Microsoft.ContentModerator/applications/read | Lee una operación |
> | . | Microsoft.ContentModerator/applications/write | Escribe una operación |
> | . | Microsoft.ContentModerator/applications/write | Escribe una operación |
> | . | Microsoft.ContentModerator/listCommunicationPreference/action | Enumera las preferencias de comunicación |
> | . | Microsoft.ContentModerator/operations/read | operaciones de lectura |
> | . | Microsoft.ContentModerator/updateCommunicationPreference/action | Actualiza las preferencias de comunicación |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.CustomerInsights/hubs/adobemetadata/action | Crea o actualiza los metadatos de Adobe de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/adobemetadata/read | Lee los metadatos de Adobe de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Elimina todas las directivas de firma de acceso compartido de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Lee todas las directivas de firma de acceso compartido de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Regenera la clave principal de la directiva de firma de acceso compartido de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Regenera la clave secundaria de la directiva de firma de acceso compartido de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Crea o actualiza todas las directivas de firma de acceso compartido de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/connectors/activate/action | Activa los conectores de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/connectors/activate/action | Activa los conectores de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/connectors/delete | Elimina todos los conectores de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Obtiene el estado del tiempo de ejecución de los conectores de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Activa las asignaciones de conectores de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Elimina todas las asignaciones de conectores de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Lee los resultados de la operación encargada de asignar conectores de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Lee todas las asignaciones de conectores de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Crea o actualiza todas las asignaciones de conectores de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/connectors/operations/read | Lee los resultados de la operación encargada de los conectores de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/connectors/read | Lee todos los conectores de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Crea o actualiza la información de autenticación de los conectores de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/connectors/update/action | Actualiza los conectores de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/connectors/write | Crea o actualiza todos los conectores de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/crmmetadata/action | Crea o actualiza los metadatos de CRM de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/crmmetadata/read | Lee los metadatos de CRM de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/delete | Elimina todos los concentradores de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/gdpr/delete | Elimina los RGPD de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/gdpr/read | Lee los RGPD de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/gdpr/write | Crea o actualiza los RGPD de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Obtiene los créditos de facturación del concentrador de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Obtiene el historial de facturación del concentrador de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/images/delete | Elimina las imágenes de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/images/read | Lee las imágenes de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/images/write | Crea o actualiza las imágenes de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/interactions/delete | Elimina las interacciones de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/interactions/operations/read | Lee los resultados de las operaciones de las interacciones de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/interactions/read | Lee todas las interacciones de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Sugiere vínculos de relación para las interacciones de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/interactions/write | Crea o actualiza todas las interacciones de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/kpi/delete | Elimina todos los indicadores clave de rendimiento de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/kpi/operations/read | Lee los resultados de las operaciones de los indicadores clave de rendimiento de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/kpi/read | Lee todos los indicadores clave de rendimiento de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Lee todos los indicadores clave de rendimiento de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/kpi/write | Crea o actualiza todos los indicadores clave de rendimiento de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/links/delete | Elimina los vínculos de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/links/operations/read | Lee los resultados de la operación encargada de los vínculos de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/links/read | Lee los vínculos de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/links/write | Crea o actualiza los vínculos de Azure Customer. |
> | . | Microsoft.CustomerInsights/hubs/msemetadata/action | Crea o actualiza los metadatos de MSE de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/msemetadata/read | Lee los metadatos de MSE de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/operationresults/read | Obtiene los resultados de las operaciones del concentrador de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/predictions/delete | Elimina las predicciones de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/predictions/operations/read | Lee los resultados de la operación encargada de las predicciones de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/predictions/read | Lee las predicciones de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/predictions/write | Crea o actualiza las predicciones de Azure Customer. |
> | . | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Elimina las directivas de coincidencia predictiva de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Lee el resultado de la operación de las directivas de coincidencia predictiva de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Lee las directivas de coincidencia predictiva de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Crea o actualiza las directivas de coincidencia predictiva de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/profiles/delete | Elimina los perfiles de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/profiles/operations/read | Lee los resultados de la operación encargada del perfil de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/profiles/read | Lee todos los perfiles de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/profiles/write | Escribe todos los perfiles de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Obtiene los registros disponibles del recurso |
> | . | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles del recurso |
> | . | Microsoft.CustomerInsights/hubs/read | Lee todos los concentradores de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Elimina los vínculos de relación de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Lee los resultados de la operación encargada de los vínculos de relación de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Lee los vínculos de relación de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Crea o actualiza los vínculos de relación de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/relationships/delete | Elimina las relaciones de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/relationships/operations/read | Lee los resultados de la operación encargada de las relaciones de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/relationships/read | Lee las relaciones de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/relationships/write | Crea o actualiza las relaciones de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Elimina todas las asignaciones de control de acceso basado en rol de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Lee los resultados de la operación para la asignación de RBAC de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/roleAssignments/read | Lee todas las asignaciones de control de acceso basado en rol de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/roleAssignments/write | Crea o actualiza todas las asignaciones de control de acceso basado en rol de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/roles/read | Lee los roles RBAC de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Crea o actualiza los metadatos de SalesForce de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Lee los metadatos de SalesForce de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/segments/delete | Elimina los segmentos de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Administra los segmentos dinámicos de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/segments/read | Lee los segmentos de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/segments/static/action | Administra los segmentos estáticos de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/segments/write | Crea o actualiza los segmentos de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Elimina los atributos SqlConnectionStrings de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Lee los atributos SqlConnectionStrings de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Crea o actualiza los atributos SqlConnectionStrings de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Crea un esquema de tipo "Suggest" a partir de los datos de ejemplo. |
> | . | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Administra la configuración de los concentradores de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/views/delete | Elimina todas las vistas de aplicación de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/views/read | Lee todas vistas de aplicación de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/views/write | Crea o actualiza todas las vistas de aplicación de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/hubs/widgettypes/read | Lee los tipos de widget de aplicación de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/hubs/write | Crea o actualiza todos los concentradores de Azure Customer Insights |
> | . | Microsoft.CustomerInsights/operations/read | Lee los metadatos de API de Azure Customer Insights. |
> | . | Microsoft.CustomerInsights/register/action | Registra la suscripción del proveedor de recursos de Customer Insights y habilita la creación de recursos de Customer Insights. |
> | . | Microsoft.CustomerInsights/unregister/action | Anula el registro de la suscripción del proveedor de recursos de Customer Insights. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Databricks/workspaces/delete | Quita un área de trabajo. |
> | . | Microsoft.Databricks/workspaces/read | Recupera una lista de áreas de trabajo. |
> | . | Microsoft.Databricks/workspaces/write | Crea un área de trabajo. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.DataCatalog/catalogs/delete | Elimina el catálogo. |
> | . | Microsoft.DataCatalog/catalogs/read | Obtiene las propiedades de los catálogos de la suscripción o grupo de recursos. |
> | . | Microsoft.DataCatalog/catalogs/write | Crea un catálogo o actualiza las etiquetas y propiedades de este. |
> | . | Microsoft.DataCatalog/checkNameAvailability/action | Comprueba la disponibilidad del nombre del catálogo del inquilino. |
> | . | Microsoft.DataCatalog/operations/read | Enumera las operaciones disponibles en el proveedor de recursos de Microsoft.DataCatalog. |
> | . | Microsoft.DataCatalog/register/action | Registra la suscripción con el proveedor de recursos de Microsoft.DataCatalog. |
> | . | Microsoft.DataCatalog/unregister/action | Anula el registro de la suscripción del proveedor de recursos de Microsoft.DataCatalog. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.DataFactory/datafactories/activitywindows/read | Lee las ventanas de actividad de Data Factory con los parámetros especificados. |
> | . | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Lee las ventanas de actividad de la actividad Canalización con los parámetros especificados. |
> | . | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Lee las ventanas de actividad de la canalización con los parámetros especificados. |
> | . | Microsoft.DataFactory/datafactories/datapipelines/delete | Elimina todas las canalizaciones. |
> | . | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Pone en pausa todas las canalizaciones. |
> | . | Microsoft.DataFactory/datafactories/datapipelines/read | Lee cualquier canalización. |
> | . | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Reanuda cualquier canalización. |
> | . | Microsoft.DataFactory/datafactories/datapipelines/update/action | Actualiza cualquier canalización. |
> | . | Microsoft.DataFactory/datafactories/datapipelines/write | Crea o actualiza cualquier canalización. |
> | . | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Lee las ventanas de actividad del conjunto de datos con los parámetros especificados. |
> | . | Microsoft.DataFactory/datafactories/datasets/delete | Elimina cualquier conjunto de datos. |
> | . | Microsoft.DataFactory/datafactories/datasets/read | Lee cualquier conjunto de datos. |
> | . | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Lee la ejecución del segmento de datos del conjunto de datos determinado con la hora de inicio determinada. |
> | . | Microsoft.DataFactory/datafactories/datasets/slices/read | Obtiene los segmentos de datos del período determinado. |
> | . | Microsoft.DataFactory/datafactories/datasets/slices/write | Actualiza el estado del segmento de datos. |
> | . | Microsoft.DataFactory/datafactories/datasets/write | Crea o actualiza cualquier conjunto de datos. |
> | . | Microsoft.DataFactory/datafactories/delete | Elimina la instancia de Data Factory. |
> | . | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Lee la información de conexión de cualquier puerta de enlace. |
> | . | Microsoft.DataFactory/datafactories/gateways/delete | Elimina cualquier puerta de enlace. |
> | . | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Enumera las claves de autenticación de cualquier puerta de enlace. |
> | . | Microsoft.DataFactory/datafactories/gateways/read | Lee cualquier puerta de enlace. |
> | . | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Vuelve a generar las claves de autenticación de cualquier puerta de enlace. |
> | . | Microsoft.DataFactory/datafactories/gateways/write | Crea o actualiza cualquier puerta de enlace. |
> | . | Microsoft.DataFactory/datafactories/linkedServices/delete | Elimina cualquier servicio vinculado. |
> | . | Microsoft.DataFactory/datafactories/linkedServices/read | Lee cualquier servicio vinculado. |
> | . | Microsoft.DataFactory/datafactories/linkedServices/write | Crea o actualiza cualquier servicio vinculado. |
> | . | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de las factorías de datos. |
> | . | Microsoft.DataFactory/datafactories/read | Lee la instancia de Data Factory. |
> | . | Microsoft.DataFactory/datafactories/runs/loginfo/read | Lee un identificador URI de SAS en un contenedor de blobs que contiene los registros. |
> | . | Microsoft.DataFactory/datafactories/tables/delete | Elimina cualquier conjunto de datos. |
> | . | Microsoft.DataFactory/datafactories/tables/read | Lee cualquier conjunto de datos. |
> | . | Microsoft.DataFactory/datafactories/tables/write | Crea o actualiza cualquier conjunto de datos. |
> | . | Microsoft.DataFactory/datafactories/write | Crea o actualiza la instancia de Data Factory. |
> | . | Microsoft.DataFactory/factories/cancelpipelinerun/action | Cancela la ejecución de una canalización especificada por el identificador de ejecución. |
> | . | Microsoft.DataFactory/factories/datasets/delete | Elimina cualquier conjunto de datos. |
> | . | Microsoft.DataFactory/factories/datasets/read | Lee cualquier conjunto de datos. |
> | . | Microsoft.DataFactory/factories/datasets/write | Crea o actualiza cualquier conjunto de datos. |
> | . | Microsoft.DataFactory/factories/delete | Elimina una instancia de Data Factory. |
> | . | Microsoft.DataFactory/factories/integrationruntimes/delete | Eliminar cualquier instancia de Integration Runtime. |
> | . | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Lee la información de conexión de una instancia de Integration Runtime. |
> | . | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Lee el estado de una instancia de Integration Runtime. |
> | . | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Enumera las claves de autenticación de cualquier instancia de Integration Runtime. |
> | . | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Obtiene los datos de supervisión de cualquier instancia de Integration Runtime. |
> | . | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Elimina el nodo de la instancia de Integration Runtime especificada. |
> | . | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Devuelve la dirección IP del nodo especificado de la instancia de Integration Runtime. |
> | . | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Actualiza un nodo de Integration Runtime autohospedado |
> | . | Microsoft.DataFactory/factories/integrationruntimes/read | Lee cualquier instancia de Integration Runtime. |
> | . | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Vuelve a generar las claves de autenticación de la instancia de Integration Runtime especificada. |
> | . | Microsoft.DataFactory/factories/integrationruntimes/start/action | Inicia cualquier instancia de Integration Runtime. |
> | . | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Detiene cualquier instancia de Integration Runtime. |
> | . | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Sincroniza las credenciales de la instancia de Integration Runtime especificada. |
> | . | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Actualiza la instancia de Integration Runtime especificada. |
> | . | Microsoft.DataFactory/factories/integrationruntimes/write | Crea o actualiza cualquier instancia de Integration Runtime. |
> | . | Microsoft.DataFactory/factories/linkedServices/delete | Elimina un servicio vinculado. |
> | . | Microsoft.DataFactory/factories/linkedServices/read | Lee un servicio vinculado. |
> | . | Microsoft.DataFactory/factories/linkedServices/write | Crea o actualiza un servicio vinculado |
> | . | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Lee que las ejecuciones de actividad del identificador de ejecución de la canalización especificada. |
> | . | Microsoft.DataFactory/factories/pipelineruns/read | Lee las ejecuciones de la canalización. |
> | . | Microsoft.DataFactory/factories/pipelines/createrun/action | Crea una ejecución para la canalización. |
> | . | Microsoft.DataFactory/factories/pipelines/delete | Elimina la canalización. |
> | . | Microsoft.DataFactory/factories/pipelines/read | Lee la canalización. |
> | . | Microsoft.DataFactory/factories/pipelines/write | Crea o actualiza la canalización |
> | . | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.DataFactory/factories/providers/Microsoft.Insights/logDefinitions/read | Obtiene los registros disponibles de las factorías. |
> | . | Microsoft.DataFactory/factories/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de las factorías. |
> | . | Microsoft.DataFactory/factories/read | Lee datos de Data Factory. |
> | . | Microsoft.DataFactory/factories/triggers/delete | Elimina cualquier desencadenador. |
> | . | Microsoft.DataFactory/factories/triggers/read | Lee cualquier desencadenador. |
> | . | Microsoft.DataFactory/factories/triggers/start/action | Inicia cualquier desencadenador. |
> | . | Microsoft.DataFactory/factories/triggers/stop/action | Detiene cualquier desencadenador. |
> | . | Microsoft.DataFactory/factories/triggers/triggerruns/read | Lee las ejecuciones del desencadenador. |
> | . | Microsoft.DataFactory/factories/triggers/write | Crea o actualiza cualquier desencadenador. |
> | . | Microsoft.DataFactory/factories/write | Crea o actualiza una instancia de Data Factory |
> | . | Microsoft.DataFactory/register/action | Registra la suscripción del proveedor de recursos de Data Factory. |
> | . | Microsoft.DataFactory/unregister/action | Anula el registro de la suscripción del proveedor de recursos de Data Factory. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Elimina una directiva de proceso. |
> | . | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Obtiene información sobre una directiva de cálculo. |
> | . | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Crea o actualiza una directiva de proceso. |
> | . | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Anula la vinculación de una cuenta de DataLakeStore a la cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Obtiene información sobre la cuenta vinculada de DataLakeStore que se encuentra en la cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Crea o actualiza una cuenta vinculada de DataLakeStore en la cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/accounts/delete | Elimina la cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Elimina una regla de firewall. |
> | . | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Obtiene información acerca de una regla de firewall. |
> | . | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Crea o actualiza una regla de firewall. |
> | . | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Obtiene el resultado de una operación de la cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico de la cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de la cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/logDefinitions/read | Obtiene los registros disponibles de la cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de la cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/accounts/read | Obtiene información acerca de una cuenta de DataLakeAnalytics existente. |
> | . | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Enumera los token de SAS de los contenedores de almacenamiento de una cuenta de almacenamiento vinculada a una cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Obtiene los contenedores de una cuenta de almacenamiento vinculada a una cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Anula la vinculación de una cuenta de almacenamiento a la cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Obtiene información acerca de una cuenta de almacenamiento vinculada a una cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Crea o actualiza una cuenta de almacenamiento vinculada a una cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Concede permisos para cancelar trabajos que enviaron otros usuarios. |
> | . | Microsoft.DataLakeAnalytics/accounts/write | Crea o actualiza una cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/locations/capability/read | Obtiene información sobre la capacidad de una suscripción acerca del uso de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Comprueba la disponibilidad de un nombre de cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/locations/operationResults/read | Obtiene el resultado de una operación de la cuenta de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/operations/read | Obtiene las operaciones disponibles de DataLakeAnalytics. |
> | . | Microsoft.DataLakeAnalytics/register/action | Registra la suscripción a DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.DataLakeStore/accounts/delete | Elimina una cuenta de DataLakeStore. |
> | . | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Habilita KeyVault para una cuenta de DataLakeStore. |
> | . | Microsoft.DataLakeStore/accounts/firewallRules/delete | Elimina una regla de firewall. |
> | . | Microsoft.DataLakeStore/accounts/firewallRules/read | Obtiene información acerca de una regla de firewall. |
> | . | Microsoft.DataLakeStore/accounts/firewallRules/write | Crea o actualiza una regla de firewall. |
> | . | Microsoft.DataLakeStore/accounts/operationResults/read | Obtiene el resultado de una operación de la cuenta de DataLakeStore. |
> | . | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico de la cuenta de DataLakeStore. |
> | . | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de la cuenta de DataLakeStore. |
> | . | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/logDefinitions/read | Obtiene los registros disponibles de la cuenta de DataLakeStore. |
> | . | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de la cuenta de DataLakeStore. |
> | . | Microsoft.DataLakeStore/accounts/read | Obtiene información acerca de una cuenta de DataLakeStore existente. |
> | . | Microsoft.DataLakeStore/accounts/Superuser/action | Concede privilegios de superusuario en Data Lake Store cuando se concede con Microsoft.Authorization/roleAssignments/write. |
> | . | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Elimina un proveedor de identidades de confianza. |
> | . | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Obtiene información acerca de un proveedor de identidades de confianza. |
> | . | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Crea o actualiza un proveedor de identidades de confianza. |
> | . | Microsoft.DataLakeStore/accounts/write | Crea o actualiza una cuenta de DataLakeStore. |
> | . | Microsoft.DataLakeStore/locations/capability/read | Obtiene información sobre la capacidad de una suscripción acerca del uso de DataLakeStore. |
> | . | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Comprueba la disponibilidad de un nombre de cuenta de DataLakeStore. |
> | . | Microsoft.DataLakeStore/locations/operationResults/read | Obtiene el resultado de una operación de la cuenta de DataLakeStore. |
> | . | Microsoft.DataLakeStore/operations/read | Obtiene las operaciones disponibles de DataLakeStore. |
> | . | Microsoft.DataLakeStore/register/action | Registra la suscripción a DataLakeStore. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.DBforMySQL/locations/performanceTiers/read | Devuelve la lista de niveles de rendimiento disponibles. |
> | . | Microsoft.DBforMySQL/performanceTiers/read | Devuelve la lista de niveles de rendimiento disponibles. |
> | . | Microsoft.DBforMySQL/servers/delete | Elimina un servidor existente. |
> | . | Microsoft.DBforMySQL/servers/firewallRules/delete | Elimina una regla de firewall existente. |
> | . | Microsoft.DBforMySQL/servers/firewallRules/read | Devuelve la lista de reglas de firewall de un servidor u obtiene las propiedades de la regla de firewall especificada. |
> | . | Microsoft.DBforMySQL/servers/firewallRules/write | Crea una regla de firewall con los parámetros especificados o actualiza una regla existente. |
> | . | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso. |
> | . | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Devuelve los tipos de métricas que están disponibles para grupos de bases de datos |
> | . | Microsoft.DBforMySQL/servers/read | Devuelve la lista de servidores u obtiene las propiedades de un servidor específico. |
> | . | Microsoft.DBforMySQL/servers/recoverableServers/read | Devuelve la información recuperable de MySQL Server. |
> | . | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Elimina una regla de Virtual Network existente. |
> | . | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Devuelve la lista de reglas de red virtual u obtiene las propiedades de una regla de red virtual específica. |
> | . | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Crea una regla de red virtual con los parámetros especificados o actualiza las propiedades o etiquetas de la regla de red virtual especificada. |
> | . | Microsoft.DBforMySQL/servers/write | Crea un servidor con los parámetros especificados o actualiza las propiedades o etiquetas del servidor especificado. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Devuelve la lista de niveles de rendimiento disponibles. |
> | . | Microsoft.DBforPostgreSQL/performanceTiers/read | Devuelve la lista de niveles de rendimiento disponibles. |
> | . | Microsoft.DBforPostgreSQL/servers/delete | Elimina un servidor existente. |
> | . | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Elimina una regla de firewall existente. |
> | . | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Devuelve la lista de reglas de firewall de un servidor u obtiene las propiedades de la regla de firewall especificada. |
> | . | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Crea una regla de firewall con los parámetros especificados o actualiza una regla existente. |
> | . | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso. |
> | . | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obtiene los registros disponibles de los servidores de Postgres. |
> | . | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Devuelve los tipos de métricas que están disponibles para grupos de bases de datos |
> | . | Microsoft.DBforPostgreSQL/servers/read | Devuelve la lista de servidores u obtiene las propiedades de un servidor específico. |
> | . | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Devuelve la información recuperable de PostgreSQL Server. |
> | . | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Recupera detalles de la directiva de detección de amenazas del servidor configurada en un servidor determinado |
> | . | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Cambia la directiva de detección de amenazas del servidor para un servidor determinado. |
> | . | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Elimina una regla de Virtual Network existente. |
> | . | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Devuelve la lista de reglas de red virtual u obtiene las propiedades de una regla de red virtual específica. |
> | . | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Crea una regla de red virtual con los parámetros especificados o actualiza las propiedades o etiquetas de la regla de red virtual especificada. |
> | . | Microsoft.DBforPostgreSQL/servers/write | Crea un servidor con los parámetros especificados o actualiza las propiedades o etiquetas del servidor especificado. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Devices/checkNameAvailability/Action | Comprueba si el nombre de la instancia de IotHub está disponible |
> | . | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Comprueba si el nombre de la instancia de IotHub está disponible |
> | . | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Elimina el recurso de inquilino de IotHub. |
> | . | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Elimina los grupos de consumidores de EventHub |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Obtiene los grupos de consumidores de EventHub |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Crea un grupo de consumidores de EventHub |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Exporta los dispositivos |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Obtiene el recurso de las estadísticas de inquilino de IotHub. |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importa los dispositivos |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Obtiene la clave de inquilino de IotHub. |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Obtiene detalles de los trabajos enviados en una determinada instancia de IotHub |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Obtiene las claves de inquilino de IotHub. |
> | . | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Obtiene las definiciones de registros disponibles del servicio IotHub. |
> | . | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Obtiene las métricas disponibles del servicio IotHub |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Obtiene las métricas de cuota |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/Read | Obtiene el recurso de inquilino de IotHub. |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Prueba un mensaje en todas las rutas existentes |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Prueba un mensaje en una ruta proporcionada de pruebas |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Obtiene el estado de todos los puntos de conexión de enrutamientos de una instancia de IotHub |
> | . | Microsoft.Devices/elasticPools/iotHubTenants/Write | Crea o actualiza el recurso de inquilino de IotHub. |
> | . | Microsoft.Devices/ElasticPools/metricDefinitions/read | Obtiene las métricas disponibles del servicio IotHub |
> | . | Microsoft.Devices/iotHubs/certificates/Delete | Elimina el certificado |
> | . | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Genera el código de verificación. |
> | . | Microsoft.Devices/iotHubs/certificates/Read | Obtiene el certificado |
> | . | Microsoft.Devices/iotHubs/certificates/verify/Action | Comprueba el recurso del certificado. |
> | . | Microsoft.Devices/iotHubs/certificates/Write | Crea o actualiza el certificado |
> | . | Microsoft.Devices/iotHubs/Delete | Elimina los recursos de IotHub |
> | . | Microsoft.Devices/IotHubs/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.Devices/IotHubs/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Elimina el filtro de Event Grid. |
> | . | Microsoft.Devices/iotHubs/eventGridFilters/Read | Obtiene el filtro de Event Grid. |
> | . | Microsoft.Devices/iotHubs/eventGridFilters/Write | Crea un filtro de Event Grid o actualiza el existente. |
> | . | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Elimina los grupos de consumidores de EventHub |
> | . | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Obtiene los grupos de consumidores de EventHub |
> | . | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Crea un grupo de consumidores de EventHub |
> | . | Microsoft.Devices/iotHubs/exportDevices/Action | Exporta los dispositivos |
> | . | Microsoft.Devices/iotHubs/importDevices/Action | Importa los dispositivos |
> | . | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Obtiene la clave de IotHub para el nombre especificado |
> | . | Microsoft.Devices/iotHubs/iotHubStats/Read | Obtiene las estadísticas de IotHub |
> | . | Microsoft.Devices/iotHubs/jobs/Read | Obtiene detalles de los trabajos enviados en una determinada instancia de IotHub |
> | . | Microsoft.Devices/iotHubs/listkeys/Action | Obtiene todas las claves de IotHub |
> | . | Microsoft.Devices/IotHubs/logDefinitions/read | Obtiene las definiciones de registros disponibles del servicio IotHub. |
> | . | Microsoft.Devices/IotHubs/metricDefinitions/read | Obtiene las métricas disponibles del servicio IotHub |
> | . | Microsoft.Devices/iotHubs/quotaMetrics/Read | Obtiene las métricas de cuota |
> | . | Microsoft.Devices/iotHubs/Read | Obtiene los recursos de IotHub |
> | . | Microsoft.Devices/iotHubs/routing/$testall/Action | Prueba un mensaje en todas las rutas existentes |
> | . | Microsoft.Devices/iotHubs/routing/$testnew/Action | Prueba un mensaje en una ruta proporcionada de pruebas |
> | . | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Obtiene el estado de todos los puntos de conexión de enrutamientos de una instancia de IotHub |
> | . | Microsoft.Devices/iotHubs/skus/Read | Obtiene SKU válidas de IotHub |
> | . | Microsoft.Devices/iotHubs/Write | Crea o actualiza los recursos de IotHub |
> | . | Microsoft.Devices/operations/Read | Obtiene todas las operaciones de ResourceProvider |
> | . | Microsoft.Devices/provisioningServices/certificates/Delete | Elimina el certificado |
> | . | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Genera el código de verificación. |
> | . | Microsoft.Devices/provisioningServices/certificates/Read | Obtiene el certificado |
> | . | Microsoft.Devices/provisioningServices/certificates/verify/Action | Comprueba el recurso del certificado. |
> | . | Microsoft.Devices/provisioningServices/certificates/Write | Crea o actualiza el certificado |
> | . | Microsoft.Devices/provisioningServices/Delete | Elimina el recurso IotDps. |
> | . | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.Devices/provisioningServices/listkeys/Action | Obtiene todas las claves de IotDps. |
> | . | Microsoft.Devices/provisioningServices/logDefinitions/read | Obtiene las definiciones de registros disponibles del servicio de aprovisionamiento. |
> | . | Microsoft.Devices/provisioningServices/metricDefinitions/read | Obtiene las métricas disponibles del servicio de aprovisionamiento. |
> | . | Microsoft.Devices/provisioningServices/ProvisioningServiceKeys/listkeys/Action | Obtiene las claves de IotDps del nombre de clave. |
> | . | Microsoft.Devices/provisioningServices/Read | Obtiene el recurso IotDps. |
> | . | Microsoft.Devices/provisioningServices/skus/Read | Obtiene las SKU válidas de IotDps. |
> | . | Microsoft.Devices/provisioningServices/Write | Crea el recurso IotDps. |
> | . | Microsoft.Devices/register/action | Registra la suscripción para el proveedor de recursos de IotHub y habilita la creación de recursos de IotHub |
> | . | Microsoft.Devices/register/action | Registra la suscripción para el proveedor de recursos de IotHub y habilita la creación de recursos de IotHub |
> | . | Microsoft.Devices/usages/Read | Obtiene los detalles de uso de la suscripción para este proveedor. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.DevTestLab/labCenters/delete | Elimina los centros de laboratorio. |
> | . | Microsoft.DevTestLab/labCenters/read | Lee los centros de laboratorio. |
> | . | Microsoft.DevTestLab/labCenters/write | Agrega o modifica centros de laboratorio. |
> | . | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Lee las plantillas de Azure Resource Manager. |
> | . | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Genera una plantilla de ARM para el artefacto en cuestión, carga los archivos necesarios en una cuenta de almacenamiento y valida el artefacto generado. |
> | . | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Lee artefactos. |
> | . | Microsoft.DevTestLab/labs/artifactSources/delete | Elimina orígenes de artefacto. |
> | . | Microsoft.DevTestLab/labs/artifactSources/read | Lee orígenes de artefacto. |
> | . | Microsoft.DevTestLab/labs/artifactSources/write | Agrega o modifica orígenes de artefacto. |
> | . | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Reclama una máquina virtual aleatoria en el laboratorio. |
> | . | Microsoft.DevTestLab/labs/costs/read | Lee los costos. |
> | . | Microsoft.DevTestLab/labs/costs/write | Agrega o modifica costos. |
> | . | Microsoft.DevTestLab/labs/CreateEnvironment/action | Crea máquinas virtuales en un laboratorio. |
> | . | Microsoft.DevTestLab/labs/customImages/delete | Elimina imágenes personalizadas. |
> | . | Microsoft.DevTestLab/labs/customImages/read | Lee imágenes personalizadas. |
> | . | Microsoft.DevTestLab/labs/customImages/write | Agrega o modifica imágenes personalizadas. |
> | . | Microsoft.DevTestLab/labs/delete | Elimina laboratorios. |
> | . | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exporta el uso de recursos del laboratorio a una cuenta de almacenamiento |
> | . | Microsoft.DevTestLab/labs/formulas/delete | Elimina fórmulas. |
> | . | Microsoft.DevTestLab/labs/formulas/read | Lee fórmulas. |
> | . | Microsoft.DevTestLab/labs/formulas/write | Agrega o modifica fórmulas. |
> | . | Microsoft.DevTestLab/labs/galleryImages/read | Lee imágenes de la galería. |
> | . | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Genera un identificador URI para cargar imágenes de disco personalizadas en un laboratorio. |
> | . | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importa una máquina virtual en un laboratorio diferente. |
> | . | Microsoft.DevTestLab/labs/ListVhds/action | Enumera las imágenes de disco disponibles para la creación de imágenes personalizadas. |
> | . | Microsoft.DevTestLab/labs/notificationChannels/delete | Elimina notificationchannels. |
> | . | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Envía una notificación al canal proporcionado. |
> | . | Microsoft.DevTestLab/labs/notificationChannels/read | Lee notificationchannels. |
> | . | Microsoft.DevTestLab/labs/notificationChannels/write | Agrega o modifica notificationchannels. |
> | . | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Evalúa la directiva de laboratorio. |
> | . | Microsoft.DevTestLab/labs/policySets/policies/delete | Elimina directivas. |
> | . | Microsoft.DevTestLab/labs/policySets/policies/read | Lee directivas. |
> | . | Microsoft.DevTestLab/labs/policySets/policies/write | Agrega o modifica directivas. |
> | . | Microsoft.DevTestLab/labs/read | Lee laboratorios. |
> | . | Microsoft.DevTestLab/labs/schedules/delete | Elimina programaciones. |
> | . | Microsoft.DevTestLab/labs/schedules/Execute/action | Ejecuta una programación. |
> | . | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Enumera todas las programaciones aplicables |
> | . | Microsoft.DevTestLab/labs/schedules/read | Lee programaciones. |
> | . | Microsoft.DevTestLab/labs/schedules/write | Agrega o modifica programaciones. |
> | . | Microsoft.DevTestLab/labs/serviceRunners/delete | Elimina ejecutores del servicio. |
> | . | Microsoft.DevTestLab/labs/serviceRunners/read | Lee ejecutores del servicio. |
> | . | Microsoft.DevTestLab/labs/serviceRunners/write | Agrega o modifica ejecutores del servicio. |
> | . | Microsoft.DevTestLab/labs/users/delete | Elimina perfiles de usuario. |
> | . | Microsoft.DevTestLab/labs/users/disks/Attach/action | Adjunta y crea la concesión del disco a la máquina virtual. |
> | . | Microsoft.DevTestLab/labs/users/disks/delete | Elimina discos. |
> | . | Microsoft.DevTestLab/labs/users/disks/Detach/action | Desasocia y separa la concesión del disco adjuntado a la máquina virtual. |
> | . | Microsoft.DevTestLab/labs/users/disks/read | Lee discos. |
> | . | Microsoft.DevTestLab/labs/users/disks/write | Agrega o modifica discos. |
> | . | Microsoft.DevTestLab/labs/users/environments/delete | Elimina entornos. |
> | . | Microsoft.DevTestLab/labs/users/environments/read | Lee entornos. |
> | . | Microsoft.DevTestLab/labs/users/environments/write | Agrega o modifica entornos. |
> | . | Microsoft.DevTestLab/labs/users/read | Lee perfiles de usuario. |
> | . | Microsoft.DevTestLab/labs/users/secrets/delete | Elimina secretos. |
> | . | Microsoft.DevTestLab/labs/users/secrets/read | Lee secretos. |
> | . | Microsoft.DevTestLab/labs/users/secrets/write | Agrega o modifica secretos. |
> | . | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Elimina instancias de Service Fabric. |
> | . | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Enumera todas las programaciones aplicables |
> | . | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Lee instancias de Service Fabric. |
> | . | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Elimina programaciones. |
> | . | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Ejecuta una programación. |
> | . | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Lee programaciones. |
> | . | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Agrega o modifica programaciones. |
> | . | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Inicia una instancia de Service Fabric. |
> | . | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Detiene una instancia de Service Fabric. |
> | . | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Agrega o modifica instancias de Service Fabric. |
> | . | Microsoft.DevTestLab/labs/users/write | Agrega o modifica perfiles de usuario. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Conecta un disco de datos nuevo o existente a una máquina virtual. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Aplica artefactos a la máquina virtual. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Toma la propiedad de una máquina virtual existente |
> | . | Microsoft.DevTestLab/labs/virtualMachines/delete | Elimina máquinas virtuales. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Desconecta el disco especificado de la máquina virtual. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Enumera todas las programaciones aplicables |
> | . | Microsoft.DevTestLab/labs/virtualMachines/read | Lee máquinas virtuales. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Vuelve a implementar una máquina virtual |
> | . | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Cambia el tamaño de una máquina virtual. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Reinicia una máquina virtual. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Elimina programaciones. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Ejecuta una programación. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Lee programaciones. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Agrega o modifica programaciones. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Inicia una máquina virtual. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Detención de una máquina virtual |
> | . | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Le transfiere a usted la propiedad de los discos de datos de una máquina virtual. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Transfiere la posesión de una máquina virtual existente. |
> | . | Microsoft.DevTestLab/labs/virtualMachines/write | Agrega o modifica máquinas virtuales. |
> | . | Microsoft.DevTestLab/labs/virtualNetworks/delete | Elimina redes virtuales. |
> | . | Microsoft.DevTestLab/labs/virtualNetworks/read | Lee redes virtuales. |
> | . | Microsoft.DevTestLab/labs/virtualNetworks/write | Agrega o modifica redes virtuales. |
> | . | Microsoft.DevTestLab/labs/vmPools/delete | Elimina grupos de máquinas virtuales. |
> | . | Microsoft.DevTestLab/labs/vmPools/read | Lee grupos de máquinas virtuales. |
> | . | Microsoft.DevTestLab/labs/vmPools/write | Agrega o modifica grupos de máquinas virtuales. |
> | . | Microsoft.DevTestLab/labs/write | Agrega o modifica laboratorios. |
> | . | Microsoft.DevTestLab/locations/operations/read | Lee operaciones. |
> | . | Microsoft.DevTestLab/register/action | Registra la suscripción |
> | . | Microsoft.DevTestLab/schedules/delete | Elimina programaciones. |
> | . | Microsoft.DevTestLab/schedules/Execute/action | Ejecuta una programación. |
> | . | Microsoft.DevTestLab/schedules/read | Lee programaciones. |
> | . | Microsoft.DevTestLab/schedules/Retarget/action | Actualiza un identificador de recurso de destino de la programación. |
> | . | Microsoft.DevTestLab/schedules/write | Agrega o modifica programaciones. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.DocumentDB/databaseAccountNames/read | Comprueba la disponibilidad del nombre. |
> | . | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Cambia el grupo de recursos de una cuenta de base de datos |
> | . | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Lee las definiciones de métricas de la colección. |
> | . | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Lee las métricas de la colección. |
> | . | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Lee las métricas de nivel de clave de partición de la cuenta de base de datos. |
> | . | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Lee las métricas de nivel de clave de partición de la cuenta de base de datos. |
> | . | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Las particiones de la cuenta de base de datos de una colección. |
> | . | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Lee los usos de nivel de partición de la cuenta de base de datos. |
> | . | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Lee los usos de la colección. |
> | . | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Lee las definiciones de métrica de la base de datos |
> | . | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Lee las métricas de base de datos. |
> | . | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Lee los usos de la base de datos. |
> | . | Microsoft.DocumentDB/databaseAccounts/delete | Elimina las cuentas de base de datos. |
> | . | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Cambia las prioridades de conmutación por error de las regiones de una cuenta de base de datos. Esto se utiliza para realizar una operación manual de conmutación por error |
> | . | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Obtiene las cadenas de conexión para una cuenta de base de datos |
> | . | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Enumera las claves de una cuenta de base de datos |
> | . | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Lee las definiciones de métricas de la cuenta de base de datos. |
> | . | Microsoft.DocumentDB/databaseAccounts/metrics/read | Lee las métricas de la cuenta de base de datos. |
> | . | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Lee el estado de una operación asincrónica. |
> | . | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Lee la latencia de las métricas. |
> | . | Microsoft.DocumentDB/databaseAccounts/percentile/read | Lee percentiles de latencias de replicación |
> | . | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Lee las métricas de latencia para una región específica de origen y de destino. |
> | . | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Lee las métricas de latencia para una región específica de destino. |
> | . | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read | Obtiene las categorías de registro disponibles para la cuenta de base de datos. |
> | . | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles para la cuenta de base de datos. |
> | . | Microsoft.DocumentDB/databaseAccounts/read | Lee una cuenta de base de datos. |
> | . | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Lee las claves de solo lectura de la cuenta de base de datos. |
> | . | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Lee las claves de solo lectura de la cuenta de base de datos. |
> | . | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Rota las claves de una cuenta de base de datos |
> | . | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Lee las métricas de la colección regional. |
> | . | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Lee las métricas de nivel de clave de partición de la cuenta de base de datos regional. |
> | . | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Lee las métricas de nivel de partición de la cuenta de base de datos regional. |
> | . | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Lee las particiones de la cuenta de base de datos regional de una colección. |
> | . | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Lee las métricas de la cuenta de base de datos y de región. |
> | . | Microsoft.DocumentDB/databaseAccounts/usages/read | Lee los usos de la cuenta de base de datos. |
> | . | Microsoft.DocumentDB/databaseAccounts/write | Actualiza una cuenta de base de datos. |
> | . | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Notifica a Microsoft.DocumentDB que se está eliminando una red virtual o una subred. |
> | . | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Lee el estado de la operación asincrónica deleteVirtualNetworkOrSubnets  |
> | . | Microsoft.DocumentDB/operationResults/read | Lee el estado de una operación asincrónica. |
> | . | Microsoft.DocumentDB/operations/read | Lee las operaciones disponibles para Microsoft DocumentDB.  |
> | . | Microsoft.DocumentDB/register/action |  Registra el proveedor de recursos de Microsoft DocumentDB de la suscripción. |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.DomainRegistration/checkDomainAvailability/Action | Comprueba si un dominio está disponible para su compra |
> | . | Microsoft.DomainRegistration/domains/Delete | Elimina un dominio ya existente. |
> | . | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Elimina el identificador de propiedad. |
> | . | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Enumera los identificadores de propiedad. |
> | . | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Obtiene el identificador de propiedad. |
> | . | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Crea o actualiza el identificador. |
> | . | Microsoft.DomainRegistration/domains/operationresults/Read | Obtiene una operación de dominio |
> | . | Microsoft.DomainRegistration/domains/Read | Obtiene la lista de dominios |
> | . | Microsoft.DomainRegistration/domains/Read | Obtiene el destino. |
> | . | Microsoft.DomainRegistration/domains/renew/Action | Renueva un dominio ya existente. |
> | . | Microsoft.DomainRegistration/domains/Write | Agrega un nuevo dominio o actualiza uno existente |
> | . | Microsoft.DomainRegistration/generateSsoRequest/Action | Genera una solicitud para iniciar sesión en el centro de control de dominios. |
> | . | Microsoft.DomainRegistration/listDomainRecommendations/Action | Recupera las recomendaciones de dominio de lista en función de las palabras clave |
> | . | Microsoft.DomainRegistration/operations/Read | Enumera todas las operaciones del registro de dominio del servicio de la aplicación. |
> | . | Microsoft.DomainRegistration/register/action | Registra el proveedor de recursos de Microsoft Domains de la suscripción |
> | . | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Enumera la acción del contrato. |
> | . | Microsoft.DomainRegistration/topLevelDomains/Read | Obtiene los dominios de nivel superior. |
> | . | Microsoft.DomainRegistration/topLevelDomains/Read | Obtiene el dominio de nivel superior. |
> | . | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Valida un objeto de compra de dominio sin enviarlo |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/read | Muestra las implementaciones de evaluación de Microsoft Dynamics AX 2012 R3 de un proyecto de Microsoft Dynamics Lifecycle Services que pertenece a un usuario |
> | . | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/write | Crea las implementaciones de evaluación de Microsoft Dynamics AX 2012 R3 de un proyecto de Microsoft Dynamics Lifecycle Services que pertenece a un usuario. Las implementaciones se pueden administrar desde el portal de administración de Azure |
> | . | Microsoft.DynamicsLcs/lcsprojects/connectors/read | Lee los conectores que pertenecen a un proyecto de Microsoft Dynamics Lifecycle Services |
> | . | Microsoft.DynamicsLcs/lcsprojects/connectors/write | Crea y actualiza los conectores que pertenecen a un proyecto de Microsoft Dynamics Lifecycle Services |
> | . | Microsoft.DynamicsLcs/lcsprojects/delete | Elimina los proyectos de Microsoft Dynamics Lifecycle Services que pertenecen al usuario |
> | . | Microsoft.DynamicsLcs/lcsprojects/read | Muestra los proyectos de Microsoft Dynamics Lifecycle Services que pertenecen a un usuario |
> | . | Microsoft.DynamicsLcs/lcsprojects/write | Crea y actualiza los proyectos de Microsoft Dynamics Lifecycle Services que pertenecen al usuario. Solo se pueden actualizar las propiedades de nombre y descripción. No se puede actualizar la suscripción y ubicación asociadas con el proyecto después de la creación |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.EventGrid/eventSubscriptions/delete | Elimina una clase eventSubscription. |
> | . | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Obtiene la dirección URL completa de la suscripción a eventos. |
> | . | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico de las suscripciones a eventos. |
> | . | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de las suscripciones a eventos. |
> | . | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de eventSubscriptions. |
> | . | Microsoft.EventGrid/eventSubscriptions/read | Lee una clase eventSubscription. |
> | . | Microsoft.EventGrid/eventSubscriptions/write | Crea o actualiza una clase eventSubscription. |
> | . | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico de los temas. |
> | . | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de los temas. |
> | . | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de los temas. |
> | . | Microsoft.EventGrid/register/action | Registra la clase eventSubscription del proveedor de recursos de EventGrid y habilita la creación de suscripciones de Event Grid. |
> | . | Microsoft.EventGrid/topics/delete | Eliminación de un tema |
> | . | Microsoft.EventGrid/topics/listKeys/action | Enumera las claves del tema. |
> | . | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico de los temas. |
> | . | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de los temas. |
> | . | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de los temas. |
> | . | Microsoft.EventGrid/topics/read | Lee un tema. |
> | . | Microsoft.EventGrid/topics/regenerateKey/action | Regenera la clave del tema. |
> | . | Microsoft.EventGrid/topics/write | Crea o actualiza un tema. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.EventHub/checkNameAvailability/action | Comprueba la disponibilidad del espacio de nombres en una suscripción dada. |
> | . | Microsoft.EventHub/checkNamespaceAvailability/action | Comprueba la disponibilidad del espacio de nombres en una suscripción dada. Esta API está en desuso; en su lugar, use CheckNameAvailabiltiy. |
> | . | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obtiene una lista de descripciones de recursos de métricas del clúster |
> | . | Microsoft.EventHub/clusters/read | Obtiene la descripción de los recursos del clúster |
> | . | Microsoft.EventHub/clusters/write | Obtiene la descripción de los recursos del clúster |
> | . | Microsoft.EventHub/namespaces/authorizationRules/action | Actualiza la regla de autorización del espacio de nombres. Esta API está en desuso. Use una llamada PUT para actualizar la regla de autorización del espacio de nombres en su lugar. Esta operación no se admite en la versión de API del 01/04/2017. |
> | . | Microsoft.EventHub/namespaces/authorizationRules/delete | Elimina la regla de autorización del espacio de nombres. No se puede eliminar la regla predeterminada de autorización del espacio de nombres.  |
> | . | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Obtiene la cadena de conexión al espacio de nombres |
> | . | Microsoft.EventHub/namespaces/authorizationRules/read | Obtiene la lista de descripciones de reglas de autorización de espacios de nombres. |
> | . | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Regenera la clave principal o secundaria del recurso |
> | . | Microsoft.EventHub/namespaces/authorizationRules/write | Crea reglas de autorización en el nivel del espacio de nombres y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria. |
> | . | Microsoft.EventHub/namespaces/Delete | Elimina el recurso del espacio de nombres |
> | . | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtiene las claves de las reglas de autorización del espacio de nombres principal de recuperación ante desastres. |
> | . | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obtiene las reglas de autorización del espacio de nombres principal de recuperación ante desastres. |
> | . | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Deshabilita la recuperación ante desastres y deja de replicar los cambios de los espacios de nombres principales a los secundarios. |
> | . | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Comprueba la disponibilidad del alias del espacio de nombres en una suscripción dada. |
> | . | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Elimina la configuración de la recuperación ante desastres asociada con el espacio de nombres. Esta operación sólo se puede invocar a través del espacio de nombres principal. |
> | . | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Invoca una conmutación por error de GEO DR y vuelve a configurar el alias del espacio de nombres para que apunte al espacio de nombres secundario. |
> | . | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Obtiene la configuración de la recuperación ante desastres asociada con el espacio de nombres. |
> | . | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Crea o actualiza la configuración de la recuperación ante desastres asociada con el espacio de nombres. |
> | . | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Operación para actualizar EventHub. Esta operación no se admite en la versión de API del 01/04/2017. Reglas de autorización. Use una llamada PUT para actualizar la regla de autorización. |
> | . | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Operación para eliminar las reglas de autorización de EventHub |
> | . | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Obtiene la cadena de conexión a EventHub |
> | . | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Obtiene la lista de reglas de autorización de EventHub |
> | . | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Regenera la clave principal o secundaria del recurso |
> | . | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Crea reglas de autorización de EventHub y actualiza sus propiedades. Los derechos de acceso de las reglas de autorización se pueden actualizar. |
> | . | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operación para eliminar un recurso de ConsumerGroup |
> | . | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Obtiene una lista de descripciones de recursos de ConsumerGroup |
> | . | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Crea o actualiza las propiedades de ConsumerGroup. |
> | . | Microsoft.EventHub/namespaces/eventhubs/Delete | Operación para eliminar los recursos de EventHub |
> | . | Microsoft.EventHub/namespaces/eventhubs/read | Obtiene una lista de descripciones de recursos de EventHub |
> | . | Microsoft.EventHub/namespaces/eventhubs/write | Crea o actualiza propiedades de EventHub. |
> | . | Microsoft.EventHub/namespaces/messagingPlan/read | Obtiene el plan de mensajería para un espacio de nombres.<br>Esta API está en desuso.<br>Las propiedades expuestas a través del recurso MessagingPlan se mueven al recurso del espacio de nombres (principal) en versiones posteriores de la API.<br>Esta operación no se admite en la versión de API del 01/04/2017. |
> | . | Microsoft.EventHub/namespaces/messagingPlan/write | Actualiza el plan mensajería de un espacio de nombres.<br>Esta API está en desuso.<br>Las propiedades expuestas a través del recurso MessagingPlan se mueven al recurso del espacio de nombres (principal) en versiones posteriores de la API.<br>Esta operación no se admite en la versión de API del 01/04/2017. |
> | . | Microsoft.EventHub/namespaces/operationresults/read | Obtiene el estado de la operación del espacio de nombres. |
> | . | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene una lista de descripciones de recursos de configuración de diagnósticos del espacio de nombres |
> | . | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obtiene una lista de descripciones de recursos de configuración de diagnósticos del espacio de nombres |
> | . | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obtiene una lista de descripciones de recursos de registros del espacio de nombres |
> | . | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obtiene una lista de descripciones de recursos de métricas del espacio de nombres |
> | . | Microsoft.EventHub/namespaces/read | Obtiene la lista de descripción del recurso del espacio de nombres |
> | . | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Quita el espacio de nombres de ACS |
> | . | Microsoft.EventHub/namespaces/write | Crea un recurso de espacio de nombres y actualiza sus propiedades. Las etiquetas y la capacidad del espacio de nombres son las propiedades que se pueden actualizar. |
> | . | Microsoft.EventHub/operations/read | Obtiene operaciones. |
> | . | Microsoft.EventHub/register/action | Registra la suscripción para el proveedor de recursos de EventHub y habilita la creación de recursos de EventHub |
> | . | Microsoft.EventHub/sku/read | Obtiene una lista de descripciones de recursos de SKU. |
> | . | Microsoft.EventHub/sku/regions/read | Obtiene una lista de descripciones de recursos de SkuRegions. |
> | . | Microsoft.EventHub/unregister/action | Registra el proveedor de recursos de EventHub. |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Features/features/read | Obtiene las características de una suscripción. |
> | . | Microsoft.Features/operations/read | Obtiene la lista de operaciones. |
> | . | Microsoft.Features/providers/features/read | Obtiene la característica de una suscripción de un proveedor de recursos determinado. |
> | . | Microsoft.Features/providers/features/register/action | Registra la característica de una suscripción de un proveedor de recursos determinado. |
> | . | Microsoft.Features/providers/features/unregister/action | Anula el registro de la característica de una suscripción de un proveedor de recursos determinado. |
> | . | Microsoft.Features/register/action | Registra la característica de una suscripción. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.HDInsight/clusters/changerdpsetting/action | Cambia la configuración de RDP de un clúster de HDInsight |
> | . | Microsoft.HDInsight/clusters/configurations/action | Actualiza la configuración de un clúster de HDInsight |
> | . | Microsoft.HDInsight/clusters/configurations/read | Obtiene las configuraciones de un clúster de HDInsight |
> | . | Microsoft.HDInsight/clusters/delete | Elimina un clúster de HDInsight |
> | . | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso de clúster de HDInsight. |
> | . | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso de clúster de HDInsight. |
> | . | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles del clúster de HDInsight. |
> | . | Microsoft.HDInsight/clusters/read | Obtiene detalles sobre el clúster de HDInsight |
> | . | Microsoft.HDInsight/clusters/roles/resize/action | Cambia el tamaño de un clúster de HDInsight |
> | . | Microsoft.HDInsight/clusters/write | Crea o actualiza un clúster de HDInsight |
> | . | Microsoft.HDInsight/locations/capabilities/read | Obtiene las funcionalidades de la suscripción |
> | . | Microsoft.HDInsight/locations/checkNameAvailability/read | Comprueba la disponibilidad del nombre |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.ImportExport/jobs/delete | Elimina un trabajo existente. |
> | . | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Obtiene las claves de BitLocker para el trabajo especificado. |
> | . | Microsoft.ImportExport/jobs/read | Obtiene las propiedades del trabajo especificado o devuelve la lista de trabajos. |
> | . | Microsoft.ImportExport/jobs/write | Crea un trabajo con los parámetros especificados o actualiza las propiedades o etiquetas del trabajo especificado. |
> | . | Microsoft.ImportExport/locations/read | Obtiene las propiedades de la ubicación especificada o devuelve la lista de ubicaciones. |
> | . | Microsoft.ImportExport/register/action | Registra la suscripción del proveedor de recursos de Import/Export y habilita la creación de trabajos de Import/Export. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Insights/ActionGroups/Delete | Elimina un grupo de acciones. |
> | . | Microsoft.Insights/ActionGroups/Read | Lee un grupo de acciones. |
> | . | Microsoft.Insights/ActionGroups/Write | Escribe un grupo de acciones. |
> | . | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Se desencadena la alerta del registro de actividad |
> | . | Microsoft.Insights/ActivityLogAlerts/Delete | Elimina una alerta de registro de actividad. |
> | . | Microsoft.Insights/ActivityLogAlerts/Read | Lee una alerta de registro de actividad. |
> | . | Microsoft.Insights/ActivityLogAlerts/Write | Lee una alerta de registro de actividad. |
> | . | Microsoft.Insights/AlertRules/Activated/Action | Regla de alerta activada |
> | . | Microsoft.Insights/AlertRules/Delete | Elimina una configuración de regla de alerta |
> | . | Microsoft.Insights/AlertRules/Incidents/Read | Lee una configuración de incidentes de regla de alerta |
> | . | Microsoft.Insights/AlertRules/Read | Lee una configuración de regla de alerta |
> | . | Microsoft.Insights/AlertRules/Resolved/Action | Regla de alerta resuelta |
> | . | Microsoft.Insights/AlertRules/Throttled/Action | Se limita la regla de alerta |
> | . | Microsoft.Insights/AlertRules/Write | Escribe en una configuración de regla de alerta |
> | . | Microsoft.Insights/AutoscaleSettings/Delete | Elimina una configuración de opciones de escalado automático |
> | . | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Lee definiciones de métricas |
> | . | Microsoft.Insights/AutoscaleSettings/Read | Lee una configuración de opciones de escalado automático |
> | . | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Operación de reducción vertical de autoescala |
> | . | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Operación de escalado vertical de autoescala |
> | . | Microsoft.Insights/AutoscaleSettings/Write | Escribe en una configuración de opciones de escalado automático |
> | . | Microsoft.Insights/Components/AnalyticsItems/Delete | Elimina un elemento de análisis de Application Insights. |
> | . | Microsoft.Insights/Components/AnalyticsItems/Read | Lee un elemento de análisis de Application Insights. |
> | . | Microsoft.Insights/Components/AnalyticsItems/Write | Escribe un elemento de análisis de Application Insights. |
> | . | Microsoft.Insights/Components/AnalyticsTables/Action | Acción de la tabla de análisis de Application Insights. |
> | . | Microsoft.Insights/Components/AnalyticsTables/Delete | Elimina un esquema de la tabla de análisis de Application Insights. |
> | . | Microsoft.Insights/Components/AnalyticsTables/Read | Lee un esquema de la tabla de análisis de Application Insights. |
> | . | Microsoft.Insights/Components/AnalyticsTables/Write | Escribe un esquema de la tabla de análisis de Application Insights. |
> | . | Microsoft.Insights/Components/Annotations/Delete | Elimina una anotación de Application Insights. |
> | . | Microsoft.Insights/Components/Annotations/Read | Lee una anotación de Application Insights. |
> | . | Microsoft.Insights/Components/Annotations/Write | Escribe una anotación de Application Insights. |
> | . | Microsoft.Insights/Components/Api/Read | Lee una API de datos de componentes de Application Insights. |
> | . | Microsoft.Insights/Components/ApiKeys/Action | Genera una clave de API de Application Insights. |
> | . | Microsoft.Insights/Components/ApiKeys/Delete | Elimina una clave de API de Application Insights. |
> | . | Microsoft.Insights/Components/ApiKeys/Read | Lee una clave de API de Application Insights. |
> | . | Microsoft.Insights/Components/BillingPlanForComponent/Read | Lee un plan de facturación para el componente de Application Insights. |
> | . | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Lee las características de facturación actuales para el componente de Application Insights. |
> | . | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Escribe las características de facturación actuales para el componente de Application Insights. |
> | . | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Lee una configuración predeterminada de integración de ALM de Application Insights. |
> | . | Microsoft.Insights/Components/Delete | Elimina la configuración de un componente de Application Insights. |
> | . | Microsoft.Insights/Components/ExportConfiguration/Action | Acción para exportar la configuración de Application Insights. |
> | . | Microsoft.Insights/Components/ExportConfiguration/Delete | Elimina la configuración exportada de Application Insights. |
> | . | Microsoft.Insights/Components/ExportConfiguration/Read | Lee la configuración exportada de Application Insights. |
> | . | Microsoft.Insights/Components/ExportConfiguration/Write | Escribe la configuración exportada de Application Insights. |
> | . | Microsoft.Insights/Components/ExtendQueries/Read | Lee los resultados de una consulta extendida de los componentes de Application Insights. |
> | . | Microsoft.Insights/Components/Favorites/Delete | Elimina un elemento favorito de Application Insights. |
> | . | Microsoft.Insights/Components/Favorites/Read | Lee un elemento favorito de Application Insights. |
> | . | Microsoft.Insights/Components/Favorites/Write | Escribe un elemento favorito de Application Insights. |
> | . | Microsoft.Insights/Components/FeatureCapabilities/Read | Lee las capacidades de las características de los componentes de Application Insights. |
> | . | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Lee las características de facturación disponibles para los componentes de Application Insights. |
> | . | Microsoft.Insights/Components/GetToken/Read | Lee un token de un componente de Application Insights. |
> | . | Microsoft.Insights/Components/MetricDefinitions/Read | Lee las definiciones de las métricas de los componentes de Application Insights. |
> | . | Microsoft.Insights/Components/Metrics/Read | Lee las métricas de los componentes de Application Insights. |
> | . | Microsoft.Insights/Components/Move/Action | Mueve un componente de Application Insights a otro grupo de recursos o suscripción. |
> | . | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Elimina un elemento de análisis personal de Application Insights. |
> | . | Microsoft.Insights/Components/MyAnalyticsItems/Read | Lee un elemento de análisis personal de Application Insights. |
> | . | Microsoft.Insights/Components/MyAnalyticsItems/Write | Escribe un elemento de análisis personal de Application Insights. |
> | . | Microsoft.Insights/Components/MyFavorites/Read | Lee un elemento favorito personal de Application Insights. |
> | . | Microsoft.Insights/Components/PricingPlans/Read | Lee un plan de precios para los componentes de Application Insights. |
> | . | Microsoft.Insights/Components/PricingPlans/Write | Escribe un plan de precios para los componentes de Application Insights. |
> | . | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Lee la configuración de detección proactiva de Application Insights. |
> | . | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Escribe la configuración de detección proactiva de Application Insights. |
> | . | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Lee definiciones de métricas |
> | . | Microsoft.Insights/Components/QuotaStatus/Read | Lee el estado de la cuota de los componentes de Application Insights. |
> | . | Microsoft.Insights/Components/Read | Lee la configuración de los componente de Application Insights. |
> | . | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Lee las ubicaciones de WebTest de Application Insights. |
> | . | Microsoft.Insights/Components/WorkItemConfigs/Delete | Elimina una configuración de integración de ALM de Application Insights. |
> | . | Microsoft.Insights/Components/WorkItemConfigs/Read | Lee una configuración de integración de ALM de Application Insights. |
> | . | Microsoft.Insights/Components/WorkItemConfigs/Write | Escribe una configuración de integración de ALM de Application Insights. |
> | . | Microsoft.Insights/Components/Write | Escribe una configuración de componente de Application Insights. |
> | . | Microsoft.Insights/DiagnosticSettings/Delete | Elimina la configuración de opciones de diagnóstico |
> | . | Microsoft.Insights/DiagnosticSettings/Read | Lee una opción de configuración de diagnóstico |
> | . | Microsoft.Insights/DiagnosticSettings/Write | Escribe la configuración de opciones de diagnóstico |
> | . | Microsoft.Insights/EventCategories/Read | Lee una categoría de eventos. |
> | . | Microsoft.Insights/eventtypes/digestevents/Read | Lee un resumen de tipos de evento de administración |
> | . | Microsoft.Insights/eventtypes/values/Read | Lee valores de tipo de evento de administración |
> | . | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Elimina la configuración de opciones de diagnóstico extendida |
> | . | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Lee una configuración de opciones de diagnóstico extendida |
> | . | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Escribe en la configuración de opciones de diagnóstico extendida |
> | . | Microsoft.Insights/ListMigrationDate/Action | Obtiene la fecha de migración de la suscripción. |
> | . | Microsoft.Insights/ListMigrationDate/Read | Obtiene la fecha de migración de la suscripción. |
> | . | Microsoft.Insights/LogDefinitions/Read | Lee definiciones de registro |
> | . | Microsoft.Insights/LogProfiles/Delete | Elimina la configuración de perfiles de registro |
> | . | Microsoft.Insights/LogProfiles/Read | Lee perfiles de registro |
> | . | Microsoft.Insights/LogProfiles/Write | Escribe en una configuración de perfil de registro |
> | . | Microsoft.Insights/MetricAlerts/Delete | Elimina una alerta de métrica. |
> | . | Microsoft.Insights/MetricAlerts/Read | Lee una alerta de métrica. |
> | . | Microsoft.Insights/MetricAlerts/Status/Read | Lectura de estado de alerta de métrica |
> | . | Microsoft.Insights/MetricAlerts/Write | Escribe una alerta de métrica. |
> | . | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Lee definiciones de métricas |
> | . | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Lee definiciones de métricas |
> | . | Microsoft.Insights/MetricDefinitions/Read | Lee definiciones de métricas |
> | . | Microsoft.Insights/Metrics/providers/Metrics/Read | Lee métricas |
> | . | Microsoft.Insights/Metrics/Read | Lee métricas |
> | . | Microsoft.Insights/Metrics/Write | Escribe métricas. |
> | . | Microsoft.Insights/MigrateToNewpricingModel/Action | Migra la suscripción al nuevo modelo de precios. |
> | . | Microsoft.Insights/Operations/Read | Lee operaciones. |
> | . | Microsoft.Insights/Register/Action | Registra el proveedor de Microsoft Insights |
> | . | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Suscripción de reversión al modelo de precios heredado. |
> | . | Microsoft.Insights/ScheduledQueryRules/Delete | Eliminación de una regla de consulta programada |
> | . | Microsoft.Insights/ScheduledQueryRules/Read | Lectura de una regla de consulta programada |
> | . | Microsoft.Insights/ScheduledQueryRules/Write | Escritura de una regla de consulta programada |
> | . | Microsoft.Insights/Tenants/Register/Action | Inicializa el proveedor de Microsoft Insights. |
> | . | Microsoft.Insights/Unregister/Action | Registra el proveedor de Microsoft Insights |
> | . | Microsoft.Insights/Webtests/Delete | Elimina una configuración de WebTest. |
> | . | Microsoft.Insights/Webtests/GetToken/Read | Lee un token de WebTest. |
> | . | Microsoft.Insights/Webtests/MetricDefinitions/Read | Lee las definiciones de métricas de WebTest. |
> | . | Microsoft.Insights/Webtests/Metrics/Read | Lee las métricas de WebTest. |
> | . | Microsoft.Insights/Webtests/Read | Lee una configuración de WebTest. |
> | . | Microsoft.Insights/Webtests/Write | Escribe una configuración de WebTest. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.KeyVault/checkNameAvailability/read | Comprueba que un nombre de almacén de claves es válido y que no está en uso |
> | . | Microsoft.KeyVault/deletedVaults/read | Ve las propiedades de los almacenes de claves eliminados temporalmente |
> | . | Microsoft.KeyVault/hsmPools/delete | Elimina un grupo HSM. |
> | . | Microsoft.KeyVault/hsmPools/joinVault/action | Une un almacén de claves a un grupo HSM. |
> | . | Microsoft.KeyVault/hsmPools/read | Consulta las propiedades de un grupo HSM. |
> | . | Microsoft.KeyVault/hsmPools/write | Crea un nuevo grupo HSM para actualizar las propiedades de un grupo HSM existente. |
> | . | Microsoft.KeyVault/locations/deletedVaults/purge/action | Purga un almacén de claves eliminado temporalmente |
> | . | Microsoft.KeyVault/locations/deletedVaults/read | Ve las propiedades de un almacén de claves eliminado temporalmente |
> | . | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Envía una notificación a Microsoft.KeyVault que indica que la subred o la red virtual se está eliminando. |
> | . | Microsoft.KeyVault/locations/operationResults/read | Comprueba el resultado de una operación de larga ejecución |
> | . | Microsoft.KeyVault/operations/read | Enumera las operaciones disponibles en el proveedor de recursos de Microsoft.KeyVault. |
> | . | Microsoft.KeyVault/register/action | Registra una suscripción |
> | . | Microsoft.KeyVault/unregister/action | Anula el registro de una suscripción. |
> | . | Microsoft.KeyVault/vaults/accessPolicies/write | Actualiza una directiva de acceso mediante combinación o sustitución, o agrega una nueva directiva de acceso a un almacén. |
> | . | Microsoft.KeyVault/vaults/delete | Elimina un almacén de claves |
> | . | Microsoft.KeyVault/vaults/deploy/action | Permite el acceso a secretos en un almacén de claves al implementar recursos de Azure |
> | . | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/logDefinitions/read | Obtiene los registros disponibles para un almacén de claves. |
> | . | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles para un almacén de claves. |
> | . | Microsoft.KeyVault/vaults/read | Ve las propiedades de un almacén de claves |
> | . | Microsoft.KeyVault/vaults/secrets/read | Ve las propiedades de un secreto, pero no su valor |
> | . | Microsoft.KeyVault/vaults/secrets/write | Crea un secreto nuevo o actualiza el valor de uno que ya existe |
> | . | Microsoft.KeyVault/vaults/write | Crea un nuevo almacén de claves o actualiza las propiedades de uno que ya existe |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.LabServices/labAccounts/CreateLab/action | Crea un laboratorio en una cuenta de laboratorio. |
> | . | Microsoft.LabServices/labAccounts/delete | Elimina las cuentas de laboratorio. |
> | . | Microsoft.LabServices/labAccounts/labs/delete | Elimina laboratorios. |
> | . | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Reclama un entorno aleatorio para un usuario en una configuración del entorno |
> | . | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Elimina la configuración del entorno. |
> | . | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | Reclama el entorno y lo asigna al usuario |
> | . | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Elimina entornos. |
> | . | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Lee entornos. |
> | . | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Inicia un entorno mediante el inicio de todos los recursos dentro del mismo. |
> | . | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Detiene un entorno mediante la detención de todos los recursos dentro del mismo. |
> | . | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Agrega o modifica entornos. |
> | . | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Aprovisiona/desaprovisiona los recursos necesarios para configurar un entorno según el estado actual de la configuración del laboratorio o entorno. |
> | . | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Lee la configuración del entorno. |
> | . | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Inicia una plantilla mediante el inicio de todos los recursos dentro de la plantilla. |
> | . | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Inicia una plantilla mediante el inicio de todos los recursos dentro de la plantilla. |
> | . | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Agrega o modifica la configuración del entorno. |
> | . | Microsoft.LabServices/labAccounts/labs/read | Lee laboratorios. |
> | . | Microsoft.LabServices/labAccounts/labs/Register/action | Registra en un laboratorio administrado. |
> | . | Microsoft.LabServices/labAccounts/labs/users/delete | Elimina usuarios. |
> | . | Microsoft.LabServices/labAccounts/labs/users/read | Lee usuarios. |
> | . | Microsoft.LabServices/labAccounts/labs/users/write | Agrega o modifica usuarios. |
> | . | Microsoft.LabServices/labAccounts/labs/write | Agrega o modifica laboratorios. |
> | . | Microsoft.LabServices/labAccounts/read | Lee las cuentas de laboratorio. |
> | . | Microsoft.LabServices/labAccounts/sizes/read | Lee tamaños. |
> | . | Microsoft.LabServices/labAccounts/write | Agrega o modifica las cuentas de laboratorio. |
> | . | Microsoft.LabServices/locations/operations/read | Lee operaciones. |
> | . | Microsoft.LabServices/register/action | Registra la suscripción |
> | . | Microsoft.LabServices/users/GetEnvironment/action | Obtiene los detalles de una máquina virtual |
> | . | Microsoft.LabServices/users/ListEnvironments/action | Enumera los entornos para el usuario |
> | . | Microsoft.LabServices/users/ListLabs/action | Enumera los laboratorios para el usuario. |
> | . | Microsoft.LabServices/users/Register/action | Registra un usuario en un laboratorio administrado |
> | . | Microsoft.LabServices/users/StartEnvironment/action | Inicia un entorno mediante el inicio de todos los recursos dentro del mismo. |
> | . | Microsoft.LabServices/users/StopEnvironment/action | Detiene un entorno mediante la detención de todos los recursos dentro del mismo. |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.LocationBasedServices/accounts/delete | (En desuso: use /providers/Microsoft.Maps) Elimina una cuenta de servicios basados en ubicación. |
> | . | Microsoft.LocationBasedServices/accounts/listKeys/action | (En desuso: use /providers/Microsoft.Maps) Enumera las claves de una cuenta de servicios basados en ubicación. |
> | . | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | (En desuso: use /providers/Microsoft.Maps) Obtiene el valor de diagnóstico del recurso |
> | . | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | (En desuso: use /providers/Microsoft.Maps) Crea o actualiza el valor de diagnóstico del recurso |
> | . | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | (En desuso: use /providers/Microsoft.Maps) Obtiene las métricas disponibles para las cuentas de servicios basados en ubicación. |
> | . | Microsoft.LocationBasedServices/accounts/read | (En desuso: use /providers/Microsoft.Maps) Obtiene una cuenta de servicios basados en ubicación. |
> | . | Microsoft.LocationBasedServices/accounts/regenerateKey/action | (En desuso: use /providers/Microsoft.Maps) Genera una clave principal o secundaria de la cuenta de servicios basados en ubicación. |
> | . | Microsoft.LocationBasedServices/accounts/write | (En desuso: use /providers/Microsoft.Maps) Crea o actualiza una cuenta de servicios basados en ubicación. |
> | . | Microsoft.LocationBasedServices/register/action | (En desuso: use /providers/Microsoft.Maps) Registra el proveedor |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.Logic/integrationAccounts/agreements/delete | Elimina el contrato de la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Obtiene la dirección URL de devolución de llamada para el contenido del contrato en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/agreements/read | Lee el contrato en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/agreements/write | Crea o actualiza el contrato en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/assemblies/delete | Elimina el ensamblado de la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Obtiene la dirección URL de devolución de llamada para el contenido de ensamblado en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/assemblies/read | Lee el ensamblado en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/assemblies/write | Crea o actualiza el ensamblado en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Elimina la configuración por lotes en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Lee la configuración por lotes en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Crea o actualiza la configuración por lotes en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/certificates/delete | Elimina el certificado de la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/certificates/read | Lee el certificado en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/certificates/write | Crea o actualiza el certificado en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/delete | Elimina la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/join/action | Combina la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Obtiene la dirección URL de devolución de llamada para la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Obtiene las claves en el almacén de claves. |
> | . | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Registra los eventos de seguimiento en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/maps/delete | Elimina la asignación de la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Obtiene la dirección URL de devolución de llamada para el contenido de asignación en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/maps/read | Lee la asignación en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/maps/write | Crea o actualiza la asignación en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/partners/delete | Elimina al asociado de la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Obtiene la dirección URL de devolución de llamada para el contenido del asociado en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/partners/read | Lee al asociado en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/partners/write | Crea o actualiza al asociado en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Lee las definiciones de registro de la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/read | Lee la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Vuelve a generar los secretos de la clave de acceso. |
> | . | Microsoft.Logic/integrationAccounts/schemas/delete | Elimina el esquema de la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Obtiene la dirección URL de devolución de llamada para el contenido del esquema en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/schemas/read | Lee el esquema en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/schemas/write | Crea o actualiza el esquema en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/sessions/delete | Elimina la sesión de la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/sessions/read | Lee la configuración por lotes en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/sessions/write | Crea o actualiza la sesión en la cuenta de integración. |
> | . | Microsoft.Logic/integrationAccounts/write | Crea o actualiza la cuenta de integración. |
> | . | Microsoft.Logic/locations/workflows/validate/action | Valida el flujo de trabajo. |
> | . | Microsoft.Logic/operations/read | Obtiene la operación. |
> | . | Microsoft.Logic/register/action | Registra el proveedor de recursos de Microsoft.Logic de una suscripción determinada. |
> | . | Microsoft.Logic/workflows/accessKeys/delete | Elimina la clave de acceso. |
> | . | Microsoft.Logic/workflows/accessKeys/list/action | Enumera los secretos de la clave de acceso. |
> | . | Microsoft.Logic/workflows/accessKeys/read | Lee la clave de acceso. |
> | . | Microsoft.Logic/workflows/accessKeys/regenerate/action | Vuelve a generar los secretos de la clave de acceso. |
> | . | Microsoft.Logic/workflows/accessKeys/write | Crea o actualiza la clave de acceso. |
> | . | Microsoft.Logic/workflows/delete | Elimina el flujo de trabajo. |
> | . | Microsoft.Logic/workflows/disable/action | Deshabilita el flujo de trabajo. |
> | . | Microsoft.Logic/workflows/enable/action | Habilita el flujo de trabajo. |
> | . | Microsoft.Logic/workflows/listCallbackUrl/action | Obtiene la dirección URL de devolución de llamada del flujo de trabajo. |
> | . | Microsoft.Logic/workflows/listSwagger/action | Obtiene las definiciones de Swagger del flujo de trabajo. |
> | . | Microsoft.Logic/workflows/move/action | Mueve el flujo de trabajo desde su identificador de suscripción, grupo de recursos o nombre existentes a un identificador de suscripción, grupo de recursos o nombre diferentes. |
> | . | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Lee la configuración de diagnóstico del flujo de trabajo. |
> | . | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del flujo de trabajo. |
> | . | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Lee las definiciones de registro del flujo de trabajo. |
> | . | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Lee las definiciones métricas del flujo de trabajo. |
> | . | Microsoft.Logic/workflows/read | Lee el flujo de trabajo. |
> | . | Microsoft.Logic/workflows/regenerateAccessKey/action | Vuelve a generar los secretos de la clave de acceso. |
> | . | Microsoft.Logic/workflows/run/action | Inicia una ejecución del flujo de trabajo. |
> | . | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Obtiene los seguimientos de expresiones de la acción de ejecución del flujo de trabajo. |
> | . | Microsoft.Logic/workflows/runs/actions/read | Lee la acción de ejecución de un flujo de trabajo. |
> | . | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Obtiene los seguimientos de expresiones de repetición de la acción de ejecución del flujo de trabajo. |
> | . | Microsoft.Logic/workflows/runs/actions/repetitions/read | Lee la repetición de la acción de ejecución del flujo de trabajo. |
> | . | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Lee la repetición del ámbito de la acción de ejecución del flujo de trabajo. |
> | . | Microsoft.Logic/workflows/runs/cancel/action | Cancela la ejecución de un flujo de trabajo. |
> | . | Microsoft.Logic/workflows/runs/operations/read | Lee el estado de la operación de ejecución de un flujo de trabajo. |
> | . | Microsoft.Logic/workflows/runs/read | Lee la ejecución de flujo de trabajo. |
> | . | Microsoft.Logic/workflows/suspend/action | Suspende el flujo de trabajo. |
> | . | Microsoft.Logic/workflows/triggers/histories/read | Lee los historiales del desencadenador. |
> | . | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Vuelve a enviar el desencadenador del flujo de trabajo. |
> | . | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Obtiene la dirección URL de devolución de llamada para el desencadenador. |
> | . | Microsoft.Logic/workflows/triggers/read | Lee el desencadenador. |
> | . | Microsoft.Logic/workflows/triggers/reset/action | Restablece el desencadenador. |
> | . | Microsoft.Logic/workflows/triggers/run/action | Ejecuta el desencadenador. |
> | . | Microsoft.Logic/workflows/triggers/setState/action | Establece el estado de los desencadenadores. |
> | . | Microsoft.Logic/workflows/validate/action | Valida el flujo de trabajo. |
> | . | Microsoft.Logic/workflows/versions/read | Lee la versión de flujo de trabajo. |
> | . | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Obtiene la dirección URL de devolución de llamada para el desencadenador. |
> | . | Microsoft.Logic/workflows/write | Crea o actualiza el flujo de trabajo. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Mueve todas las asociaciones de planes de compromiso de Machine Learning |
> | . | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Lee todas las asociaciones de planes de compromiso de Machine Learning |
> | . | Microsoft.MachineLearning/commitmentPlans/delete | Elimina todos los planes de compromiso de Machine Learning |
> | . | Microsoft.MachineLearning/commitmentPlans/join/action | Combina todos los planes de compromiso de Machine Learning |
> | . | Microsoft.MachineLearning/commitmentPlans/read | Lee todos los planes de compromiso de Machine Learning |
> | . | Microsoft.MachineLearning/commitmentPlans/write | Crea o actualiza todos los planes de compromiso de Machine Learning |
> | . | Microsoft.MachineLearning/locations/operationresults/read | Obtiene el resultado de una operación de Machine Learning. |
> | . | Microsoft.MachineLearning/locations/operationsstatus/read | Obtiene el estado de una operación de Machine Learning en curso. |
> | . | Microsoft.MachineLearning/operations/read | Obtiene operaciones de Machine Learning. |
> | . | Microsoft.MachineLearning/register/action | Registra la suscripción del proveedor de recursos del servicio web Machine Learning y habilita la creación de servicios web. |
> | . | Microsoft.MachineLearning/skus/read | Obtiene las SKU del plan de compromiso de Machine Learning. |
> | . | Microsoft.MachineLearning/webServices/action | Crea propiedades del servicio web regional las regiones admitidas |
> | . | Microsoft.MachineLearning/webServices/delete | Elimina todos los servicios web Machine Learning |
> | . | Microsoft.MachineLearning/webServices/listkeys/read | Obtiene las claves de un servicio web de Machine Learning |
> | . | Microsoft.MachineLearning/webServices/read | Lee todos los servicios web Machine Learning |
> | . | Microsoft.MachineLearning/webServices/write | Crea o actualiza todos los servicios web Machine Learning |
> | . | Microsoft.MachineLearning/Workspaces/delete | Elimina todas las áreas de trabajo de Machine Learning |
> | . | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Enumera las claves de un área de trabajo de Machine Learning |
> | . | Microsoft.MachineLearning/Workspaces/read | Lee todas las áreas de trabajo de Machine Learning |
> | . | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Vuelve a sincronizar las claves de la cuenta de almacenamiento configurada para un área de trabajo de Machine Learning |
> | . | Microsoft.MachineLearning/Workspaces/write | Crea o actualiza todas las áreas de trabajo de Machine Learning |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Comprueba si existen actualizaciones para los servicios del sistema para el clúster de puesta en marcha. |
> | . | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Elimina cualquier cuenta de hospedaje. |
> | . | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Enumera las claves asociadas con el clúster de puesta en marcha. |
> | . | Microsoft.MachineLearningCompute/operationalizationClusters/read | Lee cualquier cuenta de hospedaje. |
> | . | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Actualiza los servicios del sistema en un clúster de puesta en marcha. |
> | . | Microsoft.MachineLearningCompute/operationalizationClusters/write | Crea o actualiza cualquier cuenta de hospedaje. |
> | . | Microsoft.MachineLearningCompute/register/action | Registra el id. de la suscripción del proveedor de recursos y habilita la creación de los recursos de cálculo de Machine Learning. |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.MachineLearningModelManagement/accounts/delete | Elimina cualquier cuenta de hospedaje. |
> | . | Microsoft.MachineLearningModelManagement/accounts/read | Lee cualquier cuenta de hospedaje. |
> | . | Microsoft.MachineLearningModelManagement/accounts/write | Crea o actualiza cualquier cuenta de hospedaje. |
> | . | Microsoft.MachineLearningModelManagement/register/action | Registra el id. de la suscripción del proveedor de recursos y habilita la creación de una cuenta de hospedaje. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.MachineLearningServices/register/action | Registra la suscripción al proveedor de recursos de Machine Learning Services |
> | . | Microsoft.MachineLearningServices/workspaces/computes/delete | Elimina los recursos de proceso en las áreas de trabajo de Machine Learning Services |
> | . | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Enumera los secretos de los recursos de proceso en el área de trabajo de Machine Learning Services |
> | . | Microsoft.MachineLearningServices/workspaces/computes/read | Obtiene los recursos de proceso en las áreas de trabajo de Machine Learning Services |
> | . | Microsoft.MachineLearningServices/workspaces/computes/write | Crea o actualiza los recursos de proceso en las áreas de trabajo de Machine Learning Services |
> | . | Microsoft.MachineLearningServices/workspaces/delete | Elimina las áreas de trabajo de Machine Learning Services |
> | . | Microsoft.MachineLearningServices/workspaces/listKeys/action | Enumera los secretos un área de trabajo de Machine Learning Services |
> | . | Microsoft.MachineLearningServices/workspaces/read | Obtiene las áreas de trabajo de Machine Learning Services |
> | . | Microsoft.MachineLearningServices/workspaces/write | Crea o actualiza las áreas de trabajo de Machine Learning Services |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Acción de RBAC para asignar a un usuario existente una identidad asignada a un recurso. |
> | . | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Elimina la identidad asignada a un usuario existente. |
> | . | Microsoft.ManagedIdentity/userAssignedIdentities/read | Obtiene la identidad asignada a un usuario existente. |
> | . | Microsoft.ManagedIdentity/userAssignedIdentities/write | Crea una nueva identidad asignada a un usuario o actualiza las etiquetas asociadas a una identidad asignada a un usuario existente. |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.ManagedLab/labAccounts/CreateLab/action | Crea un laboratorio en una cuenta de laboratorio. |
> | . | Microsoft.ManagedLab/labAccounts/delete | Elimina las cuentas de laboratorio. |
> | . | Microsoft.ManagedLab/labAccounts/labs/delete | Elimina laboratorios. |
> | . | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Elimina la configuración del entorno. |
> | . | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Elimina entornos. |
> | . | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Lee entornos. |
> | . | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Agrega o modifica entornos. |
> | . | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Lee la configuración del entorno. |
> | . | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Agrega o modifica la configuración del entorno. |
> | . | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Elimina las máquinas virtuales de laboratorio. |
> | . | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Lee las máquinas virtuales. |
> | . | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Agrega o modifica las máquinas virtuales de laboratorio. |
> | . | Microsoft.ManagedLab/labAccounts/labs/read | Lee laboratorios. |
> | . | Microsoft.ManagedLab/labAccounts/labs/write | Agrega o modifica laboratorios. |
> | . | Microsoft.ManagedLab/labAccounts/read | Lee las cuentas de laboratorio. |
> | . | Microsoft.ManagedLab/labAccounts/write | Agrega o modifica las cuentas de laboratorio. |
> | . | Microsoft.ManagedLab/locations/operations/read | Lee operaciones. |
> | . | Microsoft.ManagedLab/register/action | Registra la suscripción |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Management/checkNameAvailability/action | Comprueba si el nombre del grupo de administración especificado es válido y único. |
> | . | Microsoft.Management/getEntities/action | Enumera todas las entidades (grupos de administración, suscripciones, etc.) del usuario autenticado. |
> | . | Microsoft.Management/managementGroups/delete | Elimina un grupo de administración. |
> | . | Microsoft.Management/managementGroups/read | Enumera los grupos de administración del usuario autenticado. |
> | . | Microsoft.Management/managementGroups/subscriptions/delete | Anula la asociación de la suscripción con el grupo de administración. |
> | . | Microsoft.Management/managementGroups/subscriptions/write | Asocia la suscripción existente con el grupo de administración. |
> | . | Microsoft.Management/managementGroups/write | Crea o actualiza un grupo de administración. |
> | . | Microsoft.Management/register/action | Registra la suscripción especificada en Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Maps/accounts/delete | Elimina una cuenta de Maps. |
> | . | Microsoft.Maps/accounts/listKeys/action | Enumera las claves de una cuenta de Maps. |
> | . | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.Maps/accounts/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles para las cuentas de Maps |
> | . | Microsoft.Maps/accounts/read | Obtiene una cuenta de Maps. |
> | . | Microsoft.Maps/accounts/regenerateKey/action | Genera una nueva clave principal o secundaria de la cuenta de Maps. |
> | . | Microsoft.Maps/accounts/write | Crea o actualiza un cuenta de Maps. |
> | . | Microsoft.Maps/register/action | Registra el proveedor. |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Devuelve un contrato. |
> | . | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Acepta un contrato firmado. |
> | . | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importa una imagen en el registro de control de acceso del usuario final. |
> | . | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Devuelve una configuración. |
> | . | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Guarda una configuración. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.MarketplaceApps/ClassicDevServices/delete | Realiza una operación DELETE en un recurso de servicio de desarrollo clásico. |
> | . | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Obtiene las claves de administración de un recurso de servicio de desarrollo clásico. |
> | . | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Obtiene la dirección URL del inicio de sesión único de un servicio de desarrollo clásico. |
> | . | Microsoft.MarketplaceApps/ClassicDevServices/read | Realiza una operación GET en un servicio de desarrollo clásico. |
> | . | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Genera las claves de administración de un recurso de servicio de desarrollo clásico. |
> | . | Microsoft.MarketplaceApps/Operations/read | Lee las operaciones de todos los tipos de recursos. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Cancela un acuerdo para un elemento determinado de Marketplace |
> | . | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Devuelve un acuerdo para un elemento determinado de Marketplace |
> | . | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Firma un acuerdo para un elemento determinado de Marketplace |
> | . | Microsoft.MarketplaceOrdering/agreements/read | Devuelve todos los contratos de una suscripción en concreto. |
> | . | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Obtiene un contrato para un elemento determinado de una máquina virtual de Marketplace. |
> | . | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Firma o cancela un contrato para un elemento determinado de una máquina virtual de Marketplace. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Media/checknameavailability/action | Comprueba si hay disponible un nombre de cuenta de Media Services. |
> | . | Microsoft.Media/locations/checkNameAvailability/action | Comprueba si hay disponible un nombre de cuenta de Media Services. |
> | . | Microsoft.Media/mediaservices/assets/delete | Elimina cualquier recurso |
> | . | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Obtiene una clave de cifrado de recursos |
> | . | Microsoft.Media/mediaservices/assets/listContainerSas/action | Enumera las direcciones URL de SAS del contenedor de recursos |
> | . | Microsoft.Media/mediaservices/assets/read | Lee todos los recursos |
> | . | Microsoft.Media/mediaservices/assets/write | Crea o actualiza todos los recursos |
> | . | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Elimina cualquier directiva de clave de contenido |
> | . | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Obtiene las propiedades de una directiva con secretos |
> | . | Microsoft.Media/mediaservices/contentKeyPolicies/read | Lee cualquier directiva de clave de contenido |
> | . | Microsoft.Media/mediaservices/contentKeyPolicies/write | Crea o actualiza cualquier directiva de clave de contenido |
> | . | Microsoft.Media/mediaservices/delete | Elimina cualquier cuenta de Media Services. |
> | . | Microsoft.Media/mediaservices/eventGridFilters/delete | Elimina cualquier filtro de Event Grid |
> | . | Microsoft.Media/mediaservices/eventGridFilters/read | Lee cualquier filtro de Event Grid |
> | . | Microsoft.Media/mediaservices/eventGridFilters/write | Crea o actualiza cualquier filtro de Event Grid |
> | . | Microsoft.Media/mediaservices/liveEventOperations/read | Lee cualquier operación de Evento en directo |
> | . | Microsoft.Media/mediaservices/liveEvents/delete | Elimina cualquier evento en vivo |
> | . | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Elimina cualquier salida en vivo |
> | . | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Lee cualquier salida en vivo |
> | . | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Crea o actualiza cualquier salida en vivo. |
> | . | Microsoft.Media/mediaservices/liveEvents/read | Lee cualquier evento en vivo |
> | . | Microsoft.Media/mediaservices/liveEvents/reset/action | Restablece cualquier operación de Evento en directo |
> | . | Microsoft.Media/mediaservices/liveEvents/start/action | Inicia cualquier operación de Evento en directo |
> | . | Microsoft.Media/mediaservices/liveEvents/stop/action | Detiene cualquier operación de Evento en directo |
> | . | Microsoft.Media/mediaservices/liveEvents/write | Crea o actualiza cualquier evento en vivo |
> | . | Microsoft.Media/mediaservices/liveOutputOperations/read | Lee cualquier operación de Salida en vivo |
> | . | Microsoft.Media/mediaservices/read | Lee cualquier cuenta de Media Services. |
> | . | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Lee cualquier operación del punto de conexión de streaming |
> | . | Microsoft.Media/mediaservices/streamingEndpoints/delete | Elimina cualquier punto de conexión de streaming |
> | . | Microsoft.Media/mediaservices/streamingEndpoints/read | Lee cualquier punto de conexión de streaming |
> | . | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Escala cualquier operación del punto de conexión de streaming |
> | . | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Inicia cualquier operación del punto de conexión de streaming |
> | . | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Detiene cualquier operación del punto de conexión de streaming |
> | . | Microsoft.Media/mediaservices/streamingEndpoints/write | Crea o actualiza cualquier punto de conexión de streaming |
> | . | Microsoft.Media/mediaservices/streamingLocators/delete | Elimina cualquier localizador de streaming. |
> | . | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Enumera las claves de contenido |
> | . | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Enumera las rutas |
> | . | Microsoft.Media/mediaservices/streamingLocators/read | Lee cualquier localizador de streaming |
> | . | Microsoft.Media/mediaservices/streamingLocators/write | Crea o actualiza cualquier localizador de streaming |
> | . | Microsoft.Media/mediaservices/streamingPolicies/delete | Elimina cualquier directiva de streaming |
> | . | Microsoft.Media/mediaservices/streamingPolicies/read | Lee cualquier directiva de streaming |
> | . | Microsoft.Media/mediaservices/streamingPolicies/write | Crea o actualiza cualquier directiva de streaming |
> | . | Microsoft.Media/mediaservices/syncStorageKeys/action | Sincroniza las claves de almacenamiento de una cuenta de Azure Storage adjunta. |
> | . | Microsoft.Media/mediaservices/transforms/delete | Elimina cualquier transformación |
> | . | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Cancela un trabajo |
> | . | Microsoft.Media/mediaservices/transforms/jobs/delete | Elimina cualquier trabajo |
> | . | Microsoft.Media/mediaservices/transforms/jobs/read | Lee cualquier trabajo |
> | . | Microsoft.Media/mediaservices/transforms/jobs/write | Crea o actualiza cualquier trabajo |
> | . | Microsoft.Media/mediaservices/transforms/read | Lee cualquier transformación |
> | . | Microsoft.Media/mediaservices/transforms/write | Crea o actualiza cualquier transformación |
> | . | Microsoft.Media/mediaservices/write | Crea o actualiza cualquier cuenta de Media Services. |
> | . | Microsoft.Media/operations/read | Obtiene las operaciones disponibles |
> | . | Microsoft.Media/register/action | Lee la suscripción del proveedor de recursos de Media Services y habilita la creación de cuentas de Media Services. |
> | . | Microsoft.Media/unregister/action | Registra la suscripción para el proveedor de recursos de Media Services |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.Migrate/Operations/read | Lee las operaciones expuestas. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Directiva SSL predefinida de Application Gateway. |
> | . | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Opciones SSL disponibles de Application Gateway. |
> | . | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Obtiene los conjuntos de reglas WAF disponibles en Application Gateway |
> | . | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Se une a un grupo de direcciones de back-end de Application Gateway |
> | . | Microsoft.Network/applicationGateways/backendhealth/action | Obtiene el estado de back-end de una instancia de Application Gateway |
> | . | Microsoft.Network/applicationGateways/delete | Elimina una instancia de Application Gateway |
> | . | Microsoft.Network/applicationGateways/effectiveNetworkSecurityGroups/action | Obtiene la tabla de rutas configuradas en Application Gateway. |
> | . | Microsoft.Network/applicationGateways/effectiveRouteTable/action | Obtiene la tabla de rutas configuradas en Application Gateway. |
> | . | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/logDefinitions/read | Obtiene los eventos de puerta de Application Gateway. |
> | . | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de Application Gateway. |
> | . | Microsoft.Network/applicationGateways/read | Obtiene una instancia de Application Gateway |
> | . | Microsoft.Network/applicationGateways/setSecurityCenterConfiguration/action | Establece la configuración del centro de seguridad de Application Gateway. |
> | . | Microsoft.Network/applicationGateways/start/action | Inicia una instancia de Application Gateway |
> | . | Microsoft.Network/applicationGateways/stop/action | Detiene una instancia de Application Gateway |
> | . | Microsoft.Network/applicationGateways/write | Crea una instancia de Application Gateway o actualiza una que ya existe |
> | . | Microsoft.Network/applicationSecurityGroups/delete | Elimina un grupo de seguridad de aplicaciones. |
> | . | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Combina la configuración IP a los grupos de seguridad de aplicaciones. |
> | . | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Combina una regla de seguridad a los grupos de seguridad de aplicaciones. |
> | . | Microsoft.Network/applicationSecurityGroups/read | Obtiene el id. de un grupo de seguridad de aplicaciones. |
> | . | Microsoft.Network/applicationSecurityGroups/write | Crea un grupo de seguridad de aplicaciones o actualiza un grupo de seguridad de aplicaciones ya existente. |
> | . | Microsoft.Network/bgpServiceCommunities/read | Obtiene las comunidades de servicio de BGP |
> | . | Microsoft.Network/checkTrafficManagerNameAvailability/action | Comprueba la disponibilidad de un nombre DNS relativo de Traffic Manager. |
> | . | Microsoft.Network/connections/delete | Elimina VirtualNetworkGatewayConnection |
> | . | Microsoft.Network/connections/read | Obtiene VirtualNetworkGatewayConnection |
> | . | Microsoft.Network/connections/sharedkey/action | Obtiene el valor de SharedKey de VirtualNetworkGatewayConnection. |
> | . | Microsoft.Network/connections/sharedKey/read | Obtiene el valor de SharedKey de VirtualNetworkGatewayConnection |
> | . | Microsoft.Network/connections/sharedKey/write | Crea o actualiza una SharedKey de VirtualNetworkGatewayConnection existente |
> | . | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Obtiene la configuración del dispositivo VPN de VirtualNetworkGatewayConnection. |
> | . | Microsoft.Network/connections/write | Crea o actualiza una VirtualNetworkGatewayConnection existente |
> | . | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/delete | Elimina un proxy del plan DDoS Protection. |
> | . | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/read | Obtiene una definición del proxy del plan DDoS Protection. |
> | . | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/write | Crea un proxy del plan DDoS Protection o actualiza un proxy del plan DDoS Protection ya existente. |
> | . | Microsoft.Network/ddosProtectionPlans/delete | Elimina un plan DDoS Protection. |
> | . | Microsoft.Network/ddosProtectionPlans/join/action | Combina un plan DDoS Protection. |
> | . | Microsoft.Network/ddosProtectionPlans/read | Obtiene un plan DDoS Protection. |
> | . | Microsoft.Network/ddosProtectionPlans/write | Crea un plan DDoS Protection o actualiza un plan DDoS Protection.  |
> | . | Microsoft.Network/dnsoperationresults/read | Obtiene los resultados de una operación DNS |
> | . | Microsoft.Network/dnsoperationstatuses/read | Obtiene el estado de una operación DNS  |
> | . | Microsoft.Network/dnszones/A/delete | Quita el conjunto de registros de un determinado nombre y del tipo "A" de una zona DNS. |
> | . | Microsoft.Network/dnszones/A/read | Obtiene el conjunto de registros del tipo "A", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag. |
> | . | Microsoft.Network/dnszones/A/write | Crea o actualiza un conjunto de registros del tipo "A" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros. |
> | . | Microsoft.Network/dnszones/AAAA/delete | Quita el conjunto de registros de un determinado nombre y del tipo "AAAA" de una zona DNS. |
> | . | Microsoft.Network/dnszones/AAAA/read | Obtiene el conjunto de registros del tipo "AAAA", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag. |
> | . | Microsoft.Network/dnszones/AAAA/write | Crea o actualiza un conjunto de registros del tipo "AAAA" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros. |
> | . | Microsoft.Network/dnszones/all/read | Obtiene los conjuntos de registros de DNS entre los tipos |
> | . | Microsoft.Network/dnszones/CAA/delete | Quita el conjunto de registros de un determinado nombre y del tipo "CAA" de una zona DNS. |
> | . | Microsoft.Network/dnszones/CAA/read | Obtiene el conjunto de registros del tipo "CAA", en formato JSON. El conjunto de registros contiene el TTL, las etiquetas y ETag. |
> | . | Microsoft.Network/dnszones/CAA/write | Crea o actualiza un conjunto de registros del tipo "CAA" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros. |
> | . | Microsoft.Network/dnszones/CNAME/delete | Quita el conjunto de registros de un determinado nombre y del tipo "CNAME" de una zona DNS. |
> | . | Microsoft.Network/dnszones/CNAME/read | Obtiene el conjunto de registros del tipo "CNAME", en formato JSON. El conjunto de registros contiene el TTL, las etiquetas y ETag. |
> | . | Microsoft.Network/dnszones/CNAME/write | Crea o actualiza un conjunto de registros del tipo "CNAME" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros. |
> | . | Microsoft.Network/dnszones/delete | Elimina la zona DNS, en formato JSON. Entre las propiedades de la zona se incluyen tags, etag, numberOfRecordSets y maxNumberOfRecordSets. |
> | . | Microsoft.Network/dnszones/MX/delete | Quita el conjunto de registros de un determinado nombre y del tipo "MX" de una zona DNS. |
> | . | Microsoft.Network/dnszones/MX/read | Obtiene el conjunto de registros del tipo "MX", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag. |
> | . | Microsoft.Network/dnszones/MX/write | Crea o actualiza un conjunto de registros del tipo "MX" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros. |
> | . | Microsoft.Network/dnszones/NS/delete | Elimina el conjunto de registros de DNS del tipo NS |
> | . | Microsoft.Network/dnszones/NS/read | Obtiene el conjunto de registros de DNS del tipo NS |
> | . | Microsoft.Network/dnszones/NS/write | Crea o actualiza el conjunto de registros de DNS del tipo NS |
> | . | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico de la zona DNS. |
> | . | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de la zona DNS. |
> | . | Microsoft.Network/dnszones/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las definiciones de métrica de la zona DNS. |
> | . | Microsoft.Network/dnszones/PTR/delete | Quita el conjunto de registros de un determinado nombre y del tipo "PTR" de una zona DNS. |
> | . | Microsoft.Network/dnszones/PTR/read | Obtiene el conjunto de registros del tipo "PTR", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag. |
> | . | Microsoft.Network/dnszones/PTR/write | Crea o actualiza un conjunto de registros del tipo "PTR" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros. |
> | . | Microsoft.Network/dnszones/read | Obtiene la zona DNS, en formato JSON. Entre las propiedades de la zona se incluyen tags, etag, numberOfRecordSets y maxNumberOfRecordSets. Tenga en cuenta que este comando no recupera los conjuntos de registros dentro de la zona. |
> | . | Microsoft.Network/dnszones/recordsets/read | Obtiene los conjuntos de registros de DNS entre los tipos |
> | . | Microsoft.Network/dnszones/SOA/read | Obtiene el conjunto de registros de DNS del tipo SOA |
> | . | Microsoft.Network/dnszones/SOA/write | Crea o actualiza el conjunto de registros de DNS del tipo SOA |
> | . | Microsoft.Network/dnszones/SRV/delete | Quita el conjunto de registros de un determinado nombre y del tipo "SRV" de una zona DNS. |
> | . | Microsoft.Network/dnszones/SRV/read | Obtiene el conjunto de registros del tipo "SRV", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag. |
> | . | Microsoft.Network/dnszones/SRV/write | Crea o actualiza el conjunto de registros del tipo SRV |
> | . | Microsoft.Network/dnszones/TXT/delete | Quita el conjunto de registros de un determinado nombre y del tipo "TXT" de una zona DNS. |
> | . | Microsoft.Network/dnszones/TXT/read | Obtiene el conjunto de registros del tipo "TXT", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag. |
> | . | Microsoft.Network/dnszones/TXT/write | Crea o actualiza un conjunto de registros del tipo "TXT" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros. |
> | . | Microsoft.Network/dnszones/write | Crea o actualiza una zona DNS en un grupo de recursos.  Se utiliza para actualizar las etiquetas de un recurso de zona DNS. Tenga en cuenta que este comando no se puede usar para crear o actualizar conjuntos de registros dentro de la zona. |
> | . | Microsoft.Network/expressRouteCircuits/authorizations/delete | Elimina una autorización de ExpressRouteCircuit |
> | . | Microsoft.Network/expressRouteCircuits/authorizations/read | Obtiene una autorización de ExpressRouteCircuit |
> | . | Microsoft.Network/expressRouteCircuits/authorizations/write | Crea o actualiza una autorización de ExpressRouteCircuit existente |
> | . | Microsoft.Network/expressRouteCircuits/delete | Elimina un ExpressRouteCircuit |
> | . | Microsoft.Network/expressRouteCircuits/join/action | Combina un circuito Express Route |
> | . | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Obtiene un ArpTable de emparejamiento de ExpressRouteCircuit |
> | . | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Elimina una conexión de ExpressRouteCircuit. |
> | . | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Obtiene una conexión de ExpressRouteCircuit. |
> | . | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Crea o actualiza un recurso de conexión de ExpressRouteCircuit existente. |
> | . | Microsoft.Network/expressRouteCircuits/peerings/delete | Elimina un emparejamiento de ExpressRouteCircuit |
> | . | Microsoft.Network/expressRouteCircuits/peerings/read | Obtiene un emparejamiento de ExpressRouteCircuit |
> | . | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Obtiene un RouteTable de emparejamiento de ExpressRouteCircuit |
> | . | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Obtiene un resumen de RouteTable de emparejamiento de ExpressRouteCircuit |
> | . | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Obtiene estadísticas de emparejamiento de ExpressRouteCircuit |
> | . | Microsoft.Network/expressRouteCircuits/peerings/write | Crea o actualiza un emparejamiento de ExpressRouteCircuit existente |
> | . | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico de los circuitos de ExpressRoute. |
> | . | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de los circuitos de ExpressRoute. |
> | . | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read | Obtiene los eventos de los circuitos ExpressRoute. |
> | . | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las definiciones de métrica de los circuitos ExpressRoute. |
> | . | Microsoft.Network/expressRouteCircuits/read | Obtiene un ExpressRouteCircuit |
> | . | Microsoft.Network/expressRouteCircuits/stats/read | Obtiene un estado de ExpressRouteCircuit |
> | . | Microsoft.Network/expressRouteCircuits/write | Crea o actualiza un ExpressRouteCircuit existente |
> | . | Microsoft.Network/expressRouteCrossConnections/delete | Elimina la conexión cruzada de Express Route. |
> | . | Microsoft.Network/expressRouteCrossConnections/join/action | Combina una conexión cruzada de Express Route. |
> | . | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Obtiene una la tabla ARP de la conexión cruzada de Express Route. |
> | . | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Elimina el emparejamiento de la conexión cruzada de Express Route. |
> | . | Microsoft.Network/expressRouteCrossConnections/peerings/read | Obtiene un emparejamiento de la conexión cruzada de Express Route. |
> | . | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Obtiene la tabla de rutas del emparejamiento de la conexión cruzada de Express Route. |
> | . | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Obtiene el resumen de la tabla de ruta del emparejamiento de la conexión cruzada de Express Route. |
> | . | Microsoft.Network/expressRouteCrossConnections/peerings/write | Crea un emparejamiento de la conexión cruzada de Express Route o actualiza un emparejamiento de la conexión cruzada de Express Route ya existente. |
> | . | Microsoft.Network/expressRouteCrossConnections/read | Obtiene la conexión cruzada de Express Route. |
> | . | Microsoft.Network/expressRouteCrossConnections/write | Crea o actualiza la conexión cruzada de Express Route. |
> | . | Microsoft.Network/expressRouteServiceProviders/read | Obtiene los proveedores de servicios de ExpressRoute |
> | . | Microsoft.Network/intendedPolicies/delete | Elimina una directiva deseada |
> | . | Microsoft.Network/intendedPolicies/read | Obtiene una descripción de la directiva deseada |
> | . | Microsoft.Network/intendedPolicies/write | Crea una directiva deseada o actualiza una directiva deseada existente |
> | . | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Se une a un grupo de direcciones de back-end del equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/backendAddressPools/read | Obtiene una definición de grupo de direcciones de back-end del equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/delete | Elimina un equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Obtiene una definición de configuración de dirección IP de front-end del equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Se une a conjuntos NAT de entrada del equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/inboundNatPools/read | Obtiene una definición de conjuntos NAT de entrada del equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/inboundNatRules/delete | Elimina una regla NAT de entrada del equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Se une a una regla NAT de entrada del equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/inboundNatRules/read | Obtiene una definición de reglas NAT de entrada del equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/inboundNatRules/write | Crea una regla NAT de entrada del equilibrador de carga o actualiza una que ya existe |
> | . | Microsoft.Network/loadBalancers/loadBalancingRules/read | Obtiene una definición de regla de equilibrado de carga del equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/networkInterfaces/read | Obtiene referencias a todas las interfaces de red en un equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/outboundNatRules/read | Obtiene una definición de reglas NAT de salida del equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/probes/join/action | Permite usar sondeos de un equilibrador de carga. Por ejemplo, con este permiso, la propiedad healthProbe de un conjunto de escalado de máquinas virtuales puede hacer referencia al sondeo. |
> | . | Microsoft.Network/loadBalancers/probes/read | Obtiene un sondeo del equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico de Load Balancer. |
> | . | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de Load Balancer. |
> | . | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/logDefinitions/read | Obtiene los eventos de Load Balancer. |
> | . | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de Load Balancer. |
> | . | Microsoft.Network/loadBalancers/read | Obtiene una definición del equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/virtualMachines/read | Obtiene referencias a todas las máquinas virtuales de un equilibrador de carga |
> | . | Microsoft.Network/loadBalancers/write | Crea un equilibrador de carga o actualiza uno que ya existe |
> | . | Microsoft.Network/localnetworkgateways/delete | Elimina LocalNetworkGateway |
> | . | Microsoft.Network/localnetworkgateways/read | Obtiene LocalNetworkGateway |
> | . | Microsoft.Network/localnetworkgateways/write | Crea o actualiza una LocalNetworkGateway existente |
> | . | Microsoft.Network/locations/availableDelegations/read | Obtiene las delegaciones disponibles |
> | . | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Comprueba el soporte de redes aceleradas |
> | . | Microsoft.Network/locations/checkDnsNameAvailability/read | Comprueba si la etiqueta DNS está disponible en la ubicación especificada |
> | . | Microsoft.Network/locations/operationResults/read | Obtiene el resultado de una operación POST o DELETE asincrónica |
> | . | Microsoft.Network/locations/operations/read | Obtiene el recurso de operaciones que representa el estado de una operación asincrónica |
> | . | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Obtiene tamaños de máquinas virtuales admitidas |
> | . | Microsoft.Network/locations/usages/read | Obtiene las métricas de uso de los recursos |
> | . | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Obtiene una lista de los servicios de punto de conexión disponibles de Virtual Network. |
> | . | Microsoft.Network/networkInterfaces/delete | Elimina una interfaz de red |
> | . | Microsoft.Network/networkInterfaces/diagnosticIdentity/read | Obtiene la identidad de diagnóstico del recurso. |
> | . | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Obtiene los grupos de seguridad de red configurados en la interfaz de red de la máquina virtual |
> | . | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Obtiene la tabla de rutas configuradas en la interfaz de red de la máquina virtual |
> | . | Microsoft.Network/networkInterfaces/ipconfigurations/read | Obtiene una definición de configuración de dirección IP de la interfaz de red.  |
> | . | Microsoft.Network/networkInterfaces/join/action | Une una máquina virtual a una interfaz de red |
> | . | Microsoft.Network/networkInterfaces/joinViaPrivateIp/action | Combina un recurso con una interfaz de red a través de una asociación de servicio |
> | . | Microsoft.Network/networkInterfaces/loadBalancers/read | Obtiene todos los equilibradores de carga de los que forma parte la interfaz de red |
> | . | Microsoft.Network/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de la interfaz de red. |
> | . | Microsoft.Network/networkInterfaces/read | Obtiene una definición de interfaz de red.  |
> | . | Microsoft.Network/networkInterfaces/serviceAssociations/delete | Elimina una asociación de servicio |
> | . | Microsoft.Network/networkInterfaces/serviceAssociations/read | Obtiene la definición de una asociación de servicio |
> | . | Microsoft.Network/networkInterfaces/serviceAssociations/validate/action | Valida una asociación de servicio |
> | . | Microsoft.Network/networkInterfaces/serviceAssociations/write | Crea una nueva asociación de servicio o modifica una asociación de servicio existente |
> | . | Microsoft.Network/networkInterfaces/write | Crea una interfaz de red o actualiza una interfaz de red existente.  |
> | . | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Obtiene una definición de regla de seguridad predeterminada |
> | . | Microsoft.Network/networkSecurityGroups/delete | Elimina un grupo de seguridad de red |
> | . | Microsoft.Network/networkSecurityGroups/join/action | Se une a un grupo de seguridad de red |
> | . | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico de los grupos de seguridad de red. |
> | . | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de los grupos de seguridad de red; esta operación se complementa con el proveedor de recursos de Insights. |
> | . | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Obtiene los eventos del grupo de seguridad de red. |
> | . | Microsoft.Network/networkSecurityGroups/read | Obtiene una definición de grupo de seguridad de red |
> | . | Microsoft.Network/networkSecurityGroups/securityRules/delete | Elimina una regla de seguridad |
> | . | Microsoft.Network/networkSecurityGroups/securityRules/read | Obtiene una definición de regla de seguridad |
> | . | Microsoft.Network/networkSecurityGroups/securityRules/write | Crea una regla de seguridad o actualiza una que ya existe |
> | . | Microsoft.Network/networkSecurityGroups/write | Crea un grupo de seguridad de red o actualiza uno que ya existe |
> | . | Microsoft.Network/networkWatchers/availableProvidersList/action | Devuelve todos los proveedores de servicios de Internet disponibles para una región de Azure específica. |
> | . | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Devuelve la puntuación de la latencia relativa de los proveedores de servicios de Internet desde una ubicación específica a las regiones de Azure. |
> | . | Microsoft.Network/networkWatchers/configureFlowLog/action | Configura el registro de flujos para un recurso de destino. |
> | . | Microsoft.Network/networkWatchers/connectionMonitors/delete | Elimina un monitor de conexión. |
> | . | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del monitor de conexión. |
> | . | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del monitor de conexión. |
> | . | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles del monitor de conexión. |
> | . | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Consulta la conectividad de supervisión entre los puntos de conexión especificados. |
> | . | Microsoft.Network/networkWatchers/connectionMonitors/read | Obtiene los detalles del monitor de conexión. |
> | . | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Comienza la conectividad de supervisión entre los puntos de conexión especificados. |
> | . | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Detiene o pausa la conectividad de supervisión entre los puntos de conexión especificados. |
> | . | Microsoft.Network/networkWatchers/connectionMonitors/write | Crea un monitor de conexión. |
> | . | Microsoft.Network/networkWatchers/connectivityCheck/action | Comprueba si se puede establecer una conexión TCP directa desde una máquina virtual a un punto de conexión determinado incluyendo otra máquina virtual o un servidor remoto arbitrario. |
> | . | Microsoft.Network/networkWatchers/delete | Elimina una instancia de Network Watcher |
> | . | Microsoft.Network/networkWatchers/ipFlowVerify/action | Devuelve un mensaje indicando si se permite o deniega el paquete en o desde un destino determinado. |
> | . | Microsoft.Network/networkWatchers/lenses/delete | Elimina una lente. |
> | . | Microsoft.Network/networkWatchers/lenses/query/action | Consulta el tráfico de red de supervisión en un punto de conexión especificado. |
> | . | Microsoft.Network/networkWatchers/lenses/read | Obtiene los detalles de la lente. |
> | . | Microsoft.Network/networkWatchers/lenses/start/action | Inicia el tráfico de red de supervisión en un punto de conexión especificado. |
> | . | Microsoft.Network/networkWatchers/lenses/stop/action | Detiene o pausa el tráfico de red de supervisión en un punto de conexión especificado. |
> | . | Microsoft.Network/networkWatchers/lenses/write | Crea una lente. |
> | . | Microsoft.Network/networkWatchers/nextHop/action | Para un destino y una dirección IP de destino especificados, devuelve el tipo y la dirección IP del próximo salto. |
> | . | Microsoft.Network/networkWatchers/packetCaptures/delete | Elimina una captura de paquetes |
> | . | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Obtiene información acerca de las propiedades y el estado de un recurso de captura de paquetes. |
> | . | Microsoft.Network/networkWatchers/packetCaptures/read | Obtiene la definición de captura de paquetes |
> | . | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Detiene la sesión de captura de paquetes en ejecución. |
> | . | Microsoft.Network/networkWatchers/packetCaptures/write | Crea una captura de paquetes |
> | . | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Obtiene el estado del flujo de registros de un recurso. |
> | . | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Obtiene el resultado de la operación de solución de problemas ejecutada anteriormente o de la que está actualmente en ejecución. |
> | . | Microsoft.Network/networkWatchers/read | Obtiene la definición de Network Watcher |
> | . | Microsoft.Network/networkWatchers/securityGroupView/action | Visualiza las reglas de grupos de seguridad de red configuradas y eficaces aplicadas a una máquina virtual. |
> | . | Microsoft.Network/networkWatchers/topology/action | Obtiene una vista de nivel de red de los recursos y sus relaciones en un grupo de recursos. |
> | . | Microsoft.Network/networkWatchers/troubleshoot/action | Inicia la solución de problemas en un recurso de redes de Azure. |
> | . | Microsoft.Network/networkWatchers/write | Crea una instancia de Network Watcher o actualiza una que ya existe |
> | . | Microsoft.Network/operations/read | Obtiene las operaciones disponibles |
> | . | Microsoft.Network/publicIPAddresses/delete | Elimina una dirección IP pública. |
> | . | Microsoft.Network/publicIPAddresses/join/action | Se une a una dirección IP pública |
> | . | Microsoft.Network/publicIPAddresses/loadBalancerPools/delete | Elimina un grupo de back-end del equilibrador de carga de la dirección IP pública |
> | . | Microsoft.Network/publicIPAddresses/loadBalancerPools/join/action | Combina un grupo de back-end del equilibrador de carga de la dirección IP pública |
> | . | Microsoft.Network/publicIPAddresses/loadBalancerPools/read | Obtiene la definición de un grupo de back-end del equilibrador de carga de la dirección IP pública |
> | . | Microsoft.Network/publicIPAddresses/loadBalancerPools/write | Crea un grupo de back-end del equilibrador de carga de la dirección IP pública o actualiza un grupo de back-end del equilibrador de carga de la dirección IP pública existente |
> | . | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico de la dirección IP pública. |
> | . | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de la dirección IP pública. |
> | . | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read | Obtiene las definiciones de registro de la dirección IP pública. |
> | . | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las definiciones de las métricas de la dirección IP pública. |
> | . | Microsoft.Network/publicIPAddresses/read | Obtiene una definición de la dirección ip pública. |
> | . | Microsoft.Network/publicIPAddresses/write | Crea una dirección Ip pública o actualiza una que ya existe.  |
> | . | Microsoft.Network/register/action | Registra la suscripción |
> | . | Microsoft.Network/routeFilters/delete | Elimina una definición de filtro de rutas |
> | . | Microsoft.Network/routeFilters/join/action | Se une a un filtro de ruta |
> | . | Microsoft.Network/routeFilters/read | Obtiene una definición de filtro de rutas |
> | . | Microsoft.Network/routeFilters/routeFilterRules/delete | Elimina una definición de regla de filtro de rutas |
> | . | Microsoft.Network/routeFilters/routeFilterRules/read | Obtiene una definición de regla de filtro de rutas |
> | . | Microsoft.Network/routeFilters/routeFilterRules/write | Crea una regla de filtro de rutas o actualiza una que ya existe |
> | . | Microsoft.Network/routeFilters/write | Crea un filtro de rutas o actualiza uno que ya existe |
> | . | Microsoft.Network/routeTables/delete | Elimina una definición de tabla de rutas |
> | . | Microsoft.Network/routeTables/join/action | Se une a una tabla de rutas |
> | . | Microsoft.Network/routeTables/read | Obtiene una definición de tabla de rutas |
> | . | Microsoft.Network/routeTables/routes/delete | Elimina una definición de rutas |
> | . | Microsoft.Network/routeTables/routes/read | Obtiene una definición de rutas |
> | . | Microsoft.Network/routeTables/routes/write | Crea una ruta o actualiza una que ya existe |
> | . | Microsoft.Network/routeTables/write | Crea una tabla de rutas o actualiza una que ya existe |
> | . | Microsoft.Network/securegateways/applicationRuleCollections/delete | Elimina una colección de reglas de la aplicación de una puerta de enlace segura. |
> | . | Microsoft.Network/securegateways/applicationRuleCollections/read | Recupera una colección de reglas de la aplicación de una puerta de enlace segura. |
> | . | Microsoft.Network/securegateways/applicationRuleCollections/write | Crea o actualiza una colección de reglas de la aplicación de una puerta de enlace segura. |
> | . | Microsoft.Network/securegateways/delete | Elimina la puerta de enlace segura. |
> | . | Microsoft.Network/securegateways/networkRuleCollections/delete | Elimina una colección de reglas de red de una puerta de enlace segura. |
> | . | Microsoft.Network/securegateways/networkRuleCollections/read | Recupera una colección de reglas de red de una puerta de enlace segura determinada. |
> | . | Microsoft.Network/securegateways/networkRuleCollections/write | Crea o actualiza una colección de reglas de red de una puerta de enlace segura. |
> | . | Microsoft.Network/securegateways/read | Obtiene la puerta de enlace segura. |
> | . | Microsoft.Network/securegateways/write | Crea o actualiza una puerta de enlace segura. |
> | . | Microsoft.Network/serviceEndpointPolicies/delete | Elimina una directiva de un punto de conexión de servicio. |
> | . | Microsoft.Network/serviceEndpointPolicies/join/action | Combina una directiva de un punto de conexión de servicio. |
> | . | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Combina una subred con las directivas de punto de conexión de servicio. |
> | . | Microsoft.Network/serviceEndpointPolicies/read | Obtiene una descripción de la directiva de un punto de conexión de servicio. |
> | . | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Elimina una definición de la directiva de un punto de conexión de servicio. |
> | . | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Obtiene el descifrado de una definición de la directiva de un punto de conexión de servicio. |
> | . | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Crea una definición de la directiva de un punto de conexión de servicio o actualiza una definición de la directiva de un punto de conexión de servicio existente. |
> | . | Microsoft.Network/serviceEndpointPolicies/write | Crea una directiva de un punto de conexión de servicio o actualiza una directiva de un punto de conexión de servicio existente. |
> | . | Microsoft.Network/trafficManagerGeographicHierarchies/read | Obtiene la jerarquía geográfica de Traffic Manager que contiene las regiones que se pueden usar con el método de enrutamiento del tráfico geográfico |
> | . | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Elimina un punto de conexión de Azure de un perfil de Traffic Manager existente. Traffic Manager dejará de enrutar el tráfico al punto de conexión de Azure eliminado. |
> | . | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Obtiene un punto de conexión de Azure que pertenece a un perfil de Traffic Manager, incluidas todas las propiedades de ese punto de conexión de Azure. |
> | . | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Agrega un nuevo punto de conexión de Azure en un perfil de Traffic Manager ya existente o actualiza las propiedades de un punto de conexión de Azure ya existente en dicho perfil de Traffic Manager. |
> | . | Microsoft.Network/trafficManagerProfiles/delete | Elimina el perfil de Traffic Manager. Se perderán todos los valores asociados con el perfil de Traffic Manager y el perfil ya no podrá usarse para enrutar el tráfico. |
> | . | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Elimina un punto de conexión externo de un perfil de Traffic Manager existente. Traffic Manager dejará de enrutar el tráfico al punto de conexión externo eliminado. |
> | . | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Obtiene un punto de conexión externo que pertenece a un perfil de Traffic Manager, incluidas todas las propiedades de ese punto de conexión externo. |
> | . | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Agrega un nuevo punto de conexión externo en un perfil de Traffic Manager ya existente o actualiza las propiedades de un punto de conexión externo ya existente en dicho perfil de Traffic Manager. |
> | . | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Obtiene el mapa térmico de Traffic Manager para el perfil de Traffic Manager determinado que contiene los datos de latencia y los recuentos de consultas según la ubicación y la IP de origen. |
> | . | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Elimina un punto de conexión anidado de un perfil de Traffic Manager existente. Traffic Manager dejará de enrutar el tráfico al punto de conexión anidado eliminado. |
> | . | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Obtiene un punto de conexión anidado que pertenece a un perfil de Traffic Manager, incluidas todas las propiedades de ese punto de conexión anidado. |
> | . | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Agrega un nuevo punto de conexión anidado en un perfil de Traffic Manager ya existente o actualiza las propiedades de un punto de conexión anidado ya existente en dicho perfil de Traffic Manager. |
> | . | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico de Traffic Manager. |
> | . | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de Traffic Manager; esta operación se complementa con el proveedor de recursos de Insights. |
> | . | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read | Obtiene los eventos de Traffic Manager. |
> | . | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de Traffic Manager. |
> | . | Microsoft.Network/trafficManagerProfiles/read | Obtiene la configuración del perfil de Traffic Manager. Esto incluye la configuración de DNS, la de enrutamiento de tráfico, la de supervisión de puntos de conexión y la lista de puntos de conexión que este perfil de Traffic Manager enruta. |
> | . | Microsoft.Network/trafficManagerProfiles/write | Crea un perfil de Traffic Manager, o modifica la configuración de un perfil de Traffic Manager que ya existe.<br>Esto incluye la habilitación o deshabilitación de un perfil y la modificación de la configuración de DNS, la de enrutamiento de tráfico o la de supervisión de puntos de conexión.<br>Los puntos de conexión que enruta el perfil de Traffic Manager se pueden agregar, quitar, habilitar o deshabilitar. |
> | . | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Elimina la clave de nivel de suscripción que se usa para recopilar métricas del usuario en tiempo real. |
> | . | Microsoft.Network/trafficManagerUserMetricsKeys/read | Obtiene la clave de nivel de suscripción que se usa para recopilar métricas del usuario en tiempo real. |
> | . | Microsoft.Network/trafficManagerUserMetricsKeys/write | Crea una clave de nivel de suscripción que se usará para recopilar métricas del usuario en tiempo real. |
> | . | Microsoft.Network/unregister/action | Anula el registro de la suscripción |
> | . | Microsoft.Network/virtualHubs/delete | Elimina un concentrador virtual. |
> | . | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Elimina un elemento HubVirtualNetworkConnection. |
> | . | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Obtiene un elemento HubVirtualNetworkConnection. |
> | . | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Crea o actualiza un elemento HubVirtualNetworkConnection. |
> | . | Microsoft.Network/virtualHubs/read | Obtiene un concentrador virtual. |
> | . | Microsoft.Network/virtualHubs/write | Crea o actualiza un concentrador virtual. |
> | . | microsoft.network/virtualnetworkgateways/connections/read | Obtiene un elemento VirtualNetworkGatewayConnection. |
> | . | Microsoft.Network/virtualNetworkGateways/delete | Elimina un elemento virtualNetworkGateway. |
> | . | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Genera un paquete VpnClient para virtualNetworkGateway. |
> | . | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Genera un paquete VpnProfile para VirtualNetworkGateway. |
> | . | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Obtiene las rutas que anunció virtualNetworkGateway. |
> | . | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Obtiene el estado del mismo nivel BGP de virtualNetworkGateway. |
> | . | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Obtiene las rutas aprendidas de virtualNetworkGateway. |
> | . | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Obtiene los parámetros Ipsec de Vpnclient del cliente P2S de VirtualNetworkGateway. |
> | . | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Obtiene la dirección URL de un paquete de perfil de cliente de VPN generado previamente. |
> | . | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico de la puerta de enlace de red virtual. |
> | . | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de la puerta de enlace de red virtual; esta operación se complementa con el proveedor de recursos de Insights. |
> | . | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read | Obtiene los eventos de la puerta de enlace de red virtual. |
> | . | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de la puerta de enlace de red virtual. |
> | . | Microsoft.Network/virtualNetworkGateways/read | Obtiene un elemento virtualNetworkGateway. |
> | . | microsoft.network/virtualnetworkgateways/reset/action | Restablece un elemento virtualNetworkGateway. |
> | . | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Establece los parámetros Ipsec de Vpnclient del cliente P2S de VirtualNetworkGateway. |
> | . | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Enumera los dispositivos VPN admitidos. |
> | . | Microsoft.Network/virtualNetworkGateways/write | Crea o actualiza un elemento VirtualNetworkGateway. |
> | . | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Comprueba si la dirección IP está disponible en la red virtual especificada |
> | . | Microsoft.Network/virtualNetworks/customViews/get/action | Obtiene el contenido de una vista personalizada de Virtual Network. |
> | . | Microsoft.Network/virtualNetworks/customViews/read | Obtiene la definición de una vista personalizada de Virtual Network. |
> | . | Microsoft.Network/virtualNetworks/delete | Elimina una red virtual |
> | . | Microsoft.Network/virtualNetworks/peer/action | Empareja una red virtual con otra red virtual |
> | . | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico de Virtual Network. |
> | . | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de Virtual Network. |
> | . | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read | Obtiene las definiciones de registro de Virtual Network. |
> | . | Microsoft.Network/virtualNetworks/read | Obtiene la definición de red virtual |
> | . | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Elimina un proxy de emparejamiento de redes virtuales. |
> | . | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Obtiene una definición de un proxy emparejamiento de redes virtuales. |
> | . | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Crea un proxy de emparejamiento de redes virtuales o actualiza uno que ya existe. |
> | . | Microsoft.Network/virtualNetworks/subnets/delete | Elimina una subred de red virtual |
> | . | Microsoft.Network/virtualNetworks/subnets/join/action | Se une a una red virtual |
> | . | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Combina un recurso como una cuenta de almacenamiento o una instancia de SQL Database con una subred. |
> | . | Microsoft.Network/virtualNetworks/subnets/read | Obtiene una definición de subred de red virtual |
> | . | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/delete | Elimina un vínculo de navegación de recursos. |
> | . | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/read | Obtiene la definición del vínculo de navegación de recursos. |
> | . | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/write | Crea un vínculo de navegación de recursos o actualiza uno ya existente. |
> | . | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/delete | Elimina el vínculo de una asociación de servicio |
> | . | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/details/read | Obtiene la definición del detalle del vínculo de una asociación de servicio |
> | . | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/read | Obtiene la definición de un vínculo de una asociación de servicio |
> | . | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/validate/action | Valida un vínculo de una asociación de servicio |
> | . | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/write | Crea un vínculo de una asociación de servicio o actualiza un vínculo de una asociación de servicio existente |
> | . | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Obtiene referencias a todas las máquinas virtuales de una subred de red virtual |
> | . | Microsoft.Network/virtualNetworks/subnets/write | Crea una subred de red virtual o actualiza una que ya existe |
> | . | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/delete | Elimina un consumidor de tráfico etiquetado. |
> | . | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/read | Obtiene la definición del consumidor de tráfico etiquetado. |
> | . | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/validate/action | Valida un consumidor de tráfico etiquetado. |
> | . | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/write | Crea un consumidor de tráfico etiquetado o actualiza uno ya existente. |
> | . | Microsoft.Network/virtualNetworks/usages/read | Obtiene los usos de IP para cada subred de la red virtual. |
> | . | Microsoft.Network/virtualNetworks/virtualMachines/read | Obtiene referencias a todas las máquinas virtuales de una red virtual |
> | . | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Elimina un emparejamiento de redes virtuales |
> | . | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Obtiene una definición de emparejamiento de redes virtuales |
> | . | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Crea un emparejamiento de redes virtuales o actualiza uno que ya existe |
> | . | Microsoft.Network/virtualNetworks/write | Crea una red virtual o actualiza una que ya existe |
> | . | Microsoft.Network/virtualNetworkTaps/delete | Elimina Virtual Network Tap. |
> | . | Microsoft.Network/virtualNetworkTaps/join/action | Combina Virtual Network Tap. |
> | . | Microsoft.Network/virtualNetworkTaps/read | Obtiene Virtual Network Tap. |
> | . | Microsoft.Network/virtualNetworkTaps/write | Crea o actualiza Virtual Network Tap. |
> | . | Microsoft.Network/virtualWans/delete | Elimina una red WAN virtual. |
> | . | Microsoft.Network/virtualWans/read | Obtiene una red WAN virtual. |
> | . | Microsoft.Network/virtualWans/virtualHubProxies/delete | Elimina un proxy de un concentrador virtual. |
> | . | Microsoft.Network/virtualWans/virtualHubProxies/read | Obtiene la definición de un proxy de concentrador virtual. |
> | . | Microsoft.Network/virtualWans/virtualHubProxies/write | Crea a un proxy de concentrador virtual o actualiza uno ya existente, |
> | . | Microsoft.Network/virtualWans/virtualHubs/read | Obtiene todos los concentradores virtuales que hacen referencia a una red WAN virtual. |
> | . | Microsoft.Network/virtualwans/vpnconfiguration/action | Obtiene una configuración de VPN. |
> | . | Microsoft.Network/virtualWans/vpnSiteProxies/delete | Elimina el proxy de un sitio VPN. |
> | . | Microsoft.Network/virtualWans/vpnSiteProxies/read | Obtiene la definición del proxy de un sitio VPN. |
> | . | Microsoft.Network/virtualWans/vpnSiteProxies/write | Crea el proxy de un sitio VPN o actualiza uno ya existente. |
> | . | Microsoft.Network/virtualWans/vpnSites/read | Obtiene todos los sitios VPN que hacen referencia a una red WAN virtual. |
> | . | Microsoft.Network/virtualWans/write | Crea o actualiza una red WAN virtual. |
> | . | Microsoft.Network/vpnGateways/read | Obtiene un elemento VpnGateway. |
> | . | microsoft.network/vpnGateways/vpnConnections/read | Obtiene un elemento VpnConnection. |
> | . | microsoft.network/vpnGateways/vpnConnections/write | Establece un elemento VpnConnection. |
> | . | Microsoft.Network/vpnGateways/write | Establece un elemento VpnGateway. |
> | . | Microsoft.Network/vpnsites/delete | Elimina el recurso de un sitio VPN. |
> | . | Microsoft.Network/vpnsites/read | Obtiene el recurso de un sitio VPN. |
> | . | Microsoft.Network/vpnsites/write | Crea o actualiza el recurso de un sitio VPN. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Comprueba si un nombre de recurso determinado de espacio de nombres está disponible en el servicio NotificationHub. |
> | . | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Obtiene la lista de descripciones de reglas de autorización de espacios de nombres. |
> | . | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Elimina la regla de autorización del espacio de nombres. No se puede eliminar la regla predeterminada de autorización del espacio de nombres.  |
> | . | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Obtiene la cadena de conexión al espacio de nombres |
> | . | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Obtiene la lista de descripciones de reglas de autorización de espacios de nombres. |
> | . | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Namespace Authorization Rule Regenerate Primary/SecondaryKey especifica la clave que se debe regenerar |
> | . | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Crea reglas de autorización en el nivel del espacio de nombres y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria. |
> | . | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Comprueba si un nombre de NotificationHub determinado está disponible dentro de un espacio de nombres. |
> | . | Microsoft.NotificationHubs/Namespaces/Delete | Elimina el recurso del espacio de nombres |
> | . | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Obtiene la lista de reglas de autorización del Centro de notificaciones |
> | . | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Elimina las reglas de autorización del Centro de notificaciones |
> | . | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Obtiene la cadena de conexión al Centro de notificaciones |
> | . | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Obtiene la lista de reglas de autorización del Centro de notificaciones |
> | . | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Notification Hub Authorization Rule Regenerate Primary/SecondaryKey especifica la clave que se debe regenerar |
> | . | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Crea reglas de autorización del Centro de notificaciones y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria. |
> | . | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Envía una notificación de inserción de prueba. |
> | . | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Elimina un recurso del Centro de notificaciones |
> | . | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Obtiene una lista de descripciones de recursos de métricas del espacio de nombres |
> | . | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Obtiene todas las credenciales PNS del Centro de notificaciones. Esto incluye las credenciales WNS, MPNS, APNS, GCM y Baidu |
> | . | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Obtiene una lista de descripciones de recursos del Centro de notificaciones |
> | . | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Crea un Centro de notificaciones y actualiza sus propiedades. Sus propiedades incluyen principalmente las credenciales PNS. Reglas de autorización y TTL |
> | . | Microsoft.NotificationHubs/Namespaces/read | Obtiene la lista de descripción del recurso del espacio de nombres |
> | . | Microsoft.NotificationHubs/Namespaces/write | Crea un recurso de espacio de nombres y actualiza sus propiedades. Las etiquetas y la capacidad del espacio de nombres son las propiedades que se pueden actualizar. |
> | . | Microsoft.NotificationHubs/register/action | Registra la suscripción para el proveedor de recursos de NotificationHubs y habilita la creación de espacios de nombres y NotificationHubs |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.OperationalInsights/linkTargets/read | Enumera las cuentas existentes que no están asociadas a una suscripción de Azure. Para vincular esta suscripción de Azure a un área de trabajo, use un identificador de cliente devuelto por esta operación en la propiedad correspondiente de la operación Create Workspace. |
> | . | Microsoft.OperationalInsights/register/action | Registra una suscripción en un proveedor de recursos. |
> | . | Microsoft.OperationalInsights/workspaces/analytics/query/action | Realiza búsquedas mediante el nuevo motor. |
> | . | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Obtiene el esquema de búsqueda V2. |
> | . | Microsoft.OperationalInsights/workspaces/api/query/action | Realiza búsquedas mediante el nuevo motor. |
> | . | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Obtiene el esquema de búsqueda V2. |
> | . | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Elimina el ámbito de configuración |
> | . | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Obtiene el ámbito de configuración |
> | . | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Establece el ámbito de configuración |
> | . | Microsoft.OperationalInsights/workspaces/datasources/delete | Elimina los orígenes de datos en un área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/datasources/read | Obtiene los orígenes de datos en un área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/datasources/write | Crea o actualiza los orígenes de datos en un área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/delete | Elimina un área de trabajo. Si el área de trabajo se vinculó a un área de trabajo ya existente en el momento de la creación, no se eliminará el área de trabajo a la que está vinculado. |
> | . | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Genera el registro de certificado para el área de trabajo. Este certificado se usa para conectar Microsoft System Center Operation Manager al área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Deshabilita un Intelligence Pack para un área de trabajo determinado. |
> | . | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Habilita un Intelligence Pack para un área de trabajo determinado. |
> | . | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Enumera todos los Intelligence Packs que están visibles para un área de trabajo determinada y también muestra si el paquete está habilitado o deshabilitado para esa área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Elimina los servicios vinculados en función del área de trabajo establecida. |
> | . | Microsoft.OperationalInsights/workspaces/linkedServices/read | Obtiene los servicios vinculados en función del área de trabajo establecida. |
> | . | Microsoft.OperationalInsights/workspaces/linkedServices/write | Crea o actualiza los servicios vinculados en función del área de trabajo establecida. |
> | . | Microsoft.OperationalInsights/workspaces/listKeys/action | Recupera las claves de lista del área de trabajo. Estas claves se utilizan para conectar los agentes de Microsoft Operational Insights al área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/listKeys/read | Recupera las claves de lista del área de trabajo. Estas claves se utilizan para conectar los agentes de Microsoft Operational Insights al área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/managementGroups/read | Obtiene los nombres y metadatos de los grupos de administración de System Center Operations Manager conectados a esta área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Obtiene las definiciones de métricas en el área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Elimina la configuración de notificaciones del usuario del área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Obtiene la configuración de notificaciones del usuario del área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Establece la configuración de notificaciones del usuario del área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/purge/action | Elimina los datos especificados del área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/read | Obtiene un área de trabajo existente |
> | . | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Elimina una consulta de búsqueda guardada |
> | . | Microsoft.OperationalInsights/workspaces/savedSearches/read | Obtiene una consulta de búsqueda guardada |
> | . | Microsoft.OperationalInsights/workspaces/savedSearches/write | Crea una consulta de búsqueda guardada |
> | . | Microsoft.OperationalInsights/workspaces/schema/read | Obtiene el esquema de búsqueda del área de trabajo.  El esquema de búsqueda incluye los campos expuestos y sus tipos. |
> | . | Microsoft.OperationalInsights/workspaces/search/action | Ejecuta una consulta de búsqueda |
> | . | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Recupera las claves compartidas del área de trabajo. Estas claves se utilizan para conectar los agentes de Microsoft Operational Insights al área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera las claves compartidas del área de trabajo. Estas claves se utilizan para conectar los agentes de Microsoft Operational Insights al área de trabajo. |
> | . | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Elimina una configuración de almacenamiento. Esto impide a Microsoft Operational Insights leer los datos de la cuenta de almacenamiento. |
> | . | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Obtiene una configuración de almacenamiento. |
> | . | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Crea una nueva configuración de almacenamiento. Estas configuraciones se usan para extraer datos de una ubicación de una cuenta de almacenamiento existente. |
> | . | Microsoft.OperationalInsights/workspaces/usages/read | Obtiene los datos de uso de un área de trabajo incluida la cantidad de datos que esta lee. |
> | . | Microsoft.OperationalInsights/workspaces/write | Crea una nueva área de trabajo o vincula a un área de trabajo que ya existe proporcionando el identificador de cliente de esta. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.OperationsManagement/managementAssociations/delete | Elimina la asociación de administración existente. |
> | . | Microsoft.OperationsManagement/managementAssociations/read | Obtiene la asociación de administración existente. |
> | . | Microsoft.OperationsManagement/managementAssociations/write | Crea una nueva asociación de administración. |
> | . | Microsoft.OperationsManagement/managementConfigurations/delete | Elimina la configuración de administración existente. |
> | . | Microsoft.OperationsManagement/managementConfigurations/read | Obtiene la configuración de administración existente. |
> | . | Microsoft.OperationsManagement/managementConfigurations/write | Crea una configuración de administración nueva. |
> | . | Microsoft.OperationsManagement/register/action | Registra una suscripción en un proveedor de recursos. |
> | . | Microsoft.OperationsManagement/solutions/delete | Elimina una solución OMS ya existente |
> | . | Microsoft.OperationsManagement/solutions/read | Obtiene una solución OMS ya existente |
> | . | Microsoft.OperationsManagement/solutions/write | Crea una nueva solución OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.PolicyInsights/policyEvents/queryResults/action | Consulta información acerca de eventos de directivas. |
> | . | Microsoft.PolicyInsights/policyStates/queryResults/action | Consulta información acerca de estados de directivas. |
> | . | Microsoft.PolicyInsights/policyStates/summarize/action | Consulta de información resumen acerca de los estados más recientes de las directivas. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.Portal/dashboards/delete | Quita el panel de la suscripción. |
> | . | Microsoft.Portal/dashboards/read | Lee los paneles de la suscripción. |
> | . | Microsoft.Portal/dashboards/write | Agrega un panel a una suscripción o lo modifica. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.PowerBIDedicated/capacities/checkNameAvailability/action | Comprueba que, dada la capacidad dedicada de Power BI, el nombre sea válido y no esté en uso. |
> | . | Microsoft.PowerBIDedicated/capacities/delete | Elimina la capacidad dedicada de Power BI. |
> | . | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/logDefinitions/read | Obtiene los registros disponibles de las funcionalidades dedicadas de Power BI |
> | . | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de la capacidad dedicada de Power BI. |
> | . | Microsoft.PowerBIDedicated/capacities/read | Recupera la información de la capacidad dedicada de Power BI. |
> | . | Microsoft.PowerBIDedicated/capacities/resume/action | Reanuda la funcionalidad. |
> | . | Microsoft.PowerBIDedicated/capacities/skus/read | Recupera información de la SKU disponible de la capacidad. |
> | . | Microsoft.PowerBIDedicated/capacities/suspend/action | Suspende la capacidad. |
> | . | Microsoft.PowerBIDedicated/capacities/write | Crea o actualiza la capacidad dedicada de Power BI especificada. |
> | . | Microsoft.PowerBIDedicated/locations/operationresults/read | Recupera la información del resultado de la operación especificada. |
> | . | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Recupera la información del estado de la operación especificada. |
> | . | Microsoft.PowerBIDedicated/operations/read | Recupera la información de las operaciones. |
> | . | Microsoft.PowerBIDedicated/register/action | Registra el proveedor de recursos dedicado de Power BI. |
> | . | Microsoft.PowerBIDedicated/skus/read | Recupera la información de las SKU. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp es una operación interna que el servicio usa |
> | . | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp es una operación interna que el servicio usa |
> | . | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | . | Microsoft.RecoveryServices/locations/backupStatus/action | Comprueba el estado de la copia de seguridad de los almacenes de Recovery Services. |
> | . | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Valida las características. |
> | . | Microsoft.RecoveryServices/operations/read | La operación devuelve la lista de operaciones de un proveedor de recursos. |
> | . | Microsoft.RecoveryServices/register/action | Registra la suscripción de un proveedor de recursos determinado. |
> | . | Microsoft.RecoveryServices/Vaults/backupconfig/read | Devuelve la configuración del almacén de Recovery Services. |
> | . | Microsoft.RecoveryServices/Vaults/backupconfig/write | Actualiza la configuración del almacén de Recovery Services. |
> | . | Microsoft.RecoveryServices/Vaults/backupEngines/read | Devuelve todos los servidores de administración de copia de seguridad que se registraron con el almacén. |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Crea una intención de protección de la copia de seguridad. |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Devuelve el estado de la operación |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obtiene todos los contenedores que se pueden proteger. |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Elimina el contenedor registrado |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Realiza consultas para las cargas de trabajo de un contenedor. |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obtiene todos los elementos de un contenedor. |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtiene los resultados de la operación realizada en el contenedor de protección. |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Hace una copia de seguridad del elemento protegido. |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Elimina los elementos protegidos |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtiene el resultado de la operación realizada en los elementos protegidos. |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Devuelve el estado de la operación realizada en los elementos protegidos. |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devuelve detalles de objeto del elemento protegido |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Aprovisiona una recuperación de elementos instantánea para los elementos protegidos |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obtiene los puntos de recuperación de los elementos protegidos. |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaura los puntos de recuperación de los elementos protegidos. |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revoca la recuperación de elementos instantánea para los elementos protegidos |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Crea un elemento protegido de copia de seguridad |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Devuelve todos los contenedores registrados |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Crea un contenedor registrado. |
> | . | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Actualiza la lista de contenedores |
> | . | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Cancela el trabajo |
> | . | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Devuelve el resultado de la operación de trabajo. |
> | . | Microsoft.RecoveryServices/Vaults/backupJobs/read | Devuelve todos los objetos de trabajo |
> | . | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exporta trabajos |
> | . | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Devuelve el resultado de la operación del trabajo de exportación. |
> | . | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Devuelve los metadatos de administración de Backup para el almacén de Recovery Services. |
> | . | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Devuelve el resultado de la operación de Backup para el almacén de Recovery Services. |
> | . | Microsoft.RecoveryServices/Vaults/backupOperations/read | Devuelve el estado de la operación de Backup para el almacén de Recovery Services. |
> | . | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Elimina una directiva de protección |
> | . | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obtiene los resultados de la operación de directiva. |
> | . | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obtiene el estado de la operación de directiva. |
> | . | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Devuelve todas las directivas de protección |
> | . | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Crea una directiva de protección |
> | . | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Devuelve una lista de todos los elementos que se pueden proteger. |
> | . | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Devuelve la lista de todos los elementos protegidos. |
> | . | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Devuelve todos los contenedores que pertenecen a la suscripción |
> | . | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Devuelve la información del PIN de seguridad del almacén de Recovery Services. |
> | . | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Devuelve la configuración de almacenamiento del almacén de Recovery Services. |
> | . | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Actualiza la configuración de almacenamiento del almacén de Recovery Services. |
> | . | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Devuelve resúmenes de los elementos y servidores protegidos para un almacén de Recovery Services. |
> | . | Microsoft.RecoveryServices/Vaults/certificates/write | La operación Actualizar certificado de recursos permite actualizar el certificado de credencial de recursos o almacenes. |
> | . | Microsoft.RecoveryServices/Vaults/delete | La operación Delete Vault elimina el recurso de Azure del tipo "almacén" especificado |
> | . | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén? |
> | . | Microsoft.RecoveryServices/Vaults/extendedInformation/read | La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén? |
> | . | Microsoft.RecoveryServices/Vaults/extendedInformation/write | La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén? |
> | . | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtiene las alertas del almacén de Recovery Services. |
> | . | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resuelve la alerta. |
> | . | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Obtiene la configuración de notificaciones del almacén de Recovery Services. |
> | . | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Configura las notificaciones por correo electrónico para el almacén de servicios de recuperación. |
> | . | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/read | Diagnósticos de Azure Backup. |
> | . | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/write | Diagnósticos de Azure Backup. |
> | . | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/logDefinitions/read | Registros de Azure Backup. |
> | . | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/metricDefinitions/read | Métricas de Azure Backup. |
> | . | Microsoft.RecoveryServices/Vaults/read | La operación Get Vault obtiene un objeto que representa el recurso de Azure del tipo "almacén" |
> | . | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | La operación Anular registro de contenedor se puede usar para eliminar el registro de un contenedor. |
> | . | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | La operación Obtener resultados de la operación se puede usar para obtener el estado y el resultado de la operación enviada de forma asincrónica |
> | . | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | La operación Obtener contenedores se puede usar para obtener los contenedores registrados para un recurso. |
> | . | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | La operación Registrar contenedor de servicios se puede usar para registrar un contenedor con servicio de recuperación. |
> | . | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lee la configuración de todas las alertas |
> | . | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Crea o actualiza la configuración de todas las alertas |
> | . | Microsoft.RecoveryServices/vaults/replicationEvents/read | Lee todos los eventos |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Comprueba la coherencia del tejido |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Elimina todos los tejidos |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Implementa la imagen del servidor de proceso |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Lee todos los tejidos |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Vuelve a asociar la puerta de enlace |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Quita el tejido |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renueva un certificado para Fabric. |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Lee todas las redes |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Elimina todas las asignaciones de redes |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Lee todas las asignaciones de redes |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Crea o actualiza todas las asignaciones de redes |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Detecta los elementos que se pueden proteger |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Lee todos los contenedores de protección |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Elimina los contenedores de protección |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Lee todos los elementos que se pueden proteger |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplica un punto de recuperación |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Elimina todos los elementos protegidos |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Confirma la conmutación por error |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Conmutación por error planeada |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Lee todos los elementos protegidos |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Lee los puntos de recuperación de todas las replicaciones |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Quita los elementos protegidos |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Repara una replicación |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Vuelva a proteger el elemento protegido |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test Failover |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Prueba la limpieza de la conmutación por error |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Conmutación por error |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Actualiza Mobility Service |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Crea o actualiza todos los elementos protegidos |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Elimina todas las asignaciones de los contenedores de protección |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Lee todas las asignaciones de los contenedores de protección |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Quita las asignaciones de los contenedores de protección |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Crea o actualiza todas las asignaciones de los contenedores de protección |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Cambia los contenedores de protección |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Crea o actualiza todos los contenedores de protección |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Elimina todos los proveedores de Recovery Services |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Lee todos los proveedores de Recovery Services |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Actualiza el proveedor |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Quita los proveedores de Recovery Services |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Crea o actualiza los proveedores de Recovery Services. |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Lee todas las clasificaciones de almacenamiento |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Elimina todas las asignaciones de clasificaciones de almacenamiento |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Lee todas las asignaciones de clasificaciones de almacenamiento |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Crea o actualiza todas las asignaciones de clasificaciones de almacenamiento |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Elimina todos los trabajos |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Lee todos los trabajos |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Crea o actualiza todos los trabajos |
> | . | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Crea o actualiza todos los tejidos |
> | . | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Cancela un trabajo |
> | . | Microsoft.RecoveryServices/vaults/replicationJobs/read | Lee todos los trabajos |
> | . | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Reinicia un trabajo |
> | . | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Reanuda un trabajo |
> | . | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Elimina todas las directivas |
> | . | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Lee todas las directivas |
> | . | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Crea o actualiza todas las directivas |
> | . | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Elimina todos los planes de recuperación |
> | . | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plan de recuperación de confirmación de la conmutación por error |
> | . | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plan de recuperación de conmutación por error planeado |
> | . | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Lee todos los planes de recuperación |
> | . | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Vuelve a proteger el plan de recuperación |
> | . | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Prueba el plan de recuperación de conmutación por error |
> | . | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Prueba el plan de recuperación de limpieza de la conmutación por error |
> | . | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan de recuperación de conmutación por error |
> | . | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Crea o actualiza todos los planes de recuperación |
> | . | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Devuelve la información del token del almacén de Recovery Services. |
> | . | Microsoft.RecoveryServices/vaults/usages/read | Lee todos los usos de almacén |
> | . | Microsoft.RecoveryServices/Vaults/usages/read | Devuelve los detalles de uso de un almacén de Recovery Services. |
> | . | Microsoft.RecoveryServices/Vaults/vaultTokens/read | La operación Token de almacén se puede usar para obtener el token de almacén de las operaciones back-end a nivel de almacén. |
> | . | Microsoft.RecoveryServices/Vaults/write | La operación Create Vault crea un recurso de Azure del tipo "almacén" |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Relay/checkNameAvailability/action | Comprueba la disponibilidad del espacio de nombres en una suscripción dada. |
> | . | Microsoft.Relay/checkNamespaceAvailability/action | Comprueba la disponibilidad del espacio de nombres en una suscripción dada. Esta API está en desuso; en su lugar, use CheckNameAvailabiltiy. |
> | . | Microsoft.Relay/namespaces/authorizationRules/action | Actualiza la regla de autorización del espacio de nombres. Esta API está en desuso. Use una llamada PUT para actualizar la regla de autorización del espacio de nombres en su lugar. Esta operación no se admite en la versión de API del 01/04/2017. |
> | . | Microsoft.Relay/namespaces/authorizationRules/delete | Elimina la regla de autorización del espacio de nombres. No se puede eliminar la regla predeterminada de autorización del espacio de nombres.  |
> | . | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Obtiene la cadena de conexión al espacio de nombres |
> | . | Microsoft.Relay/namespaces/authorizationRules/read | Obtiene la lista de descripciones de reglas de autorización de espacios de nombres. |
> | . | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Regenera la clave principal o secundaria del recurso |
> | . | Microsoft.Relay/namespaces/authorizationRules/write | Crea reglas de autorización en el nivel del espacio de nombres y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria. |
> | . | Microsoft.Relay/namespaces/Delete | Elimina el recurso del espacio de nombres |
> | . | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtiene las claves de las reglas de autorización del espacio de nombres principal de recuperación ante desastres. |
> | . | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obtiene las reglas de autorización del espacio de nombres principal de recuperación ante desastres. |
> | . | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Deshabilita la recuperación ante desastres y deja de replicar los cambios de los espacios de nombres principales a los secundarios. |
> | . | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Comprueba la disponibilidad del alias del espacio de nombres en una suscripción dada. |
> | . | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Elimina la configuración de la recuperación ante desastres asociada con el espacio de nombres. Esta operación sólo se puede invocar a través del espacio de nombres principal. |
> | . | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Invoca una conmutación por error de GEO DR y vuelve a configurar el alias del espacio de nombres para que apunte al espacio de nombres secundario. |
> | . | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Obtiene la configuración de la recuperación ante desastres asociada con el espacio de nombres. |
> | . | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Crea o actualiza la configuración de la recuperación ante desastres asociada con el espacio de nombres. |
> | . | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operación para actualizar HybridConnection. Esta operación no se admite en la versión de API del 01/04/2017. Reglas de autorización. Use una llamada PUT para actualizar la regla de autorización. |
> | . | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operación para eliminar las reglas de autorización de HybridConnection |
> | . | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Obtiene la cadena de conexión a HybridConnection |
> | . | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Obtiene la lista de las reglas de autorización de HybridConnection. |
> | . | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Regenera la clave principal o secundaria del recurso |
> | . | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Crea reglas de autorización de HybridConnection y actualiza sus propiedades. Los derechos de acceso de las reglas de autorización se pueden actualizar. |
> | . | Microsoft.Relay/namespaces/HybridConnections/Delete | Operación para eliminar el recurso de HybridConnection |
> | . | Microsoft.Relay/namespaces/HybridConnections/read | Obtiene una lista de descripciones de recursos de HybridConnection |
> | . | Microsoft.Relay/namespaces/HybridConnections/write | Crea o actualiza las propiedades de HybridConnection. |
> | . | Microsoft.Relay/namespaces/messagingPlan/read | Obtiene el plan de mensajería para un espacio de nombres.<br>Esta API está en desuso.<br>Las propiedades expuestas a través del recurso MessagingPlan se mueven al recurso del espacio de nombres (principal) en versiones posteriores de la API.<br>Esta operación no se admite en la versión de API del 01/04/2017. |
> | . | Microsoft.Relay/namespaces/messagingPlan/write | Actualiza el plan mensajería de un espacio de nombres.<br>Esta API está en desuso.<br>Las propiedades expuestas a través del recurso MessagingPlan se mueven al recurso del espacio de nombres (principal) en versiones posteriores de la API.<br>Esta operación no se admite en la versión de API del 01/04/2017. |
> | . | Microsoft.Relay/namespaces/operationresults/read | Obtiene el estado de la operación del espacio de nombres. |
> | . | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obtiene una lista de descripciones de recursos de métricas del espacio de nombres |
> | . | Microsoft.Relay/namespaces/read | Obtiene la lista de descripción del recurso del espacio de nombres |
> | . | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Quita el espacio de nombres de ACS |
> | . | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operación para actualizar WcfRelay. Esta operación no se admite en la versión de API del 01/04/2017. Reglas de autorización. Use una llamada PUT para actualizar la regla de autorización. |
> | . | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operación para eliminar las reglas de autorización de WcfRelay |
> | . | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Obtiene la cadena de conexión a WcfRelay |
> | . | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Obtiene la lista de reglas de autorización de WcfRelay. |
> | . | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Regenera la clave principal o secundaria del recurso |
> | . | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Crea reglas de autorización de WcfRelay y actualiza sus propiedades. Los derechos de acceso de las reglas de autorización se pueden actualizar. |
> | . | Microsoft.Relay/namespaces/WcfRelays/Delete | Operación para eliminar los recursos de WcfRelay |
> | . | Microsoft.Relay/namespaces/WcfRelays/read | Obtiene una lista de descripciones de recursos de WcfRelay |
> | . | Microsoft.Relay/namespaces/WcfRelays/write | Crea o actualiza las propiedades de WcfRelay. |
> | . | Microsoft.Relay/namespaces/write | Crea un recurso de espacio de nombres y actualiza sus propiedades. Las etiquetas y la capacidad del espacio de nombres son las propiedades que se pueden actualizar. |
> | . | Microsoft.Relay/operations/read | Obtiene operaciones. |
> | . | Microsoft.Relay/register/action | Registra la suscripción para el proveedor de recursos de Relay y habilita la creación de recursos de Relay |
> | . | Microsoft.Relay/unregister/action | Registra la suscripción para el proveedor de recursos de Relay y habilita la creación de recursos de Relay |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Obtiene el estado de disponibilidad del recurso especificado |
> | . | Microsoft.ResourceHealth/AvailabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | . | Microsoft.ResourceHealth/events/read | Obtiene los eventos de Service Health de una suscripción determinada |
> | . | Microsoft.Resourcehealth/healthevent/action | Denota el cambio del estado de mantenimiento del recurso especificado. |
> | . | Microsoft.Resourcehealth/healthevent/Activated/action | Denota el cambio del estado de mantenimiento del recurso especificado. |
> | . | Microsoft.Resourcehealth/healthevent/InProgress/action | Denota el cambio del estado de mantenimiento del recurso especificado. |
> | . | Microsoft.Resourcehealth/healthevent/Pending/action | Denota el cambio del estado de mantenimiento del recurso especificado. |
> | . | Microsoft.Resourcehealth/healthevent/Resolved/action | Denota el cambio del estado de mantenimiento del recurso especificado. |
> | . | Microsoft.Resourcehealth/healthevent/Updated/action | Denota el cambio del estado de mantenimiento del recurso especificado. |
> | . | Microsoft.ResourceHealth/impactedResources/read | Obtiene los recursos a los que afecta una suscripción determinada |
> | . | Microsoft.ResourceHealth/register/action | Registra la suscripción para Microsoft Resource Health. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.Resources/checkPolicyCompliance/action | Compruebe el estado de cumplimiento de un recurso determinado en relación con las directivas de recursos. |
> | . | Microsoft.Resources/checkResourceName/action | Comprueba la validez del nombre de recurso. |
> | . | Microsoft.Resources/deployments/cancel/action | Cancela una implementación. |
> | . | Microsoft.Resources/deployments/delete | Elimina una implementación. |
> | . | Microsoft.Resources/deployments/operations/read | Obtiene o enumera las operaciones de implementación. |
> | . | Microsoft.Resources/deployments/read | Obtiene o enumera implementaciones. |
> | . | Microsoft.Resources/deployments/validate/action | Valida una implementación. |
> | . | Microsoft.Resources/deployments/write | Crea o actualiza una implementación. |
> | . | Microsoft.Resources/links/delete | Elimina un vínculo de recursos. |
> | . | Microsoft.Resources/links/read | Obtiene o enumera los vínculos de recursos. |
> | . | Microsoft.Resources/links/write | Crea o actualiza un vínculo de recursos. |
> | . | Microsoft.Resources/marketplace/purchase/action | Compra un recurso de Marketplace. |
> | . | Microsoft.Resources/providers/read | Obtiene la lista de proveedores. |
> | . | Microsoft.Resources/resources/read | Obtiene la lista de recursos en función de los filtros. |
> | . | Microsoft.Resources/subscriptions/locations/read | Obtiene la lista de ubicaciones admitidas. |
> | . | Microsoft.Resources/subscriptions/operationresults/read | Obtiene los resultados de la operación de suscripción. |
> | . | Microsoft.Resources/subscriptions/providers/read | Obtiene o enumera los proveedores de recursos. |
> | . | Microsoft.Resources/subscriptions/read | Obtiene la lista de suscripciones. |
> | . | Microsoft.Resources/subscriptions/resourceGroups/delete | Elimina un grupo de recursos y todos sus recursos. |
> | . | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Obtiene o enumera las operaciones de implementación. |
> | . | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Obtiene o enumera los estados de la operación de implementación. |
> | . | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Obtiene o enumera implementaciones. |
> | . | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Crea o actualiza una implementación. |
> | . | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Mueve recursos de un grupo de recursos a otro. |
> | . | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | . | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Obtiene los recursos del grupo de recursos. |
> | . | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Valida el movimiento de recursos de un grupo de recursos a otro. |
> | . | Microsoft.Resources/subscriptions/resourceGroups/write | Crea o actualiza un grupo de recursos. |
> | . | Microsoft.Resources/subscriptions/resources/read | Obtiene recursos de una suscripción. |
> | . | Microsoft.Resources/subscriptions/tagNames/delete | Elimina una etiqueta de suscripción. |
> | . | Microsoft.Resources/subscriptions/tagNames/read | Obtiene o enumera las etiquetas de suscripción. |
> | . | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Elimina el valor de una etiqueta de suscripción. |
> | . | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Obtiene o enumera los valores de las etiquetas de suscripción. |
> | . | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Agrega un valor de etiqueta de suscripción. |
> | . | Microsoft.Resources/subscriptions/tagNames/write | Agrega una etiqueta de suscripción. |
> | . | Microsoft.Resources/tenants/read | Obtiene la lista de inquilinos. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.Scheduler/jobcollections/delete | Elimina una colección de trabajos. |
> | . | Microsoft.Scheduler/jobcollections/disable/action | Deshabilita una colección de trabajos. |
> | . | Microsoft.Scheduler/jobcollections/enable/action | Habilita una colección de trabajos. |
> | . | Microsoft.Scheduler/jobcollections/jobs/delete | Elimina el trabajo. |
> | . | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Genera una definición de Logic Apps basada en un trabajo de Scheduler. |
> | . | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Obtiene el historial de trabajos. |
> | . | Microsoft.Scheduler/jobcollections/jobs/read | Obtiene un trabajo. |
> | . | Microsoft.Scheduler/jobcollections/jobs/run/action | Ejecuta el trabajo. |
> | . | Microsoft.Scheduler/jobcollections/jobs/write | Crea o actualiza el trabajo. |
> | . | Microsoft.Scheduler/jobcollections/read | Obtiene una colección de trabajos |
> | . | Microsoft.Scheduler/jobcollections/write | Crea o actualiza una colección de trabajos. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.Search/checkNameAvailability/action | Comprueba la disponibilidad del nombre del servicio. |
> | . | Microsoft.Search/operations/read | Enumera todas las operaciones disponibles del proveedor de Microsoft.Search. |
> | . | Microsoft.Search/register/action | Registra la suscripción para el proveedor de recursos de búsqueda y habilita la creación de servicios de búsqueda. |
> | . | Microsoft.Search/searchServices/createQueryKey/action | Crea la clave de consulta. |
> | . | Microsoft.Search/searchServices/delete | Elimina el servicio de búsqueda. |
> | . | Microsoft.Search/searchServices/deleteQueryKey/delete | Elimina las claves de consulta. |
> | . | Microsoft.Search/searchServices/diagnosticSettings/read | Obtiene la lectura de la configuración de diagnóstico del recurso. |
> | . | Microsoft.Search/searchServices/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso. |
> | . | Microsoft.Search/searchServices/listAdminKeys/action | Lee las claves de administración. |
> | . | Microsoft.Search/searchServices/listQueryKeys/read | Devuelve la lista de claves de API de consulta para el servicio Azure Search dado. |
> | . | Microsoft.Search/searchServices/logDefinitions/read | Obtiene los registros disponibles del servicio de búsqueda. |
> | . | Microsoft.Search/searchServices/metricDefinitions/read | Obtiene las métricas disponibles del servicio de búsqueda. |
> | . | Microsoft.Search/searchServices/read | Lee el servicio de búsqueda. |
> | . | Microsoft.Search/searchServices/regenerateAdminKey/action | Regenera la clave de administración. |
> | . | Microsoft.Search/searchServices/start/action | Inicia el servicio de búsqueda. |
> | . | Microsoft.Search/searchServices/stop/action | Detiene el servicio de búsqueda. |
> | . | Microsoft.Search/searchServices/write | Crea o actualiza el servicio de búsqueda. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Security/alerts/read | Obtiene todas las alertas de seguridad disponibles. |
> | . | Microsoft.Security/applicationWhitelistings/read | Obtiene la lista de permitidos de la aplicación |
> | . | Microsoft.Security/applicationWhitelistings/write | Crea una nueva lista de permitidos de la aplicación o actualiza una ya existente |
> | . | Microsoft.Security/complianceResults/read | Obtiene los resultados de cumplimiento del recurso. |
> | . | Microsoft.Security/locations/alerts/activate/action | Activa una alerta de seguridad. |
> | . | Microsoft.Security/locations/alerts/dismiss/action | Descarta una alerta de seguridad. |
> | . | Microsoft.Security/locations/alerts/read | Obtiene todas las alertas de seguridad disponibles. |
> | . | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Inicia una directiva de acceso de red just-in-time |
> | . | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Obtiene las directivas just-in-time de acceso de red |
> | . | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Crea una nueva directiva just-in-time de acceso de red o actualiza una que ya existe |
> | . | Microsoft.Security/locations/read | Obtiene la ubicación de los datos de seguridad. |
> | . | Microsoft.Security/locations/tasks/activate/action | Activa una recomendación de seguridad |
> | . | Microsoft.Security/locations/tasks/dismiss/action | Descarta una recomendación de seguridad |
> | . | Microsoft.Security/locations/tasks/read | Obtiene todas las recomendaciones de seguridad disponibles |
> | . | Microsoft.Security/locations/tasks/resolve/action | Resuelve una recomendación de seguridad. |
> | . | Microsoft.Security/locations/tasks/start/action | Inicia una recomendación de seguridad. |
> | . | Microsoft.Security/policies/read | Obtiene la directiva de seguridad |
> | . | Microsoft.Security/policies/write | Actualiza la directiva de seguridad |
> | . | Microsoft.Security/pricings/delete | Elimina la configuración de precios del ámbito. |
> | . | Microsoft.Security/pricings/read | Obtiene la configuración de precios del ámbito. |
> | . | Microsoft.Security/pricings/write | Actualiza la configuración de precios del ámbito. |
> | . | Microsoft.Security/register/action | Registra la suscripción de Azure Security Center. |
> | . | Microsoft.Security/securityContacts/delete | Elimina el contacto de seguridad. |
> | . | Microsoft.Security/securityContacts/read | Obtiene el contacto de seguridad. |
> | . | Microsoft.Security/securityContacts/write | Actualiza el contacto de seguridad. |
> | . | Microsoft.Security/securitySolutions/delete | Elimina una solución de seguridad |
> | . | Microsoft.Security/securitySolutions/read | Obtiene las soluciones de seguridad |
> | . | Microsoft.Security/securitySolutions/write | Crea una nueva solución de seguridad o actualiza una ya existente |
> | . | Microsoft.Security/securitySolutionsReferenceData/read | Obtiene los datos de referencia de las soluciones de seguridad |
> | . | Microsoft.Security/securityStatuses/read | Obtiene el estado de seguridad de los recursos de Azure |
> | . | Microsoft.Security/securityStatusesSummaries/read | Obtiene resúmenes de los estados de seguridad del ámbito. |
> | . | Microsoft.Security/tasks/read | Obtiene todas las recomendaciones de seguridad disponibles |
> | . | Microsoft.Security/webApplicationFirewalls/delete | Elimina un firewall de aplicaciones web |
> | . | Microsoft.Security/webApplicationFirewalls/read | Obtiene los firewalls de aplicaciones web |
> | . | Microsoft.Security/webApplicationFirewalls/write | Crea un nuevo firewall de aplicaciones web o actualiza uno que ya existe |
> | . | Microsoft.Security/workspaceSettings/connect/action | Cambia la opción de reconexión de la configuración del área de trabajo. |
> | . | Microsoft.Security/workspaceSettings/delete | Elimina la configuración del área de trabajo. |
> | . | Microsoft.Security/workspaceSettings/read | Obtiene la configuración del área de trabajo. |
> | . | Microsoft.Security/workspaceSettings/write | Actualiza la configuración del área de trabajo. |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.SecurityGraph/diagnosticsettings/delete | Eliminar una configuración de diagnóstico. |
> | . | Microsoft.SecurityGraph/diagnosticsettings/read | Leer una configuración de diagnóstico. |
> | . | Microsoft.SecurityGraph/diagnosticsettings/write | Escribir en una configuración de diagnóstico. |
> | . | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Leer las categorías de la configuración de diagnóstico. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.ServiceBus/checkNameAvailability/action | Comprueba la disponibilidad del espacio de nombres en una suscripción dada. |
> | . | Microsoft.ServiceBus/checkNamespaceAvailability/action | Comprueba la disponibilidad del espacio de nombres en una suscripción dada. Esta API está en desuso; en su lugar, use CheckNameAvailabiltiy. |
> | . | Microsoft.ServiceBus/namespaces/authorizationRules/action | Actualiza la regla de autorización del espacio de nombres. Esta API está en desuso. Use una llamada PUT para actualizar la regla de autorización del espacio de nombres en su lugar. Esta operación no se admite en la versión de API del 01/04/2017. |
> | . | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Elimina la regla de autorización del espacio de nombres. No se puede eliminar la regla predeterminada de autorización del espacio de nombres.  |
> | . | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Obtiene la cadena de conexión al espacio de nombres |
> | . | Microsoft.ServiceBus/namespaces/authorizationRules/read | Obtiene la lista de descripciones de reglas de autorización de espacios de nombres. |
> | . | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Regenera la clave principal o secundaria del recurso |
> | . | Microsoft.ServiceBus/namespaces/authorizationRules/write | Crea reglas de autorización en el nivel del espacio de nombres y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria. |
> | . | Microsoft.ServiceBus/namespaces/Delete | Elimina el recurso del espacio de nombres |
> | . | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtiene las claves de las reglas de autorización del espacio de nombres principal de recuperación ante desastres. |
> | . | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obtiene las reglas de autorización del espacio de nombres principal de recuperación ante desastres. |
> | . | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Deshabilita la recuperación ante desastres y deja de replicar los cambios de los espacios de nombres principales a los secundarios. |
> | . | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Comprueba la disponibilidad del alias del espacio de nombres en una suscripción dada. |
> | . | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Elimina la configuración de la recuperación ante desastres asociada con el espacio de nombres. Esta operación sólo se puede invocar a través del espacio de nombres principal. |
> | . | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Invoca una conmutación por error de GEO DR y vuelve a configurar el alias del espacio de nombres para que apunte al espacio de nombres secundario. |
> | . | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Obtiene la configuración de la recuperación ante desastres asociada con el espacio de nombres. |
> | . | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Crea o actualiza la configuración de la recuperación ante desastres asociada con el espacio de nombres. |
> | . | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Elimina el filtro de Event Grid asociado con el espacio de nombres. |
> | . | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Obtiene el filtro de Event Grid asociado con el espacio de nombres. |
> | . | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Crea o actualiza el filtro de Event Grid asociado con el espacio de nombres. |
> | . | Microsoft.ServiceBus/namespaces/eventhubs/read | Obtiene una lista de descripciones de recursos de EventHub |
> | . | Microsoft.ServiceBus/namespaces/messagingPlan/read | Obtiene el plan de mensajería para un espacio de nombres.<br>Esta API está en desuso.<br>Las propiedades expuestas a través del recurso MessagingPlan se mueven al recurso del espacio de nombres (principal) en versiones posteriores de la API.<br>Esta operación no se admite en la versión de API del 01/04/2017. |
> | . | Microsoft.ServiceBus/namespaces/messagingPlan/write | Actualiza el plan mensajería de un espacio de nombres.<br>Esta API está en desuso.<br>Las propiedades expuestas a través del recurso MessagingPlan se mueven al recurso del espacio de nombres (principal) en versiones posteriores de la API.<br>Esta operación no se admite en la versión de API del 01/04/2017. |
> | . | Microsoft.ServiceBus/namespaces/migrate/action | Operación de migración del espacio de nombres. |
> | . | Microsoft.ServiceBus/namespaces/operationresults/read | Obtiene el estado de la operación del espacio de nombres. |
> | . | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene una lista de descripciones de recursos de configuración de diagnósticos del espacio de nombres |
> | . | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obtiene una lista de descripciones de recursos de configuración de diagnósticos del espacio de nombres |
> | . | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obtiene una lista de descripciones de recursos de registros del espacio de nombres |
> | . | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obtiene una lista de descripciones de recursos de métricas del espacio de nombres |
> | . | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operación para actualizar la cola. Esta operación no se admite en la versión de API del 01/04/2017. Reglas de autorización. Use una llamada PUT para actualizar la regla de autorización. |
> | . | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operación para eliminar las reglas de autorización de Queue |
> | . | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Obtiene la cadena de conexión a Queue |
> | . | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Obtiene la lista de reglas de autorización de Queue |
> | . | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Regenera la clave principal o secundaria del recurso |
> | . | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Crea reglas de autorización de Queue y actualiza sus propiedades. Los derechos de acceso de las reglas de autorización se pueden actualizar. |
> | . | Microsoft.ServiceBus/namespaces/queues/Delete | Operación para eliminar los recursos de Queue |
> | . | Microsoft.ServiceBus/namespaces/queues/read | Obtiene una lista de descripciones de recursos de Queue |
> | . | Microsoft.ServiceBus/namespaces/queues/write | Crea o actualiza las propiedades de Queue. |
> | . | Microsoft.ServiceBus/namespaces/read | Obtiene la lista de descripción del recurso del espacio de nombres |
> | . | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Quita el espacio de nombres de ACS |
> | . | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Operación para actualizar el tema. Esta operación no se admite en la versión de API del 01/04/2017. Reglas de autorización. Use una llamada PUT para actualizar la regla de autorización. |
> | . | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Operación para eliminar las reglas de autorización de temas |
> | . | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Obtiene la cadena de conexión al tema |
> | . | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Obtiene la lista de reglas de autorización de temas |
> | . | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Regenera la clave principal o secundaria del recurso |
> | . | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Crea reglas de autorización de temas y actualiza sus propiedades. Los derechos de acceso de las reglas de autorización se pueden actualizar. |
> | . | Microsoft.ServiceBus/namespaces/topics/Delete | Operación para eliminar los recursos de un tema |
> | . | Microsoft.ServiceBus/namespaces/topics/read | Obtiene una lista de descripciones de recursos de tema |
> | . | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operación para eliminar los recursos de TopicSubscription |
> | . | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Obtiene una lista de descripciones de recursos de TopicSubscription |
> | . | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operación para eliminar los recursos de reglas |
> | . | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Obtiene una lista de descripciones de recursos de reglas |
> | . | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Crea o actualiza las propiedades de reglas. |
> | . | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Crear o actualizar las propiedades de TopicSubscription. |
> | . | Microsoft.ServiceBus/namespaces/topics/write | Crea o actualiza las propiedades de tema. |
> | . | Microsoft.ServiceBus/namespaces/write | Crea un recurso de espacio de nombres y actualiza sus propiedades. Las etiquetas y la capacidad del espacio de nombres son las propiedades que se pueden actualizar. |
> | . | Microsoft.ServiceBus/operations/read | Obtiene operaciones. |
> | . | Microsoft.ServiceBus/register/action | Registra la suscripción para el proveedor de recursos de ServiceBus y habilita la creación de recursos de ServiceBus |
> | . | Microsoft.ServiceBus/sku/read | Obtiene una lista de descripciones de recursos de SKU. |
> | . | Microsoft.ServiceBus/sku/regions/read | Obtiene una lista de descripciones de recursos de SkuRegions. |
> | . | Microsoft.ServiceBus/unregister/action | Registra la suscripción para el proveedor de recursos de ServiceBus y habilita la creación de recursos de ServiceBus |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.ServiceFabric/clusters/applications/delete | Elimina cualquier aplicación. |
> | . | Microsoft.ServiceFabric/clusters/applications/read | Lee cualquier aplicación. |
> | . | Microsoft.ServiceFabric/clusters/applications/services/delete | Elimina cualquier servicio. |
> | . | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Lee cualquier partición. |
> | . | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Lee cualquier réplica. |
> | . | Microsoft.ServiceFabric/clusters/applications/services/read | Lee cualquier servicio. |
> | . | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Lee cualquier estado del servicio. |
> | . | Microsoft.ServiceFabric/clusters/applications/services/write | Crea o actualiza cualquier servicio. |
> | . | Microsoft.ServiceFabric/clusters/applications/write | Crea o actualiza cualquier aplicación. |
> | . | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Elimina cualquier tipo de aplicación. |
> | . | Microsoft.ServiceFabric/clusters/applicationTypes/read | Lee cualquier tipo de aplicación. |
> | . | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Elimina cualquier versión de tipo de aplicación. |
> | . | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Lee cualquier versión de tipo de aplicación. |
> | . | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Crea o actualiza cualquier versión de tipo de aplicación. |
> | . | Microsoft.ServiceFabric/clusters/applicationTypes/write | Crea o actualiza cualquier tipo de aplicación. |
> | . | Microsoft.ServiceFabric/clusters/delete | Borra cualquier clúster. |
> | . | Microsoft.ServiceFabric/clusters/nodes/read | Lee cualquier nodo. |
> | . | Microsoft.ServiceFabric/clusters/read | Lee cualquier clúster. |
> | . | Microsoft.ServiceFabric/clusters/statuses/read | Lee cualquier estado del clúster. |
> | . | Microsoft.ServiceFabric/clusters/write | Crea o actualiza cualquier clúster. |
> | . | Microsoft.ServiceFabric/locations/clusterVersions/read | Lee cualquier versión del clúster. |
> | . | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Lee cualquier versión de clúster para un entorno específico. |
> | . | Microsoft.ServiceFabric/locations/operationresults/read | Lee cualquier resultado de la operación. |
> | . | Microsoft.ServiceFabric/locations/operations/read | Lee cualquier operación según la ubicación. |
> | . | Microsoft.ServiceFabric/operations/read | Lee cualquier operación disponible. |
> | . | Microsoft.ServiceFabric/register/action | Registra cualquier acción. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.SignalRService/checknameavailability/action | Comprueba si un nombre está disponible para su uso con un servicio SignalR nuevo |
> | . | Microsoft.SignalRService/register/action | Registra el proveedor de recursos "Microsoft.SignalRService" en una suscripción |
> | . | Microsoft.SignalRService/SignalR/delete | Elimina todo SignalR |
> | . | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de SignalR |
> | . | Microsoft.SignalRService/SignalR/read | Vea los valores y configuraciones de SignalR en el portal de administración o con la API |
> | . | Microsoft.SignalRService/SignalR/write | Modifica los valores y configuraciones de SignalR en el portal de administración o con la API |
> | . | Microsoft.SignalRService/unregister/action | Anula el registro del proveedor de recursos "Microsoft.SignalRService" en una suscripción |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Solutions/applicationDefinitions/delete | Quita una definición de aplicación. |
> | . | Microsoft.Solutions/applicationDefinitions/read | Recupera una lista de definiciones de aplicación. |
> | . | Microsoft.Solutions/applicationDefinitions/write | Agrega o modifica una definición de aplicación. |
> | . | Microsoft.Solutions/applications/delete | Quita una aplicación. |
> | . | Microsoft.Solutions/applications/read | Recupera una lista de aplicaciones. |
> | . | Microsoft.Solutions/applications/write | Crea una aplicación. |
> | . | Microsoft.Solutions/locations/operationStatuses/read | Lee el estado de la operación de los recursos. |
> | . | Microsoft.Solutions/register/action | Permite registrarse en Solutions. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.Sql/checkNameAvailability/action | Comprueba si un nombre de servidor dado está disponible para su aprovisionamiento en todo el mundo según una suscripción determinada. |
> | . | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Recupera el resultado de la operación Set para la directiva de auditoría de blobs del servidor extendido. |
> | . | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Recupera el resultado de la operación de establecimiento de directivas de auditoría de blobs del servidor |
> | . | Microsoft.Sql/locations/capabilities/read | Obtiene las capacidades de esta suscripción en una ubicación específica. |
> | . | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Obtiene el estado de una operación de base de datos. |
> | . | Microsoft.Sql/locations/databaseOperationResults/read | Obtiene el estado de una operación de base de datos. |
> | . | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Obtiene las operaciones en curso del servidor eliminado. |
> | . | Microsoft.Sql/locations/deletedServerOperationResults/read | Obtiene las operaciones en curso del servidor eliminado. |
> | . | Microsoft.Sql/locations/deletedServers/read | Devuelve la lista de servidores eliminados u obtiene las propiedades de un servidor específico eliminado. |
> | . | Microsoft.Sql/locations/deletedServers/recover/action | Recupera un servidor eliminado. |
> | . | Microsoft.Sql/locations/deleteVirtualNetworkOrSubnets/action | Elimina las reglas de red virtual asociadas a una red o subred virtual. |
> | . | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Obtiene la operación asincrónica de Azure para una operación asincrónica de grupo elástico. |
> | . | Microsoft.Sql/locations/elasticPoolOperationResults/read | Obtiene el resultado de una operación de grupo elástico. |
> | . | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Recupera el resultado de la operación Set para la directiva de auditoría de blobs del servidor extendido. |
> | . | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Recupera el resultado de la operación Set para la directiva de auditoría de blobs del servidor extendido. |
> | . | Microsoft.Sql/locations/instanceFailoverGroups/delete | Elimina un grupo de conmutación por error de instancias existente. |
> | . | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Ejecuta la conmutación por error planeada en un grupo de conmutación por error de instancias existente. |
> | . | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Ejecuta la conmutación por error forzada en un grupo de conmutación por error de instancias existente. |
> | . | Microsoft.Sql/locations/instanceFailoverGroups/read | Devuelve la lista de grupos de conmutación por error de instancias u obtiene las propiedades del grupo de conmutación por error de instancias especificado. |
> | . | Microsoft.Sql/locations/instanceFailoverGroups/write | Crea un grupo de conmutación por error de instancias con los parámetros especificados o actualiza las propiedades o etiquetas del grupo de conmutación por error de instancias especificado. |
> | . | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Completa una operación de restauración de base de datos administrada. |
> | . | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Obtiene las operaciones en curso para el cifrado de datos transparente de la base de datos administrada. |
> | . | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Obtiene las operaciones en curso para el cifrado de datos transparente de la base de datos administrada. |
> | . | Microsoft.Sql/locations/networkInterfaceAzureAsyncOperation/read | Devuelve los detalles de una operación asincrónica de Azure de la interfaz de red específica |
> | . | Microsoft.Sql/locations/networkInterfaceOperationResults/read | Devuelve los detalles de la operación de la interfaz de red específica |
> | . | Microsoft.Sql/locations/read | Obtiene las ubicaciones disponibles para una suscripción determinada. |
> | . | Microsoft.Sql/locations/syncAgentOperationResults/read | Recupera el resultado de la operación de recursos del agente de sincronización. |
> | . | Microsoft.Sql/locations/syncDatabaseIds/read | Recupera los identificadores de la base de datos de sincronización de una región y una suscripción determinadas. |
> | . | Microsoft.Sql/locations/syncGroupOperationResults/read | Recupera el resultado de la operación de recursos del grupo de sincronización. |
> | . | Microsoft.Sql/locations/syncMemberOperationResults/read | Recupera el resultado de la operación de recursos del miembro de sincronización. |
> | . | Microsoft.Sql/locations/usages/read | Obtiene una colección de métricas de uso para esta suscripción en una ubicación. |
> | . | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Devuelve los detalles de una operación asincrónica de Azure para las reglas de red virtual especificadas.  |
> | . | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Devuelve los detalles de una operación para las reglas de red virtual especificadas.  |
> | . | Microsoft.Sql/managedInstances/administrators/delete | Elimina un administrador existente de la instancia administrada. |
> | . | Microsoft.Sql/managedInstances/administrators/read | Obtiene una lista de administradores de la instancia administrada. |
> | . | Microsoft.Sql/managedInstances/administrators/write | Crea o actualiza el administrador de la instancia administrada con los parámetros especificados. |
> | . | Microsoft.Sql/managedInstances/databases/delete | Elimina una base de datos administrada ya existente. |
> | . | Microsoft.Sql/managedInstances/databases/read | Obtiene una base de datos administrada ya existente. |
> | . | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Recupera los detalles de la directiva de detección de amenazas en bases de datos que está configurada en una base de datos administrada específica. |
> | . | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Cambia la directiva de detección de amenazas en bases de datos para una base de datos administrada específica. |
> | . | Microsoft.Sql/managedInstances/databases/securityEvents/read | Recupera los eventos de seguridad de una base de datos administrada. |
> | . | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Recupera los detalles del cifrado de datos transparente de una base de datos para una base de datos administrada específica. |
> | . | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Cambia el cifrado de datos transparente de una base de datos para una base de datos administrada específica. |
> | . | Microsoft.Sql/managedInstances/databases/write | Crea una nueva base de datos o actualiza una ya existente. |
> | . | Microsoft.Sql/managedInstances/delete | Elimina una base de datos administrada ya existente. |
> | . | Microsoft.Sql/managedInstances/metricDefinitions/read | Obtiene las definiciones de métricas de la instancia administrada. |
> | . | Microsoft.Sql/managedInstances/metrics/read | Obtiene las métricas de la instancia administrada. |
> | . | Microsoft.Sql/managedInstances/read | Devuelve la lista de instancias administradas u obtiene las propiedades de una instancia administrada específica. |
> | . | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Recupera los detalles de la directiva de detección de amenazas en un servidor administrado que está configurada en un servidor administrado específico. |
> | . | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Cambia la directiva de detección de amenazas en un servidor administrado para un servidor administrado específico. |
> | . | Microsoft.Sql/managedInstances/write | Crea una instancia administrada con los parámetros especificados o actualiza las propiedades o etiquetas de la instancia administrada especificada. |
> | . | Microsoft.Sql/operations/read | Obtiene las operaciones de REST disponibles. |
> | . | Microsoft.Sql/register/action | Registra la suscripción del proveedor de recursos de Microsoft SQL Database y habilita la creación de bases de datos de Microsoft SQL Database. |
> | . | Microsoft.Sql/servers/administratorOperationResults/read | Obtiene las operaciones en curso de los administradores del servidor. |
> | . | Microsoft.Sql/servers/administrators/delete | Elimina el administrador del servidor. |
> | . | Microsoft.Sql/servers/administrators/read | Recupera los detalles del administrador del servidor |
> | . | Microsoft.Sql/servers/administrators/write | Crea o actualiza el administrador del servidor |
> | . | Microsoft.Sql/servers/advisors/read | Devuelve la lista de asesores disponibles para el servidor |
> | . | Microsoft.Sql/servers/advisors/recommendedActions/read | Devuelve una lista de acciones recomendadas del asesor especificado para el servidor |
> | . | Microsoft.Sql/servers/advisors/recommendedActions/write | Aplica la acción recomendada en el servidor |
> | . | Microsoft.Sql/servers/advisors/write | Actualiza el estado de ejecución automática de un asesor en el nivel del servidor. |
> | . | Microsoft.Sql/servers/auditingPolicies/read | Recupera detalles de la directiva predeterminada de auditoría de tablas del servidor configurada en un servidor determinado |
> | . | Microsoft.Sql/servers/auditingPolicies/write | Cambia la directiva predeterminada de auditoría de tablas del servidor para un servidor determinado |
> | . | Microsoft.Sql/servers/auditingSettings/operationResults/read | Recupera el resultado de la operación de establecimiento de directivas de auditoría de blobs del servidor |
> | . | Microsoft.Sql/servers/auditingSettings/read | Recupera detalles de la directiva de auditoría de blobs del servidor configurada en un servidor determinado |
> | . | Microsoft.Sql/servers/auditingSettings/write | Cambia la auditoría de blobs del servidor para un servidor determinado |
> | . | Microsoft.Sql/servers/automaticTuning/read | Devuelve la configuración de optimización automática del servidor. |
> | . | Microsoft.Sql/servers/automaticTuning/write | Actualiza la configuración de optimización automática del servidor y devuelve la configuración actualizada. |
> | . | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Elimina un almacén de archivos de copia de seguridad. |
> | . | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Esta operación se usa para obtener un almacén de retención de copia de seguridad a largo plazo. Devuelve información acerca del almacén registrado en este servidor. |
> | . | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Esta operación se usa para registrar un almacén de retención de copias de seguridad a largo plazo en un servidor. |
> | . | Microsoft.Sql/servers/communicationLinks/delete | Elimina un vínculo de comunicación del servidor existente. |
> | . | Microsoft.Sql/servers/communicationLinks/read | Devuelve la lista de vínculos de comunicación de un servidor específico. |
> | . | Microsoft.Sql/servers/communicationLinks/write | Crea o actualiza un vínculo de comunicación del servidor. |
> | . | Microsoft.Sql/servers/connectionPolicies/read | Devuelve la lista de directivas de conexión del servidor de un servidor específico. |
> | . | Microsoft.Sql/servers/connectionPolicies/write | Crea o actualiza una directiva de conexión del servidor. |
> | . | Microsoft.Sql/servers/databases/advisors/read | Devuelve la lista de asesores disponibles para la base de datos |
> | . | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Devuelve una lista de acciones recomendadas del asesor especificado para la base de datos |
> | . | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Aplica la acción recomendada en la base de datos |
> | . | Microsoft.Sql/servers/databases/advisors/write | Actualiza el estado de ejecución automática de un asesor en el nivel de la base de datos. |
> | . | Microsoft.Sql/servers/databases/auditingPolicies/read | Recupera detalles de la directiva de auditoría de tablas configurada en una base de datos determinada |
> | . | Microsoft.Sql/servers/databases/auditingPolicies/write | Cambia la directiva de auditoría de tablas para una base de datos determinada |
> | . | Microsoft.Sql/servers/databases/auditingSettings/read | Recupera detalles de la directiva de auditoría de blobs configurada en una base de datos determinada |
> | . | Microsoft.Sql/servers/databases/auditingSettings/write | Cambia la directiva de auditoría de blobs para una base de datos determinada |
> | . | Microsoft.Sql/servers/databases/auditRecords/read | Recupera los registros de auditoría de blobs de bases de datos |
> | . | Microsoft.Sql/servers/databases/automaticTuning/read | Devuelve la configuración de optimización automática de una base de datos. |
> | . | Microsoft.Sql/servers/databases/automaticTuning/write | Actualiza la configuración de optimización automática de una base de datos y devuelve la configuración actualizada. |
> | . | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Obtiene el estado de una operación de base de datos. |
> | . | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Devuelve la lista de las directivas de archivado de copias de seguridad de una base de datos específica. |
> | . | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Crea o actualiza una directiva de archivado de copias de seguridad de bases de datos. |
> | . | Microsoft.Sql/servers/databases/connectionPolicies/read | Recupera detalles de la directiva de conexión configurada en una base de datos determinada |
> | . | Microsoft.Sql/servers/databases/connectionPolicies/write | Cambia la directiva de conexión para una base de datos determinada |
> | . | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Devuelve la lista de directivas de enmascaramiento de datos de una base de datos. |
> | . | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Elimina la regla de la directiva de enmascaramiento de datos de una base de datos determinada. |
> | . | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Recupera los detalles de la regla de directiva de enmascaramiento de datos configurada en una determinada base de datos |
> | . | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Cambia la regla de directiva de enmascaramiento de datos de una determinada base de datos |
> | . | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Cambia la directiva de enmascaramiento de datos de una determinada base de datos |
> | . | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Devuelve la información de pasos de la consulta distribuida de almacenamiento de datos para el identificador del paso seleccionado |
> | . | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Devuelve la información de la consulta de distribución de almacenamiento de datos para el identificador de la consulta seleccionado |
> | . | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Recupera las actividades de usuario de una instancia de SQL Data Warehouse que incluye las consultas en ejecución y las suspendidas. |
> | . | Microsoft.Sql/servers/databases/delete | Elimina una base de datos existente. |
> | . | Microsoft.Sql/servers/databases/export/action | Exporta Azure SQL Database. |
> | . | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Recupera los detalles de la directiva de auditoría de blobs extendida y configurada en una base de datos determinada. |
> | . | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Cambia la directiva de auditoría de blobs extendida para una base de datos determinada. |
> | . | Microsoft.Sql/servers/databases/extensions/read | Obtiene una colección de extensiones para la base de datos. |
> | . | Microsoft.Sql/servers/databases/extensions/write | Cambia la extensión de una base de datos determinada. |
> | . | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Recupera las directivas de copia de seguridad de replicación geográfica para una base de datos determinada. |
> | . | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Crea o actualiza una directiva de replicación geográfica de copias de seguridad de bases de datos. |
> | . | Microsoft.Sql/servers/databases/importExportOperationResults/read | Obtiene las operaciones de importación y exportación en curso. |
> | . | Microsoft.Sql/servers/databases/metricDefinitions/read | Devuelve los tipos de métricas que están disponibles para grupos de bases de datos |
> | . | Microsoft.Sql/servers/databases/metrics/read | Devuelve las métricas de las bases de datos. |
> | . | Microsoft.Sql/servers/databases/move/action | Cambia el nombre de la instancia de Azure SQL Database. |
> | . | Microsoft.Sql/servers/databases/operationResults/read | Obtiene el estado de una operación de base de datos. |
> | . | Microsoft.Sql/servers/databases/operations/cancel/action | Cancela la instancia de Azure SQL Database que tiene pendiente una operación asincrónica que aún no ha finalizado. |
> | . | Microsoft.Sql/servers/databases/operations/read | Devuelve la lista de las operaciones realizadas en la base de datos. |
> | . | Microsoft.Sql/servers/databases/pause/action | Pausa la base de datos de Azure SQL Data Warehouse. |
> | . | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Obtiene los registros disponibles para las bases de datos |
> | . | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Devuelve los tipos de métricas que están disponibles para grupos de bases de datos |
> | . | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Devuelve la colección de los textos de consulta que corresponden a los parámetros especificados. |
> | . | Microsoft.Sql/servers/databases/queryStore/read | Devuelve los valores actuales de configuración del almacén de consultas de la base de datos. |
> | . | Microsoft.Sql/servers/databases/queryStore/write | Actualiza la configuración del almacén de consultas de la base de datos |
> | . | Microsoft.Sql/servers/databases/read | Devuelve la lista de bases de datos u obtiene las propiedades de una base de datos específica. |
> | . | Microsoft.Sql/servers/databases/replicationLinks/delete | Finaliza la relación de replicación a la fuerza con una posible pérdida de datos |
> | . | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Realiza una conmutación por error después de sincronizar todos los cambios desde el servidor principal, haciendo que esta base de datos sea la principal de la relación de replicación y haciendo que el control remoto principal se convierta en secundario. |
> | . | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Realiza una conmutación por error de forma inmediata con posible pérdida de datos, haciendo que esta base de datos sea la principal de la relación de replicación y haciendo que el control remoto principal se convierta en secundario. |
> | . | Microsoft.Sql/servers/databases/replicationLinks/read | Devuelve detalles acerca de los vínculos de replicación establecidos para una determinada base de datos |
> | . | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Finaliza la relación de replicación a la fuerza o después de sincronizar con el asociado |
> | . | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Actualiza el modo de replicación para vincular al modo sincrónico o asincrónico |
> | . | Microsoft.Sql/servers/databases/restorePoints/action | Crea un nuevo punto de restauración |
> | . | Microsoft.Sql/servers/databases/restorePoints/delete | Elimina un punto de restauración de la base de datos. |
> | . | Microsoft.Sql/servers/databases/restorePoints/read | Devuelve los puntos de restauración de la base de datos. |
> | . | Microsoft.Sql/servers/databases/resume/action | Reanuda la base de datos de Azure SQL Data Warehouse. |
> | . | Microsoft.Sql/servers/databases/schemas/read | Recupera la lista de esquemas de una base de datos. |
> | . | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Recuperar la lista de columnas de una tabla |
> | . | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Elimina la etiqueta de confidencialidad de una columna determinada. |
> | . | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Obtiene la etiqueta de confidencialidad de una columna determinada. |
> | . | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Crea o actualiza la etiqueta de confidencialidad de una columna determinada. |
> | . | Microsoft.Sql/servers/databases/schemas/tables/read | Recupera la lista de tablas de una base de datos. |
> | . | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Recupera la lista de recomendaciones de índice de una base de datos |
> | . | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Aplica la recomendación de índice |
> | . | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Recupera detalles de la directiva de detección de amenazas configurada en una base de datos determinada |
> | . | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Cambia la directiva de detección de amenazas para una base de datos determinada |
> | . | Microsoft.Sql/servers/databases/securityMetrics/read | Obtiene una colección de métricas de seguridad de una base de datos. |
> | . | Microsoft.Sql/servers/databases/sensitivityLabels/read | Enumera las etiquetas de confidencialidad de una base de datos determinada. |
> | . | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Devuelve sugerencias acerca de cómo escalar o reducir verticalmente la base de datos en función de las estadísticas de ejecución de consultas para mejorar el rendimiento o reducir los costos |
> | . | Microsoft.Sql/servers/databases/skus/read | Obtiene una colección de los SKU disponibles para una base de datos |
> | . | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Cancela la sincronización del grupo de sincronización. |
> | . | Microsoft.Sql/servers/databases/syncGroups/delete | Elimina un grupo de sincronización existente. |
> | . | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Devuelve la lista de esquemas de la base de datos central de sincronización. |
> | . | Microsoft.Sql/servers/databases/syncGroups/logs/read | Devuelve la lista de registros del grupo de sincronización. |
> | . | Microsoft.Sql/servers/databases/syncGroups/read | Devuelve la lista de grupos de sincronización u obtiene las propiedades de un grupo de sincronización específico. |
> | . | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Actualiza el esquema de la base de datos central de sincronización. |
> | . | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Recupera el resultado de la operación de actualización del esquema de la base de datos central de sincronización. |
> | . | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Elimina un miembro de sincronización existente. |
> | . | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Devuelve la lista de miembros de sincronización u obtiene las propiedades de un miembro de sincronización específico. |
> | . | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Actualiza el esquema de un miembro de sincronización. |
> | . | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Recupera el resultado de la operación de actualización del esquema de un miembro sincronización. |
> | . | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Devuelve la lista de esquemas de la base de datos del miembro de sincronización. |
> | . | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Crea un miembro de sincronización con los parámetros especificados o actualiza las propiedades del miembro de sincronización especificado. |
> | . | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Desencadena la sincronización del grupo de sincronización. |
> | . | Microsoft.Sql/servers/databases/syncGroups/write | Crea un grupo de sincronización con los parámetros especificados o actualiza las propiedades de un grupo de sincronización especificado. |
> | . | Microsoft.Sql/servers/databases/topQueries/queryText/action | Devuelve el texto de Transact-SQL para el identificador de la consulta seleccionado |
> | . | Microsoft.Sql/servers/databases/topQueries/read | Devuelve estadísticas agregadas de tiempo de ejecución para la consulta seleccionada en el período de tiempo seleccionado |
> | . | Microsoft.Sql/servers/databases/topQueries/statistics/read | Devuelve estadísticas agregadas de tiempo de ejecución para la consulta seleccionada en el período de tiempo seleccionado |
> | . | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Obtiene las operaciones en curso para el cifrado de datos transparente. |
> | . | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Recupera el estado y los detalles de la característica de seguridad de cifrado de datos transparente para una base de datos determinada |
> | . | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Cambia el estado del cifrado de datos transparente. |
> | . | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Actualiza la base de datos de Azure SQL Data Warehouse. |
> | . | Microsoft.Sql/servers/databases/usages/read | Obtiene información sobre los usos de Azure SQL Database. |
> | . | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Quita la valoración de vulnerabilidades de una base de datos determinada. |
> | . | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Recupera los detalles de la valoración de vulnerabilidades configurada en una base de datos determinada. |
> | . | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Quita la línea de base de la regla de valoración de vulnerabilidades de una base de datos determinada. |
> | . | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Obtiene la línea de base de la regla de valoración de vulnerabilidades de una base de datos determinada. |
> | . | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Cambia la línea de base de la regla de valoración de vulnerabilidades de una base de datos determinada. |
> | . | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Convierte un resultado del análisis existente en un formato legible. Si ya existe no ocurre nada. |
> | . | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Ejecuta un análisis de la base datos de evaluación de vulnerabilidad. |
> | . | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Devuelve la lista de los registros del análisis de evaluación de vulnerabilidades u obtiene el registro del análisis para el id. del análisis específico. |
> | . | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Cambia la valoración de vulnerabilidades de una base de datos determinada. |
> | . | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Ejecuta un análisis de la base datos de evaluación de vulnerabilidad. |
> | . | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Recupera el resultado de la operación Execute correspondiente al análisis de valoración de vulnerabilidades de la base de datos. |
> | . | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Recupera los detalles de la valoración de vulnerabilidades configurada en una base de datos determinada. |
> | . | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Cambia la valoración de vulnerabilidades de una base de datos determinada. |
> | . | Microsoft.Sql/servers/databases/write | Crea una base de datos con los parámetros especificados o actualiza las propiedades o etiquetas de la base de datos especificada. |
> | . | Microsoft.Sql/servers/delete | Elimina un servidor existente. |
> | . | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Elimina las configuraciones de recuperación ante desastres existentes de un servidor determinado. |
> | . | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Realiza una conmutación por error de DisasterRecoveryConfiguration. |
> | . | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Fuerza una conmutación por error de DisasterRecoveryConfiguration. |
> | . | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Obtiene una colección de configuraciones de recuperación ante desastres que incluyan este servidor. |
> | . | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Cambia la configuración de recuperación ante desastres del servidor. |
> | . | Microsoft.Sql/servers/elasticPoolEstimates/read | Devuelve una lista de las estimaciones de grupos elásticos ya creados para este servidor |
> | . | Microsoft.Sql/servers/elasticPoolEstimates/write | Crea una nueva estimación de grupos elásticos para la lista de bases de datos proporcionada |
> | . | Microsoft.Sql/servers/elasticPools/advisors/read | Devuelve una lista de asesores disponibles para el grupo elástico |
> | . | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Devuelve una lista de acciones recomendadas del asesor especificado para el grupo elástico |
> | . | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Aplica la acción recomendada en el grupo elástico |
> | . | Microsoft.Sql/servers/elasticPools/advisors/write | Actualiza el estado de ejecución automática de un asesor en el nivel del grupo elástico. |
> | . | Microsoft.Sql/servers/elasticPools/databases/read | Obtiene una lista de bases de datos de un grupo elástico. |
> | . | Microsoft.Sql/servers/elasticPools/delete | Elimina un grupo elástico ya existente. |
> | . | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Recupera las actividades y detalles de un grupo de bases de datos elásticas determinado |
> | . | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Recupera las actividades y detalles de una determinada base de datos que forma parte del grupo de bases de datos elásticas |
> | . | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Devuelve los tipos de métricas que están disponibles para grupos de bases de datos elásticas |
> | . | Microsoft.Sql/servers/elasticPools/metrics/read | Devuelve las métricas de los grupos de bases de datos elásticas. |
> | . | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Cancela la el grupo elástico de Azure SQL que tiene pendiente una operación asincrónica que aún no ha finalizado. |
> | . | Microsoft.Sql/servers/elasticPools/operations/read | Devuelve la lista de las operaciones realizadas en el grupo elástico. |
> | . | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Devuelve los tipos de métricas que están disponibles para grupos de bases de datos elásticas |
> | . | Microsoft.Sql/servers/elasticPools/read | Recupera los detalles de un grupo elástico de un servidor determinado. |
> | . | Microsoft.Sql/servers/elasticPools/skus/read | Obtiene una colección de las SKU disponibles para un grupo elástico |
> | . | Microsoft.Sql/servers/elasticPools/write | Crea o cambia las propiedades de un grupo elástico. |
> | . | Microsoft.Sql/servers/encryptionProtector/read | Devuelve una lista de protectores de cifrado de servidor u obtiene las propiedades de un protector de cifrado de servidor específico. |
> | . | Microsoft.Sql/servers/encryptionProtector/write | Actualiza las propiedades de un protector de cifrado de servidor específico. |
> | . | Microsoft.Sql/servers/extendedAuditingSettings/read | Recupera los detalles de la directiva de auditoría de blobs del servidor extendido que está configurada en un servidor determinado. |
> | . | Microsoft.Sql/servers/extendedAuditingSettings/write | Cambia la auditoría de blobs del servidor extendido para un servidor determinado. |
> | . | Microsoft.Sql/servers/failoverGroups/delete | Elimina un grupo de conmutación por error existente. |
> | . | Microsoft.Sql/servers/failoverGroups/failover/action | Ejecuta la conmutación por error planeada en un grupo de conmutación por error existente. |
> | . | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Ejecuta la conmutación por error forzada en un grupo de conmutación por error existente. |
> | . | Microsoft.Sql/servers/failoverGroups/read | Devuelve la lista de grupos de conmutación por error u obtiene las propiedades de un grupo de conmutación por error específico. |
> | . | Microsoft.Sql/servers/failoverGroups/write | Crea un grupo de conmutación por error con los parámetros especificados o actualiza las propiedades o etiquetas del grupo de conmutación por error especificado. |
> | . | Microsoft.Sql/servers/firewallRules/delete | Elimina una regla de firewall de servidor existente. |
> | . | Microsoft.Sql/servers/firewallRules/read | Devuelve la lista de reglas de firewall de servidor u obtiene las propiedades de la regla de firewall de servidor especificada. |
> | . | Microsoft.Sql/servers/firewallRules/write | Crea una regla de firewall de servidor con los parámetros especificados, actualiza las propiedades de la regla especificada o sobrescribe todas las reglas existentes con nuevas reglas de firewall de servidor. |
> | . | Microsoft.Sql/servers/import/action | Crea una base de datos nueva en el servidor e implementa el esquema y los datos de un paquete DacPac |
> | . | Microsoft.Sql/servers/importExportOperationResults/read | Obtiene las operaciones de importación y exportación en curso. |
> | . | Microsoft.Sql/servers/keys/delete | Elimina una clave de servidor existente. |
> | . | Microsoft.Sql/servers/keys/read | Devuelve la lista de claves de servidor u obtiene las propiedades de una clave de servidor específica. |
> | . | Microsoft.Sql/servers/keys/write | Crea una clave con los parámetros especificados o actualiza las propiedades o etiquetas de la clave de servidor especificada. |
> | . | Microsoft.Sql/servers/networkInterfaces/read | Devuelve las propiedades de la interfaz de red especificada |
> | . | Microsoft.Sql/servers/networkInterfaces/write | Crea una interfaz de red con los parámetros especificados o actualiza las propiedades o etiquetas de la interfaz de red especificado. |
> | . | Microsoft.Sql/servers/operationResults/read | Obtiene las operaciones de servidor en curso. |
> | . | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Devuelve los tipos de métricas que están disponibles en los servidores. |
> | . | Microsoft.Sql/servers/read | Devuelve la lista de servidores u obtiene las propiedades de un servidor específico. |
> | . | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Recupera las métricas para los grupos de bases de datos elásticas recomendados para un servidor determinado |
> | . | Microsoft.Sql/servers/recommendedElasticPools/read | Recupera la recomendación para los grupos de bases de datos elásticas para reducir los costos o mejorar el rendimiento en función del historial de uso de los recursos |
> | . | Microsoft.Sql/servers/recoverableDatabases/read | Esta operación se usa para la recuperación ante desastres de una base de datos activa para restaurar la base de datos al último punto bueno de copia de seguridad conocido. Devuelve información acerca de la última copia de seguridad correcta, pero no restaura realmente la base de datos. |
> | . | Microsoft.Sql/servers/restorableDroppedDatabases/read | Obtiene una lista de bases de datos que se han quitado de un servidor determinado y que siguen estando dentro de la directiva de retención. |
> | . | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Recupera los resultados de la operación de escritura de directivas de detección de amenazas del servidor. |
> | . | Microsoft.Sql/servers/securityAlertPolicies/read | Recupera detalles de la directiva de detección de amenazas del servidor configurada en un servidor determinado |
> | . | Microsoft.Sql/servers/securityAlertPolicies/write | Cambia la directiva de detección de amenazas del servidor para un servidor determinado. |
> | . | Microsoft.Sql/servers/serviceObjectives/read | Recupera la lista de objetivos de nivel de servicio (también conocida como niveles de rendimiento) disponible en un servidor determinado |
> | . | Microsoft.Sql/servers/syncAgents/delete | Elimina un agente de sincronización existente. |
> | . | Microsoft.Sql/servers/syncAgents/generateKey/action | Genera la clave de registro del agente de sincronización. |
> | . | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Devuelve la lista de bases de datos de sincronización del agente vinculado. |
> | . | Microsoft.Sql/servers/syncAgents/read | Devuelve la lista de agentes de sincronización u obtiene las propiedades de un agente de sincronización específico. |
> | . | Microsoft.Sql/servers/syncAgents/write | Crea un agente de sincronización con los parámetros especificados o actualiza las propiedades de un agente de sincronización específico. |
> | . | Microsoft.Sql/servers/usages/read | Devuelve la cuota de DTU del servidor y el consumo actual de DTU de todas las bases de datos del servidor |
> | . | Microsoft.Sql/servers/virtualNetworkRules/delete | Elimina una regla de Virtual Network existente. |
> | . | Microsoft.Sql/servers/virtualNetworkRules/read | Devuelve la lista de reglas de red virtual u obtiene las propiedades de una regla de red virtual específica. |
> | . | Microsoft.Sql/servers/virtualNetworkRules/write | Crea una regla de red virtual con los parámetros especificados o actualiza las propiedades o etiquetas de la regla de red virtual especificada. |
> | . | Microsoft.Sql/servers/write | Crea un servidor con los parámetros especificados o actualiza las propiedades o etiquetas del servidor especificado. |
> | . | Microsoft.Sql/unregister/action | Quita el registro a la suscripción del proveedor de recursos de Microsoft SQL Database y habilita la creación de bases de datos de Microsoft SQL Database. |
> | . | Microsoft.Sql/virtualClusters/read | Devuelve la lista de clúster virtuales u obtiene las propiedades de un clúster virtual específico. |
> | . | Microsoft.Sql/virtualClusters/write | Actualiza las etiquetas de los clúster virtuales. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Storage/checknameavailability/read | Comprueba que el nombre de la cuenta es válido y que no está en uso. |
> | . | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Notifica a Microsoft.Storage que se está eliminando una red virtual o subred |
> | . | Microsoft.Storage/operations/read | Sondea el estado de una operación asincrónica. |
> | . | Microsoft.Storage/register/action | Registra la suscripción para el proveedor de recursos de almacenamiento y habilita la creación de cuentas de almacenamiento. |
> | . | Microsoft.Storage/skus/read | Enumera las SKU compatibles con Microsoft.Storage. |
> | . | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Borra la suspensión legal del contenedor de blobs. |
> | . | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Devuelve el resultado de la eliminación de un contenedor. |
> | . | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Elimina la directiva de inmutabilidad del contenedor de blobs. |
> | . | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Extiende la directiva de inmutabilidad del contenedor de blobs. |
> | . | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Bloquea la directiva de inmutabilidad del contenedor de blobs. |
> | . | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Obtiene la directiva de inmutabilidad del contenedor de blobs. |
> | . | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Establece la directiva de inmutabilidad del contenedor de blobs. |
> | . | Microsoft.Storage/storageAccounts/blobServices/containers/read | Devuelve un contenedor o una lista de contenedores. |
> | . | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Establece la suspensión legal del contenedor de blobs. |
> | . | Microsoft.Storage/storageAccounts/blobServices/containers/write | Devuelve el resultado de la colocación o concesión del contenedor de blobs. |
> | . | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso. |
> | . | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso. |
> | . | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | Obtiene la lista de definiciones de métricas de almacenamiento de Microsoft. |
> | . | Microsoft.Storage/storageAccounts/blobServices/read | Devuelve las propiedades o las estadísticas de Blob service. |
> | . | Microsoft.Storage/storageAccounts/blobServices/write | Devuelve el resultado de las propiedades de colocación de Blob service. |
> | . | Microsoft.Storage/storageAccounts/delete | Agrega una cuenta de almacenamiento existente. |
> | . | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso. |
> | . | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso. |
> | . | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | Obtiene la lista de definiciones de métricas de almacenamiento de Microsoft. |
> | . | Microsoft.Storage/storageAccounts/listAccountSas/action | Devuelve el token de SAS de la cuenta de almacenamiento especificada. |
> | . | Microsoft.Storage/storageAccounts/listkeys/action | Devuelve las claves de acceso de la cuenta de almacenamiento especificada. |
> | . | Microsoft.Storage/storageAccounts/listServiceSas/action | Devuelve el token de SAS del servicio para la cuenta de almacenamiento especificada. |
> | . | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso. |
> | . | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso. |
> | . | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | Obtiene la lista de definiciones de métricas de almacenamiento de Microsoft. |
> | . | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso. |
> | . | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read | Obtiene la lista de definiciones de métricas de almacenamiento de Microsoft. |
> | . | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Devuelve el resultado de la eliminación de una cola. |
> | . | Microsoft.Storage/storageAccounts/queueServices/queues/read | Devuelve una cola o una lista de colas. |
> | . | Microsoft.Storage/storageAccounts/queueServices/queues/write | Devuelve el resultado de escribir una cola. |
> | . | Microsoft.Storage/storageAccounts/queueServices/read | Devuelve las propiedades o las estadísticas de Queue service. |
> | . | Microsoft.Storage/storageAccounts/queueServices/write | Devuelve el resultado de establecer las propiedades de Queue service. |
> | . | Microsoft.Storage/storageAccounts/read | Devuelve la lista de cuentas de almacenamiento u obtiene las propiedades de la cuenta de almacenamiento especificada. |
> | . | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenera las claves de acceso de la cuenta de almacenamiento especificada. |
> | . | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico de la cuenta de almacenamiento. |
> | . | Microsoft.Storage/storageAccounts/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso. |
> | . | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso. |
> | . | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso. |
> | . | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read | Obtiene la lista de definiciones de métricas de almacenamiento de Microsoft. |
> | . | Microsoft.Storage/storageAccounts/write | Crea una cuenta de almacenamiento con los parámetros especificados o actualiza las propiedades o etiquetas, o agrega un dominio personalizado para la cuenta de almacenamiento especificada. |
> | . | Microsoft.Storage/usages/read | Devuelve el límite y el recuento actual de utilización de recursos en la suscripción especificada |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | microsoft.storagesync/storageSyncServices/delete | Elimina cualquier servicio de sincronización del almacenamiento. |
> | . | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles para los servicios de sincronización de almacenamiento. |
> | . | microsoft.storagesync/storageSyncServices/read | Lee cualquier servicio de sincronización de almacenamiento. |
> | . | microsoft.storagesync/storageSyncServices/registeredServers/delete | Elimina cualquier servidor registrado. |
> | . | microsoft.storagesync/storageSyncServices/registeredServers/read | Lee cualquier servidor registrado. |
> | . | microsoft.storagesync/storageSyncServices/registeredServers/write | Crea o actualiza cualquier servidor registrado. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Eliminar cualquier punto de conexión de nube. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | API de ubicación para las llamadas de copia de seguridad asincrónicas. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Llama a esta acción después de realizar la copia de seguridad. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Llama a esta acción después de realizar el proceso de restauración. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Llama a esta acción antes de realizar la copia de seguridad. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Llama a esta acción antes de realizar el proceso de restauración. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Lee cualquier punto de conexión de nube. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Restaura el latido. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Crea o actualiza cualquier punto de conexión de nube. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/delete | Elimina cualquier grupo de sincronización. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/read | Lee cualquier grupo de sincronización. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Elimina cualquier punto de conexión del servidor. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Lee cualquier punto de conexión del servidor. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Llama a esta acción para recuperar los archivos de un servidor. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Crea o actualiza cualquier punto de conexión del servidor. |
> | . | microsoft.storagesync/storageSyncServices/syncGroups/write | Crea o actualiza cualquier grupo de sincronización. |
> | . | microsoft.storagesync/storageSyncServices/write | Crea o actualiza cualquier servicio de sincronización del almacenamiento. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.StorSimple/managers/accessControlRecords/delete | Elimina los registros de control de acceso |
> | . | Microsoft.StorSimple/managers/accessControlRecords/read | Muestra u obtiene el registro de control de acceso |
> | . | Microsoft.StorSimple/managers/accessControlRecords/write | Crea o actualiza el registro de control de acceso |
> | . | Microsoft.StorSimple/managers/alerts/read | Muestra u obtiene las alertas |
> | . | Microsoft.StorSimple/managers/bandwidthSettings/delete | Elimina una configuración de ancho de banda (solo serie 8000) que ya existe |
> | . | Microsoft.StorSimple/managers/bandwidthSettings/read | Muestra la configuración de ancho de banda (solo serie 8000) |
> | . | Microsoft.StorSimple/managers/bandwidthSettings/write | Crea una nueva configuración de ancho de banda (solo serie 8000) o actualiza la que ya existe |
> | . | Microsoft.StorSimple/Managers/certificates/write | La operación Actualizar certificado de recursos permite actualizar el certificado de credencial de recursos o almacenes. |
> | . | Microsoft.StorSimple/managers/clearAlerts/action | Borra todas las alertas asociadas con el administrador de dispositivos. |
> | . | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Muestra las configuraciones admitidas de la aplicación en la nube |
> | . | Microsoft.StorSimple/managers/configureDevice/action | Configura un dispositivo |
> | . | Microsoft.StorSimple/managers/delete | Elimina los administradores de dispositivos |
> | . | Microsoft.StorSimple/Managers/delete | La operación Delete Vault elimina el recurso de Azure del tipo "almacén" especificado |
> | . | Microsoft.StorSimple/managers/devices/alertSettings/read | Muestra u obtiene la configuración de las alertas |
> | . | Microsoft.StorSimple/managers/devices/alertSettings/write | Crea o actualiza la configuración de las alertas |
> | . | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Realiza una copia de seguridad manual para crear una copia de seguridad a petición de todos los volúmenes protegidos por la directiva. |
> | . | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Elimina una directiva de copia de seguridad existente (solo serie 8000) |
> | . | Microsoft.StorSimple/managers/devices/backupPolicies/read | Enumera las directivas de copia de seguridad (solo serie 8000) |
> | . | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Elimina una programación existente |
> | . | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Enumera las programaciones |
> | . | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Crea una nueva programación o actualiza una que ya existe |
> | . | Microsoft.StorSimple/managers/devices/backupPolicies/write | Crea una nueva directiva de copia de seguridad (solo serie 8000) o actualiza una que ya existe |
> | . | Microsoft.StorSimple/managers/devices/backups/delete | Elimina el conjunto de copia de seguridad |
> | . | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Clona un recurso o volumen compartido mediante un elemento de copia de seguridad. |
> | . | Microsoft.StorSimple/managers/devices/backups/read | Muestra u obtiene el conjunto de copia de seguridad |
> | . | Microsoft.StorSimple/managers/devices/backups/restore/action | Restaurar todos los volúmenes a partir de un conjunto de copias de seguridad. |
> | . | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Elimina los grupos de programación de copias de seguridad |
> | . | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Muestra u obtiene los grupos de programación de copias de seguridad |
> | . | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Crea o actualiza los grupos de programación de copias de seguridad |
> | . | Microsoft.StorSimple/managers/devices/chapSettings/delete | Elimina la configuración de CHAP |
> | . | Microsoft.StorSimple/managers/devices/chapSettings/read | Muestra u obtiene la configuración de CHAP |
> | . | Microsoft.StorSimple/managers/devices/chapSettings/write | Crea o actualiza la configuración de CHAP |
> | . | Microsoft.StorSimple/managers/devices/deactivate/action | Desactiva un dispositivo. |
> | . | Microsoft.StorSimple/managers/devices/delete | Elimina los dispositivos |
> | . | Microsoft.StorSimple/managers/devices/download/action | Descarga actualizaciones para un dispositivo. |
> | . | Microsoft.StorSimple/managers/devices/failover/action | Conmutación por error del dispositivo. |
> | . | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Realiza una copia de seguridad de un servidor de archivos. |
> | . | Microsoft.StorSimple/managers/devices/fileservers/delete | Elimina los servidores de archivos |
> | . | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Muestra u obtiene las métricas |
> | . | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Muestra u obtiene las definiciones de métricas |
> | . | Microsoft.StorSimple/managers/devices/fileservers/read | Muestra u obtiene los servidores de archivos |
> | . | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Elimina los recursos compartidos |
> | . | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Muestra u obtiene las métricas |
> | . | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Muestra u obtiene las definiciones de métricas |
> | . | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Muestra u obtiene los recursos compartidos |
> | . | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Crea o actualiza los recursos compartidos |
> | . | Microsoft.StorSimple/managers/devices/fileservers/write | Crea o actualiza los servidores de archivos |
> | . | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Cambia el estado de energía del controlador de los grupos de componentes de hardware |
> | . | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Muestra los grupos de componentes de hardware |
> | . | Microsoft.StorSimple/managers/devices/install/action | Instala actualizaciones en un dispositivo. |
> | . | Microsoft.StorSimple/managers/devices/installUpdates/action | Instala actualizaciones en los dispositivos |
> | . | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Realiza una copia de seguridad de un servidor iSCSI. |
> | . | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Elimina los servidores de iSCSI |
> | . | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Elimina los discos |
> | . | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Muestra u obtiene las métricas |
> | . | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Muestra u obtiene las definiciones de métricas |
> | . | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Muestra u obtiene los discos |
> | . | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Crea o actualiza los discos |
> | . | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Muestra u obtiene las métricas |
> | . | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Muestra u obtiene las definiciones de métricas |
> | . | Microsoft.StorSimple/managers/devices/iscsiservers/read | Muestra u obtiene los servidores de iSCSI |
> | . | Microsoft.StorSimple/managers/devices/iscsiservers/write | Crea o actualiza los servidores de iSCSI |
> | . | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Cancela un trabajo de ejecución |
> | . | Microsoft.StorSimple/managers/devices/jobs/read | Muestra u obtiene los trabajos |
> | . | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Obtiene la lista de conjuntos de conmutación por error de un dispositivo existente. |
> | . | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Muestra los destinos de conmutación por error de los dispositivos |
> | . | Microsoft.StorSimple/managers/devices/metrics/read | Muestra u obtiene las métricas |
> | . | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Muestra u obtiene las definiciones de métricas |
> | . | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Confirma una migración correcta y la confirma. |
> | . | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Recupera el estado de confirmación de la migración. |
> | . | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Recupera el estado del trabajo de estimación de la migración. |
> | . | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Recupera el estado de la migración. |
> | . | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importa las configuraciones de origen para la migración |
> | . | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Inicia la migración mediante configuraciones de origen |
> | . | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Inicia un trabajo para calcular la duración del proceso de migración. |
> | . | Microsoft.StorSimple/managers/devices/networkSettings/read | Muestra u obtiene la configuración de red |
> | . | Microsoft.StorSimple/managers/devices/networkSettings/write | Crea una nueva configuración de red o actualiza una que ya existe |
> | . | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Muestra la clave de cifrado pública del administrador de dispositivos |
> | . | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publica el paquete de soporte de un dispositivo para la solución de problemas del Soporte técnico de Microsoft. |
> | . | Microsoft.StorSimple/managers/devices/read | Muestra u obtiene los dispositivos |
> | . | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Busca actualizaciones de un dispositivo. |
> | . | Microsoft.StorSimple/managers/devices/securitySettings/read | Muestra la configuración de seguridad |
> | . | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Sincroniza el certificado de administración remota de un dispositivo. |
> | . | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Actualiza la configuración de seguridad. |
> | . | Microsoft.StorSimple/managers/devices/securitySettings/write | Crea una nueva configuración de seguridad o actualiza una que ya existe |
> | . | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Envía un correo electrónico de alerta de prueba a destinatarios configurados de correo electrónico. |
> | . | Microsoft.StorSimple/managers/devices/timeSettings/read | Muestra u obtiene la configuración de hora |
> | . | Microsoft.StorSimple/managers/devices/timeSettings/write | Crea una nueva configuración de hora o actualiza una que ya existe |
> | . | Microsoft.StorSimple/managers/devices/updateSummary/read | Muestra u obtiene el resumen de actualización |
> | . | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Elimina los contenedores de volumen existentes (solo para la serie 8000) |
> | . | Microsoft.StorSimple/managers/devices/volumeContainers/listEncryptionKeys/action | Enumera las claves de cifrado de los contenedores de volumen |
> | . | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Enumera las métricas |
> | . | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Enumera las definiciones de métricas |
> | . | Microsoft.StorSimple/managers/devices/volumeContainers/read | Enumera los contenedores de volumen (solo para la serie 8000) |
> | . | Microsoft.StorSimple/managers/devices/volumeContainers/rolloverEncryptionKey/action | Sustituye las claves de cifrado de los contenedores de volumen |
> | . | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Elimina un volumen existente |
> | . | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Enumera las métricas |
> | . | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Enumera las definiciones de métricas |
> | . | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Enumera los volúmenes |
> | . | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Crea un nuevo volumen o actualiza uno que ya existe |
> | . | Microsoft.StorSimple/managers/devices/volumeContainers/write | Crea un nuevo contenedor de volumen (solo para la serie 8000) o actualiza uno que ya existe |
> | . | Microsoft.StorSimple/managers/devices/write | Crea o actualiza los dispositivos |
> | . | Microsoft.StorSimple/managers/encryptionSettings/read | Muestra u obtiene la configuración de cifrado |
> | . | Microsoft.StorSimple/Managers/extendedInformation/delete | La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén? |
> | . | Microsoft.StorSimple/Managers/extendedInformation/read | La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén? |
> | . | Microsoft.StorSimple/Managers/extendedInformation/write | La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén? |
> | . | Microsoft.StorSimple/managers/getActivationKey/action | Obtiene la clave de activación del administrador de dispositivos. |
> | . | Microsoft.StorSimple/managers/getEncryptionKey/action | Obtiene la clave de cifrado del administrador de dispositivos. |
> | . | Microsoft.StorSimple/managers/listActivationKey/action | Obtiene la clave de activación del administrador de dispositivos de StorSimple. |
> | . | Microsoft.StorSimple/managers/listPrivateEncryptionKey/action | Obtiene una clave de cifrado privada de un administrador de dispositivos de StorSimple. |
> | . | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Enumera las claves de cifrado públicas de un administrador de dispositivos de StorSimple. |
> | . | Microsoft.StorSimple/managers/metrics/read | Muestra u obtiene las métricas |
> | . | Microsoft.StorSimple/managers/metricsDefinitions/read | Muestra u obtiene las definiciones de métricas |
> | . | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Crea una nueva aplicación en la nube. |
> | . | Microsoft.StorSimple/managers/read | Enumera u obtiene los administradores de dispositivos |
> | . | Microsoft.StorSimple/Managers/read | La operación Get Vault obtiene un objeto que representa el recurso de Azure del tipo "almacén" |
> | . | Microsoft.StorSimple/managers/regenarateRegistationCertificate/action | Regenera el certificado de registro de los administradores de dispositivos. |
> | . | Microsoft.StorSimple/managers/regenerateActivationKey/action | Regenera la clave de activación del administrador de dispositivos. |
> | . | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Elimina las credenciales de la cuenta de almacenamiento |
> | . | Microsoft.StorSimple/managers/storageAccountCredentials/listAccessKey/action | Muestra las claves de acceso de las credenciales de la cuenta de almacenamiento |
> | . | Microsoft.StorSimple/managers/storageAccountCredentials/read | Muestra u obtiene las credenciales de la cuenta de almacenamiento |
> | . | Microsoft.StorSimple/managers/storageAccountCredentials/write | Crea o actualiza las credenciales de la cuenta de almacenamiento |
> | . | Microsoft.StorSimple/managers/storageDomains/delete | Elimina los dominios de almacenamiento |
> | . | Microsoft.StorSimple/managers/storageDomains/read | Muestra u obtiene los dominios de almacenamiento |
> | . | Microsoft.StorSimple/managers/storageDomains/write | Crea o actualiza los dominios de almacenamiento |
> | . | Microsoft.StorSimple/managers/write | Crea o actualiza los administradores de dispositivos |
> | . | Microsoft.StorSimple/Managers/write | La operación Create Vault crea un recurso de Azure del tipo "almacén" |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.StreamAnalytics/locations/quotas/Read | Lee la cuota de suscripción de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/operations/Read | Lee las operaciones de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/Register/action | Registra la suscripción con el proveedor de recursos de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/streamingjobs/Delete | Elimina el trabajo de Stream Analytics |
> | . | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Elimina la función de trabajo de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Lee los resultados de la operación de la función de trabajo de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Lee la función de trabajo de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Recupera la definición predeterminada de una función de trabajo de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Prueba la función de trabajo de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Escribe la función de trabajo de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Elimina la entrada del trabajo de Stream Analytics |
> | . | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Lee los resultados de la operación de la entrada del trabajo de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Lee la entrada del trabajo de Stream Analytics |
> | . | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Muestra un ejemplo de la entrada del trabajo de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Prueba la entrada del trabajo de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Escribe la entrada del trabajo de Stream Analytics |
> | . | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Lee las definiciones de métricas. |
> | . | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Lee los resultados de la operación del trabajo de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Elimina la salida del trabajo de Stream Analytics |
> | . | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Lee los resultados de la operación de la salida del trabajo de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Lee la salida del trabajo de Stream Analytics |
> | . | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Prueba la salida del trabajo de Stream Analytics. |
> | . | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Escribe la salida del trabajo de Stream Analytics |
> | . | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Lee la configuración de diagnóstico. |
> | . | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Escribe la configuración de diagnóstico. |
> | . | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Obtiene los registros disponibles de los trabajos del flujo |
> | . | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de los trabajos del flujo |
> | . | Microsoft.StreamAnalytics/streamingjobs/Read | Lee el trabajo de Stream Analytics |
> | . | Microsoft.StreamAnalytics/streamingjobs/Start/action | Inicia el trabajo de Stream Analytics |
> | . | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Detiene el trabajo de Stream Analytics |
> | . | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Elimina la transformación de trabajos de Stream Analytics |
> | . | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Lee la transformación de trabajos de Stream Analytics |
> | . | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Escribe la transformación de trabajos de Stream Analytics |
> | . | Microsoft.StreamAnalytics/streamingjobs/Write | Escribe el trabajo de Stream Analytics |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Subscription/CreateSubscription/action | Cree una suscripción a Azure |
> | . | Microsoft.Subscription/SubscriptionDefinitions/read | Obtiene una definición de la suscripción de Azure dentro de un grupo de administración. |
> | . | Microsoft.Subscription/SubscriptionDefinitions/write | Crea una definición de la suscripción de Azure. |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.Support/register/action | Registra para admitir el proveedor de recursos |
> | . | Microsoft.Support/supportTickets/read | Obtiene los detalles de las incidencias de soporte técnico (incluido el estado, gravedad, detalles de contacto y comunicaciones) u obtiene la lista de incidencias de soporte técnico de las diversas suscripciones. |
> | . | Microsoft.Support/supportTickets/write | Crea o actualiza una incidencia de soporte técnico. Puede crear una incidencia de soporte técnico para problemas relacionados con cuestiones técnicas, de facturación, cuotas o de administración de suscripciones. Puede actualizar la gravedad, los detalles de contacto y las comunicaciones de las incidencias de soporte técnico existentes. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Elimina la directiva de acceso. |
> | . | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Obtiene las propiedades de una directiva de acceso. |
> | . | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Crea una nueva directiva de acceso para un entorno o actualiza una directiva de acceso ya existente. |
> | . | Microsoft.TimeSeriesInsights/environments/delete | Elimina el entorno. |
> | . | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Elimina el origen del evento. |
> | . | Microsoft.TimeSeriesInsights/environments/eventsources/eventsources/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles de los orígenes de los eventos. |
> | . | Microsoft.TimeSeriesInsights/environments/eventsources/read | Obtiene las propiedades de un origen del evento. |
> | . | Microsoft.TimeSeriesInsights/environments/eventsources/write | Crea un nuevo origen del evento para un entorno o actualiza un origen del evento ya existente. |
> | . | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/metricDefinitions/read | Obtiene las métricas disponibles para los entornos. |
> | . | Microsoft.TimeSeriesInsights/environments/read | Obtiene las propiedades de un entorno. |
> | . | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Elimina un conjunto de datos de referencia. |
> | . | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Obtiene las propiedades de un conjunto de datos referencia. |
> | . | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Crea un conjunto de datos de referencia para un entorno o actualiza un conjunto de datos de referencia ya existente. |
> | . | Microsoft.TimeSeriesInsights/environments/status/read | Obtiene el estado del entorno, y el estado de sus operaciones asociadas como las de entrada. |
> | . | Microsoft.TimeSeriesInsights/environments/write | Crea un entorno o actualiza uno ya existente. |
> | . | Microsoft.TimeSeriesInsights/register/action | Registra la suscripción del proveedor de recursos de Time Series Insights y habilita la creación de entornos de Time Series Insights. |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | DESCRIPCIÓN |
> | --- | --- | --- |
> | . | Microsoft.VisualStudio/Account/Delete | Elimina una cuenta |
> | . | Microsoft.VisualStudio/Account/Read | Lee una cuenta |
> | . | Microsoft.VisualStudio/Account/Write | Configure una cuenta |
> | . | Microsoft.VisualStudio/Extension/Delete | Elimina una extensión |
> | . | Microsoft.VisualStudio/Extension/Read | Lee una extensión |
> | . | Microsoft.VisualStudio/Extension/Write | Establece una extensión |
> | . | Microsoft.VisualStudio/Project/Delete | Elimina un proyecto |
> | . | Microsoft.VisualStudio/Project/Read | Lee un proyecto |
> | . | Microsoft.VisualStudio/Project/Write | Establece un proyecto |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | microsoft.web/apimanagementaccounts/apiacls/read | Obtiene las ACL de API de las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Elimina las ACL de API de las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/apiacls/read | Obtiene las ACL de las API de las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/apiacls/write | Actualiza las ACL de las API de las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Obtiene las ACL de Connection de las API para las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Confirma las conexiones de API de las cuentas de API Management del código de consentimiento. |
> | . | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Elimina las ACL de Connection de las API para las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Obtiene las ACL de Connection de las conexiones de API para las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Actualiza las ACL de Connection de las conexiones de API para las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/connections/delete | Elimina conexiones de API para las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Obtiene los vínculos de consentimiento para conexiones de API de las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Enumera las conexiones de API de las cuentas de API Management para las claves de conexión. |
> | . | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Enumera las conexiones de API de las cuentas de API Management para los secretos. |
> | . | microsoft.web/apimanagementaccounts/apis/connections/read | Obtiene las conexiones de API para las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/connections/write | Actualiza las conexiones de API para las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/delete | Elimina las API de las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Elimina las definiciones localizadas de API para las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Obtiene las definiciones localizadas de API para las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Actualiza las definiciones localizadas de API para las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/read | Obtiene las API de las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/apis/write | Actualiza las API de las cuentas de API Management. |
> | . | microsoft.web/apimanagementaccounts/connectionacls/read | Obtiene las ACL de Connection de las cuentas de API Management. |
> | . | microsoft.web/availablestacks/read | Obtiene las pilas disponibles. |
> | . | microsoft.web/billingmeters/read | Obtiene la lista de medidores de facturación. |
> | . | Microsoft.Web/certificates/Delete | Elimina un certificado existente. |
> | . | Microsoft.Web/certificates/Read | Obtiene la lista de certificados. |
> | . | Microsoft.Web/certificates/Write | Agrega un nuevo certificado o actualiza uno existente. |
> | . | microsoft.web/checknameavailability/read | Comprueba si el nombre de recurso está disponible. |
> | . | microsoft.web/classicmobileservices/read | Obtiene Mobile Services clásico. |
> | . | Microsoft.Web/connectionGateways/Delete | Elimina una puerta de enlace de conexión. |
> | . | Microsoft.Web/connectionGateways/Join/Action | Se une a una puerta de enlace de conexión. |
> | . | microsoft.web/connectiongateways/liststatus/action | Enumera las puertas de enlace de la conexión de estado. |
> | . | Microsoft.Web/connectionGateways/ListStatus/Action | Enumera los estados de una puertas de enlace de la conexión. |
> | . | Microsoft.Web/connectionGateways/Move/Action | Mueve una puerta de enlace de conexión. |
> | . | Microsoft.Web/connectionGateways/Read | Obtiene la lista de puertas de enlace de conexiones. |
> | . | Microsoft.Web/connectionGateways/Write | Crea o actualiza una puerta de enlace de conexión. |
> | . | microsoft.web/connections/confirmconsentcode/action | Confirma el código de consentimiento de conexiones. |
> | . | Microsoft.Web/connections/Delete | Elimina una conexión. |
> | . | Microsoft.Web/connections/Join/Action | Se une a una conexión. |
> | . | microsoft.web/connections/listconsentlinks/action | Enumera los vínculos de consentimiento de las conexiones. |
> | . | Microsoft.Web/connections/Move/Action | Mueve una conexión. |
> | . | Microsoft.Web/connections/Read | Obtiene la lista de conexiones. |
> | . | Microsoft.Web/connections/Write | Crea o actualiza una conexión. |
> | . | Microsoft.Web/customApis/Delete | Elimina una API personalizada. |
> | . | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extrae la definición de la API de un archivo WSDL. |
> | . | Microsoft.Web/customApis/Join/Action | Combina una API personalizada. |
> | . | Microsoft.Web/customApis/listWsdlInterfaces/Action | Enumera las interfaces WSDL de una API personalizada. |
> | . | Microsoft.Web/customApis/Move/Action | Mueve una API personalizada. |
> | . | Microsoft.Web/customApis/Read | Obtiene la lista de API personalizadas. |
> | . | Microsoft.Web/customApis/Write | Crea o actualiza una API personalizada. |
> | . | microsoft.web/deploymentlocations/read | Obtiene las ubicaciones de implementación. |
> | . | Microsoft.Web/geoRegions/Read | Obtiene la lista de regiones geográficas. |
> | . | microsoft.web/hostingenvironments/capacities/read | Obtiene las funcionalidades de los entornos de hospedaje. |
> | . | Microsoft.Web/hostingEnvironments/Delete | Elimina un entorno de App Service |
> | . | microsoft.web/hostingenvironments/diagnostics/read | Obtiene los diagnósticos de los entornos de hospedaje. |
> | . | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Obtiene los puntos de conexión de red de todas las dependencias de entrada. |
> | . | microsoft.web/hostingenvironments/metricdefinitions/read | Obtiene las definiciones de métricas de los entornos de hospedaje. |
> | . | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Obtiene las definiciones de métricas de los grupos MultiRole de los entornos de hospedaje. |
> | . | microsoft.web/hostingenvironments/multirolepools/metrics/read | Obtiene las métricas de los grupos MultiRole de los entornos de hospedaje. |
> | . | Microsoft.Web/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/metricDefinitions/Read | Obtiene las métricas disponibles para el multirol de App Service Environment. |
> | . | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Obtiene las propiedades de un grupo de front-end en un entorno de App Service |
> | . | microsoft.web/hostingenvironments/multirolepools/skus/read | Obtiene las SKU de los grupos MultiRole de los entornos de hospedaje. |
> | . | microsoft.web/hostingenvironments/multirolepools/usages/read | Obtiene los usos de los grupos MultiRole de los entornos de hospedaje. |
> | . | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Crea un nuevo grupo de front-end en un entorno de App Service o actualiza uno que ya existe |
> | . | microsoft.web/hostingenvironments/operations/read | Obtiene las operaciones de los entornos de hospedaje. |
> | . | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Obtiene los puntos de conexión de red de todas las dependencias de salida. |
> | . | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.Web/hostingEnvironments/Read | Obtiene las propiedades de un entorno de App Service |
> | . | Microsoft.Web/hostingEnvironments/reboot/Action | Reinicia todas las máquinas de un entorno de App Service |
> | . | microsoft.web/hostingenvironments/resume/action | Reanuda los entornos de hospedaje. |
> | . | microsoft.web/hostingenvironments/serverfarms/read | Obtiene los planes de App Service de los entornos de hospedaje. |
> | . | microsoft.web/hostingenvironments/sites/read | Obtiene las instancias de Web Apps de los entornos de hospedaje. |
> | . | microsoft.web/hostingenvironments/suspend/action | Suspende los entornos de hospedaje. |
> | . | microsoft.web/hostingenvironments/usages/read | Obtiene los usos de los entornos de hospedaje. |
> | . | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Obtiene las definiciones de métricas de los grupos de trabajo de los entornos de hospedaje. |
> | . | microsoft.web/hostingenvironments/workerpools/metrics/read | Obtiene las métricas de los grupos de trabajo de los entornos de hospedaje. |
> | . | Microsoft.Web/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read | Obtiene las métricas disponibles para el conjunto de trabajo de App Service Environment. |
> | . | Microsoft.Web/hostingEnvironments/workerPools/Read | Obtiene las propiedades de un conjunto de trabajo en un entorno de App Service |
> | . | microsoft.web/hostingenvironments/workerpools/skus/read | Obtiene las SKU de los grupos de trabajo de los entornos de hospedaje. |
> | . | microsoft.web/hostingenvironments/workerpools/usages/read | Obtiene los usos de los grupos de trabajo de los entornos de hospedaje. |
> | . | Microsoft.Web/hostingEnvironments/workerPools/Write | Crea un nuevo grupo de trabajo en un entorno de App Service o actualiza uno que ya existe |
> | . | Microsoft.Web/hostingEnvironments/Write | Crea un nuevo entorno de App Service o actualiza uno que ya existe |
> | . | microsoft.web/ishostingenvironmentnameavailable/read | Averigua si el nombre del entorno de hospedaje está disponible. |
> | . | microsoft.web/ishostnameavailable/read | Comprueba si el nombre de host está disponible. |
> | . | microsoft.web/isusernameavailable/read | Comprueba si el nombre de usuario está disponible. |
> | . | Microsoft.Web/listSitesAssignedToHostName/Read | Obtiene los nombres de sitios asignados al nombre de host. |
> | . | microsoft.web/locations/apioperations/read | Obtiene las operaciones de API de ubicaciones. |
> | . | microsoft.web/locations/connectiongatewayinstallations/read | Obtiene las instalaciones de puertas de enlace de conexiones de ubicaciones. |
> | . | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extrae la definición de API de WSDL para las ubicaciones. |
> | . | microsoft.web/locations/listwsdlinterfaces/action | Enumera las interfaces de WSDL para las ubicaciones. |
> | . | microsoft.web/locations/managedapis/apioperations/read | Obtiene las operaciones de API administradas de las ubicaciones. |
> | . | Microsoft.Web/locations/managedapis/Join/Action | Combina una API administrada. |
> | . | microsoft.web/locations/managedapis/read | Obtiene las API administradas de ubicaciones. |
> | . | microsoft.web/operations/read | Obtiene operaciones. |
> | . | microsoft.web/publishingusers/read | Obtiene los usuarios de publicación. |
> | . | microsoft.web/publishingusers/write | Actualiza los usuarios de publicación. |
> | . | Microsoft.Web/recommendations/Read | Obtiene la lista de recomendaciones de las suscripciones. |
> | . | microsoft.web/register/action | Registra el proveedor de recursos de Microsoft.Web de la suscripción. |
> | . | microsoft.web/resourcehealthmetadata/read | Obtiene los metadatos de Resource Health. |
> | . | microsoft.web/serverfarms/capabilities/read | Obtiene las funcionalidades de los planes de App Service. |
> | . | Microsoft.Web/serverfarms/Delete | Eliminación de un plan de App Service |
> | . | microsoft.web/serverfarms/firstpartyapps/settings/delete | Elimina la configuración de aplicaciones propias de los planes de App Service. |
> | . | microsoft.web/serverfarms/firstpartyapps/settings/read | Obtiene la configuración de aplicaciones propias de los planes de App Service. |
> | . | microsoft.web/serverfarms/firstpartyapps/settings/write | Actualiza la configuración de aplicaciones propias de los planes de App Service. |
> | . | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Elimina las retransmisiones de espacios de nombres de conexión híbrida de los planes de App Service. |
> | . | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Obtiene las retransmisiones de espacios de nombres de conexión híbrida de los planes de App Service. |
> | . | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Obtiene las instancias de Web Apps de las retransmisiones de espacios de nombres de conexión híbrida de los planes de App Service. |
> | . | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Obtiene los límites del plan de conexión híbrida de los planes de App Service. |
> | . | microsoft.web/serverfarms/hybridconnectionrelays/read | Obtiene las retransmisiones de conexión híbrida de los planes de App Service. |
> | . | microsoft.web/serverfarms/metricdefinitions/read | Obtiene las definiciones de métricas de los planes de App Service. |
> | . | microsoft.web/serverfarms/metrics/read | Obtiene las métricas de los planes de App Service. |
> | . | microsoft.web/serverfarms/operationresults/read | Obtiene los resultados de la operación de planes de App Service. |
> | . | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.Web/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read | Obtiene las métricas disponibles para un plan de App Service. |
> | . | Microsoft.Web/serverfarms/Read | Obtiene las propiedades de un plan de App Service |
> | . | Microsoft.Web/serverfarms/restartSites/Action | Reinicia todas las instancias de Web Apps de un plan de App Service |
> | . | microsoft.web/serverfarms/sites/read | Obtiene las instancias de Web Apps de los planes de App Service. |
> | . | microsoft.web/serverfarms/skus/read | Obtiene las SKU de los planes de App Service. |
> | . | microsoft.web/serverfarms/usages/read | Obtiene los usos de los planes de App Service. |
> | . | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Actualiza las puertas de enlace de las conexiones de red virtual de los planes de App Service. |
> | . | microsoft.web/serverfarms/virtualnetworkconnections/read | Obtiene las conexiones de red virtual de los planes de App Service. |
> | . | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Elimina las rutas de las conexiones de red virtual de los planes de App Service. |
> | . | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Obtiene las rutas de las conexiones de red virtual de los planes de App Service. |
> | . | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Actualiza las rutas de las conexiones de red virtual de los planes de App Service. |
> | . | microsoft.web/serverfarms/workers/reboot/action | Reinicia los trabajos de los planes de App Service. |
> | . | Microsoft.Web/serverfarms/Write | Crea un nuevo plan de App Service o actualiza uno que ya existe |
> | . | microsoft.web/sites/analyzecustomhostname/read | Analiza el nombre de host personalizado. |
> | . | Microsoft.Web/sites/applySlotConfig/Action | Aplica la configuración de ranuras de la aplicación web desde la ranura de destino a la aplicación web actual |
> | . | Microsoft.Web/sites/backup/Action | Crea una copia de seguridad de una nueva aplicación web |
> | . | microsoft.web/sites/backup/read | Obtiene una copia de seguridad de Web Apps. |
> | . | microsoft.web/sites/backup/write | Actualiza la copia de seguridad de Web Apps. |
> | . | microsoft.web/sites/backups/delete | Elimina las copias de seguridad de Web Apps. |
> | . | microsoft.web/sites/backups/list/action | Muestra las copias de seguridad de Web Apps. |
> | . | Microsoft.Web/sites/backups/Read | Obtiene las propiedades de una copia de seguridad de una aplicación web |
> | . | microsoft.web/sites/backups/restore/action | Restaura las copias de seguridad de Web Apps. |
> | . | microsoft.web/sites/config/delete | Elimina la configuración de Web Apps. |
> | . | Microsoft.Web/sites/config/list/Action | Muestra las opciones confidenciales de seguridad de Web Apps como las credenciales de publicación, la configuración de la aplicación y las cadenas de conexión |
> | . | Microsoft.Web/sites/config/Read | Obtiene las opciones de configuración de Web Apps |
> | . | Microsoft.Web/sites/config/Write | Actualiza las opciones de configuración de Web Apps |
> | . | microsoft.web/sites/continuouswebjobs/delete | Elimina WebJobs continuos de Web Apps. |
> | . | microsoft.web/sites/continuouswebjobs/read | Obtiene WebJobs continuos de Web Apps. |
> | . | microsoft.web/sites/continuouswebjobs/start/action | Inicia WebJobs continuos de Web Apps. |
> | . | microsoft.web/sites/continuouswebjobs/stop/action | Detiene WebJobs continuos de Web Apps. |
> | . | Microsoft.Web/sites/Delete | Eliminación de una aplicación web existente |
> | . | microsoft.web/sites/deployments/delete | Elimina las implementaciones de Web Apps. |
> | . | microsoft.web/sites/deployments/log/read | Obtiene el registro de las implementaciones de Web Apps. |
> | . | microsoft.web/sites/deployments/read | Obtiene las implementaciones de Web Apps. |
> | . | microsoft.web/sites/deployments/write | Actualiza las implementaciones de Web Apps. |
> | . | microsoft.web/sites/diagnostics/analyses/execute/Action | Ejecuta el análisis de diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/analyses/read | Obtiene el análisis de diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/aspnetcore/read | Obtiene los diagnósticos de Web Apps para la aplicación de ASP.NET Core. |
> | . | microsoft.web/sites/diagnostics/autoheal/read | Obtiene la característica AutoHeal para los diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/deployment/read | Obtiene la implementación de los diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/deployments/read | Obtiene las implementaciones de los diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/detectors/execute/Action | Ejecuta el detector de diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/detectors/read | Obtiene el detector de diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Obtiene las solicitudes erróneas de diagnóstico de Web Apps según la URI. |
> | . | microsoft.web/sites/diagnostics/frebanalysis/read | Obtiene el análisis FREB de diagnóstico de Web Apps. |
> | . | microsoft.web/sites/diagnostics/loganalyzer/read | Obtiene el analizador de registros de diagnóstico de Web Apps. |
> | . | microsoft.web/sites/diagnostics/read | Obtiene las categorías de los diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/runtimeavailability/read | Obtiene la disponibilidad de tiempo de ejecución de diagnóstico de Web Apps. |
> | . | microsoft.web/sites/diagnostics/servicehealth/read | Obtiene el estado del servicio de diagnóstico de Web Apps. |
> | . | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Obtiene el análisis de CPU del sitio de diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/sitecrashes/read | Obtiene los bloqueos del sitio de diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/sitelatency/read | Obtiene la latencia del sitio de diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Obtiene el análisis de memoria del sitio de diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Obtiene la actualización de la configuración de reinicio del sitio de diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Obtiene el proceso de reinicio del sitio de diagnósticos de Web Apps que realizó el usuario. |
> | . | microsoft.web/sites/diagnostics/siteswap/read | Obtiene el intercambio del sitio de diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/threadcount/read | Obtiene el número de subprocesos del sitio de diagnósticos de Web Apps. |
> | . | microsoft.web/sites/diagnostics/workeravailability/read | Obtiene el valor de Workeravailability de diagnóstico de Web Apps. |
> | . | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Obtiene el reciclaje del proceso de trabajo de diagnóstico de Web Apps. |
> | . | microsoft.web/sites/domainownershipidentifiers/read | Obtiene identificadores de propiedad de dominio de Web Apps. |
> | . | microsoft.web/sites/domainownershipidentifiers/write | Actualiza identificadores de propiedad de dominio de Web Apps. |
> | . | microsoft.web/sites/functions/action | Funciones de Web Apps. |
> | . | microsoft.web/sites/functions/delete | Elimina funciones de Web Apps. |
> | . | microsoft.web/sites/functions/listsecrets/action | Muestra secretos de Web Apps. |
> | . | microsoft.web/sites/functions/masterkey/read | Obtiene la clave maestra de las funciones de Web Apps. |
> | . | microsoft.web/sites/functions/read | Obtiene funciones de Web Apps. |
> | . | microsoft.web/sites/functions/token/read | Obtiene el token de las funciones de Web Apps. |
> | . | microsoft.web/sites/functions/write | Actualiza funciones de Web Apps. |
> | . | microsoft.web/sites/hostnamebindings/delete | Elimina enlaces de nombre de host de Web Apps. |
> | . | microsoft.web/sites/hostnamebindings/read | Obtiene enlaces de nombre de host de Web Apps. |
> | . | microsoft.web/sites/hostnamebindings/write | Actualiza enlaces de nombre de host de Web Apps. |
> | . | microsoft.web/sites/hybridconnection/delete | Elimina la conexión híbrida de Web Apps. |
> | . | microsoft.web/sites/hybridconnection/read | Obtiene la conexión híbrida de Web Apps. |
> | . | microsoft.web/sites/hybridconnection/write | Actualiza la conexión híbrida de Web Apps. |
> | . | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Elimina las retransmisiones de espacios de nombres de conexión híbrida de Web Apps. |
> | . | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Enumera las claves de las retransmisiones de espacios de nombres de conexión híbrida de Web Apps. |
> | . | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Obtiene las retransmisiones de espacios de nombres de conexión híbrida de Web Apps. |
> | . | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Actualiza las retransmisiones de espacios de nombres de conexión híbrida de Web Apps. |
> | . | microsoft.web/sites/hybridconnectionrelays/read | Obtiene las retransmisiones de conexiones híbridas de Web Apps. |
> | . | microsoft.web/sites/instances/deployments/delete | Elimina las implementaciones de instancias de Web Apps. |
> | . | microsoft.web/sites/instances/deployments/read | Obtiene implementaciones de instancias de Web Apps. |
> | . | microsoft.web/sites/instances/extensions/log/read | Obtiene el registro de extensiones de instancias de Web Apps. |
> | . | microsoft.web/sites/instances/extensions/read | Obtiene las extensiones de instancias de Web Apps. |
> | . | microsoft.web/sites/instances/processes/delete | Elimina procesos de instancias de Web Apps. |
> | . | microsoft.web/sites/instances/processes/read | Obtiene procesos de instancias de Web Apps. |
> | . | microsoft.web/sites/instances/read | Obtiene instancias de Web Apps. |
> | . | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Enumera las instancias de Web Apps de estado del desencadenador de funciones de sincronización. |
> | . | microsoft.web/sites/metricdefinitions/read | Obtiene las definiciones de métricas de Web Apps. |
> | . | microsoft.web/sites/metrics/read | Obtiene las métricas de Web Apps. |
> | . | microsoft.web/sites/metricsdefinitions/read | Obtiene las definiciones de métricas de Web Apps. |
> | . | microsoft.web/sites/migratemysql/action | Migra Web Apps de MySql. |
> | . | microsoft.web/sites/migratemysql/read | Consigue que Web Apps migre de MySql. |
> | . | microsoft.web/sites/networktrace/action | Realiza un seguimiento de red de las instancias de Web Apps. |
> | . | microsoft.web/sites/newpassword/action | Genera una nueva contraseña para las instancias de Web Apps. |
> | . | microsoft.web/sites/operationresults/read | Obtiene los resultados de la operación de Web Apps. |
> | . | microsoft.web/sites/operations/read | Obtiene las operaciones de Web Apps. |
> | . | microsoft.web/sites/perfcounters/read | Obtiene los contadores de rendimiento de Web Apps. |
> | . | microsoft.web/sites/premieraddons/delete | Elimina complementos Premier de Web Apps. |
> | . | microsoft.web/sites/premieraddons/read | Obtiene complementos Premier de Web Apps. |
> | . | microsoft.web/sites/premieraddons/write | Actualiza complementos Premier de Web Apps. |
> | . | microsoft.web/sites/processes/read | Obtiene los procesos de Web Apps. |
> | . | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.Web/sites/providers/Microsoft.Insights/metricDefinitions/Read | Obtiene las métricas disponibles de Web Apps. |
> | . | microsoft.web/sites/publiccertificates/delete | Elimina los certificados públicos de Web Apps. |
> | . | microsoft.web/sites/publiccertificates/read | Obtiene los certificados públicos de Web Apps. |
> | . | microsoft.web/sites/publiccertificates/write | Actualiza los certificados públicos de Web Apps. |
> | . | Microsoft.Web/sites/publish/Action | Publica una aplicación web |
> | . | Microsoft.Web/sites/publishxml/Action | Obtiene el xml del perfil de publicación de una aplicación web |
> | . | microsoft.web/sites/publishxml/read | Obtiene el XML de publicación de Web Apps. |
> | . | Microsoft.Web/sites/Read | Obtiene las propiedades de una aplicación web |
> | . | microsoft.web/sites/recommendationhistory/read | Obtiene el historial de recomendaciones de Web Apps. |
> | . | microsoft.web/sites/recommendations/disable/action | Deshabilita las recomendaciones de Web Apps. |
> | . | Microsoft.Web/sites/recommendations/Read | Obtiene la lista de recomendaciones de la aplicación web. |
> | . | microsoft.web/sites/recover/action | Recupera Web Apps. |
> | . | Microsoft.Web/sites/resetSlotConfig/Action | Restablece la configuración de la aplicación web |
> | . | microsoft.web/sites/resourcehealthmetadata/read | Obtiene los metadatos de Resource Health de Web Apps. |
> | . | Microsoft.Web/sites/restart/Action | Reinicia una aplicación web |
> | . | microsoft.web/sites/restore/read | Obtiene la restauración de Web Apps. |
> | . | microsoft.web/sites/restore/write | Restaura Web Apps. |
> | . | microsoft.web/sites/restoresnapshot/action | Restaura las instantáneas de Web Apps. |
> | . | microsoft.web/sites/siteextensions/delete | Elimina las extensiones de sitio de Web Apps. |
> | . | microsoft.web/sites/siteextensions/read | Obtiene las extensiones de sitio de Web Apps. |
> | . | microsoft.web/sites/siteextensions/write | Actualiza las extensiones de sitio de Web Apps. |
> | . | microsoft.web/sites/slots/analyzecustomhostname/read | Obtiene el nombre de host personalizado del análisis de ranuras de Web Apps. |
> | . | Microsoft.Web/sites/slots/applySlotConfig/Action | Aplica una configuración de ranuras de la aplicación web desde la ranura de destino a la ranura actual. |
> | . | Microsoft.Web/sites/slots/backup/Action | Crea una nueva copia de seguridad de ranura de aplicación web. |
> | . | microsoft.web/sites/slots/backup/read | Obtiene las copias de seguridad de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/backup/write | Actualiza la copia de seguridad de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/backups/delete | Elimina las copias de seguridad de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/backups/list/action | Muestra las copias de seguridad de ranuras de Web Apps. |
> | . | Microsoft.Web/sites/slots/backups/Read | Obtiene las propiedades de una copia de seguridad de las ranuras de una aplicación web |
> | . | microsoft.web/sites/slots/backups/restore/action | Restaura las copias de seguridad de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/config/delete | Elimina la configuración de ranuras de Web Apps. |
> | . | Microsoft.Web/sites/slots/config/list/Action | Muestra las opciones confidenciales de seguridad de ranuras de Web Apps como las credenciales de publicación, la configuración de la aplicación y las cadenas de conexión |
> | . | Microsoft.Web/sites/slots/config/Read | Obtiene las opciones de configuración de ranura de Web Apps |
> | . | Microsoft.Web/sites/slots/config/Write | Actualiza las opciones de configuración de ranura de Web Apps |
> | . | microsoft.web/sites/slots/continuouswebjobs/delete | Elimina WebJobs continuos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/continuouswebjobs/read | Obtiene WebJobs continuos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/continuouswebjobs/start/action | Inicia WebJobs continuos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/continuouswebjobs/stop/action | Detiene WebJobs continuos de ranuras de Web Apps. |
> | . | Microsoft.Web/sites/slots/Delete | Elimina una ranura de aplicación web existente |
> | . | microsoft.web/sites/slots/deployments/delete | Elimina las implementaciones de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/deployments/log/read | Obtiene el registro de implementaciones de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/deployments/read | Obtiene las implementaciones de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/deployments/write | Actualiza las implementaciones de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Ejecuta el análisis de diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/analyses/read | Obtiene el análisis de diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Obtiene los diagnósticos de ranuras de Web Apps para la aplicación de ASP.NET Core. |
> | . | microsoft.web/sites/slots/diagnostics/autoheal/read | Obtiene la característica AutoHeal para los diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/deployment/read | Obtiene la implementación para los diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/deployments/read | Obtiene las implementaciones para los diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Ejecuta el detector de diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/detectors/read | Obtiene el detector de diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Obtiene el análisis FREB de los diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Obtiene el analizador de registros de diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/read | Obtiene los diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Obtiene la disponibilidad de tiempo de ejecución de los diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/servicehealth/read | Obtiene una instancia de Service Health para los diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Obtiene el análisis de CPU del sitio de diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Obtiene los bloqueos del sitio de diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/sitelatency/read | Obtiene la latencia del sitio de diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Obtiene el análisis de memoria del sitio de diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Obtiene la actualización de la configuración de reinicio del sitio de diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Obtiene proceso de reinicio el sitio de diagnósticos de ranuras de Web Apps que realizó el usuario. |
> | . | microsoft.web/sites/slots/diagnostics/siteswap/read | Obtiene el intercambio del sitio de diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/threadcount/read | Obtiene número de subprocesos del sitio de diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/workeravailability/read | Obtiene el valor de Workeravailability de los diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Obtiene el reciclaje del proceso de trabajo de los diagnósticos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/domainownershipidentifiers/read | Obtiene los identificadores de propiedad del dominio de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/hostnamebindings/delete | Elimina enlaces de nombre de host de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/hostnamebindings/read | Obtiene enlaces de nombre de host de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/hostnamebindings/write | Actualiza enlaces de nombre de host de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/hybridconnection/delete | Elimine la conexión híbrida de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/hybridconnection/read | Obtiene la conexión híbrida de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/hybridconnection/write | Actualiza la conexión híbrida de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Elimina las retransmisiones de espacios de nombres de conexión híbrida de las ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Actualiza las retransmisiones de espacios de nombres de conexión híbrida de las ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/hybridconnectionrelays/read | Obtiene las retransmisiones de conexión híbrida de las ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/instances/deployments/read | Obtiene implementaciones de instancias de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/instances/processes/delete | Elimina los procesos de instancias de las ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/instances/processes/read | Obtiene procesos de instancias de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/instances/read | Obtiene instancias de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/metricdefinitions/read | Obtiene las definiciones de métricas de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/metrics/read | Obtiene las métricas de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/migratemysql/read | Consigue que las ranuras de Web Apps migren de MySql. |
> | . | microsoft.web/sites/slots/networktrace/action | Realiza un seguimiento de red de las ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/newpassword/action | Genera una nueva contraseña para las ranuras de instancias de Web Apps. |
> | . | microsoft.web/sites/slots/operationresults/read | Obtiene los resultados de la operación de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/operations/read | Obtiene la operación de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/perfcounters/read | Obtiene los contadores de rendimiento de las ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/phplogging/read | Obtiene Phplogging de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/premieraddons/delete | Elimina complementos Premier de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/premieraddons/read | Obtiene complementos Premier de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/premieraddons/write | Actualiza complementos Premier de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read | Obtiene la configuración de diagnóstico del recurso |
> | . | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write | Crea o actualiza la configuración de diagnóstico del recurso |
> | . | Microsoft.Web/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read | Obtiene las métricas disponibles para la ranura de Web Apps. |
> | . | microsoft.web/sites/slots/publiccertificates/read | Obtiene los certificados públicos de las ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/publiccertificates/write | Crea o actualiza los certificados públicos de las ranuras de Web Apps. |
> | . | Microsoft.Web/sites/slots/publish/Action | Publica una ranura de aplicación web |
> | . | Microsoft.Web/sites/slots/publishxml/Action | Obtiene el xml del perfil de publicación de una ranura de aplicación web |
> | . | Microsoft.Web/sites/slots/Read | Obtiene las propiedades de una ranura de implementación de aplicación web |
> | . | microsoft.web/sites/slots/recover/action | Recupera ranuras de Web Apps. |
> | . | Microsoft.Web/sites/slots/resetSlotConfig/Action | Restablece la configuración de ranuras de la aplicación web |
> | . | microsoft.web/sites/slots/resourcehealthmetadata/read | Obtiene los metadatos de Resource Health de las ranuras de Web Apps. |
> | . | Microsoft.Web/sites/slots/restart/Action | Reinicia una ranura de aplicación web |
> | . | microsoft.web/sites/slots/restore/read | Obtiene la restauración de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/restore/write | Restaura las ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/restoresnapshot/action | Restaura instantáneas de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/siteextensions/delete | Elimina las extensiones de sitio de las ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/siteextensions/read | Obtiene las extensiones de sitio de las ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/siteextensions/write | Actualiza las extensiones de sitio de las ranuras de Web Apps. |
> | . | Microsoft.Web/sites/slots/slotsdiffs/Action | Obtiene las diferencias de configuración entre la aplicación web y las ranuras |
> | . | Microsoft.Web/sites/slots/slotsswap/Action | Intercambia ranuras de implementación de aplicación web |
> | . | microsoft.web/sites/slots/snapshots/read | Obtiene instantáneas de las ranuras de Web Apps. |
> | . | Microsoft.Web/sites/slots/sourcecontrols/Delete | Elimina las opciones de configuración de control de código fuente de ranuras de Web Apps |
> | . | Microsoft.Web/sites/slots/sourcecontrols/Read | Obtiene las opciones de configuración de control de código fuente de ranuras de Web Apps |
> | . | Microsoft.Web/sites/slots/sourcecontrols/Write | Actualiza las opciones de configuración de control de código fuente de ranuras de Web Apps |
> | . | Microsoft.Web/sites/slots/start/Action | Inicia una ranura de aplicación web |
> | . | Microsoft.Web/sites/slots/stop/Action | Detiene una ranura de aplicación web |
> | . | microsoft.web/sites/slots/sync/action | Sincroniza ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/triggeredwebjobs/delete | Elimina WebJobs desencadenados de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/triggeredwebjobs/read | Obtiene WebJobs desencadenados de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/triggeredwebjobs/run/action | Ejecuta WebJobs desencadenados de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/usages/read | Obtiene los usos de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/virtualnetworkconnections/delete | Elimina conexiones de red virtual de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Actualiza puertas de enlace de conexiones de red virtual de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/virtualnetworkconnections/read | Obtiene conexiones de red virtual de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/virtualnetworkconnections/write | Actualiza conexiones de red virtual de ranuras de Web Apps. |
> | . | microsoft.web/sites/slots/webjobs/read | Obtiene los WebJobs de ranuras de Web Apps. |
> | . | Microsoft.Web/sites/slots/Write | Crea una nueva ranura de aplicación web o actualiza una ya existente |
> | . | Microsoft.Web/sites/slotsdiffs/Action | Obtiene las diferencias de configuración entre la aplicación web y las ranuras |
> | . | Microsoft.Web/sites/slotsswap/Action | Intercambia ranuras de implementación de aplicación web |
> | . | microsoft.web/sites/snapshots/read | Obtiene instantáneas de Web Apps. |
> | . | Microsoft.Web/sites/sourcecontrols/Delete | Elimina las opciones de configuración de control de código fuente de Web Apps |
> | . | Microsoft.Web/sites/sourcecontrols/Read | Obtiene las opciones de configuración de control de código fuente de Web Apps |
> | . | Microsoft.Web/sites/sourcecontrols/Write | Actualiza las opciones de configuración de control de código fuente de Web Apps |
> | . | Microsoft.Web/sites/start/Action | Inicia una aplicación web |
> | . | Microsoft.Web/sites/stop/Action | Detiene una aplicación web |
> | . | microsoft.web/sites/sync/action | Sincroniza las instancias de Web Apps. |
> | . | microsoft.web/sites/syncfunctiontriggers/action | Desencadenadores de función de sincronización para Web Apps. |
> | . | microsoft.web/sites/triggeredwebjobs/delete | Elimina WebJobs desencadenados de Web Apps. |
> | . | microsoft.web/sites/triggeredwebjobs/history/read | Obtiene el historial de WebJobs desencadenado en Web Apps. |
> | . | microsoft.web/sites/triggeredwebjobs/read | Obtiene WebJobs desencadenados de Web Apps. |
> | . | microsoft.web/sites/triggeredwebjobs/run/action | Ejecuta WebJobs desencadenados de Web Apps. |
> | . | microsoft.web/sites/usages/read | Obtiene los usos de Web Apps. |
> | . | microsoft.web/sites/virtualnetworkconnections/delete | Elimina conexiones de red virtual de Web Apps. |
> | . | microsoft.web/sites/virtualnetworkconnections/gateways/read | Obtiene puertas de enlace de conexiones de red virtual de Web Apps. |
> | . | microsoft.web/sites/virtualnetworkconnections/gateways/write | Actualiza puertas de enlace de conexiones de red virtual de Web Apps. |
> | . | microsoft.web/sites/virtualnetworkconnections/read | Obtiene conexiones de red virtual de Web Apps. |
> | . | microsoft.web/sites/virtualnetworkconnections/write | Actualiza conexiones de red virtual de Web Apps. |
> | . | microsoft.web/sites/webjobs/read | Obtiene los WebJobs de Web Apps. |
> | . | Microsoft.Web/sites/Write | Crea una nueva aplicación web o actualiza una ya existente |
> | . | microsoft.web/skus/read | Obtiene las SKU. |
> | . | microsoft.web/sourcecontrols/read | Obtiene los controles de origen. |
> | . | microsoft.web/sourcecontrols/write | Actualiza los controles de origen. |
> | . | microsoft.web/unregister/action | Anula el registro del proveedor de recursos de Microsoft.Web de la suscripción. |
> | . | microsoft.web/validate/action | Valida. |
> | . | microsoft.web/verifyhostingenvironmentvnet/action | Comprueba la red virtual del entorno de hospedaje. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de acción | Operación | Descripción |
> | --- | --- | --- |
> | . | Microsoft.WorkloadMonitor/components/read | Lee los recursos de las operaciones. |
> | . | Microsoft.WorkloadMonitor/healthInstances/read | Lee los recursos de las operaciones. |
> | . | Microsoft.WorkloadMonitor/Operations/read | Lee los recursos de las operaciones. |
> | . | Microsoft.WorkloadMonitor/workloads/delete | Elimina un recurso de carga de trabajo. |
> | . | Microsoft.WorkloadMonitor/workloads/read | Lee un recurso de carga de trabajo. |
> | . | Microsoft.WorkloadMonitor/workloads/write | Escribe un recurso de carga de trabajo. |

## <a name="next-steps"></a>Pasos siguientes

- [Roles personalizados](custom-roles.md)
- [Roles integrados](built-in-roles.md)
