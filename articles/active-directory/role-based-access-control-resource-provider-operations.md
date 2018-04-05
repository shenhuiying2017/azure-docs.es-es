---
title: Operaciones del proveedor de Azure Resource Manager | Microsoft Docs
description: Los detalles de las operaciones están disponibles en los proveedores de recursos de Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.openlocfilehash: 0b8c8823c6d21df96dcfd926db1855169f1570e4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operaciones del proveedor de recursos de Azure Resource Manager

Este documento enumera las operaciones que están disponibles para cada proveedor de recursos de Microsoft Azure Resource Manager. Estos se pueden usar en roles personalizados para proporcionar permisos granulares de control de acceso basado en roles (RBAC) a los recursos de Azure. Tenga en cuenta que esta no es una lista exhaustiva y que se pueden agregar o eliminar operaciones a medida que se actualiza cada proveedor. Las cadenas de operaciones usan el formato `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Si desea consultar una lista exhaustiva y actualizada, use `Get-AzureRmProviderOperation` (en PowerShell) o `az provider operation list` (en la CLI de Azure v2) para mostrar las operaciones de los proveedores de recursos de Azure.

## <a name="microsoftaad"></a>Microsoft.AAD

| Operación | DESCRIPCIÓN |
|---|---|
|/domainServices/delete|Elimina los servicios de dominio.|
|/domainServices/read|Lee los servicios de dominio.|
|/domainServices/write|Escribe en los servicios de dominio.|
|/locations/operationresults/read|Lee el estado de una operación asincrónica.|
|/Operations/read|Descripción localizada de la operación, tal y como se debería mostrar al usuario.|

## <a name="microsoftaadiam"></a>microsoft.aadiam

| Operación | DESCRIPCIÓN |
|---|---|
|/diagnosticsettings/delete|Eliminar una configuración de diagnóstico.|
|/diagnosticsettings/read|Leer una configuración de diagnóstico.|
|/diagnosticsettings/write|Escribir en una configuración de diagnóstico.|
|/diagnosticsettingscategories/read|Leer las categorías de la configuración de diagnóstico.|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/tenants/providers/Microsoft.Insights/logDefinitions/read|Obtiene los registros disponibles de los inquilinos.|

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Operación | DESCRIPCIÓN |
|---|---|
|/configuration/action|Actualiza la configuración del inquilino.|
|/configuration/read|Lee la configuración de inquilino.|
|/configuration/write|Crea una configuración de inquilino.|
|/services/action|Actualiza una instancia de servicio en el inquilino.|
|/services/alerts/read|Lee las alertas de un servicio.|
|/services/alerts/read|Lee las alertas de un servicio.|
|/services/delete|Elimina una instancia de servicio del inquilino.|
|/services/read|Lee las instancias de servicio del inquilino.|
|/services/servicemembers/action|Crea una instancia de miembro de servicio del inquilino.|
|/services/servicemembers/alerts/read|Lee las alertas de un miembro de servicio.|
|/services/servicemembers/delete|Elimina una instancia de miembro de servicio del inquilino.|
|/services/servicemembers/read|Lee una instancia de miembro de servicio del inquilino.|
|/services/write|Crea una instancia de servicio del inquilino.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Operación | DESCRIPCIÓN |
|---|---|
|/configurations/read|Obtener configuraciones|
|/configurations/write|Crea o actualiza la configuración.|
|/generateRecommendations/action|Genera recomendaciones|
|/generateRecommendations/read|Obtiene el estado de las recomendaciones generadas|
|/operations/read|Obtiene las operaciones de Microsoft Advisor.|
|/recommendations/read|Lee las recomendaciones|
|/recommendations/suppressions/delete|Elimina las supresiones|
|/recommendations/suppressions/read|Obtiene las supresiones|
|/recommendations/suppressions/write|Crea o actualiza supresiones|
|/register/action|Registra la suscripción de Microsoft Advisor|
|/suppressions/delete|Elimina las supresiones|
|/suppressions/read|Obtiene las supresiones|
|/suppressions/write|Crea o actualiza supresiones|
|/unregister/action|Anula el registro de la suscripción de Microsoft Advisor.|

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

| Operación | DESCRIPCIÓN |
|---|---|
|/alerts/read|Obtiene todas las alertas de los filtros de entrada.|
|/alerts/resolve/action|Cambia el estado de la alerta a "Resolve".|
|/alertsSummary/read|Obtiene el resumen de las alertas.|
|/Operations/read|Lee las operaciones proporcionadas.|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Operación | DESCRIPCIÓN |
|---|---|
|/locations/checkNameAvailability/action|Comprueba que el nombre dado de la instancia de Analysis Server es válido y no está en uso.|
|/locations/operationresults/read|Recupera la información del resultado de la operación especificada.|
|/locations/operationstatuses/read|Recupera la información del estado de la operación especificada.|
|/operations/read|Recupera la información de las operaciones.|
|/register/action|Registra el proveedor de recursos de Analysis Services.|
|/servers/delete|Elimina la instancia de Analysis Server.|
|/servers/listGatewayStatus/action|Enumera el estado de la puerta de enlace asociada con el servidor.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico de Analysis Server.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de Analysis Server.|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Obtiene los registros disponibles de los servidores.|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de Analysis Server.|
|/servers/read|Recupera la información de la instancia de Analysis Server especificada.|
|/servers/resume/action|Reanuda la instancia de Analysis Server.|
|/servers/skus/read|Recupera la información de SKU disponible del servidor.|
|/servers/suspend/action|Suspende la instancia de Analysis Server.|
|/servers/write|Crea o actualiza la instancia de Analysis Server especificada.|
|/skus/read|Recupera la información de las SKU.|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Operación | DESCRIPCIÓN |
|---|---|
|/checkNameAvailability/read|Comprueba si el nombre del servicio proporcionado está disponible|
|/operations/read|Lee todas las operaciones de API disponibles del recurso Microsoft.ApiManagement.|
|/register/action|Registra la suscripción para el proveedor de recursos de Microsoft.ApiManagement|
|/reports/read|Obtiene informes agregados según los períodos de tiempo, la región geográfica, los desarrolladores, los productos, las API, las operaciones, la suscripción y el elemento byRequest.|
|/service/apis/delete|Elimina una API ya existente|
|/service/apis/diagnostics/delete|Elimina un diagnóstico ya existente.|
|/service/apis/diagnostics/loggers/delete|Quita la asignación de un registrador con una configuración de diagnóstico.|
|/service/apis/diagnostics/loggers/read|Obtiene una lista de registradores de diagnóstico.|
|/service/apis/diagnostics/loggers/write|Asigna un registrador a una configuración de diagnóstico.|
|/service/apis/diagnostics/read|Obtiene una lista de diagnósticos u obtiene detalles del diagnóstico.|
|/service/apis/diagnostics/write|Agrega un nuevo diagnóstico o actualiza los detalles de los diagnóstico existentes.|
|/service/apis/operations/delete|Elimina una operación de API ya existente|
|/service/apis/operations/policies/delete|Elimina la configuración de directivas de las directivas de API Operation.|
|/service/apis/operations/policies/read|Obtiene las directivas de API Operation u obtiene los detalles de configuración de directivas de API Operation.|
|/service/apis/operations/policies/write|Establece los detalles de configuración de directivas de API Operation.|
|/service/apis/operations/policy/delete|Elimina la configuración de directivas de una operación|
|/service/apis/operations/policy/read|Obtiene los detalles de configuración de directivas de una operación|
|/service/apis/operations/policy/write|Establece los detalles de configuración de directivas de una operación|
|/service/apis/operations/read|Obtiene una lista de las operaciones de API existentes o detalles de una operación de API|
|/service/apis/operations/tags/delete|Elimina la asociación de una etiqueta existente con la operación existente.|
|/service/apis/operations/tags/read|Obtiene etiquetas asociadas con la operación u obtiene los detalles de la etiqueta.|
|/service/apis/operations/tags/write|Asocia una etiqueta existente con la operación existente.|
|/service/apis/operations/write|Crea una nueva operación de API o actualiza una operación de API ya existente|
|/service/apis/operationsByTags/read|Obtiene una lista de asociaciones de operaciones o etiquetas.|
|/service/apis/policies/delete|Elimina la configuración de directiva de las directivas de API.|
|/service/apis/policies/read|Obtiene las directivas de API u obtiene los detalles de configuración de directivas de API.|
|/service/apis/policies/write|Establece los detalles de configuración de directivas de una API|
|/service/apis/policy/delete|Elimina la configuración de directivas de la API|
|/service/apis/policy/read|Obtiene los detalles de configuración de directivas de una API|
|/service/apis/policy/write|Establece los detalles de configuración de directivas de una API|
|/service/apis/products/read|Obtiene todos los productos que forman parte de la API.|
|/service/apis/read|Obtiene una lista de todas las API registradas o detalles de una API|
|/service/apis/releases/delete|Quita todas las versiones de la API o quita la versión de la API.|
|/service/apis/releases/read|Obtiene versiones de una API u obtiene detalles de la versión de la API.|
|/service/apis/releases/write|Crea una nueva versión de API o actualiza la versión de API ya existente.|
|/service/apis/revisions/delete|Quita todas las revisiones de una API.|
|/service/apis/revisions/read|Obtiene las revisiones que pertenecen a una API.|
|/service/apis/schemas/delete|Elimina un esquema ya existente.|
|/service/apis/schemas/document/read|Obtiene el documento que describe el esquema.|
|/service/apis/schemas/document/write|Actualiza el documento que describe el esquema.|
|/service/apis/schemas/read|Obtiene todos los esquemas de una API determinada u obtiene los esquemas utilizados por la API.|
|/service/apis/schemas/write|Establece los esquemas que usa la API.|
|/service/apis/tagDescriptions/delete|Quita la descripción de la etiqueta de la API.|
|/service/apis/tagDescriptions/read|Obtiene descripciones de las etiquetas en el ámbito de la API u obtiene una descripción de las etiquetas en el ámbito de la API.|
|/service/apis/tagDescriptions/write|Crea o cambia la descripción de la etiqueta en el ámbito de la API.|
|/service/apis/tags/delete|Quita la asociación existente entre API y etiquetas.|
|/service/apis/tags/read|Obtiene todas las asociaciones entre API y etiquetas de la API u obtiene los detalles de la asociación entre API y etiquetas.|
|/service/apis/tags/write|Agrega una nueva asociación entre API y etiquetas.|
|/service/apis/write|Crea una nueva API o actualiza los detalles de una API ya existente|
|/service/apisByTags/read|Obtiene una lista de asociaciones entre API y etiquetas.|
|/service/api-version-sets/delete|Quita un elemento VersionSet ya existente.|
|/service/api-version-sets/read|Obtiene la lista de entidades del grupo de versiones u obtiene los detalles de un elemento VersionSet.|
|/service/api-version-sets/versions/read|Obtiene la lista de entidades de versión.|
|/service/api-version-sets/write|Crea un nuevo elemento VersionSet o actualiza los detalles existentes de VersionSet.|
|/service/applynetworkconfigurationupdates/action|Actualiza los recursos de Microsoft.ApiManagement que se ejecutan en una instancia de Virtual Network para seleccionar la configuración de red actualizada.|
|/service/authorizationServers/delete|Quita un servidor de autorización ya existente|
|/service/authorizationServers/read|Obtiene una lista de servidores de autorización o detalles de un servidor de autorización|
|/service/authorizationServers/write|Crea un nuevo servidor de autorización o actualiza los detalles de un servidor de autorización ya existente|
|/service/backends/delete|Elimina un backend existente|
|/service/backends/read|Obtiene una lista de back-ends o detalles de un back-end|
|/service/backends/reconnect/action|Crea una solicitud de reconexión.|
|/service/backends/write|Agregar un nuevo back-end o actualiza los detalles de un back-end ya existente|
|/service/backup/action|Realiza una copia de seguridad del servicio API Management en el contenedor especificado de una cuenta de almacenamiento proporcionada por el usuario|
|/service/certificates/delete|Elimina un certificado existente|
|/service/certificates/read|Obtiene una lista de certificados o detalles de un certificado|
|/service/certificates/write|Agrega un nuevo certificado|
|/service/delete|Elimina una instancia del servicio API Management|
|/service/diagnostics/delete|Elimina un diagnóstico ya existente.|
|/service/diagnostics/loggers/delete|Quita la asignación de un registrador con una configuración de diagnóstico.|
|/service/diagnostics/loggers/read|Obtiene una lista de registradores de diagnóstico.|
|/service/diagnostics/loggers/write|Asigna un registrador a una configuración de diagnóstico.|
|/service/diagnostics/read|Obtiene una lista de diagnósticos u obtiene detalles del diagnóstico.|
|/service/diagnostics/write|Agrega un nuevo diagnóstico o actualiza los detalles de los diagnóstico existentes.|
|/service/getssotoken/action|Obtiene el token de SSO que se puede usar para iniciar sesión en el portal heredado del servicio API Management como administrador|
|/service/groups/delete|Elimina un grupo ya existente|
|/service/groups/read|Obtiene una lista de grupos o detalles de un grupo|
|/service/groups/users/delete|Elimina un usuario existente de un grupo que ya existe|
|/service/groups/users/read|Obtiene una lista de los usuarios del grupo|
|/service/groups/users/write|Agrega un usuario existente a un grupo que ya existe|
|/service/groups/write|Crea un nuevo grupo o actualiza los detalles de un grupo ya existente|
|/service/identityProviders/delete|Elimina el proveedor de identidades existente|
|/service/identityProviders/read|Obtiene una lista de proveedores de identidades o detalles del proveedor de identidades|
|/service/identityProviders/write|Crea un nuevo proveedor de identidades o actualiza los detalles de un proveedor ya existente|
|/service/locations/networkstatus/read|Obtiene el estado de acceso de red de los recursos de los que depende el servicio en la ubicación.|
|/service/loggers/delete|Quita un registrador existente|
|/service/loggers/read|Obtiene una lista de registradores o los detalles de uno de ellos|
|/service/loggers/write|Agrega un nuevo registrador o actualiza los detalles de los registradores existentes|
|/service/managedeployments/action|Cambia SKU y unidades, y agrega o quita las implementaciones regionales del servicio API Management|
|/service/networkstatus/read|Obtiene el estado de acceso de red de los recursos de los que depende el servicio.|
|/service/notifications/action|Envía una notificación a un usuario especificado.|
|/service/notifications/read|Obtiene todas las notificaciones del editor de API Management u obtiene los detalles de la notificación del editor de API Management.|
|/service/notifications/recipientEmails/delete|Quita el correo electrónico existente asociado con una notificación.|
|/service/notifications/recipientEmails/read|Obtiene los destinatarios de correo electrónico asociados con la notificación del editor de API Management.|
|/service/notifications/recipientEmails/write|Crea un nuevo destinatario de correo electrónico de la notificación.|
|/service/notifications/recipientUsers/delete|Quita el usuario asociado con los destinatarios de la notificación.|
|/service/notifications/recipientUsers/read|Obtiene los usuarios de destinatario asociados con la notificación.|
|/service/notifications/recipientUsers/write|Agrega usuarios a los destinatarios de la notificación.|
|/service/notifications/write|Crea o actualiza la notificación del editor de API Management.|
|/service/openidConnectProviders/delete|Elimina un proveedor de OpenID Connect ya existente|
|/service/openidConnectProviders/read|Obtiene una lista de proveedores de OpenID Connect o detalles del proveedor de OpenID Connect|
|/service/openidConnectProviders/write|Crea un nuevo proveedor de OpenID Connect o actualiza los detalles de un proveedor de OpenID Connect ya existente|
|/service/operationresults/read|Obtiene el estado actual de una operación de larga duración|
|/service/policies/delete|Quita la configuración de directiva de las directivas de inquilino.|
|/service/policies/read|Obtiene las directivas del inquilino u obtiene los detalles de configuración de directivas del inquilino.|
|/service/policies/write|Establece los detalles de configuración de directivas del inquilino.|
|/service/policySnippets/read|Obtiene todos los fragmentos de código de la directiva.|
|/service/portalsettings/read|Obtiene la configuración para registrarse en el portal u obtiene la configuración de inicio de sesión del portal u obtiene la configuración de delegación del portal.|
|/service/portalsettings/write|Actualiza la configuración para registrarse o actualiza la configuración de inicio de sesión o actualiza la configuración de delegación.|
|/service/products/apis/delete|Elimina una API existente de un producto que ya existe|
|/service/products/apis/read|Obtiene una lista de las API que se agregan a un producto existente|
|/service/products/apis/write|Agrega una API existente a un producto que ya existe|
|/service/products/delete|Elimina un producto ya existente|
|/service/products/groups/delete|Elimina la asociación de un grupo de desarrolladores ya existente con un producto que ya existe|
|/service/products/groups/read|Obtiene una lista de grupos de desarrolladores asociados con el producto|
|/service/products/groups/write|Asocia un grupo de desarrolladores ya existente con un producto que ya existe|
|/service/products/policies/delete|Quita la configuración de directiva de las directivas del producto.|
|/service/products/policies/read|Obtiene las directivas del producto u obtiene los detalles de configuración de directivas del producto.|
|/service/products/policies/write|Establece los detalles de configuración de directivas del producto.|
|/service/products/policy/delete|Elimina la configuración de directivas de un producto ya existente|
|/service/products/policy/read|Obtiene la configuración de directivas de un producto ya existente|
|/service/products/policy/write|Establece la configuración de directivas de un producto ya existente|
|/service/products/read|Obtiene una lista de productos o detalles de un producto|
|/service/products/subscriptions/read|Obtiene una lista de suscripciones de productos|
|/service/products/tags/delete|Elimina la asociación de una etiqueta existente con el producto existente.|
|/service/products/tags/read|Obtiene etiquetas asociadas con el producto u obtiene los detalles de la etiqueta.|
|/service/products/tags/write|Asocia una etiqueta existente con el producto existente.|
|/service/products/write|Crea un nuevo producto o actualiza los detalles de un producto existente|
|/service/properties/delete|Elimina una propiedad ya existente|
|/service/properties/read|Obtiene una lista de todas las propiedades o detalles de una propiedad especificada|
|/service/properties/write|Crea una nueva propiedad o actualiza el valor de una propiedad especificada|
|/service/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del servicio de API Management.|
|/service/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del servicio de API Management.|
|/service/providers/Microsoft.Insights/logDefinitions/read|Obtiene los registros disponibles del servicio de API Management.|
|/service/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles del servicio de API Management.|
|/service/quotas/periods/read|Obtener el valor del contador de cuota del período.|
|/service/quotas/periods/write|Establece el valor actual del contador de cuota.|
|/service/quotas/read|Obtiene los valores de cuota.|
|/service/quotas/write|Establece el valor actual del contador de cuota.|
|/service/read|Lectura de los metadatos de una instancia del servicio API Management|
|/service/reports/read|Obtiene el informe agregado según el período de tiempo u obtiene el informe agregado según la región geográfica u obtiene el informe agregado según los desarrolladores. u obtiene el informe agregado según los productos. u obtiene el informe agregado según las API u obtiene el informe agregado según las operaciones u obtiene el informe agregado según la suscripción. u obtiene los datos de los informes de las solicitudes.|
|/service/restore/action|Restauración del servicio API Management desde el contenedor especificado de una cuenta de almacenamiento proporcionada por el usuario|
|/service/subscriptions/delete|Elimina la suscripción. Esta operación se puede usar para eliminar la suscripción|
|/service/subscriptions/read|Obtiene una lista de suscripciones de productos o detalles de una suscripción de producto|
|/service/subscriptions/regeneratePrimaryKey/action|Regenera la clave principal de suscripción|
|/service/subscriptions/regenerateSecondaryKey/action|Regenera la clave secundaria de suscripción|
|/service/subscriptions/write|Suscribe un usuario existente a un producto existente o actualiza los detalles de la suscripción existente. Esta operación se puede usar para renovar la suscripción|
|/service/tagResources/read|Obtiene una lista de etiquetas con recursos asociados.|
|/service/tags/delete|Quita una etiqueta ya existente.|
|/service/tags/read|Obtiene una lista de etiquetas u obtiene los detalles de una etiqueta.|
|/service/tags/write|Agrega una nueva etiqueta o actualiza los detalles de una etiqueta existente.|
|/service/templates/delete|Restablece la plantilla de correo electrónico predeterminada de API Management.|
|/service/templates/read|Obtiene todas las plantillas de correo electrónico u obtiene los detalles de la plantilla de correo electrónico de API Management.|
|/service/templates/write|Crea o actualiza la plantilla de correo electrónico de API Management o actualiza la plantilla de correo electrónico de API Management.|
|/service/tenant/delete|Elimina la configuración de directivas del inquilino|
|/service/tenant/deploy/action|Ejecuta una tarea de implementación para aplicar los cambios de la bifurcación git especificada en la configuración de la base de datos.|
|/service/tenant/operationResults/read|Obtiene una lista de resultados de operaciones o el resultado de una operación específica|
|/service/tenant/read|Obtiene la configuración de directiva del inquilino u obtiene los detalles de la información de acceso del inquilino.|
|/service/tenant/regeneratePrimaryKey/action|Regenera la clave de acceso principal|
|/service/tenant/regenerateSecondaryKey/action|Regenera la clave de acceso secundaria|
|/service/tenant/save/action|Crea una confirmación con la instantánea de configuración en la bifurcación especificada del repositorio|
|/service/tenant/syncState/read|Obtiene el estado de la última sincronización de git|
|/service/tenant/validate/action|Valida los cambios de la bifurcación git especificada|
|/service/tenant/write|Establece la configuración de directiva del inquilino o actualiza los detalles de la información de acceso del inquilino.|
|/service/updatecertificate/action|Carga el certificado SSL de un servicio API Management|
|/service/updatehostname/action|Configura, actualiza o elimina los nombres de dominio personalizado de un servicio API Management|
|/service/users/action|Registra un nuevo usuario.|
|/service/users/applications/attachments/delete|Quita los datos adjuntos.|
|/service/users/applications/attachments/read|Obtiene los datos adjuntos de una aplicación u obtiene los datos adjuntos.|
|/service/users/applications/attachments/write|Agrega los datos adjuntos a la aplicación.|
|/service/users/applications/delete|Quita la aplicación existente.|
|/service/users/applications/read|Obtiene una lista de todas las aplicaciones de usuario u obtiene los detalles de la aplicación de API Management.|
|/service/users/applications/write|Registra una aplicación en API Management o actualiza los detalles de la aplicación.|
|/service/users/delete|Quita una cuenta de usuario|
|/service/users/generateSsoUrl/action|Genera la dirección URL de SSO. La dirección URL se puede utilizar para acceder al portal de administración|
|/service/users/groups/read|Obtiene una lista de grupos de usuario|
|/service/users/keys/read|Obtiene una lista de claves de usuario|
|/service/users/read|Obtiene una lista de usuarios registrados o los detalles de la cuenta de un usuario|
|/service/users/subscriptions/read|Obtiene una lista de suscripciones de usuario|
|/service/users/token/action|Obtiene el token de acceso al token de un usuario.|
|/service/users/write|Registra un nuevo usuario o actualiza los detalles de la cuenta de un usuario existente|
|/service/write|Creación de una nueva instancia del servicio API Management|
|/unregister/action|Anula el registro de la suscripción para el proveedor de recursos de Microsoft.ApiManagement|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Operación | DESCRIPCIÓN |
|---|---|
|/checkAccess/action|Comprueba si el autor de la llamada tiene permiso para realizar una acción específica.|
|/classicAdministrators/delete|Quita al administrador de la suscripción.|
|/classicAdministrators/read|Lee los administradores para la suscripción.|
|/classicAdministrators/write|Agrega o modifica el administrador para una suscripción.|
|/elevateAccess/action|Concede al llamador acceso de administrador de acceso de usuario en el ámbito de inquilinos|
|/locks/delete|Elimina bloqueos en el ámbito especificado.|
|/locks/read|Obtiene bloqueos en el ámbito especificado.|
|/locks/write|Agrega bloqueos en el ámbito especificado.|
|/permissions/read|Enumera todos los permisos que tiene el llamador en un ámbito concreto.|
|/policyAssignments/delete|Elimina una asignación de directiva en el ámbito especificado.|
|/policyAssignments/read|Obtiene información sobre una asignación de directiva.|
|/policyAssignments/write|Crea una asignación de directiva en el ámbito especificado.|
|/policyDefinitions/delete|Elimina una definición de directiva.|
|/policyDefinitions/read|Obtiene información sobre una definición de directiva.|
|/policyDefinitions/write|Crea una definición de directiva personalizada.|
|/policySetDefinitions/delete|Elimina una definición de un conjunto de directivas.|
|/policySetDefinitions/read|Obtiene información sobre una definición de un conjunto de directivas.|
|/policySetDefinitions/write|Crea una definición personalizada de un conjunto de directivas.|
|/providerOperations/read|Obtiene operaciones para todos los proveedores de recursos que pueden usarse en definiciones de roles.|
|/roleAssignments/delete|Elimine una asignación de roles en el ámbito especificado.|
|/roleAssignments/read|Obtiene información sobre una asignación de roles.|
|/roleAssignments/write|Crea una asignación de roles en el ámbito especificado.|
|/roleDefinitions/delete|Elimina la definición de roles personalizada especificada.|
|/roleDefinitions/read|Obtiene información sobre una definición de roles.|
|/roleDefinitions/write|Crea o actualiza una definición de roles personalizada con permisos especificados y ámbitos asignables.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Operación | DESCRIPCIÓN |
|---|---|
|/automationAccounts/agentRegistrationInformation/read|Lee la información de registro de DSC de Azure Automation.|
|/automationAccounts/agentRegistrationInformation/regenerateKey/action|Escribe una solicitud para regenerar las claves de DSC de Azure Automation.|
|/automationAccounts/certificates/delete|Elimina un recurso de certificado de Azure Automation|
|/automationAccounts/certificates/read|Obtiene un recurso de credencial de Azure Automation|
|/automationAccounts/certificates/write|Crea o actualiza un recurso de certificado de Azure Automation|
|/automationAccounts/compilationjobs/read|Lee una compilación de DSC de Azure Automation.|
|/automationAccounts/compilationjobs/write|Escribe una compilación de DSC de Azure Automation.|
|/automationAccounts/configurations/delete|Elimina contenido de DSC de Azure Automation.|
|/automationAccounts/configurations/getCount/action|Lee el recuento de contenido de DSC de Azure Automation.|
|/automationAccounts/configurations/read|Obtiene contenidos de DSC de Azure Automation|
|/automationAccounts/configurations/write|Escribe contenido de DSC de Azure Automation.|
|/automationAccounts/connections/delete|Elimina un recurso de conexión de Azure Automation|
|/automationAccounts/connections/read|Obtiene un recurso de conexión de Azure Automation|
|/automationAccounts/connections/write|Crea o actualiza un recurso de conexión de Azure Automation|
|/automationAccounts/connectionTypes/delete|Elimina un recurso de tipo de conexión de Azure Automation|
|/automationAccounts/connectionTypes/read|Obtiene un recurso de tipo de conexión de Azure Automation|
|/automationAccounts/connectionTypes/write|Crea un recurso de tipo de conexión de Azure Automation|
|/automationAccounts/credentials/delete|Elimina un recurso de credencial de Azure Automation|
|/automationAccounts/credentials/read|Obtiene un recurso de credencial de Azure Automation|
|/automationAccounts/credentials/write|Crea o actualiza un recurso de credencial de Azure Automation|
|/automationAccounts/delete|Elimina una cuenta de Azure Automation|
|/automationAccounts/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/automationAccounts/diagnosticSettings/write|Establece la configuración de diagnóstico del recurso.|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Elimina los recursos de Hybrid Runbook Worker|
|/automationAccounts/hybridRunbookWorkerGroups/read|Lee los recursos de Hybrid Runbook Worker|
|/automationAccounts/jobs/output/action|Obtiene la salida de un trabajo|
|/automationAccounts/jobs/output/action|Obtiene la salida de un trabajo|
|/automationAccounts/jobs/read|Obtiene un trabajo de Azure Automation|
|/automationAccounts/jobs/read|Obtiene un trabajo de Azure Automation|
|/automationAccounts/jobs/resume/action|Reanuda un trabajo de Azure Automation|
|/automationAccounts/jobs/resume/action|Reanuda un trabajo de Azure Automation|
|/automationAccounts/jobs/runbookContent/action|Obtiene el contenido del runbook de Azure Automation en el momento de la ejecución del trabajo|
|/automationAccounts/jobs/runbookContent/action|Obtiene el contenido del runbook de Azure Automation en el momento de la ejecución del trabajo|
|/automationAccounts/jobs/stop/action|Detiene un trabajo de Azure Automation|
|/automationAccounts/jobs/stop/action|Detiene un trabajo de Azure Automation|
|/automationAccounts/jobs/streams/read|Obtiene un flujo de trabajos de Azure Automation|
|/automationAccounts/jobs/streams/read|Obtiene un flujo de trabajos de Azure Automation|
|/automationAccounts/jobs/suspend/action|Suspende un trabajo de Azure Automation|
|/automationAccounts/jobs/suspend/action|Suspende un trabajo de Azure Automation|
|/automationAccounts/jobs/write|Crea un trabajo de Azure Automation|
|/automationAccounts/jobs/write|Crea un trabajo de Azure Automation|
|/automationAccounts/jobSchedules/delete|Elimina una programación de trabajos de Azure Automation|
|/automationAccounts/jobSchedules/read|Obtiene una programación de trabajos de Azure Automation|
|/automationAccounts/jobSchedules/write|Crea una programación de trabajos de Azure Automation|
|/automationAccounts/linkedWorkspace/read|Obtiene el área de trabajo vinculada a la cuenta de Automation.|
|/automationAccounts/logDefinitions/read|Obtiene los registros disponibles en la cuenta de Automation.|
|/automationAccounts/modules/activities/read|Obtiene las actividades de Azure Automation.|
|/automationAccounts/modules/delete|Elimina un módulo de Azure Automation|
|/automationAccounts/modules/read|Obtiene un módulo de Azure Automation|
|/automationAccounts/modules/write|Crea o actualiza un módulo de Azure Automation|
|/automationAccounts/nodeConfigurations/delete|Elimina la configuración de nodo de DSC de Azure Automation.|
|/automationAccounts/nodeConfigurations/read|Lee la configuración de nodo de DSC de Azure Automation.|
|/automationAccounts/nodeConfigurations/readContent/action|Lee el contenido de la configuración de nodo de DSC de Azure Automation.|
|/automationAccounts/nodeConfigurations/write|Escribe la configuración de nodo de DSC de Azure Automation.|
|/automationAccounts/nodes/delete|Elimina los nodos de DSC de Azure Automation.|
|/automationAccounts/nodes/read|Lee los nodos de DSC de Azure Automation.|
|/automationAccounts/nodes/reports/read|Lee el contenido de los informes de DSC de Azure Automation.|
|/automationAccounts/nodes/reports/read|Lee los informes de DSC de Azure Automation.|
|/automationAccounts/objectDataTypes/fields/read|Obtiene el elemento TypeFields de Azure Automation.|
|/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las definiciones de las métricas de Automation.|
|/automationAccounts/read|Obtiene una cuenta de Azure Automation|
|/automationAccounts/runbooks/delete|Elimina un runbook de Azure Automation|
|/automationAccounts/runbooks/draft/publish/action|Publica un borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/read|Obtiene un borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/readContent/action|Obtiene el contenido de un borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/testJob/read|Obtiene un trabajo de prueba del borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/testJob/resume/action|Reanuda un trabajo de prueba del borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/testJob/stop/action|Detiene un trabajo de prueba del borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Suspende un trabajo de prueba del borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/testJob/write|Crea un trabajo de prueba del borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/undoEdit/action|Deshace las ediciones de un borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/writeContent/action|Crea el contenido de un borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/read|Obtiene un runbook de Azure Automation|
|/automationAccounts/runbooks/readContent/action|Obtiene el contenido de un runbook de Azure Automation|
|/automationAccounts/runbooks/write|Crea o actualiza un runbook de Azure Automation|
|/automationAccounts/schedules/delete|Elimina un recurso de programación de Azure Automation|
|/automationAccounts/schedules/read|Obtiene un recurso de programación de Azure Automation|
|/automationAccounts/schedules/write|Crea o actualiza un recurso de programación de Azure Automation|
|/automationAccounts/statistics/read|Obtiene las estadísticas de Azure Automation.|
|/automationAccounts/usages/read|Obtiene el uso de Azure Automation.|
|/automationAccounts/variables/delete|Elimina un recurso de variable de Azure Automation|
|/automationAccounts/variables/read|Lee un recurso de variable en Azure Automation|
|/automationAccounts/variables/write|Crea o actualiza un recurso de variable de Azure Automation|
|/automationAccounts/watchers/streams/read|Obtiene un flujo de trabajos de monitor de Azure Automation.|
|/automationAccounts/webhooks/delete|Elimina un webhook de Azure Automation |
|/automationAccounts/webhooks/generateUri/action|Genera un identificador URI para un webhook de Azure Automation|
|/automationAccounts/webhooks/read|Lee un webhook de Azure Automation|
|/automationAccounts/webhooks/write|Crea o actualiza un webhook de Azure Automation|
|/automationAccounts/write|Crea o actualiza una cuenta de Azure Automation|
|/automationAccounts/write|Crea o actualiza una cuenta de Azure Automation|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

| Operación | DESCRIPCIÓN |
|---|---|
|/b2cDirectories/delete|Elimina el recurso del directorio B2C.|
|/b2cDirectories/read|Comprueba el recurso del directorio B2C.|
|/b2cDirectories/write|Crea o actualiza el recurso del directorio B2C.|
|/operations/read|Lee todas las operaciones de API disponibles para el proveedor de recursos Microsoft.AzureActiveDirectory.|
|/register/action|Registra la suscripción para el proveedor de recursos Microsoft.AzureActiveDirectory.|

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

| Operación | DESCRIPCIÓN |
|---|---|
|/Operations/read|Obtiene las propiedades de una operación del proveedor de recursos.|
|/register/action|Registra la suscripción con el proveedor de recursos Microsoft.AzureStack.|
|/registrations/customerSubscriptions/delete|Elimina una suscripción de cliente de Azure Stack.|
|/registrations/customerSubscriptions/read|Obtiene las propiedades de una suscripción de cliente de Azure Stack.|
|/registrations/customerSubscriptions/write|Crea o actualiza una suscripción de cliente de Azure Stack.|
|/registrations/delete|Elimina un registro de Azure Stack.|
|/registrations/getActivationKey/action|Obtiene la clave de activación de Azure Stack más reciente.|
|/registrations/products/listDetails/action|Recupera los detalles extendidos de un producto de Marketplace de Azure Stack.|
|/registrations/products/read|Obtiene las propiedades de un producto de Marketplace de Azure Stack.|
|/registrations/read|Obtiene las propiedades de un registro de Azure Stack.|
|/registrations/write|Crea o actualiza un registro de Azure Stack.|

## <a name="microsoftbatch"></a>Microsoft.Batch

| Operación | DESCRIPCIÓN |
|---|---|
|/batchAccounts/applications/delete|Elimina una aplicación|
|/batchAccounts/applications/read|Enumera las aplicaciones u obtiene las propiedades de una aplicación|
|/batchAccounts/applications/versions/activate/action|Activa un paquete de aplicación|
|/batchAccounts/applications/versions/delete|Elimina un paquete de aplicación|
|/batchAccounts/applications/versions/read|Obtiene las propiedades de un paquete de aplicación|
|/batchAccounts/applications/versions/write|Crea un nuevo paquete de aplicación o actualiza uno ya existente|
|/batchAccounts/applications/write|Crea una nueva aplicación o actualiza una aplicación ya existente|
|/batchAccounts/certificateOperationResults/read|Obtiene los resultados de una operación de certificado de larga ejecución en una cuenta de Batch.|
|/batchAccounts/certificates/cancelDelete/action|Cancela el error al eliminar un certificado en una cuenta de Batch.|
|/batchAccounts/certificates/delete|Elimina un certificado de una cuenta de Batch.|
|/batchAccounts/certificates/read|Enumera los certificados de una cuenta de Batch u obtiene las propiedades de un certificado.|
|/batchAccounts/certificates/write|Crea un nuevo certificado en una cuenta de Batch o actualiza un certificado existente.|
|/batchAccounts/delete|Elimina una cuenta de Batch|
|/batchAccounts/listkeys/action|Enumera las claves de acceso de una cuenta de Batch|
|/batchAccounts/operationResults/read|Obtiene los resultados de una operación de cuenta de Batch de larga ejecución.|
|/batchAccounts/poolOperationResults/read|Obtiene los resultados de una operación de grupo de larga ejecución en una cuenta de Batch.|
|/batchAccounts/pools/delete|Elimina un grupo de una cuenta de Batch.|
|/batchAccounts/pools/disableAutoscale/action|Deshabilita el escalado automático de un grupo de una cuenta de Batch.|
|/batchAccounts/pools/read|Enumera los grupos de una cuentas de Batch u obtiene las propiedades de un grupo.|
|/batchAccounts/pools/stopResize/action|Detiene un operación en curso de cambio de tamaño en un grupo de una cuenta de Batch.|
|/batchAccounts/pools/upgradeOs/action|Actualiza el sistema operativo de un grupo de una cuenta de Batch.|
|/batchAccounts/pools/write|Crea un nuevo grupo en una cuenta de Batch o actualiza un grupo ya existente.|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/batchAccounts/providers/Microsoft.Insights/logDefinitions/read|Obtiene los registros disponibles del servicio de Batch.|
|/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles del servicio de Batch.|
|/batchAccounts/read|Enumera las cuentas de Batch u obtiene las propiedades de una de ellas|
|/batchAccounts/regeneratekeys/action|Regenera las claves de acceso de una cuenta de Batch|
|/batchAccounts/syncAutoStorageKeys/action|Sincroniza las teclas de acceso para la cuenta de almacenamiento automática configurada para una cuenta de Batch|
|/batchAccounts/write|Crea una nueva cuenta de Batch o actualiza una cuenta de Batch ya existente|
|/locations/checkNameAvailability/action|Comprueba que el nombre de la cuenta sea válido y que no esté en uso.|
|/locations/quotas/read|Obtiene las cuotas de Batch de la suscripción especificada en la región especificada de Azure|
|/register/action|Registra la suscripción para el proveedor de recursos de Batch y habilita la creación de cuentas de Batch|
|/unregister/action|Quita el registro de la suscripción para el proveedor de recursos de Batch evitando así la creación de cuentas de Batch.|

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

| Operación | DESCRIPCIÓN |
|---|---|
|/clusters/delete|Elimina un clúster de Batch AI.|
|/clusters/read|Enumera los clúster de Batch AI u obtiene las propiedades de un clúster de Batch AI.|
|/clusters/remoteLoginInformation/action|Enumera la información de inicio de sesión remoto de un clúster de Batch AI.|
|/clusters/write|Crea un nuevo clúster de Batch AI o actualiza un clúster de Batch AI ya existente.|
|/fileservers/delete|Elimina un servidor de archivos de Batch AI.|
|/fileservers/read|Enumera los servidores de archivos de Batch AI u obtiene las propiedades de servidor de archivos de Batch AI.|
|/fileservers/resume/action|Reanuda un servidor de archivos de Batch AI.|
|/fileservers/suspend/action|Suspende un servidor de archivos de Batch AI.|
|/fileservers/write|Crea un nuevo servidor de archivos de Batch AI o actualiza un servidor de archivos de Batch AI ya existente.|
|/jobs/delete|Elimina un trabajo de Batch AI.|
|/jobs/read|Enumera los trabajos de Batch AI u obtiene las propiedades de un trabajo de Batch AI.|
|/jobs/remoteLoginInformation/action|Enumera la información de inicio de sesión remoto de un trabajo de Batch AI.|
|/jobs/terminate/action|Finaliza un trabajo de Batch AI.|
|/jobs/write|Crea un nuevo trabajo de Batch AI o actualiza un trabajo de Batch AI ya existente|
|/register/action|Registra la suscripción del proveedor de recursos de Batch AI y habilita la creación de recursos de Batch AI.|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Operación | DESCRIPCIÓN |
|---|---|
|/billingPeriods/read|Enumera los períodos de facturación disponibles.|
|/invoices/read|Enumera las facturas disponibles|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Operación | DESCRIPCIÓN |
|---|---|
|/mapApis/Delete|Elimina una operación|
|/mapApis/listSecrets/action|Enumera los secretos|
|/mapApis/listSingleSignOnToken/action|Lee el token de autorización de inicio de sesión único del recurso|
|/mapApis/Read|Lee una operación|
|/mapApis/regenerateKey/action|Regenera la clave|
|/mapApis/Write|Escribe una operación|
|/Operations/read|Descripción de la operación.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Operación | DESCRIPCIÓN |
|---|---|
|/checknameavailability/action|Comprueba si un nombre está disponible para su uso con una nueva instancia de Redis Cache|
|/operations/read|Enumera las operaciones que admite el proveedor "Microsoft.Cache".|
|/redis/delete|Elimina la instancia completa de Redis Cache|
|/redis/export/action|Exporta datos de Redis a blobs de almacenamiento prefijados en un formato especificado|
|/redis/firewallRules/delete|Elimina las reglas de firewall IP de una instancia de Redis Cache|
|/redis/firewallRules/read|Obtiene las reglas de firewall IP de una instancia de Redis Cache|
|/redis/firewallRules/write|Edita las reglas de firewall IP de una instancia de Redis Cache|
|/redis/forceReboot/action|Fuerza el reinicio de una instancia de memoria caché, posiblemente con pérdida de datos.|
|/redis/import/action|Importa datos de un formato especificado desde varios blobs en Redis|
|/redis/linkedservers/delete|Elimina un servidor vinculado de una instancia de Redis Cache|
|/redis/linkedservers/read|Obtiene los servidores vinculados asociados con una instancia de Redis Cache.|
|/redis/linkedservers/write|Agrega un servidor vinculado a una instancia de Redis Cache|
|/redis/listKeys/action|Visualiza el valor de las claves de acceso de Redis Cache en el portal de administración|
|/redis/listUpgradeNotifications/read|Enumera las notificaciones de actualización más recientes para el inquilino de la caché.|
|/redis/locations/operationresults/read|Obtiene el resultado de una operación de larga ejecución para la cual el encabezado "Location" se devolvió previamente al cliente.|
|/redis/metricDefinitions/read|Obtiene las métricas disponibles para una instancia de Redis Cache|
|/redis/patchSchedules/delete|Elimina la programación de una aplicación de revisiones de una instancia de Redis Cache|
|/redis/patchSchedules/read|Obtiene la programación de una aplicación de revisiones de una instancia de Redis Cache|
|/redis/patchSchedules/write|Modifica la programación de una aplicación de revisiones de una instancia de Redis Cache|
|/redis/read|Visualiza la configuración de Redis Cache en el portal de administración|
|/redis/regenerateKey/action|Cambia el valor de las claves de acceso de Redis Cache en el portal de administración|
|/redis/start/action|Inicia una instancia de la memoria caché.|
|/redis/stop/action|Detiene una instancia de la memoria caché.|
|/redis/write|Modifica la configuración de Redis Cache en el portal de administración|
|/register/action|Registra el proveedor de recursos "Microsoft.Cache" con una suscripción|
|/unregister/action|Anula el registro del proveedor de recursos "Microsoft.Cache" con una suscripción|

## <a name="microsoftcapacity"></a>Microsoft.Capacity

| Operación | DESCRIPCIÓN |
|---|---|
|/register/action|Registra al proveedor de recursos de Capacity y habilita la creación de recursos de tipo Capacity.|
|/reservationorders/action|Actualiza cualquier reserva.|
|/reservationorders/delete|Elimina cualquier reserva.|
|/reservationorders/read|Lee todas las reservas.|
|/reservationorders/reservations/action|Actualiza cualquier reserva.|
|/reservationorders/reservations/delete|Elimina cualquier reserva.|
|/reservationorders/reservations/read|Lee todas las reservas.|
|/reservationorders/reservations/revisions/read|Lee todas las reservas.|
|/reservationorders/reservations/write|Crea cualquier reserva.|
|/reservationorders/write|Crea cualquier reserva.|

## <a name="microsoftcdn"></a>Microsoft.Cdn

| Operación | DESCRIPCIÓN |
|---|---|
|/CheckNameAvailability/action||
|/CheckResourceUsage/action||
|/edgenodes/delete||
|/edgenodes/read||
|/edgenodes/write||
|/operationresults/delete||
|/operationresults/profileresults/CheckResourceUsage/action||
|/operationresults/profileresults/delete||
|/operationresults/profileresults/endpointresults/CheckResourceUsage/action||
|/operationresults/profileresults/endpointresults/customdomainresults/delete||
|/operationresults/profileresults/endpointresults/customdomainresults/ DisableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/ EnableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/read||
|/operationresults/profileresults/endpointresults/customdomainresults/write||
|/operationresults/profileresults/endpointresults/delete||
|/operationresults/profileresults/endpointresults/Load/action||
|/operationresults/profileresults/endpointresults/originresults/delete||
|/operationresults/profileresults/endpointresults/originresults/read||
|/operationresults/profileresults/endpointresults/originresults/write||
|/operationresults/profileresults/endpointresults/Purge/action||
|/operationresults/profileresults/endpointresults/read||
|/operationresults/profileresults/endpointresults/Start/action||
|/operationresults/profileresults/endpointresults/Stop/action||
|/operationresults/profileresults/endpointresults/ValidateCustomDomain/action||
|/operationresults/profileresults/endpointresults/write||
|/operationresults/profileresults/GenerateSsoUri/action||
|/operationresults/profileresults/GetSupportedOptimizationTypes/action||
|/operationresults/profileresults/read||
|/operationresults/profileresults/write||
|/operationresults/read||
|/operationresults/write||
|/operations/read||
|/profiles/CheckResourceUsage/action||
|/profiles/delete||
|/profiles/endpoints/CheckResourceUsage/action||
|/profiles/endpoints/customdomains/delete||
|/profiles/endpoints/customdomains/DisableCustomHttps/action||
|/profiles/endpoints/customdomains/EnableCustomHttps/action||
|/profiles/endpoints/customdomains/read||
|/profiles/endpoints/customdomains/write||
|/profiles/endpoints/delete||
|/profiles/endpoints/Load/action||
|/profiles/endpoints/origins/delete||
|/profiles/endpoints/origins/read||
|/profiles/endpoints/origins/write||
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso.|
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso.|
|/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read|Obtiene los registros disponibles de Microsoft.Cdn.|
|/profiles/endpoints/Purge/action||
|/profiles/endpoints/read||
|/profiles/endpoints/Start/action||
|/profiles/endpoints/Stop/action||
|/profiles/endpoints/ValidateCustomDomain/action||
|/profiles/endpoints/write||
|/profiles/GenerateSsoUri/action||
|/profiles/GetSupportedOptimizationTypes/action||
|/profiles/read||
|/profiles/write||
|/register/action|Registra la suscripción del proveedor de recursos de CDN y habilita la creación de perfiles de CDN.|
|/ValidateProbe/action||

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Operación | DESCRIPCIÓN |
|---|---|
|/certificateOrders/certificates/Delete|Elimina un certificado existente|
|/certificateOrders/certificates/Read|Obtiene la lista de certificados|
|/certificateOrders/certificates/Write|Agrega un nuevo certificado o actualiza uno existente|
|/certificateOrders/Delete|Elimina un AppServiceCertificate existente|
|/certificateOrders/operations/Read|Enumera todas las operaciones del registro de certificados del servicio de la aplicación.|
|/certificateOrders/Read|Obtiene la lista de CertificateOrders|
|/certificateOrders/reissue/Action|Vuelve a emitir un certificateorder existente|
|/certificateOrders/renew/Action|Renueva un certificateorder existente|
|/certificateOrders/resendEmail/Action|Vuelve a enviar el correo electrónico de certificado|
|/certificateOrders/resendRequestEmails/Action|Vuelve a enviar los mensajes de correo electrónico de solicitud a otra dirección de correo electrónico|
|/certificateOrders/resendRequestEmails/Action|Recupera el sello del sitio de un certificado emitido de App Service|
|/certificateOrders/retrieveCertificateActions/Action|Recupera la lista de acciones de certificado|
|/certificateOrders/retrieveEmailHistory/Action|Recupera el historial de correos electrónicos del certificado|
|/certificateOrders/verifyDomainOwnership/Action|Comprobar la propiedad del dominio|
|/certificateOrders/Write|Agrega un nuevo certificateOrder o actualiza uno existente|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Aprovisiona una entidad de servicio para entidad de aplicación de servicio|
|/register/action|Registra el proveedor de recursos de Microsoft Certificates de la suscripción|
|/validateCertificateRegistrationInformation/Action|Valida un objeto de compra de certificado sin enviarlo|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Operación | DESCRIPCIÓN |
|---|---|
|/capabilities/read|Muestra las funcionalidades|
|/checkDomainNameAvailability/action|Comprueba la disponibilidad de un nombre de dominio determinado.|
|/domainNames/active/write|Establece el nombre de dominio activo.|
|/domainNames/availabilitySets/read|Muestra el conjunto de disponibilidad para el recurso.|
|/domainNames/capabilities/read|Muestra las funcionalidades del nombre de dominio|
|/domainNames/delete|Quita los nombres de dominio de los recursos.|
|/domainNames/extensions/delete|Quita las extensiones del nombre de dominio.|
|/domainNames/extensions/operationStatuses/read|Lee el estado de la operación de las extensiones de los nombres de dominio.|
|/domainNames/extensions/read|Devuelve las extensiones del nombre de dominio.|
|/domainNames/extensions/write|Agrega las extensiones del nombre de dominio.|
|/domainNames/internalLoadBalancers/delete|Quita un nuevo equilibrio de carga interno.|
|/domainNames/internalLoadBalancers/operationStatuses/read|Lee el estado de la operación de los equilibradores de carga internos de los nombres de dominio.|
|/domainNames/internalLoadBalancers/read|Obtiene los equilibradores de carga internos.|
|/domainNames/internalLoadBalancers/write|Crea un nuevo equilibrio de carga interno.|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|Lee el estado de la operación de los conjuntos de puntos de conexión de carga equilibrada de los nombres de dominio.|
|/domainNames/loadBalancedEndpointSets/read|Muestra los conjuntos de puntos de conexión de carga equilibrada|
|/domainNames/read|Devuelve los nombres de dominio de los recursos.|
|/domainNames/serviceCertificates/delete|Elimina los certificados de servicio usados.|
|/domainNames/serviceCertificates/operationStatuses/read|Lee el estado de la operación de los certificados de servicio de los nombres de dominio.|
|/domainNames/serviceCertificates/read|Devuelve los certificados de servicio usados.|
|/domainNames/serviceCertificates/write|Agrega o modifica los certificados de servicio usados.|
|/domainNames/slots/delete|Elimina una ranura de implementación concreta.|
|/domainNames/slots/operationStatuses/read|Lee el estado de la operación de las ranuras de los nombres de dominio.|
|/domainNames/slots/read|Muestra las ranuras de implementación.|
|/domainNames/slots/roles/extensionReferences/delete|Quita la referencia de extensión para el rol de la ranura de implementación.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|Lee el estado de la operación de las referencias de extensiones de los roles de las ranuras de nombres de dominio.|
|/domainNames/slots/roles/extensionReferences/read|Devuelve la referencia de extensión para el rol de la ranura de implementación.|
|/domainNames/slots/roles/extensionReferences/write|Agrega o modifica la referencia de extensión para el rol de la ranura de implementación.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write|Agrega o modifica la configuración de diagnóstico.|
|/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las definiciones de métricas.|
|/domainNames/slots/roles/read|Obtiene el rol de la ranura de implementación.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|Lee el estado de la operación de las instancias de rol de los roles de las ranuras de nombres de dominio.|
|/domainNames/slots/roles/roleInstances/read|Obtiene la instancia de rol.|
|/domainNames/slots/roles/roleInstances/rebuild/action|Recompila la instancia de rol.|
|/domainNames/slots/roles/roleInstances/reimage/action|Restablece la imagen inicial de la instancia de rol.|
|/domainNames/slots/roles/roleInstances/restart/action|Reinicia las instancias de rol.|
|/domainNames/slots/start/action|Inicia una ranura de implementación.|
|/domainNames/slots/state/start/write|Cambia el estado de la ranura de implementación a detenido.|
|/domainNames/slots/state/stop/write|Cambia el estado de la ranura de implementación a iniciado.|
|/domainNames/slots/stop/action|Suspende la ranura de implementación.|
|/domainNames/slots/upgradeDomain/write|Recorre el dominio de actualización.|
|/domainNames/slots/write|Crea o actualiza la implementación.|
|/domainNames/swap/action|Cambia la ranura de ensayo a la de producción.|
|/domainNames/write|Agrega o modifica los nombres de dominio de los recursos.|
|/moveSubscriptionResources/action|Mueve todos los recursos clásicos a una suscripción diferente.|
|/operatingSystemFamilies/read|Enumera las familias del sistema operativo invitado que están disponibles en Microsoft Azure, así como las versiones del sistema operativo disponibles para cada familia.|
|/operatingSystems/read|Enumera las versiones del sistema operativo invitado que están actualmente disponibles en Microsoft Azure.|
|/quotas/read|Obtiene la cuota de la suscripción.|
|/register/action|Realiza el registro en un proceso clásico|
|/resourceTypes/skus/read|Obtiene la lista de Sku para los tipos de recursos admitidos.|
|/validateSubscriptionMoveAvailability/action|Valida la disponibilidad de la suscripción para la operación de movimiento clásico.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|Elimina el grupo de seguridad de red asociado con la máquina virtual.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|Lee el estado de la operación de los grupos de seguridad de red asociados a las máquinas virtuales.|
|/virtualMachines/associatedNetworkSecurityGroups/read|Obtiene el grupo de seguridad de red asociado con la máquina virtual.|
|/virtualMachines/associatedNetworkSecurityGroups/write|Agrega un grupo de seguridad de red asociado con la máquina virtual.|
|/virtualMachines/asyncOperations/read|Obtiene las operaciones asincrónicas posibles|
|/virtualMachines/attachDisk/action|Conecta un disco de datos a una máquina virtual.|
|/virtualMachines/delete|Quita máquinas virtuales.|
|/virtualMachines/detachDisk/action|Desconecta un disco de datos de una máquina virtual.|
|/virtualMachines/disks/read|Recupera la lista de discos de datos|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|Descarga el archivo RDP para la máquina virtual.|
|/virtualMachines/extensions/operationStatuses/read|Lee el estado de la operación de las extensiones de máquinas virtuales.|
|/virtualMachines/extensions/read|Obtiene la extensión de máquina virtual.|
|/virtualMachines/extensions/write|Pone la extensión de máquina virtual.|
|/virtualMachines/metrics/read|Obtiene las métricas.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete|Elimina el grupo de seguridad de red asociado con la interfaz de red.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/ operationStatuses/read|Lee el estado de la operación de los grupos de seguridad de red asociados a las máquinas virtuales.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read|Obtiene el grupo de seguridad de red asociado con la interfaz de red.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write|Agrega un grupo de seguridad de red asociado con la interfaz de red.|
|/virtualMachines/operationStatuses/read|Lee el estado de la operación de las máquinas virtuales.|
|/virtualMachines/performMaintenance/action|Realiza el mantenimiento en la máquina virtual.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Agrega o modifica la configuración de diagnóstico.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las definiciones de métricas.|
|/virtualMachines/read|Recupera una lista de máquinas virtuales.|
|/virtualMachines/redeploy/action|Vuelve a implementar la máquina virtual.|
|/virtualMachines/restart/action|Reinicia las máquinas virtuales.|
|/virtualMachines/shutdown/action|Apaga la máquina virtual.|
|/virtualMachines/start/action|Inicie la máquina virtual.|
|/virtualMachines/stop/action|Detiene la máquina virtual.|
|/virtualMachines/write|Agrega o modifica máquinas virtuales.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Operación | DESCRIPCIÓN |
|---|---|
|/gatewaySupportedDevices/read|Recupera la lista de dispositivos compatibles.|
|/networkSecurityGroups/delete|Elimina el grupo de seguridad de red.|
|/networkSecurityGroups/operationStatuses/read|Lee el estado de la operación del grupo de seguridad de red.|
|/networkSecurityGroups/read|Obtiene el grupo de seguridad de red.|
|/networkSecurityGroups/securityRules/delete|Elimina la regla de seguridad.|
|/networkSecurityGroups/securityRules/operationStatuses/read|Lee el estado de la operación de las reglas de seguridad del grupo de seguridad de red.|
|/networkSecurityGroups/securityRules/read|Obtiene la regla de seguridad.|
|/networkSecurityGroups/securityRules/write|Agrega o actualiza una regla de seguridad.|
|/networkSecurityGroups/write|Agrega un nuevo grupo de seguridad de red.|
|/quotas/read|Obtiene la cuota de la suscripción.|
|/register/action|Realiza el registro en una red clásica|
|/reservedIps/delete|Elimina una IP reservada.|
|/reservedIps/join/action|Une una IP reservada|
|/reservedIps/link/action|Vincula una IP reservada|
|/reservedIps/operationStatuses/read|Lee el estado de la operación de las IP reservadas.|
|/reservedIps/read|Obtiene las IP reservadas|
|/reservedIps/write|Agregar una nueva IP reservada|
|/virtualNetworks/capabilities/read|Muestra las funcionalidades|
|/virtualNetworks/checkIPAddressAvailability/action|Comprueba la disponibilidad de una dirección IP determinada en una red virtual.|
|/virtualNetworks/delete|Elimina la red virtual.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|Anula la revocación de un certificado de cliente.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|Lee los certificados de cliente revocados.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|Revoca un certificado de cliente.|
|/virtualNetworks/gateways/clientRootCertificates/delete|Elimina el certificado de cliente de la puerta de enlace de red virtual.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|Descarga el certificado con huella digital.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|Muestra el paquete de certificado de la puerta de enlace de red virtual.|
|/virtualNetworks/gateways/clientRootCertificates/read|Busca los certificados raíz de cliente.|
|/virtualNetworks/gateways/clientRootCertificates/write|Carga un nuevo certificado raíz de cliente.|
|/virtualNetworks/gateways/connections/connect/action|Realiza una conexión de puerta de enlace de sitio a sitio.|
|/virtualNetworks/gateways/connections/disconnect/action|Desconecta una conexión de puerta de enlace de sitio a sitio.|
|/virtualNetworks/gateways/connections/read|Recupera la lista de conexiones.|
|/virtualNetworks/gateways/connections/test/action|Prueba una conexión de puerta de enlace de sitio a sitio.|
|/virtualNetworks/gateways/delete|Elimina la puerta de enlace de red virtual.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|Descarga el script de configuración del dispositivo.|
|/virtualNetworks/gateways/downloadDiagnostics/action|Descarga los diagnósticos de la puerta de enlace.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Recupera la clave de servicio del circuito.|
|/virtualNetworks/gateways/listPackage/action|Muestra el paquete de la puerta de enlace de red virtual.|
|/virtualNetworks/gateways/operationStatuses/read|Lee el estado de la operación de las puertas de enlace de redes virtuales.|
|/virtualNetworks/gateways/packages/read|Obtiene el paquete de la puerta de enlace de red virtual.|
|/virtualNetworks/gateways/read|Obtiene las puertas de enlace de red virtual.|
|/virtualNetworks/gateways/startDiagnostics/action|Inicia el diagnóstico de la puerta de enlace de red virtual.|
|/virtualNetworks/gateways/stopDiagnostics/action|Detiene el diagnóstico de la puerta de enlace de red virtual.|
|/virtualNetworks/gateways/write|Agrega una puerta de enlace de red virtual.|
|/virtualNetworks/join/action|Une la red virtual.|
|/virtualNetworks/operationStatuses/read|Lee el estado de la operación de las redes virtuales.|
|/virtualNetworks/peer/action|Empareja una red virtual con otra red virtual.|
|/virtualNetworks/read|Obtiene la red virtual.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete|Elimina el grupo de seguridad de red asociado con la subred.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read|Lee el estado de la operación del grupo de seguridad de red asociado a la subred de red virtual.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/read|Obtiene el grupo de seguridad de red asociado con la subred.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/write|Agrega un grupo de seguridad de red asociado con la subred.|
|/virtualNetworks/write|Agrega una nueva red virtual.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Operación | DESCRIPCIÓN |
|---|---|
|/capabilities/read|Muestra las funcionalidades|
|/checkStorageAccountAvailability/action|Comprueba la disponibilidad de una cuenta de almacenamiento.|
|/disks/read|Devuelve el disco de la cuenta de almacenamiento.|
|/images/read|Devuelve la imagen.|
|/osImages/read|Devuelve la imagen de sistema operativo.|
|/publicImages/read|Obtiene la imagen de máquina virtual pública.|
|/quotas/read|Obtiene la cuota de la suscripción.|
|/register/action|Registra elementos en el almacenamiento clásico|
|/storageAccounts/delete|Elimina la cuenta de almacenamiento.|
|/storageAccounts/disks/delete|Elimina un disco de cuenta de almacenamiento determinado.|
|/storageAccounts/disks/operationStatuses/read|Lee el estado de la operación de los recursos.|
|/storageAccounts/disks/read|Devuelve el disco de la cuenta de almacenamiento.|
|/storageAccounts/disks/write|Agrega un disco de cuenta de almacenamiento.|
|/storageAccounts/images/delete|Elimina una imagen determinada de la cuenta de almacenamiento.|
|/storageAccounts/images/read|Devuelve la imagen de la cuenta de almacenamiento.|
|/storageAccounts/listKeys/action|Enumera las claves de acceso de las cuentas de almacenamiento.|
|/storageAccounts/operationStatuses/read|Lee el estado de la operación de los recursos.|
|/storageAccounts/osImages/delete|Elimina una imagen del sistema operativo de la cuenta de almacenamiento.|
|/storageAccounts/osImages/read|Devuelve la imagen del sistema operativo de la cuenta de almacenamiento.|
|/storageAccounts/read|Devuelve la cuenta de almacenamiento con la cuenta especificada.|
|/storageAccounts/regenerateKey/action|Regenera las claves de acceso existentes de la cuenta de almacenamiento.|
|/storageAccounts/services/diagnosticSettings/read|Obtiene la configuración de diagnóstico.|
|/storageAccounts/services/diagnosticSettings/write|Agrega o modifica la configuración de diagnóstico.|
|/storageAccounts/services/metricDefinitions/read|Obtiene las definiciones de métricas.|
|/storageAccounts/services/metrics/read|Obtiene las métricas.|
|/storageAccounts/services/read|Obtiene los servicios disponibles.|
|/storageAccounts/write|Agrega una nueva cuenta de almacenamiento.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Operación | DESCRIPCIÓN |
|---|---|
|/accounts/delete|Elimina las cuentas de la API|
|/accounts/listKeys/action|Enumera las claves|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles para Cognitive Services.|
|/accounts/read|Lee las cuentas de la API.|
|/accounts/regenerateKey/action|Regenera una clave|
|/accounts/skus/read|Lee las SKU disponibles para un recurso existente.|
|/accounts/usages/read|Obtiene el uso de cuota para un recurso existente.|
|/accounts/write|Escribe las cuentas de la API.|
|/locations/checkSkuAvailability/action|Lee las SKU disponibles en una suscripción.|
|/Operations/read|Enumera todas las operaciones disponibles.|
|/register/action|Registra la suscripción de Cognitive Services.|
|/skus/read|Lee las SKU disponibles en Cognitive Services.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Operación | DESCRIPCIÓN |
|---|---|
|/RateCard/read|Devuelve datos de oferta, metadatos de recurso o medidor, y las tarifas de las suscripciones dadas.|
|/UsageAggregates/read|Recupera el consumo de Microsoft Azure por parte de una suscripción. El resultado contiene datos de uso de agregados, información relacionada con la suscripción y los recursos, en un determinado intervalo de tiempo.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Operación | DESCRIPCIÓN |
|---|---|
|/availabilitySets/delete|Elimina el conjunto de disponibilidad|
|/availabilitySets/read|Obtiene las propiedades de un conjunto de disponibilidad|
|/availabilitySets/vmSizes/read|Enumera los tamaños disponibles para crear o actualizar una máquina virtual del conjunto de disponibilidad|
|/availabilitySets/write|Crea un nuevo conjunto de disponibilidad o actualiza uno ya existente|
|/disks/beginGetAccess/action|Obtiene el URI de SAS del disco de acceso de blob|
|/disks/delete|Elimina el disco|
|/disks/endGetAccess/action|Revoca el URI de SAS del disco|
|/disks/read|Obtiene las propiedades de un disco|
|/disks/write|Crea un nuevo disco o actualiza uno ya existente|
|/images/delete|Elimina la imagen|
|/images/read|Obtiene las propiedades de la imagen|
|/images/write|Crea una nueva imagen o actualiza una ya existente|
|/locations/capsOperations/read|Obtiene el estado de una operación Caps asincrónica.|
|/locations/diskOperations/read|Obtiene el estado de una operación Disk asincrónica.|
|/locations/operations/read|Obtiene el estado de una operación asincrónica|
|/locations/publishers/artifacttypes/offers/read|Obtiene las propiedades de una oferta de imagen de plataforma.|
|/locations/publishers/artifacttypes/offers/skus/read|Obtiene las propiedades de una SKU de imagen de plataforma.|
|/locations/publishers/artifacttypes/offers/skus/versions/read|Obtiene las propiedades de una versión de imagen de plataforma.|
|/locations/publishers/artifacttypes/types/read|Obtiene las propiedades de un tipo de VMExtension.|
|/locations/publishers/artifacttypes/types/versions/read|Obtiene las propiedades de una versión de VMExtension.|
|/locations/publishers/read|Obtiene las propiedades de un editor.|
|/locations/runCommands/read|Enumera los comandos de ejecución disponibles en la ubicación.|
|/locations/usages/read|Obtiene los límites de servicio y las cantidades de uso actuales de los recursos de proceso de la suscripción en una ubicación|
|/locations/vmSizes/read|Enumera los tamaños disponibles de máquina virtual en una ubicación|
|/operations/read|Enumera las operaciones disponibles en el proveedor de recursos de Microsoft.Compute|
|/register/action|Registra la suscripción con el proveedor de recursos de Microsoft.Compute|
|/restorePointCollections/delete|Elimina la colección de puntos de restauración y los puntos de restauración que contiene|
|/restorePointCollections/read|Obtiene las propiedades de una colección de puntos de restauración|
|/restorePointCollections/restorePoints/delete|Elimina el punto de restauración|
|/restorePointCollections/restorePoints/read|Obtiene las propiedades de un punto de restauración|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Obtiene las propiedades de un punto de restauración junto con los identificadores URI de SAS de blob|
|/restorePointCollections/restorePoints/write|Crea un nuevo punto de restauración|
|/restorePointCollections/write|Crea una nueva colección de puntos de restauración o actualiza una ya existente|
|/sharedVMImages/delete|Elimina SharedVMImage.|
|/sharedVMImages/read|Obtiene las propiedades de SharedVMImage.|
|/sharedVMImages/versions/delete|Elimina SharedVMImageVersion.|
|/sharedVMImages/versions/read|Obtiene las propiedades de SharedVMImageVersion.|
|/sharedVMImages/versions/replicate/action|Replica SharedVMImageVersion en las regiones de destino.|
|/sharedVMImages/versions/write|Crea un nuevo elemento SharedVMImageVersion o actualiza uno existente.|
|/sharedVMImages/write|Crea un nuevo elemento SharedVMImage o actualiza uno existente.|
|/skus/read|Obtiene la lista de SKU de Microsoft.Compute disponibles para la suscripción.|
|/snapshots/beginGetAccess/action|Obtiene el URI de SAS de la instantánea para el acceso de blob.|
|/snapshots/delete|Elimina una instantánea|
|/snapshots/endGetAccess/action|Revoca el URI de SAS de la instantánea.|
|/snapshots/read|Obtiene las propiedades de una instantánea|
|/snapshots/write|Crea una nueva instantánea o actualiza una ya existente|
|/virtualMachines/capture/action|Captura la máquina virtual mediante la copia de los discos duros virtuales y genera una plantilla que se puede usar para crear máquinas virtuales similares|
|/virtualMachines/convertToManagedDisks/action|Convierte los discos basados en blobs de la máquina virtual en instancias de Managed Disks|
|/virtualMachines/deallocate/action|Apaga la máquina virtual y libera los recursos de proceso|
|/virtualMachines/delete|Elimina la máquina virtual|
|/virtualMachines/extensions/delete|Elimina la extensión de máquina virtual|
|/virtualMachines/extensions/read|Obtiene las propiedades de una extensión de máquina virtual|
|/virtualMachines/extensions/write|Crea una nueva extensión de máquina virtual o actualiza una ya existente|
|/virtualMachines/generalize/action|Establece el estado de la máquina virtual en Generalizado y prepara la máquina virtual para la captura|
|/virtualMachines/instanceView/read|Obtiene el estado detallado en tiempo de ejecución de la máquina virtual y sus recursos|
|/virtualMachines/performMaintenance/action|Realiza la operación de mantenimiento en la máquina virtual.|
|/virtualMachines/powerOff/action|Apaga la máquina virtual. Tenga en cuenta que se seguirá facturando la máquina virtual.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Lee las definiciones de métricas de las máquinas virtuales|
|/virtualMachines/read|Obtiene las propiedades de una máquina virtual|
|/virtualMachines/redeploy/action|Vuelve a implementar la máquina virtual|
|/virtualMachines/reimage/action|Restablece la imagen inicial de la máquina virtual que está usando el disco de comparación.|
|/virtualMachines/restart/action|Reinicia la máquina virtual|
|/virtualMachines/runCommand/action|Ejecuta un script predefinido en la máquina virtual.|
|/virtualMachines/start/action|Inicia la máquina virtual|
|/virtualMachines/vmSizes/read|Enumera los tamaños disponibles a los que se puede actualizar la máquina virtual|
|/virtualMachines/write|Crea una nueva máquina virtual o actualiza una ya existente|
|/virtualMachineScaleSets/deallocate/action|Apaga y libera los recursos de proceso de las instancias del conjunto de escalado de máquinas virtuales. |
|/virtualMachineScaleSets/delete|Elimina el conjunto de escalado de las máquinas virtuales.|
|/virtualMachineScaleSets/delete/action|Elimina las instancias del conjunto de escalado de las máquinas virtuales.|
|/virtualMachineScaleSets/extensions/delete|Elimina la extensión del conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/extensions/read|Obtiene las propiedades de una extensión de un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/extensions/write|Crea una extensión nueva del conjunto de escalado de máquinas virtuales o actualiza una existente.|
|/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action|Recorre manualmente los dominios de actualización de plataformas de un conjunto de escalado de máquinas virtuales de Service Fabric para finalizar una actualización pendiente que se bloqueó.|
|/virtualMachineScaleSets/instanceView/read|Obtiene la vista de instancias del conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/manualUpgrade/action|Actualiza manualmente las instancias al último modelo del conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/networkInterfaces/read|Obtiene las propiedades de todas las interfaces de red de un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/osUpgradeHistory/read|Obtiene el historial de las actualizaciones del sistema operativo de un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/performMaintenance/action|Realiza el mantenimiento planeado en las instancias del conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/powerOff/action|Apaga las instancias del conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read|Lee las definiciones de métricas del conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/publicIPAddresses/read|Obtiene las propiedades de todas las direcciones IP públicas de un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/read|Obtiene las propiedades de un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/redeploy/action|Vuelve a implementar las instancias del conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/reimage/action|Restablece la imagen inicial de las instancias del conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/restart/action|Reinicia las instancias del conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/rollingUpgrades/cancel/action|Cancela la actualización gradual de un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/rollingUpgrades/read|Obtiene el estado de actualización gradual más reciente de un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/scale/action|Permite verificar si un conjunto de escalado de máquinas virtuales existente puede reducirse o escalarse horizontalmente al número de instancias especificado.|
|/virtualMachineScaleSets/skus/read|Enumera las SKU válidas de un conjunto de escalado de máquinas virtuales ya existente.|
|/virtualMachineScaleSets/start/action|Inicia las instancias del conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|Apaga y libera los recursos de proceso de una máquina virtual del conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/delete|Elimina una máquina virtual específica de un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|Recupera la vista de instancias de una máquina virtual de un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ ipConfigurations/publicIPAddresses/read|Obtiene las propiedades de las direcciones IP públicas creadas mediante un conjunto de escalado de máquinas virtuales. El conjunto de escalado de máquinas virtuales puede crear a lo sumo una IP pública por ipconfiguration (IP privada).|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read|Obtiene las propiedades de una o todas las configuraciones de IP de una interfaz de red creada mediante el conjunto de escalado de máquinas virtuales. Las configuraciones de IP representan direcciones IP privadas.|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/read|Obtiene las propiedades de una o todas las interfaces de red de una máquina virtual creada mediante el conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/performMaintenance/action|Realiza el mantenimiento planeado en una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|Apaga una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/providers/ Microsoft.Insights/metricDefinitions/read|Lee la máquina virtual en las definiciones de métricas del conjunto de escalado.|
|/virtualMachineScaleSets/virtualMachines/read|Recupera las propiedades de una máquina virtual de un conjunto de escalado de máquinas virtuales|
|/virtualMachineScaleSets/virtualMachines/redeploy/action|Vuelve a implementar una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/reimage/action|Restablece la imagen inicial de una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/restart/action|Reinicia una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/start/action|Inicia una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/write|Actualiza las propiedades de una máquina virtual en un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/write|Crea un nuevo conjunto de escalado de máquinas virtuales o actualiza uno ya existente.|

## <a name="microsoftconsumption"></a>Microsoft.Consumption

| Operación | DESCRIPCIÓN |
|---|---|
|/balances/read|Enumera el uso del resumen durante el período de facturación de un grupo de administración.|
|/budgets/read|Enumera los presupuestos con una suscripción o un grupo de administración.|
|/budgets/write|Crea, actualiza y eliminar los presupuestos mediante una suscripción o un grupo de administración.|
|/marketplaces/read|Enumera los detalles de uso de los recursos de Marketplace referentes a un ámbito de las suscripciones EA y WebDirect.|
|/operations/read|Enumera todas las operaciones que admite el proveedor de recursos de Microsoft.Consumption.|
|/pricesheets/read|Enumera los datos de las hojas de precios de una suscripción o un grupo de administración.|
|/reservationDetails/read|Enumera los detalles de uso de las instancias que se reservaron mediante una solicitud de reserva o los grupos de administración. Los datos de los detalles se distribuyen por instancia y día.|
|/reservationSummaries/read|Enumera el resumen de uso de las instancias que se reservaron mediante una solicitud de reserva o los grupos de administración. Los datos de resumen son mensuales o diarios.|
|/reservationTransactions/read|Enumera el historial de transacciones de las instancias que reservaron grupos de administración.|
|/terms/read|Enumera los términos de una suscripción o un grupo de administración.|
|/usageDetails/read|Enumera los detalles de uso referentes a un ámbito de las suscripciones EA y WebDirect.|

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

| Operación | DESCRIPCIÓN |
|---|---|
|/containerGroups/containers/logs/read|Obtiene los registros de un contenedor específico.|
|/containerGroups/delete|Elimina el grupo de contenedores específico.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del grupo de contenedores.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del grupo de contenedores.|
|/containerGroups/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles del grupo de contenedores.|
|/containerGroups/read|Obtiene todos los grupos de contenedores.|
|/containerGroups/write|Crea o actualiza un grupo de contenedores específico.|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Operación | DESCRIPCIÓN |
|---|---|
|/checkNameAvailability/read|Comprueba si el nombre del registro de contenedor está disponible para su uso.|
|/locations/operationResults/read|Obtiene el resultado de una operación asincrónica.|
|/operations/read|Enumera todas las operaciones de API de REST disponibles de Azure Container Registry.|
|/register/action|Registra la suscripción para el proveedor de recursos del registro de contenedor y habilita la creación de registros de contenedor.|
|/registries/delete|Elimina un registro de contenedor.|
|/registries/eventGridFilters/delete|Elimina un filtro de la cuadrícula de eventos de un registro de contenedor.|
|/registries/eventGridFilters/read|Obtiene las propiedades de un filtro específico de la cuadrícula de eventos o enumera todos los filtros de la cuadrícula de eventos del registro de contenedor especificado.|
|/registries/eventGridFilters/write|Crea o actualiza un filtro de la cuadrícula de eventos para un registro de contenedor con los parámetros especificados.|
|/registries/listCredentials/action|Enumera las credenciales de inicio de sesión del registro de contenedor especificado.|
|/registries/listUsages/read|Enumera los usos de cuota del registro de contenedor especificado.|
|/registries/operationStatuses/read|Obtiene el estado de la operación asincrónica del registro.|
|/registries/read|Obtiene las propiedades del registro de contenedor especificado o enumera todos los registros de contenedores en la suscripción o el grupo de recursos especificado.|
|/registries/regenerateCredential/action|Regenera una de las credenciales de inicio de sesión del registro de contenedor especificado.|
|/registries/replications/delete|Elimina una réplica de un registro de contenedor.|
|/registries/replications/operationStatuses/read|Obtiene el estado de la operación asincrónica de replicación.|
|/registries/replications/read|Obtiene las propiedades de una replicación específica o enumera todas las replicaciones del registro de contenedor especificado.|
|/registries/replications/write|Crea o actualiza una replicación de un registro de contenedor con los parámetros especificados.|
|/registries/webhooks/delete|Elimina un webhook de un registro de contenedor.|
|/registries/webhooks/getCallbackConfig/action|Obtiene la configuración del servicio de URI y los encabezados personalizados del webhook.|
|/registries/webhooks/listEvents/action|Enumera los eventos recientes del webhook especificado.|
|/registries/webhooks/operationStatuses/read|Obtiene el estado de la operación asincrónica del webhook.|
|/registries/webhooks/ping/action|Desencadena el evento de ping que se enviará al webhook.|
|/registries/webhooks/read|Obtiene las propiedades de un webhook específico o enumera todos los webhooks del registro de contenedor especificado.|
|/registries/webhooks/write|Crea o actualiza un webhook de un registro de contenedor con los parámetros especificados.|
|/registries/write|Crea o actualiza un registro de contenedor con los parámetros especificados.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Operación | DESCRIPCIÓN |
|---|---|
|/containerServices/delete|Elimina el servicio de contenedor especificado|
|/containerServices/read|Obtiene el servicio de contenedor especificado|
|/containerServices/write|Pone o actualiza el servicio de contenedor especificado|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Operación | DESCRIPCIÓN |
|---|---|
|/applications/delete|Elimina una operación|
|/applications/listSecrets/action|Enumera secretos|
|/applications/listSingleSignOnToken/action|Lee tokens de inicio de sesión único|
|/applications/read|Lee una operación|
|/applications/write|Escribe una operación|
|/applications/write|Escribe una operación|
|/listCommunicationPreference/action|Enumera las preferencias de comunicación|
|/operations/read|operaciones de lectura|
|/updateCommunicationPreference/action|Actualiza las preferencias de comunicación|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Operación | DESCRIPCIÓN |
|---|---|
|/hubs/adobemetadata/action|Crea o actualiza los metadatos de Adobe de Azure Customer Insights.|
|/hubs/adobemetadata/read|Lee los metadatos de Adobe de Azure Customer Insights.|
|/hubs/authorizationPolicies/delete|Elimina todas las directivas de firma de acceso compartido de Azure Customer Insights|
|/hubs/authorizationPolicies/read|Lee todas las directivas de firma de acceso compartido de Azure Customer Insights|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Regenera la clave principal de la directiva de firma de acceso compartido de Azure Customer Insights|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Regenera la clave secundaria de la directiva de firma de acceso compartido de Azure Customer Insights|
|/hubs/authorizationPolicies/write|Crea o actualiza todas las directivas de firma de acceso compartido de Azure Customer Insights|
|/hubs/connectors/activate/action|Activa los conectores de Azure Customer Insights.|
|/hubs/connectors/activate/action|Activa los conectores de Azure Customer Insights.|
|/hubs/connectors/delete|Elimina todos los conectores de Azure Customer Insights|
|/hubs/connectors/getruntimestatus/action|Obtiene el estado del tiempo de ejecución de los conectores de Azure Customer Insights.|
|/hubs/connectors/mappings/activate/action|Activa las asignaciones de conectores de Azure Customer Insights.|
|/hubs/connectors/mappings/delete|Elimina todas las asignaciones de conectores de Azure Customer Insights|
|/hubs/connectors/mappings/operations/read|Lee los resultados de la operación encargada de asignar conectores de Azure Customer Insights.|
|/hubs/connectors/mappings/read|Lee todas las asignaciones de conectores de Azure Customer Insights|
|/hubs/connectors/mappings/write|Crea o actualiza todas las asignaciones de conectores de Azure Customer Insights|
|/hubs/connectors/operations/read|Lee los resultados de la operación encargada de los conectores de Azure Customer Insights.|
|/hubs/connectors/read|Lee todos los conectores de Azure Customer Insights|
|/hubs/connectors/saveauthinfo/action|Crea o actualiza la información de autenticación de los conectores de Azure Customer Insights.|
|/hubs/connectors/update/action|Actualiza los conectores de Azure Customer Insights.|
|/hubs/connectors/write|Crea o actualiza todos los conectores de Azure Customer Insights|
|/hubs/crmmetadata/action|Crea o actualiza los metadatos de CRM de Azure Customer Insights.|
|/hubs/crmmetadata/read|Lee los metadatos de CRM de Azure Customer Insights.|
|/hubs/delete|Elimina todos los concentradores de Azure Customer Insights|
|/hubs/gdpr/delete|Elimina los RGPD de Azure Customer Insights.|
|/hubs/gdpr/read|Lee los RGPD de Azure Customer Insights.|
|/hubs/gdpr/write|Crea o actualiza los RGPD de Azure Customer Insights.|
|/hubs/getbillingcredits/read|Obtiene los créditos de facturación del concentrador de Azure Customer Insights.|
|/hubs/getbillinghistory/read|Obtiene el historial de facturación del concentrador de Azure Customer Insights.|
|/hubs/images/delete|Elimina las imágenes de Azure Customer Insights.|
|/hubs/images/read|Lee las imágenes de Azure Customer Insights.|
|/hubs/images/write|Crea o actualiza las imágenes de Azure Customer Insights.|
|/hubs/interactions/delete|Elimina las interacciones de Azure Customer Insights.|
|/hubs/interactions/operations/read|Lee los resultados de las operaciones de las interacciones de Azure Customer Insights.|
|/hubs/interactions/read|Lee todas las interacciones de Azure Customer Insights|
|/hubs/interactions/suggestrelationshiplinks/action|Sugiere vínculos de relación para las interacciones de Azure Customer Insights.|
|/hubs/interactions/write|Crea o actualiza todas las interacciones de Azure Customer Insights|
|/hubs/kpi/delete|Elimina todos los indicadores clave de rendimiento de Azure Customer Insights|
|/hubs/kpi/operations/read|Lee los resultados de las operaciones de los indicadores clave de rendimiento de Azure Customer Insights.|
|/hubs/kpi/read|Lee todos los indicadores clave de rendimiento de Azure Customer Insights|
|/hubs/kpi/reprocess/action|Lee todos los indicadores clave de rendimiento de Azure Customer Insights.|
|/hubs/kpi/write|Crea o actualiza todos los indicadores clave de rendimiento de Azure Customer Insights|
|/hubs/links/delete|Elimina los vínculos de Azure Customer Insights.|
|/hubs/links/operations/read|Lee los resultados de la operación encargada de los vínculos de Azure Customer Insights.|
|/hubs/links/read|Lee los vínculos de Azure Customer Insights.|
|/hubs/links/write|Crea o actualiza los vínculos de Azure Customer.|
|/hubs/msemetadata/action|Crea o actualiza los metadatos de MSE de Azure Customer Insights.|
|/hubs/msemetadata/read|Lee los metadatos de MSE de Azure Customer Insights.|
|/hubs/operationresults/read|Obtiene los resultados de las operaciones del concentrador de Azure Customer Insights.|
|/hubs/predictions/delete|Elimina las predicciones de Azure Customer Insights.|
|/hubs/predictions/operations/read|Lee los resultados de la operación encargada de las predicciones de Azure Customer Insights.|
|/hubs/predictions/read|Lee las predicciones de Azure Customer Insights.|
|/hubs/predictions/write|Crea o actualiza las predicciones de Azure Customer.|
|/hubs/predictivematchpolicies/delete|Elimina las directivas de coincidencia predictiva de Azure Customer Insights.|
|/hubs/predictivematchpolicies/operations/read|Lee el resultado de la operación de las directivas de coincidencia predictiva de Azure Customer Insights.|
|/hubs/predictivematchpolicies/read|Lee las directivas de coincidencia predictiva de Azure Customer Insights.|
|/hubs/predictivematchpolicies/write|Crea o actualiza las directivas de coincidencia predictiva de Azure Customer Insights.|
|/hubs/profiles/delete|Elimina los perfiles de Azure Customer Insights.|
|/hubs/profiles/operations/read|Lee los resultados de la operación encargada del perfil de Azure Customer Insights.|
|/hubs/profiles/read|Lee todos los perfiles de Azure Customer Insights|
|/hubs/profiles/write|Escribe todos los perfiles de Azure Customer Insights|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|Obtiene los registros disponibles del recurso|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles del recurso|
|/hubs/read|Lee todos los concentradores de Azure Customer Insights|
|/hubs/relationshiplinks/delete|Elimina los vínculos de relación de Azure Customer Insights.|
|/hubs/relationshiplinks/operations/read|Lee los resultados de la operación encargada de los vínculos de relación de Azure Customer Insights.|
|/hubs/relationshiplinks/read|Lee los vínculos de relación de Azure Customer Insights.|
|/hubs/relationshiplinks/write|Crea o actualiza los vínculos de relación de Azure Customer Insights.|
|/hubs/relationships/delete|Elimina las relaciones de Azure Customer Insights.|
|/hubs/relationships/operations/read|Lee los resultados de la operación encargada de las relaciones de Azure Customer Insights.|
|/hubs/relationships/read|Lee las relaciones de Azure Customer Insights.|
|/hubs/relationships/write|Crea o actualiza las relaciones de Azure Customer Insights.|
|/hubs/roleAssignments/delete|Elimina todas las asignaciones de control de acceso basado en rol de Azure Customer Insights|
|/hubs/roleAssignments/operations/read|Lee los resultados de la operación para la asignación de RBAC de Azure Customer Insights.|
|/hubs/roleAssignments/read|Lee todas las asignaciones de control de acceso basado en rol de Azure Customer Insights|
|/hubs/roleAssignments/write|Crea o actualiza todas las asignaciones de control de acceso basado en rol de Azure Customer Insights|
|/hubs/roles/read|Lee los roles RBAC de Azure Customer Insights.|
|/hubs/salesforcemetadata/action|Crea o actualiza los metadatos de SalesForce de Azure Customer Insights.|
|/hubs/salesforcemetadata/read|Lee los metadatos de SalesForce de Azure Customer Insights.|
|/hubs/segments/delete|Elimina los segmentos de Azure Customer Insights.|
|/hubs/segments/dynamic/action|Administra los segmentos dinámicos de Azure Customer Insights.|
|/hubs/segments/read|Lee los segmentos de Azure Customer Insights.|
|/hubs/segments/static/action|Administra los segmentos estáticos de Azure Customer Insights.|
|/hubs/segments/write|Crea o actualiza los segmentos de Azure Customer Insights.|
|/hubs/sqlconnectionstrings/delete|Elimina los atributos SqlConnectionStrings de Azure Customer Insights.|
|/hubs/sqlconnectionstrings/read|Lee los atributos SqlConnectionStrings de Azure Customer Insights.|
|/hubs/sqlconnectionstrings/write|Crea o actualiza los atributos SqlConnectionStrings de Azure Customer Insights.|
|/hubs/suggesttypeschema/action|Crea un esquema de tipo "Suggest" a partir de los datos de ejemplo.|
|/hubs/tenantmanagement/read|Administra la configuración de los concentradores de Azure Customer Insights.|
|/hubs/views/delete|Elimina todas las vistas de aplicación de Azure Customer Insights|
|/hubs/views/read|Lee todas vistas de aplicación de Azure Customer Insights|
|/hubs/views/write|Crea o actualiza todas las vistas de aplicación de Azure Customer Insights|
|/hubs/widgettypes/read|Lee los tipos de widget de aplicación de Azure Customer Insights.|
|/hubs/write|Crea o actualiza todos los concentradores de Azure Customer Insights|
|/operations/read|Lee los metadatos de API de Azure Customer Insights.|
|/register/action|Registra la suscripción del proveedor de recursos de Customer Insights y habilita la creación de recursos de Customer Insights.|
|/unregister/action|Anula el registro de la suscripción del proveedor de recursos de Customer Insights.|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Operación | DESCRIPCIÓN |
|---|---|
|/catalogs/delete|Elimina el catálogo.|
|/catalogs/read|Obtiene las propiedades de los catálogos de la suscripción o grupo de recursos.|
|/catalogs/write|Crea un catálogo o actualiza las etiquetas y propiedades de este.|
|/checkNameAvailability/action|Comprueba la disponibilidad del nombre del catálogo del inquilino.|
|/operations/read|Enumera las operaciones disponibles en el proveedor de recursos de Microsoft.DataCatalog.|
|/register/action|Registra la suscripción con el proveedor de recursos de Microsoft.DataCatalog.|
|/unregister/action|Anula el registro de la suscripción del proveedor de recursos de Microsoft.DataCatalog.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Operación | DESCRIPCIÓN |
|---|---|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/datafactories/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de las factorías de datos.|
|/factories/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/factories/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/factories/providers/Microsoft.Insights/logDefinitions/read|Obtiene los registros disponibles de las factorías.|
|/factories/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de las factorías.|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Operación | DESCRIPCIÓN |
|---|---|
|/accounts/computePolicies/delete|Elimina una directiva de cálculo.|
|/accounts/computePolicies/read|Obtiene información sobre una directiva de cálculo.|
|/accounts/computePolicies/write|Crea o actualiza una directiva de cálculo.|
|/accounts/dataLakeStoreAccounts/delete|Anula la vinculación de una cuenta de DataLakeStore a la cuenta de DataLakeAnalytics.|
|/accounts/dataLakeStoreAccounts/read|Obtiene información sobre la cuenta vinculada de DataLakeStore que se encuentra en la cuenta de DataLakeAnalytics.|
|/accounts/dataLakeStoreAccounts/write|Crea o actualiza una cuenta vinculada de DataLakeStore en la cuenta de DataLakeAnalytics.|
|/accounts/delete|Elimina la cuenta de DataLakeAnalytics.|
|/accounts/firewallRules/delete|Elimina una regla de firewall.|
|/accounts/firewallRules/read|Obtiene información acerca de una regla de firewall.|
|/accounts/firewallRules/write|Crea o actualiza una regla de firewall.|
|/accounts/operationResults/read|Obtiene el resultado de una operación de la cuenta de DataLakeAnalytics.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico de la cuenta de DataLakeAnalytics.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de la cuenta de DataLakeAnalytics.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Obtiene los registros disponibles de la cuenta de DataLakeAnalytics.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de la cuenta de DataLakeAnalytics.|
|/accounts/read|Obtiene información acerca de una cuenta de DataLakeAnalytics existente.|
|/accounts/storageAccounts/Containers/listSasTokens/action|Enumera los token de SAS de los contenedores de almacenamiento de una cuenta de almacenamiento vinculada a una cuenta de DataLakeAnalytics.|
|/accounts/storageAccounts/Containers/read|Obtiene los contenedores de una cuenta de almacenamiento vinculada a una cuenta de DataLakeAnalytics.|
|/accounts/storageAccounts/delete|Anula la vinculación de una cuenta de almacenamiento a la cuenta de DataLakeAnalytics.|
|/accounts/storageAccounts/read|Obtiene información acerca de una cuenta de almacenamiento vinculada a una cuenta de DataLakeAnalytics.|
|/accounts/storageAccounts/write|Crea o actualiza una cuenta de almacenamiento vinculada a una cuenta de DataLakeAnalytics.|
|/accounts/TakeOwnership/action|Concede permiso para cancelar trabajos que enviaron otros usuarios.|
|/accounts/write|Crea o actualiza una cuenta de DataLakeAnalytics.|
|/locations/capability/read|Obtiene información sobre la capacidad de una suscripción acerca del uso de DataLakeAnalytics.|
|/locations/checkNameAvailability/action|Comprueba la disponibilidad de un nombre de cuenta de DataLakeAnalytics.|
|/locations/operationResults/read|Obtiene el resultado de una operación de la cuenta de DataLakeAnalytics.|
|/operations/read|Obtiene las operaciones disponibles de DataLakeAnalytics.|
|/register/action|Registra la suscripción a DataLakeAnalytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Operación | DESCRIPCIÓN |
|---|---|
|/accounts/delete|Elimina una cuenta de DataLakeStore.|
|/accounts/enableKeyVault/action|Habilita KeyVault para una cuenta de DataLakeStore.|
|/accounts/firewallRules/delete|Elimina una regla de firewall.|
|/accounts/firewallRules/read|Obtiene información acerca de una regla de firewall.|
|/accounts/firewallRules/write|Crea o actualiza una regla de firewall.|
|/accounts/operationResults/read|Obtiene el resultado de una operación de la cuenta de DataLakeStore.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico de la cuenta de DataLakeStore.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de la cuenta de DataLakeStore.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Obtiene los registros disponibles de la cuenta de DataLakeStore.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de la cuenta de DataLakeStore.|
|/accounts/read|Obtiene información acerca de una cuenta de DataLakeStore existente.|
|/accounts/Superuser/action|Concede privilegios de superusuario en Data Lake Store cuando se concede con Microsoft.Authorization/roleAssignments/write.|
|/accounts/trustedIdProviders/delete|Elimina un proveedor de identidades de confianza.|
|/accounts/trustedIdProviders/read|Obtiene información acerca de un proveedor de identidades de confianza.|
|/accounts/trustedIdProviders/write|Crea o actualiza un proveedor de identidades de confianza.|
|/accounts/write|Crea o actualiza una cuenta de DataLakeStore.|
|/locations/capability/read|Obtiene información sobre la capacidad de una suscripción acerca del uso de DataLakeStore.|
|/locations/checkNameAvailability/action|Comprueba la disponibilidad de un nombre de cuenta de DataLakeStore.|
|/locations/operationResults/read|Obtiene el resultado de una operación de la cuenta de DataLakeStore.|
|/operations/read|Obtiene las operaciones disponibles de DataLakeStore.|
|/register/action|Registra la suscripción a DataLakeStore.|

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

| Operación | DESCRIPCIÓN |
|---|---|
|/locations/performanceTiers/read|Devuelve la lista de niveles de rendimiento disponibles.|
|/performanceTiers/read|Devuelve la lista de niveles de rendimiento disponibles.|
|/servers/delete|Elimina un servidor existente.|
|/servers/firewallRules/delete|Elimina una regla de firewall existente.|
|/servers/firewallRules/read|Devuelve la lista de reglas de firewall de un servidor u obtiene las propiedades de la regla de firewall especificada.|
|/servers/firewallRules/write|Crea una regla de firewall con los parámetros especificados o actualiza una regla existente.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Devuelve los tipos de métricas que están disponibles para grupos de bases de datos|
|/servers/read|Devuelve la lista de servidores u obtiene las propiedades de un servidor específico.|
|/servers/recoverableServers/read|Devuelve la información recuperable de MySQL Server.|
|/servers/virtualNetworkRules/delete|Elimina una regla de Virtual Network existente.|
|/servers/virtualNetworkRules/read|Devuelve la lista de reglas de red virtual u obtiene las propiedades de una regla de red virtual específica.|
|/servers/virtualNetworkRules/write|Crea una regla de red virtual con los parámetros especificados o actualiza las propiedades o etiquetas de la regla de red virtual especificada.|
|/servers/write|Crea un servidor con los parámetros especificados o actualiza las propiedades o etiquetas del servidor especificado.|

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

| Operación | DESCRIPCIÓN |
|---|---|
|/locations/performanceTiers/read|Devuelve la lista de niveles de rendimiento disponibles.|
|/performanceTiers/read|Devuelve la lista de niveles de rendimiento disponibles.|
|/servers/delete|Elimina un servidor existente.|
|/servers/firewallRules/delete|Elimina una regla de firewall existente.|
|/servers/firewallRules/read|Devuelve la lista de reglas de firewall de un servidor u obtiene las propiedades de la regla de firewall especificada.|
|/servers/firewallRules/write|Crea una regla de firewall con los parámetros especificados o actualiza una regla existente.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Devuelve los tipos de registros que están disponibles para las bases de datos.|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Devuelve los tipos de métricas que están disponibles para grupos de bases de datos|
|/servers/read|Devuelve la lista de servidores u obtiene las propiedades de un servidor específico.|
|/servers/recoverableServers/read|Devuelve la información recuperable de PostgreSQL Server.|
|/servers/virtualNetworkRules/delete|Elimina una regla de Virtual Network existente.|
|/servers/virtualNetworkRules/read|Devuelve la lista de reglas de red virtual u obtiene las propiedades de una regla de red virtual específica.|
|/servers/virtualNetworkRules/write|Crea una regla de red virtual con los parámetros especificados o actualiza las propiedades o etiquetas de la regla de red virtual especificada.|
|/servers/write|Crea un servidor con los parámetros especificados o actualiza las propiedades o etiquetas del servidor especificado.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Operación | DESCRIPCIÓN |
|---|---|
|/checkNameAvailability/Action|Comprueba si el nombre de la instancia de IotHub está disponible|
|/checkProvisioningServiceNameAvailability/Action|Comprueba si el nombre de la instancia de IotHub está disponible|
|/ElasticPools/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/ElasticPools/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/elasticPools/iotHubTenants/Delete|Elimina el recurso de inquilino de IotHub.|
|/ElasticPools/IotHubTenants/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/ElasticPools/IotHubTenants/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete|Elimina los grupos de consumidores de EventHub|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read|Obtiene los grupos de consumidores de EventHub|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write|Crea un grupo de consumidores de EventHub|
|/elasticPools/iotHubTenants/exportDevices/Action|Exporta los dispositivos|
|/elasticPools/iotHubTenants/getStats/Read|Obtiene el recurso de las estadísticas de inquilino de IotHub.|
|/elasticPools/iotHubTenants/importDevices/Action|Importa los dispositivos|
|/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action|Obtiene la clave de inquilino de IotHub.|
|/elasticPools/iotHubTenants/jobs/Read|Obtiene detalles de los trabajos enviados en una determinada instancia de IotHub|
|/elasticPools/iotHubTenants/listKeys/Action|Obtiene las claves de inquilino de IotHub.|
|/ElasticPools/IotHubTenants/logDefinitions/read|Obtiene las definiciones de registros disponibles del servicio IotHub.|
|/ElasticPools/IotHubTenants/metricDefinitions/read|Obtiene las métricas disponibles del servicio IotHub|
|/elasticPools/iotHubTenants/quotaMetrics/Read|Obtiene las métricas de cuota|
|/elasticPools/iotHubTenants/Read|Obtiene el recurso de inquilino de IotHub.|
|/elasticPools/iotHubTenants/routing/routes/$testall/Action|Prueba un mensaje en todas las rutas existentes|
|/elasticPools/iotHubTenants/routing/routes/$testnew/Action|Prueba un mensaje en una ruta proporcionada de pruebas|
|/elasticPools/iotHubTenants/routingEndpointsHealth/Read|Obtiene el estado de todos los puntos de conexión de enrutamientos de una instancia de IotHub|
|/elasticPools/iotHubTenants/Write|Crea o actualiza el recurso de inquilino de IotHub.|
|/ElasticPools/metricDefinitions/read|Obtiene las métricas disponibles del servicio IotHub|
|/iotHubs/certificates/generateVerificationCode/Action|Genera el código de verificación.|
|/iotHubs/certificates/verify/Action|Comprueba el recurso del certificado.|
|/iotHubs/Delete|Elimina los recursos de IotHub|
|/IotHubs/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/IotHubs/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/iotHubs/eventGridFilters/Delete|Elimina el filtro de Event Grid.|
|/iotHubs/eventGridFilters/Read|Obtiene el filtro de Event Grid.|
|/iotHubs/eventGridFilters/Write|Crea un filtro de Event Grid o actualiza el existente.|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|Elimina los grupos de consumidores de EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Obtiene los grupos de consumidores de EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Crea un grupo de consumidores de EventHub|
|/iotHubs/exportDevices/Action|Exporta los dispositivos|
|/iotHubs/importDevices/Action|Importa los dispositivos|
|/iotHubs/iotHubKeys/listkeys/Action|Obtiene la clave de IotHub para el nombre especificado|
|/iotHubs/iotHubStats/Read|Obtiene las estadísticas de IotHub|
|/iotHubs/jobs/Read|Obtiene detalles de los trabajos enviados en una determinada instancia de IotHub|
|/iotHubs/listkeys/Action|Obtiene todas las claves de IotHub|
|/IotHubs/logDefinitions/read|Obtiene las definiciones de registros disponibles del servicio IotHub.|
|/IotHubs/metricDefinitions/read|Obtiene las métricas disponibles del servicio IotHub|
|/iotHubs/quotaMetrics/Read|Obtiene las métricas de cuota|
|/iotHubs/Read|Obtiene los recursos de IotHub|
|/iotHubs/routing/$testall/Action|Prueba un mensaje en todas las rutas existentes|
|/iotHubs/routing/$testnew/Action|Prueba un mensaje en una ruta proporcionada de pruebas|
|/iotHubs/routingEndpointsHealth/Read|Obtiene el estado de todos los puntos de conexión de enrutamientos de una instancia de IotHub|
|/iotHubs/skus/Read|Obtiene SKU válidas de IotHub|
|/iotHubs/Write|Crea o actualiza los recursos de IotHub|
|/operations/Read|Obtiene todas las operaciones de ResourceProvider|
|/provisioningServices/certificates/generateVerificationCode/Action|Genera el código de verificación.|
|/provisioningServices/certificates/verify/Action|Comprueba el recurso del certificado.|
|/provisioningServices/Delete|Elimina el recurso IotDps.|
|/provisioningServices/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/provisioningServices/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/provisioningServices/listkeys/Action|Obtiene todas las claves de IotDps.|
|/provisioningServices/logDefinitions/read|Obtiene las definiciones de registros disponibles del servicio de aprovisionamiento.|
|/provisioningServices/metricDefinitions/read|Obtiene las métricas disponibles del servicio de aprovisionamiento.|
|/provisioningServices/ProvisioningServiceKeys/listkeys/Action|Obtiene las claves de IotDps del nombre de clave.|
|/provisioningServices/Read|Obtiene el recurso IotDps.|
|/provisioningServices/skus/Read|Obtiene las SKU válidas de IotDps.|
|/provisioningServices/Write|Crea el recurso IotDps.|
|/register/action|Registra la suscripción para el proveedor de recursos de IotHub y habilita la creación de recursos de IotHub|
|/register/action|Registra la suscripción para el proveedor de recursos de IotHub y habilita la creación de recursos de IotHub|
|/usages/Read|Obtiene los detalles de uso de la suscripción para este proveedor.|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Operación | DESCRIPCIÓN |
|---|---|
|/labCenters/delete|Elimina los centros de laboratorio.|
|/labCenters/read|Lee los centros de laboratorio.|
|/labCenters/write|Agrega o modifica centros de laboratorio.|
|/labs/artifactSources/armTemplates/read|Lee las plantillas de Azure Resource Manager.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Genera una plantilla de ARM para el artefacto en cuestión, carga los archivos necesarios en una cuenta de almacenamiento y valida el artefacto generado.|
|/labs/artifactSources/artifacts/read|Lee artefactos.|
|/labs/artifactSources/delete|Elimina orígenes de artefacto.|
|/labs/artifactSources/read|Lee orígenes de artefacto.|
|/labs/artifactSources/write|Agrega o modifica orígenes de artefacto.|
|/labs/ClaimAnyVm/action|Reclama una máquina virtual aleatoria en el laboratorio.|
|/labs/costs/read|Lee los costos.|
|/labs/costs/write|Agrega o modifica costos.|
|/labs/CreateEnvironment/action|Crea máquinas virtuales en un laboratorio.|
|/labs/customImages/delete|Elimina imágenes personalizadas.|
|/labs/customImages/read|Lee imágenes personalizadas.|
|/labs/customImages/write|Agrega o modifica imágenes personalizadas.|
|/labs/delete|Elimina laboratorios.|
|/labs/ExportResourceUsage/action|Exporta el uso de recursos del laboratorio a una cuenta de almacenamiento|
|/labs/formulas/delete|Elimina fórmulas.|
|/labs/formulas/read|Lee fórmulas.|
|/labs/formulas/write|Agrega o modifica fórmulas.|
|/labs/galleryImages/read|Lee imágenes de la galería.|
|/labs/GenerateUploadUri/action|Genera un identificador URI para cargar imágenes de disco personalizadas en un laboratorio.|
|/labs/ImportVirtualMachine/action|Importa una máquina virtual en un laboratorio diferente.|
|/labs/ListVhds/action|Enumera las imágenes de disco disponibles para la creación de imágenes personalizadas.|
|/labs/notificationChannels/delete|Elimina notificationchannels.|
|/labs/notificationChannels/Notify/action|Envía una notificación al canal proporcionado.|
|/labs/notificationChannels/read|Lee notificationchannels.|
|/labs/notificationChannels/write|Agrega o modifica notificationchannels.|
|/labs/policySets/EvaluatePolicies/action|Evalúa la directiva de laboratorio.|
|/labs/policySets/policies/delete|Elimina directivas.|
|/labs/policySets/policies/read|Lee directivas.|
|/labs/policySets/policies/write|Agrega o modifica directivas.|
|/labs/read|Lee laboratorios.|
|/labs/schedules/delete|Elimina programaciones.|
|/labs/schedules/Execute/action|Ejecuta una programación.|
|/labs/schedules/ListApplicable/action|Enumera todas las programaciones aplicables|
|/labs/schedules/read|Lee programaciones.|
|/labs/schedules/write|Agrega o modifica programaciones.|
|/labs/serviceRunners/delete|Elimina ejecutores del servicio.|
|/labs/serviceRunners/read|Lee ejecutores del servicio.|
|/labs/serviceRunners/write|Agrega o modifica ejecutores del servicio.|
|/labs/users/delete|Elimina perfiles de usuario.|
|/labs/users/disks/Attach/action|Adjunta y crea la concesión del disco a la máquina virtual.|
|/labs/users/disks/delete|Elimina discos.|
|/labs/users/disks/Detach/action|Desasocia y separa la concesión del disco adjuntado a la máquina virtual.|
|/labs/users/disks/read|Lee discos.|
|/labs/users/disks/write|Agrega o modifica discos.|
|/labs/users/environments/delete|Elimina entornos.|
|/labs/users/environments/read|Lee entornos.|
|/labs/users/environments/write|Agrega o modifica entornos.|
|/labs/users/read|Lee perfiles de usuario.|
|/labs/users/secrets/delete|Elimina secretos.|
|/labs/users/secrets/read|Lee secretos.|
|/labs/users/secrets/write|Agrega o modifica secretos.|
|/labs/users/serviceFabrics/delete|Elimina instancias de Service Fabric.|
|/labs/users/serviceFabrics/ListApplicableSchedules/action|Enumera todas las programaciones aplicables|
|/labs/users/serviceFabrics/read|Lee instancias de Service Fabric.|
|/labs/users/serviceFabrics/schedules/delete|Elimina programaciones.|
|/labs/users/serviceFabrics/schedules/Execute/action|Ejecuta una programación.|
|/labs/users/serviceFabrics/schedules/read|Lee programaciones.|
|/labs/users/serviceFabrics/schedules/write|Agrega o modifica programaciones.|
|/labs/users/serviceFabrics/Start/action|Inicia una instancia de Service Fabric.|
|/labs/users/serviceFabrics/Stop/action|Detiene una instancia de Service Fabric.|
|/labs/users/serviceFabrics/write|Agrega o modifica instancias de Service Fabric.|
|/labs/users/write|Agrega o modifica perfiles de usuario.|
|/labs/virtualMachines/AddDataDisk/action|Conecta un disco de datos nuevo o existente a una máquina virtual.|
|/labs/virtualMachines/ApplyArtifacts/action|Aplica artefactos a la máquina virtual.|
|/labs/virtualMachines/Claim/action|Toma la propiedad de una máquina virtual existente|
|/labs/virtualMachines/delete|Elimina máquinas virtuales.|
|/labs/virtualMachines/DetachDataDisk/action|Desconecta el disco especificado de la máquina virtual.|
|/labs/virtualMachines/ListApplicableSchedules/action|Enumera todas las programaciones aplicables|
|/labs/virtualMachines/read|Lee máquinas virtuales.|
|/labs/virtualMachines/Restart/action|Reinicia una máquina virtual.|
|/labs/virtualMachines/schedules/delete|Elimina programaciones.|
|/labs/virtualMachines/schedules/Execute/action|Ejecuta una programación.|
|/labs/virtualMachines/schedules/read|Lee programaciones.|
|/labs/virtualMachines/schedules/write|Agrega o modifica programaciones.|
|/labs/virtualMachines/Start/action|Inicia una máquina virtual.|
|/labs/virtualMachines/Stop/action|Detención de una máquina virtual|
|/labs/virtualMachines/TransferDisks/action|Le transfiere a usted la propiedad de los discos de datos de una máquina virtual.|
|/labs/virtualMachines/UnClaim/action|Transfiere la posesión de una máquina virtual existente.|
|/labs/virtualMachines/write|Agrega o modifica máquinas virtuales.|
|/labs/virtualNetworks/delete|Elimina redes virtuales.|
|/labs/virtualNetworks/read|Lee redes virtuales.|
|/labs/virtualNetworks/write|Agrega o modifica redes virtuales.|
|/labs/write|Agrega o modifica laboratorios.|
|/locations/operations/read|Lee operaciones.|
|/register/action|Registra la suscripción|
|/schedules/delete|Elimina programaciones.|
|/schedules/Execute/action|Ejecuta una programación.|
|/schedules/read|Lee programaciones.|
|/schedules/Retarget/action|Actualiza un identificador de recurso de destino de la programación.|
|/schedules/write|Agrega o modifica programaciones.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Operación | DESCRIPCIÓN |
|---|---|
|/databaseAccountNames/read|Comprueba la disponibilidad del nombre.|
|/databaseAccounts/changeResourceGroup/action|Cambia el grupo de recursos de una cuenta de base de datos|
|/databaseAccounts/databases/collections/metricDefinitions/read|Lee las definiciones de métricas de la colección.|
|/databaseAccounts/databases/collections/metrics/read|Lee las métricas de la colección.|
|/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read|Lee las métricas de nivel de clave de partición de la cuenta de base de datos.|
|/databaseAccounts/databases/collections/partitions/metrics/read|Lee las métricas de nivel de clave de partición de la cuenta de base de datos.|
|/databaseAccounts/databases/collections/partitions/usages/read|Lee los usos de nivel de partición de la cuenta de base de datos.|
|/databaseAccounts/databases/collections/usages/read|Lee los usos de la colección.|
|/databaseAccounts/databases/metricDefinitions/read|Lee las definiciones de métrica de la base de datos|
|/databaseAccounts/databases/metrics/read|Lee las métricas de base de datos.|
|/databaseAccounts/databases/usages/read|Lee los usos de la base de datos.|
|/databaseAccounts/delete|Elimina las cuentas de base de datos.|
|/databaseAccounts/failoverPriorityChange/action|Cambia las prioridades de conmutación por error de las regiones de una cuenta de base de datos. Esto se utiliza para realizar una operación manual de conmutación por error|
|/databaseAccounts/listConnectionStrings/action|Obtiene las cadenas de conexión para una cuenta de base de datos|
|/databaseAccounts/listKeys/action|Enumera las claves de una cuenta de base de datos|
|/databaseAccounts/metricDefinitions/read|Lee las definiciones de métricas de la cuenta de base de datos.|
|/databaseAccounts/metrics/read|Lee las métricas de la cuenta de base de datos.|
|/databaseAccounts/operationResults/read|Lee el estado de una operación asincrónica.|
|/databaseAccounts/percentile/metrics/read|Lee la latencia de las métricas.|
|/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read|Lee las métricas de latencia para una región específica de origen y de destino.|
|/databaseAccounts/percentile/targetRegion/metrics/read|Lee las métricas de latencia para una región específica de destino.|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read|Obtiene las categorías de registro disponibles para la cuenta de base de datos.|
|/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles para la cuenta de base de datos.|
|/databaseAccounts/read|Lee una cuenta de base de datos.|
|/databaseAccounts/readonlykeys/action|Lee las claves de solo lectura de la cuenta de base de datos.|
|/databaseAccounts/readonlykeys/read|Lee las claves de solo lectura de la cuenta de base de datos.|
|/databaseAccounts/regenerateKey/action|Rota las claves de una cuenta de base de datos|
|/databaseAccounts/region/databases/collections/metrics/read|Lee las métricas de la colección regional.|
|/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read|Lee las métricas de nivel de clave de partición de la cuenta de base de datos regional.|
|/databaseAccounts/region/databases/collections/partitions/metrics/read|Lee las métricas de nivel de partición de la cuenta de base de datos regional.|
|/databaseAccounts/region/databases/collections/partitions/read|Las particiones de la cuenta de base de datos de una colección.|
|/databaseAccounts/region/metrics/read|Lee las métricas de la cuenta de base de datos y de región.|
|/databaseAccounts/usages/read|Lee los usos de la cuenta de base de datos.|
|/databaseAccounts/write|Actualiza una cuenta de base de datos.|
|/locations/deleteVirtualNetworkOrSubnets/action|Notifica a Microsoft.DocumentDB que se está eliminando una red virtual o una subred.|
|/operationResults/read|Lee el estado de una operación asincrónica.|
|/operations/read|Lee las operaciones disponibles para Microsoft DocumentDB. |
|/register/action| Registra el proveedor de recursos de Microsoft DocumentDB de la suscripción.|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Operación | DESCRIPCIÓN |
|---|---|
|/checkDomainAvailability/Action|Comprueba si un dominio está disponible para su compra|
|/domains/Delete|Elimina un dominio ya existente.|
|/domains/domainownershipidentifiers/Delete|Elimina el identificador de propiedad.|
|/domains/domainownershipidentifiers/Read|Enumera los identificadores de propiedad.|
|/domains/domainownershipidentifiers/Read|Obtiene el identificador de propiedad.|
|/domains/domainownershipidentifiers/Write|Crea o actualiza el identificador.|
|/domains/operationresults/Read|Obtiene una operación de dominio|
|/domains/operations/Read|Enumera todas las operaciones del registro de dominio del servicio de la aplicación.|
|/domains/Read|Obtiene la lista de dominios|
|/domains/Read|Obtiene el destino.|
|/domains/renew/Action|Renueva un dominio ya existente.|
|/domains/Write|Agrega un nuevo dominio o actualiza uno existente|
|/generateSsoRequest/Action|Genera una solicitud para iniciar sesión en el centro de control de dominios.|
|/listDomainRecommendations/Action|Recupera las recomendaciones de dominio de lista en función de las palabras clave|
|/register/action|Registra el proveedor de recursos de Microsoft Domains de la suscripción|
|/topLevelDomains/listAgreements/Action|Enumera la acción del contrato.|
|/topLevelDomains/Read|Obtiene los dominios de nivel superior.|
|/topLevelDomains/Read|Obtiene el dominio de nivel superior.|
|/validateDomainRegistrationInformation/Action|Valida un objeto de compra de dominio sin enviarlo|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Operación | DESCRIPCIÓN |
|---|---|
|/lcsprojects/clouddeployments/read|Muestra las implementaciones de evaluación de Microsoft Dynamics AX 2012 R3 de un proyecto de Microsoft Dynamics Lifecycle Services que pertenece a un usuario|
|/lcsprojects/clouddeployments/write|Crea las implementaciones de evaluación de Microsoft Dynamics AX 2012 R3 de un proyecto de Microsoft Dynamics Lifecycle Services que pertenece a un usuario. Las implementaciones se pueden administrar desde el portal de administración de Azure|
|/lcsprojects/connectors/read|Lee los conectores que pertenecen a un proyecto de Microsoft Dynamics Lifecycle Services|
|/lcsprojects/connectors/write|Crea y actualiza los conectores que pertenecen a un proyecto de Microsoft Dynamics Lifecycle Services|
|/lcsprojects/delete|Elimina los proyectos de Microsoft Dynamics Lifecycle Services que pertenecen al usuario|
|/lcsprojects/read|Muestra los proyectos de Microsoft Dynamics Lifecycle Services que pertenecen a un usuario|
|/lcsprojects/write|Crea y actualiza los proyectos de Microsoft Dynamics Lifecycle Services que pertenecen al usuario. Solo se pueden actualizar las propiedades de nombre y descripción. No se puede actualizar la suscripción y ubicación asociadas con el proyecto después de la creación|

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

| Operación | DESCRIPCIÓN |
|---|---|
|/eventSubscriptions/delete|Elimina una clase eventSubscription.|
|/eventSubscriptions/getFullUrl/action|Obtiene la dirección URL completa de la suscripción a eventos.|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico de las suscripciones a eventos.|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de las suscripciones a eventos.|
|/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de eventSubscriptions.|
|/eventSubscriptions/read|Lee una clase eventSubscription.|
|/eventSubscriptions/write|Crea o actualiza una clase eventSubscription.|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico de los temas.|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de los temas.|
|/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de los temas.|
|/register/action|Registra la clase eventSubscription del proveedor de recursos de EventGrid y habilita la creación de suscripciones de Event Grid.|
|/topics/delete|Eliminación de un tema|
|/topics/listKeys/action|Enumera las claves del tema.|
|/topics/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico de los temas.|
|/topics/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de los temas.|
|/topics/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de los temas.|
|/topics/read|Lee un tema.|
|/topics/regenerateKey/action|Regenera la clave del tema.|
|/topics/write|Crea o actualiza un tema.|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Operación | DESCRIPCIÓN |
|---|---|
|/checkNameAvailability/action|Comprueba la disponibilidad del espacio de nombres en una suscripción dada.|
|/checkNamespaceAvailability/action|Comprueba la disponibilidad del espacio de nombres en una suscripción dada. Esta API está en desuso; en su lugar, use CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/action|Actualiza la regla de autorización del espacio de nombres. Esta API está en desuso. Use una llamada PUT para actualizar la regla de autorización del espacio de nombres en su lugar. Esta operación no se admite en la versión de API del 01/04/2017.|
|/namespaces/authorizationRules/delete|Elimina la regla de autorización del espacio de nombres. No se puede eliminar la regla predeterminada de autorización del espacio de nombres. |
|/namespaces/authorizationRules/listkeys/action|Obtiene la cadena de conexión al espacio de nombres|
|/namespaces/authorizationRules/read|Obtiene la lista de descripciones de reglas de autorización de espacios de nombres.|
|/namespaces/authorizationRules/regenerateKeys/action|Regenera la clave principal o secundaria del recurso|
|/namespaces/authorizationRules/write|Crea reglas de autorización en el nivel del espacio de nombres y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/namespaces/Delete|Elimina el recurso del espacio de nombres|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Obtiene las claves de las reglas de autorización del espacio de nombres principal de recuperación ante desastres.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Obtiene las reglas de autorización del espacio de nombres principal de recuperación ante desastres.|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Deshabilita la recuperación ante desastres y deja de replicar los cambios de los espacios de nombres principales a los secundarios.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Comprueba la disponibilidad del alias del espacio de nombres en una suscripción dada.|
|/namespaces/disasterRecoveryConfigs/delete|Elimina la configuración de la recuperación ante desastres asociada con el espacio de nombres. Esta operación sólo se puede invocar a través del espacio de nombres principal.|
|/namespaces/disasterRecoveryConfigs/failover/action|Invoca una conmutación por error de GEO DR y vuelve a configurar el alias del espacio de nombres para que apunte al espacio de nombres secundario.|
|/namespaces/disasterRecoveryConfigs/read|Obtiene la configuración de la recuperación ante desastres asociada con el espacio de nombres.|
|/namespaces/disasterRecoveryConfigs/write|Crea o actualiza la configuración de la recuperación ante desastres asociada con el espacio de nombres.|
|/namespaces/eventhubs/authorizationRules/action|Operación para actualizar EventHub. Esta operación no se admite en la versión de API del 01/04/2017. Reglas de autorización. Use una llamada PUT para actualizar la regla de autorización.|
|/namespaces/eventhubs/authorizationRules/delete|Operación para eliminar las reglas de autorización de EventHub|
|/namespaces/eventhubs/authorizationRules/listkeys/action|Obtiene la cadena de conexión a EventHub|
|/namespaces/eventhubs/authorizationRules/read| Obtiene la lista de reglas de autorización de EventHub|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|Regenera la clave principal o secundaria del recurso|
|/namespaces/eventhubs/authorizationRules/write|Crea reglas de autorización de EventHub y actualiza sus propiedades. Los derechos de acceso de las reglas de autorización se pueden actualizar.|
|/namespaces/eventHubs/consumergroups/Delete|Operación para eliminar un recurso de ConsumerGroup|
|/namespaces/eventHubs/consumergroups/read|Obtiene una lista de descripciones de recursos de ConsumerGroup|
|/namespaces/eventHubs/consumergroups/write|Crea o actualiza las propiedades de ConsumerGroup.|
|/namespaces/eventhubs/Delete|Operación para eliminar los recursos de EventHub|
|/namespaces/eventhubs/read|Obtiene una lista de descripciones de recursos de EventHub|
|/namespaces/eventhubs/write|Crea o actualiza propiedades de EventHub.|
|/namespaces/messagingPlan/read|Obtiene el plan de mensajería para un espacio de nombres. Esta API está en desuso. Las propiedades expuestas a través del recurso MessagingPlan se mueven al recurso del espacio de nombres (principal) en versiones posteriores de la API. Esta operación no se admite en la versión de API del 01/04/2017.|
|/namespaces/messagingPlan/write|Actualiza el plan mensajería de un espacio de nombres. Esta API está en desuso. Las propiedades expuestas a través del recurso MessagingPlan se mueven al recurso del espacio de nombres (principal) en versiones posteriores de la API. Esta operación no se admite en la versión de API del 01/04/2017.|
|/namespaces/operationresults/read|Obtiene el estado de la operación del espacio de nombres.|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene una lista de descripciones de recursos de configuración de diagnósticos del espacio de nombres|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Obtiene una lista de descripciones de recursos de configuración de diagnósticos del espacio de nombres|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Obtiene una lista de descripciones de recursos de registros del espacio de nombres|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Obtiene una lista de descripciones de recursos de métricas del espacio de nombres|
|/namespaces/read|Obtiene la lista de descripción del recurso del espacio de nombres|
|/namespaces/write|Crea un recurso de espacio de nombres y actualiza sus propiedades. Las etiquetas y la capacidad del espacio de nombres son las propiedades que se pueden actualizar.|
|/operations/read|Obtiene operaciones.|
|/register/action|Registra la suscripción para el proveedor de recursos de EventHub y habilita la creación de recursos de EventHub|
|/sku/read|Obtiene una lista de descripciones de recursos de SKU.|
|/sku/regions/read|Obtiene una lista de descripciones de recursos de SkuRegions.|
|/unregister/action|Registra el proveedor de recursos de EventHub.|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Operación | DESCRIPCIÓN |
|---|---|
|/features/read|Obtiene las características de una suscripción.|
|/providers/features/read|Obtiene la característica de una suscripción de un proveedor de recursos determinado.|
|/providers/features/register/action|Registra la característica de una suscripción de un proveedor de recursos determinado.|
|/providers/features/unregister/action|Anula el registro de la característica de una suscripción de un proveedor de recursos determinado.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Operación | DESCRIPCIÓN |
|---|---|
|/clusters/changerdpsetting/action|Cambia la configuración de RDP de un clúster de HDInsight|
|/clusters/configurations/action|Actualiza la configuración de un clúster de HDInsight|
|/clusters/configurations/read|Obtiene las configuraciones de un clúster de HDInsight|
|/clusters/delete|Elimina un clúster de HDInsight|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso de clúster de HDInsight.|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso de clúster de HDInsight.|
|/clusters/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles del clúster de HDInsight.|
|/clusters/read|Obtiene detalles sobre el clúster de HDInsight|
|/clusters/roles/resize/action|Cambia el tamaño de un clúster de HDInsight|
|/clusters/write|Crea o actualiza un clúster de HDInsight|
|/locations/capabilities/read|Obtiene las funcionalidades de la suscripción|
|/locations/checkNameAvailability/read|Comprueba la disponibilidad del nombre|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Operación | DESCRIPCIÓN |
|---|---|
|/jobs/delete|Elimina un trabajo existente.|
|/jobs/listBitLockerKeys/action|Obtiene las claves de BitLocker para el trabajo especificado.|
|/jobs/read|Obtiene las propiedades del trabajo especificado o devuelve la lista de trabajos.|
|/jobs/write|Crea un trabajo con los parámetros especificados o actualiza las propiedades o etiquetas del trabajo especificado.|
|/locations/read|Obtiene las propiedades de la ubicación especificada o devuelve la lista de ubicaciones.|
|/register/action|Registra la suscripción del proveedor de recursos de Import/Export y habilita la creación de trabajos de Import/Export.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Operación | DESCRIPCIÓN |
|---|---|
|/ActionGroups/Delete|Elimina un grupo de acciones.|
|/ActionGroups/Read|Lee un grupo de acciones.|
|/ActionGroups/Write|Escribe un grupo de acciones.|
|/ActivityLogAlerts/Activated/Action|Se desencadena la alerta del registro de actividad|
|/ActivityLogAlerts/Delete|Elimina una alerta de registro de actividad.|
|/ActivityLogAlerts/Read|Lee una alerta de registro de actividad.|
|/ActivityLogAlerts/Write|Lee una alerta de registro de actividad.|
|/AlertRules/Activated/Action|Regla de alerta activada|
|/AlertRules/Delete|Elimina una configuración de regla de alerta|
|/AlertRules/Incidents/Read|Lee una configuración de incidentes de regla de alerta|
|/AlertRules/Read|Lee una configuración de regla de alerta|
|/AlertRules/Resolved/Action|Regla de alerta resuelta|
|/AlertRules/Throttled/Action|Se limita la regla de alerta|
|/AlertRules/Write|Escribe en una configuración de regla de alerta|
|/AutoscaleSettings/Delete|Elimina una configuración de opciones de escalado automático|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|Lee definiciones de métricas|
|/AutoscaleSettings/Read|Lee una configuración de opciones de escalado automático|
|/AutoscaleSettings/Scaledown/Action|Operación de reducción vertical de autoescala|
|/AutoscaleSettings/Scaleup/Action|Operación de escalado vertical de autoescala|
|/AutoscaleSettings/Write|Escribe en una configuración de opciones de escalado automático|
|/Components/AnalyticsItems/Delete|Elimina un elemento de análisis de Application Insights.|
|/Components/AnalyticsItems/Read|Lee un elemento de análisis de Application Insights.|
|/Components/AnalyticsItems/Write|Escribe un elemento de análisis de Application Insights.|
|/Components/AnalyticsTables/Action|Acción de la tabla de análisis de Application Insights.|
|/Components/AnalyticsTables/Delete|Elimina un esquema de la tabla de análisis de Application Insights.|
|/Components/AnalyticsTables/Read|Lee un esquema de la tabla de análisis de Application Insights.|
|/Components/AnalyticsTables/Write|Escribe un esquema de la tabla de análisis de Application Insights.|
|/Components/Annotations/Delete|Elimina una anotación de Application Insights.|
|/Components/Annotations/Read|Lee una anotación de Application Insights.|
|/Components/Annotations/Write|Escribe una anotación de Application Insights.|
|/Components/Api/Read|Lee una API de datos de componentes de Application Insights.|
|/Components/ApiKeys/Action|Genera una clave de API de Application Insights.|
|/Components/ApiKeys/Delete|Elimina una clave de API de Application Insights.|
|/Components/ApiKeys/Read|Lee una clave de API de Application Insights.|
|/Components/BillingPlanForComponent/Read|Lee un plan de facturación para el componente de Application Insights.|
|/Components/CurrentBillingFeatures/Read|Lee las características de facturación actuales para el componente de Application Insights.|
|/Components/CurrentBillingFeatures/Write|Escribe las características de facturación actuales para el componente de Application Insights.|
|/Components/DefaultWorkItemConfig/Read|Lee una configuración predeterminada de integración de ALM de Application Insights.|
|/Components/Delete|Elimina la configuración de un componente de Application Insights.|
|/Components/ExportConfiguration/Action|Acción para exportar la configuración de Application Insights.|
|/Components/ExportConfiguration/Delete|Elimina la configuración exportada de Application Insights.|
|/Components/ExportConfiguration/Read|Lee la configuración exportada de Application Insights.|
|/Components/ExportConfiguration/Write|Escribe la configuración exportada de Application Insights.|
|/Components/ExtendQueries/Read|Lee los resultados de una consulta extendida de los componentes de Application Insights.|
|/Components/Favorites/Delete|Elimina un elemento favorito de Application Insights.|
|/Components/Favorites/Read|Lee un elemento favorito de Application Insights.|
|/Components/Favorites/Write|Escribe un elemento favorito de Application Insights.|
|/Components/FeatureCapabilities/Read|Lee las capacidades de las características de los componentes de Application Insights.|
|/Components/GetAvailableBillingFeatures/Read|Lee las características de facturación disponibles para los componentes de Application Insights.|
|/Components/GetToken/Read|Lee un token de un componente de Application Insights.|
|/Components/ListMigrationDate/Action|Obtiene la fecha de migración de la suscripción.|
|/Components/ListMigrationDate/Read|Obtiene la fecha de migración de la suscripción.|
|/Components/MetricDefinitions/Read|Lee las definiciones de las métricas de los componentes de Application Insights.|
|/Components/Metrics/Read|Lee las métricas de los componentes de Application Insights.|
|/Components/MigrateToNewpricingModel/Action|Migra la suscripción al nuevo modelo de precios.|
|/Components/Move/Action|Mueve un componente de Application Insights a otro grupo de recursos o suscripción.|
|/Components/MyAnalyticsItems/Delete|Elimina un elemento de análisis personal de Application Insights.|
|/Components/MyAnalyticsItems/Read|Lee un elemento de análisis personal de Application Insights.|
|/Components/MyAnalyticsItems/Write|Escribe un elemento de análisis personal de Application Insights.|
|/Components/MyFavorites/Read|Lee un elemento favorito personal de Application Insights.|
|/Components/PricingPlans/Read|Lee un plan de precios para los componentes de Application Insights.|
|/Components/PricingPlans/Write|Escribe un plan de precios para los componentes de Application Insights.|
|/Components/ProactiveDetectionConfigs/Read|Lee la configuración de detección proactiva de Application Insights.|
|/Components/ProactiveDetectionConfigs/Write|Escribe la configuración de detección proactiva de Application Insights.|
|/Components/providers/Microsoft.Insights/MetricDefinitions/Read|Lee definiciones de métricas|
|/Components/QuotaStatus/Read|Lee el estado de la cuota de los componentes de Application Insights.|
|/Components/Read|Lee la configuración de los componente de Application Insights.|
|/Components/RollbackToLegacyPricingModel/Action|Suscripción de reversión al modelo de precios heredado.|
|/Components/SyntheticMonitorLocations/Read|Lee las ubicaciones de WebTest de Application Insights.|
|/Components/WorkItemConfigs/Delete|Elimina una configuración de integración de ALM de Application Insights.|
|/Components/WorkItemConfigs/Read|Lee una configuración de integración de ALM de Application Insights.|
|/Components/WorkItemConfigs/Write|Escribe una configuración de integración de ALM de Application Insights.|
|/Components/Write|Escribe una configuración de componente de Application Insights.|
|/DiagnosticSettings/Delete|Elimina la configuración de opciones de diagnóstico|
|/DiagnosticSettings/Read|Lee una opción de configuración de diagnóstico|
|/DiagnosticSettings/Write|Escribe la configuración de opciones de diagnóstico|
|/EventCategories/Read|Lee una categoría de eventos.|
|/eventtypes/digestevents/Read|Lee un resumen de tipos de evento de administración|
|/eventtypes/values/Read|Lee valores de tipo de evento de administración|
|/ExtendedDiagnosticSettings/Delete|Elimina la configuración de opciones de diagnóstico extendida|
|/ExtendedDiagnosticSettings/Read|Lee una configuración de opciones de diagnóstico extendida|
|/ExtendedDiagnosticSettings/Write|Escribe en la configuración de opciones de diagnóstico extendida|
|/LogDefinitions/Read|Lee definiciones de registro|
|/LogProfiles/Delete|Elimina la configuración de perfiles de registro|
|/LogProfiles/Read|Lee perfiles de registro|
|/LogProfiles/Write|Escribe en una configuración de perfil de registro|
|/MetricAlerts/Delete|Elimina una alerta de métrica.|
|/MetricAlerts/Read|Lee una alerta de métrica.|
|/MetricAlerts/Write|Escribe una alerta de métrica.|
|/MetricDefinitions/Microsoft.Insights/Read|Lee definiciones de métricas|
|/MetricDefinitions/providers/Microsoft.Insights/Read|Lee definiciones de métricas|
|/MetricDefinitions/Read|Lee definiciones de métricas|
|/Metrics/providers/Metrics/Read|Lee métricas|
|/Metrics/Read|Lee métricas|
|/Metrics/Write|Escribe métricas.|
|/Operations/Read|Lee operaciones.|
|/Register/Action|Registra el proveedor de Microsoft Insights|
|/Tenants/Register/Action|Inicializa el proveedor de Microsoft Insights.|
|/Unregister/Action|Registra el proveedor de Microsoft Insights|
|/Webtests/Delete|Elimina una configuración de WebTest.|
|/Webtests/GetToken/Read|Lee un token de WebTest.|
|/Webtests/MetricDefinitions/Read|Lee las definiciones de métricas de WebTest.|
|/Webtests/Metrics/Read|Lee las métricas de WebTest.|
|/Webtests/Read|Lee una configuración de WebTest.|
|/Webtests/Write|Escribe una configuración de WebTest.|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Operación | DESCRIPCIÓN |
|---|---|
|/checkNameAvailability/read|Comprueba que un nombre de almacén de claves es válido y que no está en uso|
|/deletedVaults/read|Ve las propiedades de los almacenes de claves eliminados temporalmente|
|/hsmPools/delete|Elimina un grupo HSM.|
|/hsmPools/joinVault/action|Une un almacén de claves a un grupo HSM.|
|/hsmPools/read|Consulta las propiedades de un grupo HSM.|
|/hsmPools/write|Crea un nuevo grupo HSM para actualizar las propiedades de un grupo HSM existente.|
|/locations/deletedVaults/purge/action|Purga un almacén de claves eliminado temporalmente|
|/locations/deletedVaults/read|Ve las propiedades de un almacén de claves eliminado temporalmente|
|/locations/deleteVirtualNetworkOrSubnets/action|Envía una notificación a Microsoft.KeyVault que indica que la subred o la red virtual se está eliminando.|
|/locations/operationResults/read|Comprueba el resultado de una operación de larga ejecución|
|/operations/read|Enumera las operaciones disponibles en el proveedor de recursos de Microsoft.KeyVault.|
|/register/action|Registra una suscripción|
|/unregister/action|Anula el registro de una suscripción.|
|/vaults/accessPolicies/write|Actualiza una directiva de acceso mediante combinación o sustitución, o agrega una nueva directiva de acceso a un almacén.|
|/vaults/delete|Elimina un almacén de claves|
|/vaults/deploy/action|Permite el acceso a secretos en un almacén de claves al implementar recursos de Azure|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Read|Obtiene la configuración de diagnóstico del recurso|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Write|Crea o actualiza la configuración de diagnóstico del recurso|
|/vaults/providers/Microsoft.Insights/logDefinitions/read|Obtiene los registros disponibles para un almacén de claves.|
|/vaults/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles para un almacén de claves.|
|/vaults/read|Ve las propiedades de un almacén de claves|
|/vaults/secrets/read|Ve las propiedades de un secreto, pero no su valor|
|/vaults/secrets/write|Crea un secreto nuevo o actualiza el valor de uno que ya existe|
|/vaults/write|Crea un nuevo almacén de claves o actualiza las propiedades de uno que ya existe|

## <a name="microsoftlabservices"></a>Microsoft.LabServices

| Operación | DESCRIPCIÓN |
|---|---|
|/labAccounts/CreateLab/action|Crea un laboratorio en una cuenta de laboratorio.|
|/labAccounts/delete|Elimina las cuentas de laboratorio.|
|/labAccounts/labs/delete|Elimina laboratorios.|
|/labAccounts/labs/environmentSettings/delete|Elimina la configuración del entorno.|
|/labAccounts/labs/environmentSettings/environments/delete|Elimina entornos.|
|/labAccounts/labs/environmentSettings/environments/read|Lee entornos.|
|/labAccounts/labs/environmentSettings/environments/write|Agrega o modifica entornos.|
|/labAccounts/labs/environmentSettings/Publish/action|Aprovisiona/desaprovisiona los recursos necesarios para configurar un entorno según el estado actual de la configuración del laboratorio o entorno.|
|/labAccounts/labs/environmentSettings/read|Lee la configuración del entorno.|
|/labAccounts/labs/environmentSettings/write|Agrega o modifica la configuración del entorno.|
|/labAccounts/labs/read|Lee laboratorios.|
|/labAccounts/labs/users/delete|Elimina usuarios.|
|/labAccounts/labs/users/read|Lee usuarios.|
|/labAccounts/labs/users/write|Agrega o modifica usuarios.|
|/labAccounts/labs/write|Agrega o modifica laboratorios.|
|/labAccounts/read|Lee las cuentas de laboratorio.|
|/labAccounts/write|Agrega o modifica las cuentas de laboratorio.|
|/locations/operations/read|Lee operaciones.|
|/register/action|Registra la suscripción|

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

| Operación | DESCRIPCIÓN |
|---|---|
|/accounts/delete|Elimina una cuenta de Location Based Services.|
|/accounts/listKeys/action|Enumera las claves de la cuenta de Location Based Services.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles para las cuentas de Location Based Services.|
|/accounts/read|Obtiene una cuenta de Location Based Services.|
|/accounts/regenerateKey/action|Genera una nueva clave principal o secundaria para la cuenta de Location Based Services.|
|/accounts/write|Crea o actualiza una cuenta de Location Based Services.|
|/register/action|Registra el proveedor.|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Operación | DESCRIPCIÓN |
|---|---|
|/integrationAccounts/agreements/delete|Elimina el contrato de la cuenta de integración.|
|/integrationAccounts/agreements/listContentCallbackUrl/action|Obtiene la dirección URL de devolución de llamada para el contenido del contrato en la cuenta de integración.|
|/integrationAccounts/agreements/read|Lee el contrato en la cuenta de integración.|
|/integrationAccounts/agreements/write|Crea o actualiza el contrato en la cuenta de integración.|
|/integrationAccounts/assemblies/delete|Elimina el ensamblado de la cuenta de integración.|
|/integrationAccounts/assemblies/listContentCallbackUrl/action|Obtiene la dirección URL de devolución de llamada para el contenido de ensamblado en la cuenta de integración.|
|/integrationAccounts/assemblies/read|Lee el ensamblado en la cuenta de integración.|
|/integrationAccounts/assemblies/write|Crea o actualiza el ensamblado en la cuenta de integración.|
|/integrationAccounts/batchConfigurations/delete|Elimina la configuración por lotes en la cuenta de integración.|
|/integrationAccounts/batchConfigurations/read|Lee la configuración por lotes en la cuenta de integración.|
|/integrationAccounts/batchConfigurations/write|Crea o actualiza la configuración por lotes en la cuenta de integración.|
|/integrationAccounts/certificates/delete|Elimina el certificado de la cuenta de integración.|
|/integrationAccounts/certificates/read|Lee el certificado en la cuenta de integración.|
|/integrationAccounts/certificates/write|Crea o actualiza el certificado en la cuenta de integración.|
|/integrationAccounts/delete|Elimina la cuenta de integración.|
|/integrationAccounts/listCallbackUrl/action|Obtiene la dirección URL de devolución de llamada para la cuenta de integración.|
|/integrationAccounts/listKeyVaultKeys/action|Obtiene las claves en el almacén de claves.|
|/integrationAccounts/logTrackingEvents/action|Registra los eventos de seguimiento en la cuenta de integración.|
|/integrationAccounts/maps/delete|Elimina la asignación de la cuenta de integración.|
|/integrationAccounts/maps/listContentCallbackUrl/action|Obtiene la dirección URL de devolución de llamada para el contenido de asignación en la cuenta de integración.|
|/integrationAccounts/maps/read|Lee la asignación en la cuenta de integración.|
|/integrationAccounts/maps/write|Crea o actualiza la asignación en la cuenta de integración.|
|/integrationAccounts/partners/delete|Elimina al asociado de la cuenta de integración.|
|/integrationAccounts/partners/listContentCallbackUrl/action|Obtiene la dirección URL de devolución de llamada para el contenido del asociado en la cuenta de integración.|
|/integrationAccounts/partners/read|Lee al asociado en la cuenta de integración.|
|/integrationAccounts/partners/write|Crea o actualiza al asociado en la cuenta de integración.|
|/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read|Lee las definiciones de registro de la cuenta de integración.|
|/integrationAccounts/read|Lee la cuenta de integración.|
|/integrationAccounts/regenerateAccessKey/action|Vuelve a generar los secretos de la clave de acceso.|
|/integrationAccounts/schemas/delete|Elimina el esquema de la cuenta de integración.|
|/integrationAccounts/schemas/listContentCallbackUrl/action|Obtiene la dirección URL de devolución de llamada para el contenido del esquema en la cuenta de integración.|
|/integrationAccounts/schemas/read|Lee el esquema en la cuenta de integración.|
|/integrationAccounts/schemas/write|Crea o actualiza el esquema en la cuenta de integración.|
|/integrationAccounts/sessions/delete|Elimina la sesión de la cuenta de integración.|
|/integrationAccounts/sessions/read|Lee la configuración por lotes en la cuenta de integración.|
|/integrationAccounts/sessions/write|Crea o actualiza la sesión en la cuenta de integración.|
|/integrationAccounts/write|Crea o actualiza la cuenta de integración.|
|/locations/workflows/validate/action|Valida el flujo de trabajo.|
|/operations/read|Obtiene la operación.|
|/register/action|Registra el proveedor de recursos de Microsoft.Logic de una suscripción determinada.|
|/workflows/accessKeys/delete|Elimina la clave de acceso.|
|/workflows/accessKeys/list/action|Enumera los secretos de la clave de acceso.|
|/workflows/accessKeys/read|Lee la clave de acceso.|
|/workflows/accessKeys/regenerate/action|Vuelve a generar los secretos de la clave de acceso.|
|/workflows/accessKeys/write|Crea o actualiza la clave de acceso.|
|/workflows/delete|Elimina el flujo de trabajo.|
|/workflows/disable/action|Deshabilita el flujo de trabajo.|
|/workflows/enable/action|Habilita el flujo de trabajo.|
|/workflows/listCallbackUrl/action|Obtiene la dirección URL de devolución de llamada del flujo de trabajo.|
|/workflows/listSwagger/action|Obtiene las definiciones de Swagger del flujo de trabajo.|
|/workflows/move/action|Mueve el flujo de trabajo desde su identificador de suscripción, grupo de recursos o nombre existentes a un identificador de suscripción, grupo de recursos o nombre diferentes.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/read|Lee la configuración de diagnóstico del flujo de trabajo.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del flujo de trabajo.|
|/workflows/providers/Microsoft.Insights/logDefinitions/read|Lee las definiciones de registro del flujo de trabajo.|
|/workflows/providers/Microsoft.Insights/metricDefinitions/read|Lee las definiciones métricas del flujo de trabajo.|
|/workflows/read|Lee el flujo de trabajo.|
|/workflows/regenerateAccessKey/action|Vuelve a generar los secretos de la clave de acceso.|
|/workflows/run/action|Inicia una ejecución del flujo de trabajo.|
|/workflows/runs/actions/listExpressionTraces/action|Obtiene los seguimientos de expresiones de la acción de ejecución del flujo de trabajo.|
|/workflows/runs/actions/read|Lee la acción de ejecución de un flujo de trabajo.|
|/workflows/runs/actions/repetitions/listExpressionTraces/action|Obtiene los seguimientos de expresiones de repetición de la acción de ejecución del flujo de trabajo.|
|/workflows/runs/actions/repetitions/read|Lee la repetición de la acción de ejecución del flujo de trabajo.|
|/workflows/runs/actions/scoperepetitions/read|Lee la repetición del ámbito de la acción de ejecución del flujo de trabajo.|
|/workflows/runs/cancel/action|Cancela la ejecución de un flujo de trabajo.|
|/workflows/runs/operations/read|Lee el estado de la operación de ejecución de un flujo de trabajo.|
|/workflows/runs/read|Lee la ejecución de flujo de trabajo.|
|/workflows/suspend/action|Suspende el flujo de trabajo.|
|/workflows/triggers/histories/read|Lee los historiales del desencadenador.|
|/workflows/triggers/histories/resubmit/action|Vuelve a enviar el desencadenador del flujo de trabajo.|
|/workflows/triggers/listCallbackUrl/action|Obtiene la dirección URL de devolución de llamada para el desencadenador.|
|/workflows/triggers/read|Lee el desencadenador.|
|/workflows/triggers/reset/action|Restablece el desencadenador.|
|/workflows/triggers/run/action|Ejecuta el desencadenador.|
|/workflows/triggers/setState/action|Establece el estado de los desencadenadores.|
|/workflows/validate/action|Valida el flujo de trabajo.|
|/workflows/versions/read|Lee la versión de flujo de trabajo.|
|/workflows/versions/triggers/listCallbackUrl/action|Obtiene la dirección URL de devolución de llamada para el desencadenador.|
|/workflows/write|Crea o actualiza el flujo de trabajo.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Operación | DESCRIPCIÓN |
|---|---|
|/commitmentPlans/commitmentAssociations/move/action|Mueve todas las asociaciones de planes de compromiso de Machine Learning|
|/commitmentPlans/commitmentAssociations/read|Lee todas las asociaciones de planes de compromiso de Machine Learning|
|/commitmentPlans/delete|Elimina todos los planes de compromiso de Machine Learning|
|/commitmentPlans/join/action|Combina todos los planes de compromiso de Machine Learning|
|/commitmentPlans/read|Lee todos los planes de compromiso de Machine Learning|
|/commitmentPlans/write|Crea o actualiza todos los planes de compromiso de Machine Learning|
|/locations/operationresults/read|Obtiene el resultado de una operación de Machine Learning.|
|/locations/operationsstatus/read|Obtiene el estado de una operación de Machine Learning en curso.|
|/operations/read|Obtiene operaciones de Machine Learning.|
|/register/action|Registra la suscripción del proveedor de recursos del servicio web Machine Learning y habilita la creación de servicios web.|
|/skus/read|Obtiene las SKU del plan de compromiso de Machine Learning.|
|/webServices/action|Crea propiedades del servicio web regional las regiones admitidas|
|/webServices/delete|Elimina todos los servicios web Machine Learning|
|/webServices/read|Lee todos los servicios web Machine Learning|
|/webServices/write|Crea o actualiza todos los servicios web Machine Learning|
|/Workspaces/delete|Elimina todas las áreas de trabajo de Machine Learning|
|/Workspaces/listworkspacekeys/action|Enumera las claves de un área de trabajo de Machine Learning|
|/Workspaces/read|Lee todas las áreas de trabajo de Machine Learning|
|/Workspaces/resyncstoragekeys/action|Vuelve a sincronizar las claves de la cuenta de almacenamiento configurada para un área de trabajo de Machine Learning|
|/Workspaces/write|Crea o actualiza todas las áreas de trabajo de Machine Learning|

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

| Operación | DESCRIPCIÓN |
|---|---|
|/operationalizationClusters/checkUpdate/action|Comprueba si existen actualizaciones para los servicios del sistema para el clúster de puesta en marcha.|
|/operationalizationClusters/delete|Elimina cualquier cuenta de hospedaje.|
|/operationalizationClusters/listKeys/action|Enumera las claves asociadas con el clúster de puesta en marcha.|
|/operationalizationClusters/read|Lee cualquier cuenta de hospedaje.|
|/operationalizationClusters/updateSystem/action|Actualiza los servicios del sistema en un clúster de puesta en marcha.|
|/operationalizationClusters/write|Crea o actualiza cualquier cuenta de hospedaje.|
|/register/action|Registra el id. de la suscripción del proveedor de recursos y habilita la creación de los recursos de cálculo de Machine Learning.|

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

| Operación | DESCRIPCIÓN |
|---|---|
|/accounts/delete|Elimina cualquier cuenta de hospedaje.|
|/accounts/read|Lee cualquier cuenta de hospedaje.|
|/accounts/write|Crea o actualiza cualquier cuenta de hospedaje.|
|/register/action|Registra el id. de la suscripción del proveedor de recursos y habilita la creación de una cuenta de hospedaje.|

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

| Operación | DESCRIPCIÓN |
|---|---|
|/userAssignedIdentities/assign/action|Acción de RBAC para asignar a un usuario existente una identidad asignada a un recurso.|
|/userAssignedIdentities/delete|Elimina la identidad asignada a un usuario existente.|
|/userAssignedIdentities/read|Obtiene la identidad asignada a un usuario existente.|
|/userAssignedIdentities/write|Crea una nueva identidad asignada a un usuario o actualiza las etiquetas asociadas a una identidad asignada a un usuario existente.|

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

| Operación | DESCRIPCIÓN |
|---|---|
|/labAccounts/CreateLab/action|Crea un laboratorio en una cuenta de laboratorio.|
|/labAccounts/delete|Elimina las cuentas de laboratorio.|
|/labAccounts/labs/delete|Elimina laboratorios.|
|/labAccounts/labs/environmentSettings/delete|Elimina la configuración del entorno.|
|/labAccounts/labs/environmentSettings/environments/delete|Elimina entornos.|
|/labAccounts/labs/environmentSettings/environments/read|Lee entornos.|
|/labAccounts/labs/environmentSettings/environments/write|Agrega o modifica entornos.|
|/labAccounts/labs/environmentSettings/read|Lee la configuración del entorno.|
|/labAccounts/labs/environmentSettings/write|Agrega o modifica la configuración del entorno.|
|/labAccounts/labs/labVms/delete|Elimina las máquinas virtuales de laboratorio.|
|/labAccounts/labs/labVms/read|Lee las máquinas virtuales.|
|/labAccounts/labs/labVms/write|Agrega o modifica las máquinas virtuales de laboratorio.|
|/labAccounts/labs/read|Lee laboratorios.|
|/labAccounts/labs/write|Agrega o modifica laboratorios.|
|/labAccounts/read|Lee las cuentas de laboratorio.|
|/labAccounts/write|Agrega o modifica las cuentas de laboratorio.|
|/locations/operations/read|Lee operaciones.|
|/register/action|Registra la suscripción|

## <a name="microsoftmanagement"></a>Microsoft.Management

| Operación | DESCRIPCIÓN |
|---|---|
|/checkNameAvailability/action|Comprueba si el nombre del grupo de administración especificado es válido y único.|
|/getEntities/action|Enumera todas las entidades (grupos de administración, suscripciones, etc.) del usuario autenticado.|
|/managementGroups/delete|Elimina un grupo de administración.|
|/managementGroups/read|Enumera los grupos de administración del usuario autenticado.|
|/managementGroups/subscriptions/delete|Anula la asociación de la suscripción con el grupo de administración.|
|/managementGroups/subscriptions/write|Asocia la suscripción existente con el grupo de administración.|
|/managementGroups/write|Crea o actualiza un grupo de administración.|

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

| Operación | DESCRIPCIÓN |
|---|---|
|/ClassicDevServices/delete|Realiza una operación DELETE en un recurso de servicio de desarrollo clásico.|
|/ClassicDevServices/listSecrets/action|Obtiene las claves de administración de un recurso de servicio de desarrollo clásico.|
|/ClassicDevServices/listSingleSignOnToken/action|Obtiene la dirección URL del inicio de sesión único de un servicio de desarrollo clásico.|
|/ClassicDevServices/read|Realiza una operación GET en un servicio de desarrollo clásico.|
|/ClassicDevServices/regenerateKey/action|Genera las claves de administración de un recurso de servicio de desarrollo clásico.|
|/Operations/read|Lee las operaciones de todos los tipos de recursos.|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Operación | DESCRIPCIÓN |
|---|---|
|/agreements/offers/plans/cancel/action|Cancela un acuerdo para un elemento determinado de Marketplace|
|/agreements/offers/plans/read|Devuelve un acuerdo para un elemento determinado de Marketplace|
|/agreements/offers/plans/sign/action|Firma un acuerdo para un elemento determinado de Marketplace|
|/agreements/read|Devuelve todos los contratos de una suscripción en concreto.|
|/offertypes/publishers/offers/plans/agreements/read|Obtiene un contrato para un elemento determinado de una máquina virtual de Marketplace.|
|/offertypes/publishers/offers/plans/agreements/write|Firma o cancela un contrato para un elemento determinado de una máquina virtual de Marketplace.|

## <a name="microsoftmedia"></a>Microsoft.Media

| Operación | DESCRIPCIÓN |
|---|---|
|/checknameavailability/action|Comprueba si hay disponible un nombre de cuenta de Media Services.|
|/mediaservices/delete|Elimina cualquier cuenta de Media Services.|
|/mediaservices/listKeys/action|Enumera las claves de ACS de la cuenta de Media Services.|
|/mediaservices/read|Lee cualquier cuenta de Media Services.|
|/mediaservices/regenerateKey/action|Regenera una clave de ACS de Media Services.|
|/mediaservices/syncStorageKeys/action|Sincroniza las claves de almacenamiento de una cuenta de Azure Storage adjunta.|
|/mediaservices/write|Crea o actualiza cualquier cuenta de Media Services.|
|/operations/read|Lee cualquier cuenta de Media Services.|
|/register/action|Lee la suscripción del proveedor de recursos de Media Services y habilita la creación de cuentas de Media Services.|

## <a name="microsoftmigrate"></a>Microsoft.Migrate

| Operación | DESCRIPCIÓN |
|---|---|
|/Operations/read|Lee las operaciones expuestas.|

## <a name="microsoftnetwork"></a>Microsoft.Network

| Operación | DESCRIPCIÓN |
|---|---|
|/applicationGatewayAvailableSslOptions/predefinedPolicies/read|Directiva SSL predefinida de Application Gateway.|
|/applicationGatewayAvailableSslOptions/read|Opciones SSL disponibles de Application Gateway.|
|/applicationGatewayAvailableWafRuleSets/read|Obtiene los conjuntos de reglas WAF disponibles en Application Gateway|
|/applicationGateways/backendAddressPools/join/action|Se une a un grupo de direcciones de back-end de Application Gateway|
|/applicationGateways/backendhealth/action|Obtiene el estado de back-end de una instancia de Application Gateway|
|/applicationGateways/delete|Elimina una instancia de Application Gateway|
|/applicationGateways/effectiveNetworkSecurityGroups/action|Obtiene la tabla de rutas configuradas en Application Gateway.|
|/applicationGateways/effectiveRouteTable/action|Obtiene la tabla de rutas configuradas en Application Gateway.|
|/applicationGateways/providers/Microsoft.Insights/logDefinitions/read|Obtiene los eventos de puerta de Application Gateway.|
|/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de Application Gateway.|
|/applicationGateways/read|Obtiene una instancia de Application Gateway|
|/applicationGateways/setSecurityCenterConfiguration/action|Establece la configuración del centro de seguridad de Application Gateway.|
|/applicationGateways/start/action|Inicia una instancia de Application Gateway|
|/applicationGateways/stop/action|Detiene una instancia de Application Gateway|
|/applicationGateways/write|Crea una instancia de Application Gateway o actualiza una que ya existe|
|/applicationSecurityGroups/delete|Elimina un grupo de seguridad de aplicaciones.|
|/applicationSecurityGroups/joinIpConfiguration/action|Combina la configuración IP a los grupos de seguridad de aplicaciones.|
|/applicationSecurityGroups/joinNetworkSecurityRule/action|Combina una regla de seguridad a los grupos de seguridad de aplicaciones.|
|/applicationSecurityGroups/read|Obtiene el id. de un grupo de seguridad de aplicaciones.|
|/applicationSecurityGroups/write|Crea un grupo de seguridad de aplicaciones o actualiza un grupo de seguridad de aplicaciones ya existente.|
|/bgpServiceCommunities/read|Obtiene las comunidades de servicio de BGP|
|/checkTrafficManagerNameAvailability/action|Comprueba la disponibilidad de un nombre DNS relativo de Traffic Manager.|
|/connections/delete|Elimina VirtualNetworkGatewayConnection|
|/connections/read|Obtiene VirtualNetworkGatewayConnection|
|/connections/sharedkey/action|Obtiene el valor de SharedKey de VirtualNetworkGatewayConnection.|
|/connections/sharedKey/read|Obtiene el valor de SharedKey de VirtualNetworkGatewayConnection|
|/connections/sharedKey/write|Crea o actualiza una SharedKey de VirtualNetworkGatewayConnection existente|
|/connections/vpndeviceconfigurationscript/read|Obtiene la configuración del dispositivo VPN de VirtualNetworkGatewayConnection.|
|/connections/write|Crea o actualiza una VirtualNetworkGatewayConnection existente|
|/ddosProtectionPlans/ddosProtectionPlanProxies/delete|Elimina un proxy del plan DDoS Protection.|
|/ddosProtectionPlans/ddosProtectionPlanProxies/read|Obtiene una definición del proxy del plan DDoS Protection.|
|/ddosProtectionPlans/ddosProtectionPlanProxies/write|Crea un proxy del plan DDoS Protection o actualiza un proxy del plan DDoS Protection ya existente.|
|/ddosProtectionPlans/delete|Elimina un plan DDoS Protection.|
|/ddosProtectionPlans/join/action|Combina un plan DDoS Protection.|
|/ddosProtectionPlans/read|Obtiene un plan DDoS Protection.|
|/ddosProtectionPlans/write|Crea un plan DDoS Protection o actualiza un plan DDoS Protection. |
|/dnsoperationresults/read|Obtiene los resultados de una operación DNS|
|/dnsoperationstatuses/read|Obtiene el estado de una operación DNS |
|/dnszones/A/delete|Quita el conjunto de registros de un determinado nombre y del tipo "A" de una zona DNS.|
|/dnszones/A/read|Obtiene el conjunto de registros del tipo "A", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag.|
|/dnszones/A/write|Crea o actualiza un conjunto de registros del tipo "A" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros.|
|/dnszones/AAAA/delete|Quita el conjunto de registros de un determinado nombre y del tipo "AAAA" de una zona DNS.|
|/dnszones/AAAA/read|Obtiene el conjunto de registros del tipo "AAAA", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag.|
|/dnszones/AAAA/write|Crea o actualiza un conjunto de registros del tipo "AAAA" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros.|
|/dnszones/all/read|Obtiene los conjuntos de registros de DNS entre los tipos|
|/dnszones/CAA/delete|Quita el conjunto de registros de un determinado nombre y del tipo "CAA" de una zona DNS.|
|/dnszones/CAA/read|Obtiene el conjunto de registros del tipo "CAA", en formato JSON. El conjunto de registros contiene el TTL, las etiquetas y ETag.|
|/dnszones/CAA/write|Crea o actualiza un conjunto de registros del tipo "CAA" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros.|
|/dnszones/CNAME/delete|Quita el conjunto de registros de un determinado nombre y del tipo "CNAME" de una zona DNS.|
|/dnszones/CNAME/read|Obtiene el conjunto de registros del tipo "CNAME", en formato JSON. El conjunto de registros contiene el TTL, las etiquetas y ETag.|
|/dnszones/CNAME/write|Crea o actualiza un conjunto de registros del tipo "CNAME" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros.|
|/dnszones/delete|Elimina la zona DNS, en formato JSON. Entre las propiedades de la zona se incluyen tags, etag, numberOfRecordSets y maxNumberOfRecordSets.|
|/dnszones/MX/delete|Quita el conjunto de registros de un determinado nombre y del tipo "MX" de una zona DNS.|
|/dnszones/MX/read|Obtiene el conjunto de registros del tipo "MX", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag.|
|/dnszones/MX/write|Crea o actualiza un conjunto de registros del tipo "MX" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros.|
|/dnszones/NS/delete|Elimina el conjunto de registros de DNS del tipo NS|
|/dnszones/NS/read|Obtiene el conjunto de registros de DNS del tipo NS|
|/dnszones/NS/write|Crea o actualiza el conjunto de registros de DNS del tipo NS|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico de la zona DNS.|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de la zona DNS.|
|/dnszones/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las definiciones de métrica de la zona DNS.|
|/dnszones/PTR/delete|Quita el conjunto de registros de un determinado nombre y del tipo "PTR" de una zona DNS.|
|/dnszones/PTR/read|Obtiene el conjunto de registros del tipo "PTR", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag.|
|/dnszones/PTR/write|Crea o actualiza un conjunto de registros del tipo "PTR" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros.|
|/dnszones/read|Obtiene la zona DNS, en formato JSON. Entre las propiedades de la zona se incluyen tags, etag, numberOfRecordSets y maxNumberOfRecordSets. Tenga en cuenta que este comando no recupera los conjuntos de registros dentro de la zona.|
|/dnszones/recordsets/read|Obtiene los conjuntos de registros de DNS entre los tipos|
|/dnszones/SOA/read|Obtiene el conjunto de registros de DNS del tipo SOA|
|/dnszones/SOA/write|Crea o actualiza el conjunto de registros de DNS del tipo SOA|
|/dnszones/SRV/delete|Quita el conjunto de registros de un determinado nombre y del tipo "SRV" de una zona DNS.|
|/dnszones/SRV/read|Obtiene el conjunto de registros del tipo "SRV", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag.|
|/dnszones/SRV/write|Crea o actualiza el conjunto de registros del tipo SRV|
|/dnszones/TXT/delete|Quita el conjunto de registros de un determinado nombre y del tipo "TXT" de una zona DNS.|
|/dnszones/TXT/read|Obtiene el conjunto de registros del tipo "TXT", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag.|
|/dnszones/TXT/write|Crea o actualiza un conjunto de registros del tipo "TXT" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros.|
|/dnszones/write|Crea o actualiza una zona DNS en un grupo de recursos.  Se utiliza para actualizar las etiquetas de un recurso de zona DNS. Tenga en cuenta que este comando no se puede usar para crear o actualizar conjuntos de registros dentro de la zona.|
|/expressRouteCircuits/authorizations/delete|Elimina una autorización de ExpressRouteCircuit|
|/expressRouteCircuits/authorizations/read|Obtiene una autorización de ExpressRouteCircuit|
|/expressRouteCircuits/authorizations/write|Crea o actualiza una autorización de ExpressRouteCircuit existente|
|/expressRouteCircuits/delete|Elimina un ExpressRouteCircuit|
|/expressRouteCircuits/peerings/arpTables/action|Obtiene un ArpTable de emparejamiento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/connections/delete|Elimina una conexión de ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/connections/read|Obtiene una conexión de ExpressRouteCircuit.|
|/expressRouteCircuits/peerings/connections/write|Crea o actualiza un recurso de conexión de ExpressRouteCircuit existente.|
|/expressRouteCircuits/peerings/delete|Elimina un emparejamiento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/read|Obtiene un emparejamiento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/routeTables/action|Obtiene un RouteTable de emparejamiento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/routeTablesSummary/action|Obtiene un resumen de RouteTable de emparejamiento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/stats/read|Obtiene estadísticas de emparejamiento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/write|Crea o actualiza un emparejamiento de ExpressRouteCircuit existente|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico de los circuitos de ExpressRoute.|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de los circuitos de ExpressRoute.|
|/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read|Obtiene los eventos de los circuitos ExpressRoute.|
|/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las definiciones de métrica de los circuitos ExpressRoute.|
|/expressRouteCircuits/read|Obtiene un ExpressRouteCircuit|
|/expressRouteCircuits/stats/read|Obtiene un estado de ExpressRouteCircuit|
|/expressRouteCircuits/write|Crea o actualiza un ExpressRouteCircuit existente|
|/expressRouteCrossConnections/delete|Elimina la conexión cruzada de Express Route.|
|/expressRouteCrossConnections/join/action|Combina una conexión cruzada de Express Route.|
|/expressRouteCrossConnections/peerings/arpTables/action|Obtiene una la tabla ARP de la conexión cruzada de Express Route.|
|/expressRouteCrossConnections/peerings/delete|Elimina el emparejamiento de la conexión cruzada de Express Route.|
|/expressRouteCrossConnections/peerings/read|Obtiene un emparejamiento de la conexión cruzada de Express Route.|
|/expressRouteCrossConnections/peerings/routeTables/action|Obtiene la tabla de rutas del emparejamiento de la conexión cruzada de Express Route.|
|/expressRouteCrossConnections/peerings/routeTableSummary/action|Obtiene el resumen de la tabla de ruta del emparejamiento de la conexión cruzada de Express Route.|
|/expressRouteCrossConnections/peerings/stats/read|Obtiene las estadísticas del emparejamiento de la conexión cruzada de Express Route.|
|/expressRouteCrossConnections/peerings/write|Crea un emparejamiento de la conexión cruzada de Express Route o actualiza un emparejamiento de la conexión cruzada de Express Route ya existente.|
|/expressRouteCrossConnections/read|Obtiene la conexión cruzada de Express Route.|
|/expressRouteCrossConnections/write|Crea o actualiza la conexión cruzada de Express Route.|
|/expressRouteServiceProviders/read|Obtiene los proveedores de servicios de ExpressRoute|
|/loadBalancers/backendAddressPools/join/action|Se une a un grupo de direcciones de back-end del equilibrador de carga|
|/loadBalancers/backendAddressPools/read|Obtiene una definición de grupo de direcciones de back-end del equilibrador de carga|
|/loadBalancers/delete|Elimina un equilibrador de carga|
|/loadBalancers/frontendIPConfigurations/read|Obtiene una definición de configuración de dirección IP de front-end del equilibrador de carga|
|/loadBalancers/inboundNatPools/join/action|Se une a conjuntos NAT de entrada del equilibrador de carga|
|/loadBalancers/inboundNatPools/read|Obtiene una definición de conjuntos NAT de entrada del equilibrador de carga|
|/loadBalancers/inboundNatRules/delete|Elimina una regla NAT de entrada del equilibrador de carga|
|/loadBalancers/inboundNatRules/join/action|Se une a una regla NAT de entrada del equilibrador de carga|
|/loadBalancers/inboundNatRules/read|Obtiene una definición de reglas NAT de entrada del equilibrador de carga|
|/loadBalancers/inboundNatRules/write|Crea una regla NAT de entrada del equilibrador de carga o actualiza una que ya existe|
|/loadBalancers/loadBalancingRules/read|Obtiene una definición de regla de equilibrado de carga del equilibrador de carga|
|/loadBalancers/networkInterfaces/read|Obtiene referencias a todas las interfaces de red en un equilibrador de carga|
|/loadBalancers/outboundNatRules/read|Obtiene una definición de reglas NAT de salida del equilibrador de carga|
|/loadBalancers/probes/join/action|Permite usar sondeos de un equilibrador de carga. Por ejemplo, con este permiso, la propiedad healthProbe del conjunto de escalado de una máquina virtual puede hacer referencia a la prueba.|
|/loadBalancers/probes/read|Obtiene un sondeo del equilibrador de carga|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico de Load Balancer.|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de Load Balancer.|
|/loadBalancers/providers/Microsoft.Insights/logDefinitions/read|Obtiene los eventos de Load Balancer.|
|/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de Load Balancer.|
|/loadBalancers/read|Obtiene una definición del equilibrador de carga|
|/loadBalancers/virtualMachines/read|Obtiene referencias a todas las máquinas virtuales de un equilibrador de carga|
|/loadBalancers/write|Crea un equilibrador de carga o actualiza uno que ya existe|
|/localnetworkgateways/delete|Elimina LocalNetworkGateway|
|/localnetworkgateways/read|Obtiene LocalNetworkGateway|
|/localnetworkgateways/write|Crea o actualiza una LocalNetworkGateway existente|
|/locations/checkDnsNameAvailability/read|Comprueba si la etiqueta DNS está disponible en la ubicación especificada|
|/locations/operationResults/read|Obtiene el resultado de una operación POST o DELETE asincrónica|
|/locations/operations/read|Obtiene el recurso de operaciones que representa el estado de una operación asincrónica|
|/locations/usages/read|Obtiene las métricas de uso de los recursos|
|/locations/virtualNetworkAvailableEndpointServices/read|Obtiene una lista de los servicios de punto de conexión disponibles de Virtual Network.|
|/networkInterfaces/delete|Elimina una interfaz de red|
|/networkInterfaces/diagnosticIdentity/read|Obtiene la identidad de diagnóstico del recurso.|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|Obtiene los grupos de seguridad de red configurados en la interfaz de red de la máquina virtual|
|/networkInterfaces/effectiveRouteTable/action|Obtiene la tabla de rutas configuradas en la interfaz de red de la máquina virtual|
|/networkInterfaces/ipconfigurations/read|Obtiene una definición de configuración de dirección IP de la interfaz de red. |
|/networkInterfaces/join/action|Une una máquina virtual a una interfaz de red|
|/networkInterfaces/loadBalancers/read|Obtiene todos los equilibradores de carga de los que forma parte la interfaz de red|
|/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de la interfaz de red.|
|/networkInterfaces/read|Obtiene una definición de interfaz de red. |
|/networkInterfaces/write|Crea una interfaz de red o actualiza una interfaz de red existente. |
|/networkSecurityGroups/defaultSecurityRules/read|Obtiene una definición de regla de seguridad predeterminada|
|/networkSecurityGroups/delete|Elimina un grupo de seguridad de red|
|/networkSecurityGroups/join/action|Se une a un grupo de seguridad de red|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico de los grupos de seguridad de red.|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de los grupos de seguridad de red; esta operación se complementa con el proveedor de recursos de Insights.|
|/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read|Obtiene los eventos del grupo de seguridad de red.|
|/networkSecurityGroups/read|Obtiene una definición de grupo de seguridad de red|
|/networkSecurityGroups/securityRules/delete|Elimina una regla de seguridad|
|/networkSecurityGroups/securityRules/read|Obtiene una definición de regla de seguridad|
|/networkSecurityGroups/securityRules/write|Crea una regla de seguridad o actualiza una que ya existe|
|/networkSecurityGroups/write|Crea un grupo de seguridad de red o actualiza uno que ya existe|
|/networkWatchers/availableProvidersList/action|Devuelve todos los proveedores de servicios de Internet disponibles para una región de Azure específica.|
|/networkWatchers/azureReachabilityReport/action|Devuelve la puntuación de la latencia relativa de los proveedores de servicios de Internet desde una ubicación específica a las regiones de Azure.|
|/networkWatchers/configureFlowLog/action|Configura el registro de flujos para un recurso de destino.|
|/networkWatchers/connectionMonitors/delete|Elimina un monitor de conexión.|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/read|Obtiene la configuración de diagnóstico del monitor de conexión.|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del monitor de conexión.|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ metricDefinitions/read|Obtiene las métricas disponibles del monitor de conexión.|
|/networkWatchers/connectionMonitors/query/action|Consulta la conectividad de supervisión entre los puntos de conexión especificados.|
|/networkWatchers/connectionMonitors/read|Obtiene los detalles del monitor de conexión.|
|/networkWatchers/connectionMonitors/start/action|Comienza la conectividad de supervisión entre los puntos de conexión especificados.|
|/networkWatchers/connectionMonitors/stop/action|Detiene o pausa la conectividad de supervisión entre los puntos de conexión especificados.|
|/networkWatchers/connectionMonitors/write|Crea un monitor de conexión.|
|/networkWatchers/connectivityCheck/action|Comprueba si se puede establecer una conexión TCP directa desde una máquina virtual a un punto de conexión determinado incluyendo otra máquina virtual o un servidor remoto arbitrario.|
|/networkWatchers/delete|Elimina una instancia de Network Watcher|
|/networkWatchers/ipFlowVerify/action|Devuelve un mensaje indicando si se permite o deniega el paquete en o desde un destino determinado.|
|/networkWatchers/lenses/delete|Elimina una lente.|
|/networkWatchers/lenses/query/action|Consulta el tráfico de red de supervisión en un punto de conexión especificado.|
|/networkWatchers/lenses/read|Obtiene los detalles de la lente.|
|/networkWatchers/lenses/start/action|Inicia el tráfico de red de supervisión en un punto de conexión especificado.|
|/networkWatchers/lenses/stop/action|Detiene o pausa el tráfico de red de supervisión en un punto de conexión especificado.|
|/networkWatchers/lenses/write|Crea una lente.|
|/networkWatchers/nextHop/action|Para un destino y una dirección IP de destino especificados, devuelve el tipo y la dirección IP del próximo salto.|
|/networkWatchers/packetCaptures/delete|Elimina una captura de paquetes|
|/networkWatchers/packetCaptures/queryStatus/action|Obtiene información acerca de las propiedades y el estado de un recurso de captura de paquetes.|
|/networkWatchers/packetCaptures/read|Obtiene la definición de captura de paquetes|
|/networkWatchers/packetCaptures/stop/action|Detiene la sesión de captura de paquetes en ejecución.|
|/networkWatchers/packetCaptures/write|Crea una captura de paquetes|
|/networkWatchers/queryFlowLogStatus/action|Obtiene el estado del flujo de registros de un recurso.|
|/networkWatchers/queryTroubleshootResult/action|Obtiene el resultado de la operación de solución de problemas ejecutada anteriormente o de la que está actualmente en ejecución.|
|/networkWatchers/read|Obtiene la definición de Network Watcher|
|/networkWatchers/securityGroupView/action|Visualiza las reglas de grupos de seguridad de red configuradas y eficaces aplicadas a una máquina virtual.|
|/networkWatchers/topology/action|Obtiene una vista de nivel de red de los recursos y sus relaciones en un grupo de recursos.|
|/networkWatchers/troubleshoot/action|Inicia la solución de problemas en un recurso de redes de Azure.|
|/networkWatchers/write|Crea una instancia de Network Watcher o actualiza una que ya existe|
|/operations/read|Obtiene las operaciones disponibles|
|/publicIPAddresses/delete|Elimina una dirección IP pública.|
|/publicIPAddresses/join/action|Se une a una dirección IP pública|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico de la dirección IP pública.|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de la dirección IP pública.|
|/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read|Obtiene las definiciones de registro de la dirección IP pública.|
|/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las definiciones de las métricas de la dirección IP pública.|
|/publicIPAddresses/read|Obtiene una definición de la dirección ip pública.|
|/publicIPAddresses/write|Crea una dirección Ip pública o actualiza una que ya existe. |
|/register/action|Registra la suscripción|
|/routeFilters/delete|Elimina una definición de filtro de rutas|
|/routeFilters/join/action|Se une a un filtro de ruta|
|/routeFilters/read|Obtiene una definición de filtro de rutas|
|/routeFilters/routeFilterRules/delete|Elimina una definición de regla de filtro de rutas|
|/routeFilters/routeFilterRules/read|Obtiene una definición de regla de filtro de rutas|
|/routeFilters/routeFilterRules/write|Crea una regla de filtro de rutas o actualiza una que ya existe|
|/routeFilters/write|Crea un filtro de rutas o actualiza uno que ya existe|
|/routeTables/delete|Elimina una definición de tabla de rutas|
|/routeTables/join/action|Se une a una tabla de rutas|
|/routeTables/read|Obtiene una definición de tabla de rutas|
|/routeTables/routes/delete|Elimina una definición de rutas|
|/routeTables/routes/read|Obtiene una definición de rutas|
|/routeTables/routes/write|Crea una ruta o actualiza una que ya existe|
|/routeTables/write|Crea una tabla de rutas o actualiza una que ya existe|
|/securegateways/applicationRuleCollections/delete|Elimina una colección de reglas de la aplicación de una puerta de enlace segura.|
|/securegateways/applicationRuleCollections/read|Recupera una colección de reglas de la aplicación de una puerta de enlace segura.|
|/securegateways/applicationRuleCollections/write|Crea o actualiza una colección de reglas de la aplicación de una puerta de enlace segura.|
|/securegateways/delete|Elimina la puerta de enlace segura.|
|/securegateways/networkRuleCollections/delete|Elimina una colección de reglas de red de una puerta de enlace segura.|
|/securegateways/networkRuleCollections/read|Recupera una colección de reglas de red de una puerta de enlace segura determinada.|
|/securegateways/networkRuleCollections/write|Crea o actualiza una colección de reglas de red de una puerta de enlace segura.|
|/securegateways/read|Obtiene la puerta de enlace segura.|
|/securegateways/write|Crea o actualiza una puerta de enlace segura.|
|/serviceEndpointPolicies/delete|Elimina una directiva de un punto de conexión de servicio.|
|/serviceEndpointPolicies/join/action|Combina una directiva de un punto de conexión de servicio.|
|/serviceEndpointPolicies/joinSubnet/action|Combina una subred con las directivas de punto de conexión de servicio.|
|/serviceEndpointPolicies/read|Obtiene una descripción de la directiva de un punto de conexión de servicio.|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete|Elimina una definición de la directiva de un punto de conexión de servicio.|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read|Obtiene el descifrado de una definición de la directiva de un punto de conexión de servicio.|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write|Crea una definición de la directiva de un punto de conexión de servicio o actualiza una definición de la directiva de un punto de conexión de servicio existente.|
|/serviceEndpointPolicies/write|Crea una directiva de un punto de conexión de servicio o actualiza una directiva de un punto de conexión de servicio existente.|
|/trafficManagerGeographicHierarchies/read|Obtiene la jerarquía geográfica de Traffic Manager que contiene las regiones que se pueden usar con el método de enrutamiento del tráfico geográfico|
|/trafficManagerProfiles/azureEndpoints/delete|Elimina un punto de conexión de Azure de un perfil de Traffic Manager existente. Traffic Manager dejará de enrutar el tráfico al punto de conexión de Azure eliminado.|
|/trafficManagerProfiles/azureEndpoints/read|Obtiene un punto de conexión de Azure que pertenece a un perfil de Traffic Manager, incluidas todas las propiedades de ese punto de conexión de Azure.|
|/trafficManagerProfiles/azureEndpoints/write|Agrega un nuevo punto de conexión de Azure en un perfil de Traffic Manager ya existente o actualiza las propiedades de un punto de conexión de Azure ya existente en dicho perfil de Traffic Manager.|
|/trafficManagerProfiles/delete|Elimina el perfil de Traffic Manager. Se perderán todos los valores asociados con el perfil de Traffic Manager y el perfil ya no podrá usarse para enrutar el tráfico.|
|/trafficManagerProfiles/externalEndpoints/delete|Elimina un punto de conexión externo de un perfil de Traffic Manager existente. Traffic Manager dejará de enrutar el tráfico al punto de conexión externo eliminado.|
|/trafficManagerProfiles/externalEndpoints/read|Obtiene un punto de conexión externo que pertenece a un perfil de Traffic Manager, incluidas todas las propiedades de ese punto de conexión externo.|
|/trafficManagerProfiles/externalEndpoints/write|Agrega un nuevo punto de conexión externo en un perfil de Traffic Manager ya existente o actualiza las propiedades de un punto de conexión externo ya existente en dicho perfil de Traffic Manager.|
|/trafficManagerProfiles/heatMaps/read|Obtiene el mapa térmico de Traffic Manager para el perfil de Traffic Manager determinado que contiene los datos de latencia y los recuentos de consultas según la ubicación y la IP de origen.|
|/trafficManagerProfiles/nestedEndpoints/delete|Elimina un punto de conexión anidado de un perfil de Traffic Manager existente. Traffic Manager dejará de enrutar el tráfico al punto de conexión anidado eliminado.|
|/trafficManagerProfiles/nestedEndpoints/read|Obtiene un punto de conexión anidado que pertenece a un perfil de Traffic Manager, incluidas todas las propiedades de ese punto de conexión anidado.|
|/trafficManagerProfiles/nestedEndpoints/write|Agrega un nuevo punto de conexión anidado en un perfil de Traffic Manager ya existente o actualiza las propiedades de un punto de conexión anidado ya existente en dicho perfil de Traffic Manager.|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico de Traffic Manager.|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de Traffic Manager; esta operación se complementa con el proveedor de recursos de Insights.|
|/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read|Obtiene los eventos de Traffic Manager.|
|/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de Traffic Manager.|
|/trafficManagerProfiles/read|Obtiene la configuración del perfil de Traffic Manager. Esto incluye la configuración de DNS, la de enrutamiento de tráfico, la de supervisión de puntos de conexión y la lista de puntos de conexión que este perfil de Traffic Manager enruta.|
|/trafficManagerProfiles/write|Crea un perfil de Traffic Manager, o modifica la configuración de un perfil de Traffic Manager que ya existe. Esto incluye la habilitación o deshabilitación de un perfil y la modificación de la configuración de DNS, la de enrutamiento de tráfico o la de supervisión de puntos de conexión. Los puntos de conexión que enruta el perfil de Traffic Manager se pueden agregar, quitar, habilitar o deshabilitar.|
|/trafficManagerUserMetricsKeys/delete|Elimina la clave de nivel de suscripción que se usa para recopilar métricas del usuario en tiempo real.|
|/trafficManagerUserMetricsKeys/read|Obtiene la clave de nivel de suscripción que se usa para recopilar métricas del usuario en tiempo real.|
|/trafficManagerUserMetricsKeys/write|Crea una clave de nivel de suscripción que se usará para recopilar métricas del usuario en tiempo real.|
|/unregister/action|Anula el registro de la suscripción|
|/virtualHubs/delete|Elimina un concentrador virtual.|
|/virtualHubs/hubVirtualNetworkConnections/delete|Elimina un elemento HubVirtualNetworkConnection.|
|/virtualHubs/hubVirtualNetworkConnections/read|Obtiene un elemento HubVirtualNetworkConnection.|
|/virtualHubs/hubVirtualNetworkConnections/write|Crea o actualiza un elemento HubVirtualNetworkConnection.|
|/virtualHubs/read|Obtiene un concentrador virtual.|
|/virtualHubs/write|Crea o actualiza un concentrador virtual.|
|/virtualnetworkgateways/connections/read|Obtiene un elemento VirtualNetworkGatewayConnection.|
|/virtualNetworkGateways/delete|Elimina un elemento virtualNetworkGateway.|
|/virtualnetworkgateways/generatevpnclientpackage/action|Genera un paquete VpnClient para virtualNetworkGateway.|
|/virtualnetworkgateways/generatevpnprofile/action|Genera un paquete VpnProfile para VirtualNetworkGateway.|
|/virtualnetworkgateways/getadvertisedroutes/action|Obtiene las rutas que anunció virtualNetworkGateway.|
|/virtualnetworkgateways/getbgppeerstatus/action|Obtiene el estado del mismo nivel BGP de virtualNetworkGateway.|
|/virtualnetworkgateways/getlearnedroutes/action|Obtiene las rutas aprendidas de virtualNetworkGateway.|
|/virtualnetworkgateways/getvpnclientipsecparameters/action|Obtiene los parámetros Ipsec de Vpnclient del cliente P2S de VirtualNetworkGateway.|
|/virtualnetworkgateways/getvpnprofilepackageurl/action|Obtiene la dirección URL de un paquete de perfil de cliente de VPN generado previamente.|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico de la puerta de enlace de red virtual.|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de la puerta de enlace de red virtual; esta operación se complementa con el proveedor de recursos de Insights.|
|/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read|Obtiene los eventos de la puerta de enlace de red virtual.|
|/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de la puerta de enlace de red virtual.|
|/virtualNetworkGateways/read|Obtiene un elemento virtualNetworkGateway.|
|/virtualnetworkgateways/reset/action|Restablece un elemento virtualNetworkGateway.|
|/virtualnetworkgateways/setvpnclientipsecparameters/action|Establece los parámetros Ipsec de Vpnclient del cliente P2S de VirtualNetworkGateway.|
|/virtualnetworkgateways/supportedvpndevices/action|Enumera los dispositivos VPN admitidos.|
|/virtualNetworkGateways/write|Crea o actualiza un elemento VirtualNetworkGateway.|
|/virtualNetworks/checkIpAddressAvailability/read|Comprueba si la dirección IP está disponible en la red virtual especificada|
|/virtualNetworks/customViews/get/action|Obtiene el contenido de una vista personalizada de Virtual Network.|
|/virtualNetworks/customViews/read|Obtiene la definición de una vista personalizada de Virtual Network.|
|/virtualNetworks/delete|Elimina una red virtual|
|/virtualNetworks/peer/action|Empareja una red virtual con otra red virtual|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico de Virtual Network.|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de Virtual Network.|
|/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read|Obtiene las definiciones de registro de Virtual Network.|
|/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las definiciones de métricas de Virtual Network.|
|/virtualNetworks/read|Obtiene la definición de red virtual|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete|Elimina un proxy de emparejamiento de redes virtuales.|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/read|Obtiene una definición de un proxy emparejamiento de redes virtuales.|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/write|Crea un proxy de emparejamiento de redes virtuales o actualiza uno que ya existe.|
|/virtualNetworks/subnets/delete|Elimina una subred de red virtual|
|/virtualNetworks/subnets/join/action|Se une a una red virtual|
|/virtualNetworks/subnets/joinViaServiceEndpoint/action|Combina un recurso como una cuenta de almacenamiento o una instancia de SQL Database a una subred.|
|/virtualNetworks/subnets/read|Obtiene una definición de subred de red virtual|
|/virtualNetworks/subnets/resourceNavigationLinks/delete|Elimina un vínculo de navegación de recursos.|
|/virtualNetworks/subnets/resourceNavigationLinks/read|Obtiene la definición del vínculo de navegación de recursos.|
|/virtualNetworks/subnets/resourceNavigationLinks/write|Crea un vínculo de navegación de recursos o actualiza uno ya existente.|
|/virtualNetworks/subnets/virtualMachines/read|Obtiene referencias a todas las máquinas virtuales de una subred de red virtual|
|/virtualNetworks/subnets/write|Crea una subred de red virtual o actualiza una que ya existe|
|/virtualNetworks/taggedTrafficConsumers/delete|Elimina un consumidor de tráfico etiquetado.|
|/virtualNetworks/taggedTrafficConsumers/read|Obtiene la definición del consumidor de tráfico etiquetado.|
|/virtualNetworks/taggedTrafficConsumers/validate/action|Valida un consumidor de tráfico etiquetado.|
|/virtualNetworks/taggedTrafficConsumers/write|Crea un consumidor de tráfico etiquetado o actualiza uno ya existente.|
|/virtualNetworks/usages/read|Obtiene los usos de IP para cada subred de la red virtual.|
|/virtualNetworks/virtualMachines/read|Obtiene referencias a todas las máquinas virtuales de una red virtual|
|/virtualNetworks/virtualNetworkPeerings/delete|Elimina un emparejamiento de redes virtuales|
|/virtualNetworks/virtualNetworkPeerings/read|Obtiene una definición de emparejamiento de redes virtuales|
|/virtualNetworks/virtualNetworkPeerings/write|Crea un emparejamiento de redes virtuales o actualiza uno que ya existe|
|/virtualNetworks/write|Crea una red virtual o actualiza una que ya existe|
|/virtualNetworkTaps/delete|Elimina Virtual Network Tap.|
|/virtualNetworkTaps/join/action|Combina Virtual Network Tap.|
|/virtualNetworkTaps/read|Obtiene Virtual Network Tap.|
|/virtualNetworkTaps/write|Crea o actualiza Virtual Network Tap.|
|/virtualwans/delete|Elimina una red WAN virtual.|
|/virtualwans/read|Obtiene una red WAN virtual.|
|/virtualWans/virtualHubProxies/delete|Elimina un proxy de un concentrador virtual.|
|/virtualWans/virtualHubProxies/read|Obtiene la definición de un proxy de concentrador virtual.|
|/virtualWans/virtualHubProxies/write|Crea a un proxy de concentrador virtual o actualiza uno ya existente,|
|/virtualwans/virtualHubs/read|Obtiene todos los concentradores virtuales que están asociados a una red WAN virtual.|
|/virtualwans/vpnconfiguration/read|Obtiene una configuración de VPN.|
|/virtualWans/vpnSiteProxies/delete|Elimina el proxy de un sitio VPN.|
|/virtualWans/vpnSiteProxies/read|Obtiene la definición del proxy de un sitio VPN.|
|/virtualWans/vpnSiteProxies/write|Crea el proxy de un sitio VPN o actualiza uno ya existente.|
|/virtualwans/vpnSites/read|Obtiene todos los sitios VPN que están asociados a una red WAN virtual.|
|/virtualwans/write|Crea o actualiza una red WAN virtual.|
|/vpnGateways/read|Obtiene un elemento VpnGateway.|
|/vpnGateways/vpnConnections/read|Obtiene un elemento VpnConnection.|
|/vpnGateways/vpnConnections/write|Establece un elemento VpnConnection.|
|/vpnGateways/write|Establece un elemento VpnGateway.|
|/vpnsites/delete|Elimina el recurso de un sitio VPN.|
|/vpnsites/read|Obtiene el recurso de un sitio VPN.|
|/vpnsites/write|Crea o actualiza el recurso de un sitio VPN.|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Operación | DESCRIPCIÓN |
|---|---|
|/CheckNamespaceAvailability/action|Comprueba si un nombre de recurso determinado de espacio de nombres está disponible en el servicio NotificationHub.|
|/Namespaces/authorizationRules/action|Obtiene la lista de descripciones de reglas de autorización de espacios de nombres.|
|/Namespaces/authorizationRules/delete|Elimina la regla de autorización del espacio de nombres. No se puede eliminar la regla predeterminada de autorización del espacio de nombres. |
|/Namespaces/authorizationRules/listkeys/action|Obtiene la cadena de conexión al espacio de nombres|
|/Namespaces/authorizationRules/read|Obtiene la lista de descripciones de reglas de autorización de espacios de nombres.|
|/Namespaces/authorizationRules/regenerateKeys/action|Namespace Authorization Rule Regenerate Primary/SecondaryKey especifica la clave que se debe regenerar|
|/Namespaces/authorizationRules/write|Crea reglas de autorización en el nivel del espacio de nombres y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/Namespaces/CheckNotificationHubAvailability/action|Comprueba si un nombre de NotificationHub determinado está disponible dentro de un espacio de nombres.|
|/Namespaces/Delete|Elimina el recurso del espacio de nombres|
|/Namespaces/NotificationHubs/authorizationRules/action|Obtiene la lista de reglas de autorización del Centro de notificaciones|
|/Namespaces/NotificationHubs/authorizationRules/delete|Elimina las reglas de autorización del Centro de notificaciones|
|/Namespaces/NotificationHubs/authorizationRules/listkeys/action|Obtiene la cadena de conexión al Centro de notificaciones|
|/Namespaces/NotificationHubs/authorizationRules/read|Obtiene la lista de reglas de autorización del Centro de notificaciones|
|/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action|Notification Hub Authorization Rule Regenerate Primary/SecondaryKey especifica la clave que se debe regenerar|
|/Namespaces/NotificationHubs/authorizationRules/write|Crea reglas de autorización del Centro de notificaciones y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/Namespaces/NotificationHubs/debugSend/action|Envía una notificación de inserción de prueba.|
|/Namespaces/NotificationHubs/Delete|Elimina un recurso del Centro de notificaciones|
|/Namespaces/NotificationHubs/metricDefinitions/read|Obtiene una lista de descripciones de recursos de métricas del espacio de nombres|
|/Namespaces/NotificationHubs/pnsCredentials/action|Obtiene todas las credenciales PNS del Centro de notificaciones. Esto incluye las credenciales WNS, MPNS, APNS, GCM y Baidu|
|/Namespaces/NotificationHubs/read|Obtiene una lista de descripciones de recursos del Centro de notificaciones|
|/Namespaces/NotificationHubs/write|Crea un Centro de notificaciones y actualiza sus propiedades. Sus propiedades incluyen principalmente las credenciales PNS. Reglas de autorización y TTL|
|/Namespaces/read|Obtiene la lista de descripción del recurso del espacio de nombres|
|/Namespaces/write|Crea un recurso de espacio de nombres y actualiza sus propiedades. Las etiquetas y la capacidad del espacio de nombres son las propiedades que se pueden actualizar.|
|/register/action|Registra la suscripción para el proveedor de recursos de NotificationHubs y habilita la creación de espacios de nombres y NotificationHubs|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Operación | DESCRIPCIÓN |
|---|---|
|/linkTargets/read|Enumera las cuentas existentes que no están asociadas a una suscripción de Azure. Para vincular esta suscripción de Azure a un área de trabajo, use un identificador de cliente devuelto por esta operación en la propiedad correspondiente de la operación Create Workspace.|
|/register/action|Registra una suscripción en un proveedor de recursos.|
|/workspaces/analytics/query/action|Realiza búsquedas mediante el nuevo motor.|
|/workspaces/analytics/query/schema/read|Obtiene el esquema de búsqueda V2.|
|/workspaces/api/query/action|Realiza búsquedas mediante el nuevo motor.|
|/workspaces/api/query/schema/read|Obtiene el esquema de búsqueda V2.|
|/workspaces/configurationScopes/delete|Elimina el ámbito de configuración|
|/workspaces/configurationScopes/read|Obtiene el ámbito de configuración|
|/workspaces/configurationScopes/write|Establece el ámbito de configuración|
|/workspaces/datasources/delete|Elimina los orígenes de datos en un área de trabajo.|
|/workspaces/datasources/read|Obtiene los orígenes de datos en un área de trabajo.|
|/workspaces/datasources/write|Crea o actualiza los orígenes de datos en un área de trabajo.|
|/workspaces/delete|Elimina un área de trabajo. Si el área de trabajo se vinculó a un área de trabajo ya existente en el momento de la creación, no se eliminará el área de trabajo a la que está vinculado.|
|/workspaces/generateregistrationcertificate/action|Genera el registro de certificado para el área de trabajo. Este certificado se usa para conectar Microsoft System Center Operation Manager al área de trabajo.|
|/workspaces/intelligencepacks/disable/action|Deshabilita un Intelligence Pack para un área de trabajo determinado.|
|/workspaces/intelligencepacks/enable/action|Habilita un Intelligence Pack para un área de trabajo determinado.|
|/workspaces/intelligencepacks/read|Enumera todos los Intelligence Packs que están visibles para un área de trabajo determinada y también muestra si el paquete está habilitado o deshabilitado para esa área de trabajo.|
|/workspaces/linkedServices/delete|Elimina los servicios vinculados en función del área de trabajo establecida.|
|/workspaces/linkedServices/read|Obtiene los servicios vinculados en función del área de trabajo establecida.|
|/workspaces/linkedServices/write|Crea o actualiza los servicios vinculados en función del área de trabajo establecida.|
|/workspaces/listKeys/action|Recupera las claves de lista del área de trabajo. Estas claves se utilizan para conectar los agentes de Microsoft Operational Insights al área de trabajo.|
|/workspaces/listKeys/read|Recupera las claves de lista del área de trabajo. Estas claves se utilizan para conectar los agentes de Microsoft Operational Insights al área de trabajo.|
|/workspaces/managementGroups/read|Obtiene los nombres y metadatos de los grupos de administración de System Center Operations Manager conectados a esta área de trabajo.|
|/workspaces/metricDefinitions/read|Obtiene las definiciones de métricas en el área de trabajo.|
|/workspaces/notificationSettings/delete|Elimina la configuración de notificaciones del usuario del área de trabajo.|
|/workspaces/notificationSettings/read|Obtiene la configuración de notificaciones del usuario del área de trabajo.|
|/workspaces/notificationSettings/write|Establece la configuración de notificaciones del usuario del área de trabajo.|
|/workspaces/purge/action|Elimina los datos especificados del área de trabajo.|
|/workspaces/read|Obtiene un área de trabajo existente|
|/workspaces/savedSearches/delete|Elimina una consulta de búsqueda guardada|
|/workspaces/savedSearches/read|Obtiene una consulta de búsqueda guardada|
|/workspaces/savedSearches/write|Crea una consulta de búsqueda guardada|
|/workspaces/schema/read|Obtiene el esquema de búsqueda del área de trabajo.  El esquema de búsqueda incluye los campos expuestos y sus tipos.|
|/workspaces/search/action|Ejecuta una consulta de búsqueda|
|/workspaces/sharedKeys/action|Recupera las claves compartidas del área de trabajo. Estas claves se utilizan para conectar los agentes de Microsoft Operational Insights al área de trabajo.|
|/workspaces/sharedKeys/read|Recupera las claves compartidas del área de trabajo. Estas claves se utilizan para conectar los agentes de Microsoft Operational Insights al área de trabajo.|
|/workspaces/storageinsightconfigs/delete|Elimina una configuración de almacenamiento. Esto impide a Microsoft Operational Insights leer los datos de la cuenta de almacenamiento.|
|/workspaces/storageinsightconfigs/read|Obtiene una configuración de almacenamiento.|
|/workspaces/storageinsightconfigs/write|Crea una nueva configuración de almacenamiento. Estas configuraciones se usan para extraer datos de una ubicación de una cuenta de almacenamiento existente.|
|/workspaces/usages/read|Obtiene los datos de uso de un área de trabajo incluida la cantidad de datos que esta lee.|
|/workspaces/write|Crea una nueva área de trabajo o vincula a un área de trabajo que ya existe proporcionando el identificador de cliente de esta.|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Operación | DESCRIPCIÓN |
|---|---|
|/managementAssociations/delete|Elimina la asociación de administración existente.|
|/managementAssociations/read|Obtiene la asociación de administración existente.|
|/managementAssociations/write|Crea una nueva asociación de administración.|
|/managementConfigurations/delete|Elimina la configuración de administración existente.|
|/managementConfigurations/read|Obtiene la configuración de administración existente.|
|/managementConfigurations/write|Crea una configuración de administración nueva.|
|/register/action|Registra una suscripción en un proveedor de recursos.|
|/solutions/delete|Elimina una solución OMS ya existente|
|/solutions/read|Obtiene una solución OMS ya existente|
|/solutions/write|Crea una nueva solución OMS|

## <a name="microsoftportal"></a>Microsoft.Portal

| Operación | DESCRIPCIÓN |
|---|---|
|/dashboards/delete|Quita el panel de la suscripción.|
|/dashboards/read|Lee los paneles de la suscripción.|
|/dashboards/write|Agrega un panel a una suscripción o lo modifica.|

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

| Operación | DESCRIPCIÓN |
|---|---|
|/capacities/checkNameAvailability/action|Comprueba que, dada la capacidad dedicada de Power BI, el nombre sea válido y no esté en uso.|
|/capacities/delete|Elimina la capacidad dedicada de Power BI.|
|/capacities/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de la capacidad dedicada de Power BI.|
|/capacities/read|Recupera la información de la capacidad dedicada de Power BI.|
|/capacities/write|Crea o actualiza la capacidad dedicada de Power BI especificada.|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Operación | DESCRIPCIÓN |
|---|---|
|/locations/allocatedStamp/read|GetAllocatedStamp es una operación interna que el servicio usa|
|/locations/allocateStamp/action|AllocateStamp es una operación interna que el servicio usa|
|/locations/backupPreValidateProtection/action||
|/locations/backupStatus/action|Comprueba el estado de la copia de seguridad de los almacenes de Recovery Services.|
|/locations/backupValidateFeatures/action|Valida las características.|
|/operations/read|La operación devuelve la lista de operaciones de un proveedor de recursos.|
|/register/action|Registra la suscripción de un proveedor de recursos determinado.|
|/Vaults/backupconfig/read|Devuelve la configuración del almacén de Recovery Services.|
|/Vaults/backupconfig/write|Actualiza la configuración del almacén de Recovery Services.|
|/Vaults/backupEngines/read|Devuelve todos los servidores de administración de copia de seguridad que se registraron con el almacén.|
|/Vaults/backupFabrics/{fabricName}/protectionContainers/{containerName}/items/read|Obtiene todos los elementos de un contenedor.|
|/Vaults/backupFabrics/backupProtectionIntent/write|Crea la intención de protección de la copia de seguridad.|
|/Vaults/backupFabrics/operationResults/read|Devuelve el estado de la operación|
|/Vaults/backupFabrics/protectableContainers/read|Obtiene todos los contenedores que se pueden proteger.|
|/Vaults/backupFabrics/protectionContainers/inquire/action|Realiza consultas para las cargas de trabajo de un contenedor.|
|/Vaults/backupFabrics/protectionContainers/operationResults/read|Obtiene los resultados de la operación realizada en el contenedor de protección.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action|Hace una copia de seguridad del elemento protegido.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/delete|Elimina los elementos protegidos|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read|Obtiene el resultado de la operación realizada en los elementos protegidos.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read|Devuelve el estado de la operación realizada en los elementos protegidos.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/read|Devuelve detalles de objeto del elemento protegido|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/provisionInstantItemRecovery/action|Aprovisiona una recuperación de elementos instantánea para los elementos protegidos|
|/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read|Obtiene los puntos de recuperación de los elementos protegidos.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/restore/action|Restaura los puntos de recuperación de los elementos protegidos.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/revokeInstantItemRecovery/action|Revoca la recuperación de elementos instantánea para los elementos protegidos|
|/Vaults/backupFabrics/protectionContainers/protectedItems/write|Crea un elemento protegido de copia de seguridad|
|/Vaults/backupFabrics/protectionContainers/read|Devuelve todos los contenedores registrados|
|/Vaults/backupFabrics/protectionContainers/write|Crea un contenedor registrado.|
|/Vaults/backupFabrics/refreshContainers/action|Actualiza la lista de contenedores|
|/Vaults/backupJobs/cancel/action|Cancela el trabajo|
|/Vaults/backupJobs/operationResults/read|Devuelve el resultado de la operación de trabajo.|
|/Vaults/backupJobs/read|Devuelve todos los objetos de trabajo|
|/Vaults/backupJobsExport/action|Exporta trabajos|
|/Vaults/backupJobsExport/operationResults/read|Devuelve el resultado de la operación del trabajo de exportación.|
|/Vaults/backupManagementMetaData/read|Devuelve los metadatos de administración de Backup para el almacén de Recovery Services.|
|/Vaults/backupOperationResults/read|Devuelve el resultado de la operación de Backup para el almacén de Recovery Services.|
|/Vaults/backupOperations/read|Devuelve el estado de la operación de Backup para el almacén de Recovery Services.|
|/Vaults/backupPolicies/delete|Elimina una directiva de protección|
|/Vaults/backupPolicies/operationResults/read|Obtiene los resultados de la operación de directiva.|
|/Vaults/backupPolicies/operations/read|Obtiene el estado de la operación de directiva.|
|/Vaults/backupPolicies/read|Devuelve todas las directivas de protección|
|/Vaults/backupPolicies/write|Crea una directiva de protección|
|/Vaults/backupProtectableItems/read|Devuelve una lista de todos los elementos que se pueden proteger.|
|/Vaults/backupProtectedItems/read|Devuelve la lista de todos los elementos protegidos.|
|/Vaults/backupProtectionContainers/read|Devuelve todos los contenedores que pertenecen a la suscripción|
|/Vaults/backupSecurityPIN/action|Devuelve la información del PIN de seguridad del almacén de Recovery Services.|
|/Vaults/backupstorageconfig/read|Devuelve la configuración de almacenamiento del almacén de Recovery Services.|
|/Vaults/backupstorageconfig/write|Actualiza la configuración de almacenamiento del almacén de Recovery Services.|
|/Vaults/backupUsageSummaries/read|Devuelve resúmenes de los elementos y servidores protegidos para un almacén de Recovery Services.|
|/Vaults/certificates/write|La operación Actualizar certificado de recursos permite actualizar el certificado de credencial de recursos o almacenes.|
|/Vaults/delete|La operación Delete Vault elimina el recurso de Azure del tipo "almacén" especificado|
|/Vaults/extendedInformation/delete|La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén?|
|/Vaults/extendedInformation/read|La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén?|
|/Vaults/extendedInformation/write|La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén?|
|/Vaults/monitoringAlerts/read|Obtiene las alertas del almacén de Recovery Services.|
|/Vaults/monitoringAlerts/write|Resuelve la alerta.|
|/Vaults/monitoringConfigurations/read|Obtiene la configuración de notificaciones del almacén de Recovery Services.|
|/Vaults/monitoringConfigurations/write|Configura las notificaciones por correo electrónico para el almacén de servicios de recuperación.|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/read|Diagnósticos de Azure Backup.|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/write|Diagnósticos de Azure Backup.|
|/Vaults/providers/Microsoft.Insights/logDefinitions/read|Registros de Azure Backup.|
|/Vaults/providers/Microsoft.Insights/metricDefinitions/read|Métricas de Azure Backup.|
|/Vaults/read|La operación Get Vault obtiene un objeto que representa el recurso de Azure del tipo "almacén"|
|/Vaults/registeredIdentities/delete|La operación Anular registro de contenedor se puede usar para eliminar el registro de un contenedor.|
|/Vaults/registeredIdentities/operationResults/read|La operación Obtener resultados de la operación se puede usar para obtener el estado y el resultado de la operación enviada de forma asincrónica|
|/Vaults/registeredIdentities/read|La operación Obtener contenedores se puede usar para obtener los contenedores registrados para un recurso.|
|/Vaults/registeredIdentities/write|La operación Registrar contenedor de servicios se puede usar para registrar un contenedor con servicio de recuperación.|
|/vaults/replicationAlertSettings/read|Lee la configuración de todas las alertas|
|/vaults/replicationAlertSettings/write|Crea o actualiza la configuración de todas las alertas|
|/vaults/replicationEvents/read|Lee todos los eventos|
|/vaults/replicationFabrics/checkConsistency/action|Comprueba la coherencia del tejido|
|/vaults/replicationFabrics/delete|Elimina todos los tejidos|
|/vaults/replicationFabrics/deployProcessServerImage/action|Implementa la imagen del servidor de proceso|
|/vaults/replicationFabrics/read|Lee todos los tejidos|
|/vaults/replicationFabrics/reassociateGateway/action|Vuelve a asociar la puerta de enlace|
|/vaults/replicationFabrics/remove/action|Quita el tejido|
|/vaults/replicationFabrics/renewcertificate/action|Renueva un certificado para Fabric.|
|/vaults/replicationFabrics/replicationNetworks/read|Lee todas las redes|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete|Elimina todas las asignaciones de redes|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read|Lee todas las asignaciones de redes|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write|Crea o actualiza todas las asignaciones de redes|
|/vaults/replicationFabrics/replicationProtectionContainers/ discoverProtectableItem/action|Detecta los elementos que se pueden proteger|
|/vaults/replicationFabrics/replicationProtectionContainers/read|Lee todos los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/remove/action|Elimina los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectableItems/read|Lee todos los elementos que se pueden proteger|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/applyRecoveryPoint/action|Aplica un punto de recuperación|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/delete|Elimina todos los elementos protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/failoverCommit/action|Confirma la conmutación por error|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/plannedFailover/action|Conmutación por error planeada|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/read|Lee todos los elementos protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/recoveryPoints/read|Lee los puntos de recuperación de todas las replicaciones|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/remove/action|Quita los elementos protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/repairReplication/action|Repara una replicación|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/reProtect/action|Vuelva a proteger el elemento protegido|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailover/action|Test Failover|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailoverCleanup/action|Prueba la limpieza de la conmutación por error|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/unplannedFailover/action|Conmutación por error|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/updateMobilityService/action|Actualiza Mobility Service|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/write|Crea o actualiza todos los elementos protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/delete|Elimina todas las asignaciones de los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/read|Lee todas las asignaciones de los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/remove/action|Quita las asignaciones de los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/write|Crea o actualiza todas las asignaciones de los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action|Cambia los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/write|Crea o actualiza todos los contenedores de protección|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete|Elimina todos los proveedores de Recovery Services|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/read|Lee todos los proveedores de Recovery Services|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/ refreshProvider/action|Actualiza el proveedor|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action|Quita los proveedores de Recovery Services|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/write|Crea o actualiza los proveedores de Recovery Services.|
|/vaults/replicationFabrics/replicationStorageClassifications/read|Lee todas las clasificaciones de almacenamiento|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/delete|Elimina todas las asignaciones de clasificaciones de almacenamiento|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/read|Lee todas las asignaciones de clasificaciones de almacenamiento|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/write|Crea o actualiza todas las asignaciones de clasificaciones de almacenamiento|
|/vaults/replicationFabrics/replicationvCenters/delete|Elimina todos los trabajos|
|/vaults/replicationFabrics/replicationvCenters/read|Lee todos los trabajos|
|/vaults/replicationFabrics/replicationvCenters/write|Crea o actualiza todos los trabajos|
|/vaults/replicationFabrics/write|Crea o actualiza todos los tejidos|
|/vaults/replicationJobs/cancel/action|Cancela un trabajo|
|/vaults/replicationJobs/read|Lee todos los trabajos|
|/vaults/replicationJobs/restart/action|Reinicia un trabajo|
|/vaults/replicationJobs/resume/action|Reanuda un trabajo|
|/vaults/replicationPolicies/delete|Elimina todas las directivas|
|/vaults/replicationPolicies/read|Lee todas las directivas|
|/vaults/replicationPolicies/write|Crea o actualiza todas las directivas|
|/vaults/replicationRecoveryPlans/delete|Elimina todos los planes de recuperación|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Plan de recuperación de confirmación de la conmutación por error|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Plan de recuperación de conmutación por error planeado|
|/vaults/replicationRecoveryPlans/read|Lee todos los planes de recuperación|
|/vaults/replicationRecoveryPlans/reProtect/action|Vuelve a proteger el plan de recuperación|
|/vaults/replicationRecoveryPlans/testFailover/action|Prueba el plan de recuperación de conmutación por error|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Prueba el plan de recuperación de limpieza de la conmutación por error|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Plan de recuperación de conmutación por error|
|/vaults/replicationRecoveryPlans/write|Crea o actualiza todos los planes de recuperación|
|/Vaults/tokenInfo/read|Devuelve la información del token del almacén de Recovery Services.|
|/vaults/usages/read|Lee todos los usos de almacén|
|/Vaults/usages/read|Devuelve los detalles de uso de un almacén de Recovery Services.|
|/Vaults/vaultTokens/read|La operación Token de almacén se puede usar para obtener el token de almacén de las operaciones back-end a nivel de almacén.|
|/Vaults/write|La operación Create Vault crea un recurso de Azure del tipo "almacén"|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Operación | DESCRIPCIÓN |
|---|---|
|/checkNameAvailability/action|Comprueba la disponibilidad del espacio de nombres en una suscripción dada.|
|/checkNamespaceAvailability/action|Comprueba la disponibilidad del espacio de nombres en una suscripción dada. Esta API está en desuso; en su lugar, use CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/action|Actualiza la regla de autorización del espacio de nombres. Esta API está en desuso. Use una llamada PUT para actualizar la regla de autorización del espacio de nombres en su lugar. Esta operación no se admite en la versión de API del 01/04/2017.|
|/namespaces/authorizationRules/delete|Elimina la regla de autorización del espacio de nombres. No se puede eliminar la regla predeterminada de autorización del espacio de nombres. |
|/namespaces/authorizationRules/listkeys/action|Obtiene la cadena de conexión al espacio de nombres|
|/namespaces/authorizationRules/read|Obtiene la lista de descripciones de reglas de autorización de espacios de nombres.|
|/namespaces/authorizationRules/regenerateKeys/action|Regenera la clave principal o secundaria del recurso|
|/namespaces/authorizationRules/write|Crea reglas de autorización en el nivel del espacio de nombres y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/namespaces/Delete|Elimina el recurso del espacio de nombres|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Obtiene las claves de las reglas de autorización del espacio de nombres principal de recuperación ante desastres.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Obtiene las reglas de autorización del espacio de nombres principal de recuperación ante desastres.|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Deshabilita la recuperación ante desastres y deja de replicar los cambios de los espacios de nombres principales a los secundarios.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Comprueba la disponibilidad del alias del espacio de nombres en una suscripción dada.|
|/namespaces/disasterRecoveryConfigs/delete|Elimina la configuración de la recuperación ante desastres asociada con el espacio de nombres. Esta operación sólo se puede invocar a través del espacio de nombres principal.|
|/namespaces/disasterRecoveryConfigs/failover/action|Invoca una conmutación por error de GEO DR y vuelve a configurar el alias del espacio de nombres para que apunte al espacio de nombres secundario.|
|/namespaces/disasterRecoveryConfigs/read|Obtiene la configuración de la recuperación ante desastres asociada con el espacio de nombres.|
|/namespaces/disasterRecoveryConfigs/write|Crea o actualiza la configuración de la recuperación ante desastres asociada con el espacio de nombres.|
|/namespaces/HybridConnections/authorizationRules/action|Operación para actualizar HybridConnection. Esta operación no se admite en la versión de API del 01/04/2017. Reglas de autorización. Use una llamada PUT para actualizar la regla de autorización.|
|/namespaces/HybridConnections/authorizationRules/delete|Operación para eliminar las reglas de autorización de HybridConnection|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|Obtiene la cadena de conexión a HybridConnection|
|/namespaces/HybridConnections/authorizationRules/read| Obtiene la lista de las reglas de autorización de HybridConnection.|
|/namespaces/HybridConnections/authorizationRules/regeneratekeys/action|Regenera la clave principal o secundaria del recurso|
|/namespaces/HybridConnections/authorizationRules/write|Crea reglas de autorización de HybridConnection y actualiza sus propiedades. Los derechos de acceso de las reglas de autorización se pueden actualizar.|
|/namespaces/HybridConnections/Delete|Operación para eliminar el recurso de HybridConnection|
|/namespaces/HybridConnections/read|Obtiene una lista de descripciones de recursos de HybridConnection|
|/namespaces/HybridConnections/write|Crea o actualiza las propiedades de HybridConnection.|
|/namespaces/messagingPlan/read|Obtiene el plan de mensajería para un espacio de nombres. Esta API está en desuso. Las propiedades expuestas a través del recurso MessagingPlan se mueven al recurso del espacio de nombres (principal) en versiones posteriores de la API. Esta operación no se admite en la versión de API del 01/04/2017.|
|/namespaces/messagingPlan/write|Actualiza el plan mensajería de un espacio de nombres. Esta API está en desuso. Las propiedades expuestas a través del recurso MessagingPlan se mueven al recurso del espacio de nombres (principal) en versiones posteriores de la API. Esta operación no se admite en la versión de API del 01/04/2017.|
|/namespaces/operationresults/read|Obtiene el estado de la operación del espacio de nombres.|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Obtiene una lista de descripciones de recursos de métricas del espacio de nombres|
|/namespaces/read|Obtiene la lista de descripción del recurso del espacio de nombres|
|/namespaces/WcfRelays/authorizationRules/action|Operación para actualizar WcfRelay. Esta operación no se admite en la versión de API del 01/04/2017. Reglas de autorización. Use una llamada PUT para actualizar la regla de autorización.|
|/namespaces/WcfRelays/authorizationRules/delete|Operación para eliminar las reglas de autorización de WcfRelay|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|Obtiene la cadena de conexión a WcfRelay|
|/namespaces/WcfRelays/authorizationRules/read| Obtiene la lista de reglas de autorización de WcfRelay.|
|/namespaces/WcfRelays/authorizationRules/regeneratekeys/action|Regenera la clave principal o secundaria del recurso|
|/namespaces/WcfRelays/authorizationRules/write|Crea reglas de autorización de WcfRelay y actualiza sus propiedades. Los derechos de acceso de las reglas de autorización se pueden actualizar.|
|/namespaces/WcfRelays/Delete|Operación para eliminar los recursos de WcfRelay|
|/namespaces/WcfRelays/read|Obtiene una lista de descripciones de recursos de WcfRelay|
|/namespaces/WcfRelays/write|Crea o actualiza las propiedades de WcfRelay.|
|/namespaces/write|Crea un recurso de espacio de nombres y actualiza sus propiedades. Las etiquetas y la capacidad del espacio de nombres son las propiedades que se pueden actualizar.|
|/operations/read|Obtiene operaciones.|
|/register/action|Registra la suscripción para el proveedor de recursos de Relay y habilita la creación de recursos de Relay|
|/unregister/action|Registra la suscripción para el proveedor de recursos de Relay y habilita la creación de recursos de Relay|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Operación | DESCRIPCIÓN |
|---|---|
|/AvailabilityStatuses/current/read|Obtiene el estado de disponibilidad del recurso especificado|
|/AvailabilityStatuses/read|Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado|
|/healthevent/action|Denota el cambio del estado de mantenimiento del recurso especificado.|
|/healthevent/Activated/action|Denota el cambio del estado de mantenimiento del recurso especificado.|
|/healthevent/InProgress/action|Denota el cambio del estado de mantenimiento del recurso especificado.|
|/healthevent/Pending/action|Denota el cambio del estado de mantenimiento del recurso especificado.|
|/healthevent/Resolved/action|Denota el cambio del estado de mantenimiento del recurso especificado.|
|/healthevent/Updated/action|Denota el cambio del estado de mantenimiento del recurso especificado.|
|/register/action|Registra la suscripción para Microsoft Resource Health.|

## <a name="microsoftresources"></a>Microsoft.Resources

| Operación | DESCRIPCIÓN |
|---|---|
|/checkResourceName/action|Comprueba la validez del nombre de recurso.|
|/deployments/cancel/action|Cancela una implementación.|
|/deployments/delete|Elimina una implementación.|
|/deployments/operations/read|Obtiene o enumera las operaciones de implementación.|
|/deployments/read|Obtiene o enumera implementaciones.|
|/deployments/validate/action|Valida una implementación.|
|/deployments/write|Crea o actualiza una implementación.|
|/links/delete|Elimina un vínculo de recursos.|
|/links/read|Obtiene o enumera los vínculos de recursos.|
|/links/write|Crea o actualiza un vínculo de recursos.|
|/marketplace/purchase/action|Compra un recurso de Marketplace.|
|/providers/read|Obtiene la lista de proveedores.|
|/resources/read|Obtiene la lista de recursos en función de los filtros.|
|/subscriptions/locations/read|Obtiene la lista de ubicaciones admitidas.|
|/subscriptions/operationresults/read|Obtiene los resultados de la operación de suscripción.|
|/subscriptions/providers/read|Obtiene o enumera los proveedores de recursos.|
|/subscriptions/read|Obtiene la lista de suscripciones.|
|/subscriptions/resourceGroups/delete|Elimina un grupo de recursos y todos sus recursos.|
|/subscriptions/resourcegroups/deployments/operations/read|Obtiene o enumera las operaciones de implementación.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|Obtiene o enumera los estados de la operación de implementación.|
|/subscriptions/resourcegroups/deployments/read|Obtiene o enumera implementaciones.|
|/subscriptions/resourcegroups/deployments/write|Crea o actualiza una implementación.|
|/subscriptions/resourceGroups/moveResources/action|Mueve recursos de un grupo de recursos a otro.|
|/subscriptions/resourceGroups/read|Obtiene o enumera los grupos de recursos.|
|/subscriptions/resourcegroups/resources/read|Obtiene los recursos del grupo de recursos.|
|/subscriptions/resourceGroups/validateMoveResources/action|Valida el movimiento de recursos de un grupo de recursos a otro.|
|/subscriptions/resourceGroups/write|Crea o actualiza un grupo de recursos.|
|/subscriptions/resources/read|Obtiene recursos de una suscripción.|
|/subscriptions/tagNames/delete|Elimina una etiqueta de suscripción.|
|/subscriptions/tagNames/read|Obtiene o enumera las etiquetas de suscripción.|
|/subscriptions/tagNames/tagValues/delete|Elimina el valor de una etiqueta de suscripción.|
|/subscriptions/tagNames/tagValues/read|Obtiene o enumera los valores de las etiquetas de suscripción.|
|/subscriptions/tagNames/tagValues/write|Agrega un valor de etiqueta de suscripción.|
|/subscriptions/tagNames/write|Agrega una etiqueta de suscripción.|
|/tenants/read|Obtiene la lista de inquilinos.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Operación | DESCRIPCIÓN |
|---|---|
|/jobcollections/delete|Elimina una colección de trabajos.|
|/jobcollections/disable/action|Deshabilita una colección de trabajos.|
|/jobcollections/enable/action|Habilita una colección de trabajos.|
|/jobcollections/jobs/delete|Elimina el trabajo.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Genera una definición de Logic Apps basada en un trabajo de Scheduler.|
|/jobcollections/jobs/jobhistories/read|Obtiene el historial de trabajos.|
|/jobcollections/jobs/read|Obtiene un trabajo.|
|/jobcollections/jobs/run/action|Ejecuta el trabajo.|
|/jobcollections/jobs/write|Crea o actualiza el trabajo.|
|/jobcollections/read|Obtiene una colección de trabajos|
|/jobcollections/write|Crea o actualiza una colección de trabajos.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Operación | DESCRIPCIÓN |
|---|---|
|/checkNameAvailability/action|Comprueba la disponibilidad del nombre del servicio.|
|/register/action|Registra la suscripción para el proveedor de recursos de búsqueda y habilita la creación de servicios de búsqueda.|
|/searchServices/createQueryKey/action|Crea la clave de consulta.|
|/searchServices/delete|Elimina el servicio de búsqueda.|
|/searchServices/diagnosticSettings/read|Obtiene la lectura de la configuración de diagnóstico del recurso.|
|/searchServices/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso.|
|/searchServices/listAdminKeys/action|Lee las claves de administración.|
|/searchServices/logDefinitions/read|Obtiene los registros disponibles del servicio de búsqueda.|
|/searchServices/metricDefinitions/read|Obtiene las métricas disponibles del servicio de búsqueda.|
|/searchServices/queryKey/delete|Elimina las claves de consulta.|
|/searchServices/queryKey/read|Lee las claves de consulta.|
|/searchServices/read|Lee el servicio de búsqueda.|
|/searchServices/regenerateAdminKey/action|Regenera la clave de administración.|
|/searchServices/start/action|Inicia el servicio de búsqueda.|
|/searchServices/stop/action|Detiene el servicio de búsqueda.|
|/searchServices/write|Crea o actualiza el servicio de búsqueda.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Operación | DESCRIPCIÓN |
|---|---|
|/alerts/read|Obtiene todas las alertas de seguridad disponibles.|
|/applicationWhitelistings/read|Obtiene la lista de permitidos de la aplicación|
|/applicationWhitelistings/write|Crea una nueva lista de permitidos de la aplicación o actualiza una ya existente|
|/complianceResults/read|Obtiene los resultados de cumplimiento del recurso.|
|/locations/alerts/activate/action|Activa una alerta de seguridad.|
|/locations/alerts/dismiss/action|Descarta una alerta de seguridad.|
|/locations/alerts/read|Obtiene todas las alertas de seguridad disponibles.|
|/locations/jitNetworkAccessPolicies/initiate/action|Inicia una directiva de acceso de red just-in-time|
|/locations/jitNetworkAccessPolicies/read|Obtiene las directivas just-in-time de acceso de red|
|/locations/jitNetworkAccessPolicies/write|Crea una nueva directiva just-in-time de acceso de red o actualiza una que ya existe|
|/locations/read|Obtiene la ubicación de los datos de seguridad.|
|/locations/tasks/activate/action|Activa una recomendación de seguridad|
|/locations/tasks/dismiss/action|Descarta una recomendación de seguridad|
|/locations/tasks/read|Obtiene todas las recomendaciones de seguridad disponibles|
|/locations/tasks/resolve/action|Resuelve una recomendación de seguridad.|
|/locations/tasks/start/action|Inicia una recomendación de seguridad.|
|/policies/read|Obtiene la directiva de seguridad|
|/policies/write|Actualiza la directiva de seguridad|
|/pricings/delete|Elimina la configuración de precios del ámbito.|
|/pricings/read|Obtiene la configuración de precios del ámbito.|
|/pricings/write|Actualiza la configuración de precios del ámbito.|
|/register/action|Registra la suscripción de Azure Security Center.|
|/securityContacts/delete|Elimina el contacto de seguridad.|
|/securityContacts/read|Obtiene el contacto de seguridad.|
|/securityContacts/write|Actualiza el contacto de seguridad.|
|/securitySolutions/delete|Elimina una solución de seguridad|
|/securitySolutions/read|Obtiene las soluciones de seguridad|
|/securitySolutions/write|Crea una nueva solución de seguridad o actualiza una ya existente|
|/securitySolutionsReferenceData/read|Obtiene los datos de referencia de las soluciones de seguridad|
|/securityStatuses/read|Obtiene el estado de seguridad de los recursos de Azure|
|/securityStatusesSummaries/read|Obtiene resúmenes de los estados de seguridad del ámbito.|
|/tasks/read|Obtiene todas las recomendaciones de seguridad disponibles|
|/webApplicationFirewalls/delete|Elimina un firewall de aplicaciones web|
|/webApplicationFirewalls/read|Obtiene los firewalls de aplicaciones web|
|/webApplicationFirewalls/write|Crea un nuevo firewall de aplicaciones web o actualiza uno que ya existe|
|/workspaceSettings/connect/action|Cambia la opción de reconexión de la configuración del área de trabajo.|
|/workspaceSettings/delete|Elimina la configuración del área de trabajo.|
|/workspaceSettings/read|Obtiene la configuración del área de trabajo.|
|/workspaceSettings/write|Actualiza la configuración del área de trabajo.|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Operación | DESCRIPCIÓN |
|---|---|
|/checkNameAvailability/action|Comprueba la disponibilidad del espacio de nombres en una suscripción dada.|
|/checkNamespaceAvailability/action|Comprueba la disponibilidad del espacio de nombres en una suscripción dada. Esta API está en desuso; en su lugar, use CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/action|Actualiza la regla de autorización del espacio de nombres. Esta API está en desuso. Use una llamada PUT para actualizar la regla de autorización del espacio de nombres en su lugar. Esta operación no se admite en la versión de API del 01/04/2017.|
|/namespaces/authorizationRules/delete|Elimina la regla de autorización del espacio de nombres. No se puede eliminar la regla predeterminada de autorización del espacio de nombres. |
|/namespaces/authorizationRules/listkeys/action|Obtiene la cadena de conexión al espacio de nombres|
|/namespaces/authorizationRules/read|Obtiene la lista de descripciones de reglas de autorización de espacios de nombres.|
|/namespaces/authorizationRules/regenerateKeys/action|Regenera la clave principal o secundaria del recurso|
|/namespaces/authorizationRules/write|Crea reglas de autorización en el nivel del espacio de nombres y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/namespaces/Delete|Elimina el recurso del espacio de nombres|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Obtiene las claves de las reglas de autorización del espacio de nombres principal de recuperación ante desastres.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Obtiene las reglas de autorización del espacio de nombres principal de recuperación ante desastres.|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Deshabilita la recuperación ante desastres y deja de replicar los cambios de los espacios de nombres principales a los secundarios.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Comprueba la disponibilidad del alias del espacio de nombres en una suscripción dada.|
|/namespaces/disasterRecoveryConfigs/delete|Elimina la configuración de la recuperación ante desastres asociada con el espacio de nombres. Esta operación sólo se puede invocar a través del espacio de nombres principal.|
|/namespaces/disasterRecoveryConfigs/failover/action|Invoca una conmutación por error de GEO DR y vuelve a configurar el alias del espacio de nombres para que apunte al espacio de nombres secundario.|
|/namespaces/disasterRecoveryConfigs/read|Obtiene la configuración de la recuperación ante desastres asociada con el espacio de nombres.|
|/namespaces/disasterRecoveryConfigs/write|Crea o actualiza la configuración de la recuperación ante desastres asociada con el espacio de nombres.|
|/namespaces/eventGridFilters/delete|Elimina el filtro de Event Grid asociado con el espacio de nombres.|
|/namespaces/eventGridFilters/read|Obtiene el filtro de Event Grid asociado con el espacio de nombres.|
|/namespaces/eventGridFilters/write|Crea o actualiza el filtro de Event Grid asociado con el espacio de nombres.|
|/namespaces/eventhubs/read|Obtiene una lista de descripciones de recursos de EventHub|
|/namespaces/messagingPlan/read|Obtiene el plan de mensajería para un espacio de nombres. Esta API está en desuso. Las propiedades expuestas a través del recurso MessagingPlan se mueven al recurso del espacio de nombres (principal) en versiones posteriores de la API. Esta operación no se admite en la versión de API del 01/04/2017.|
|/namespaces/messagingPlan/write|Actualiza el plan mensajería de un espacio de nombres. Esta API está en desuso. Las propiedades expuestas a través del recurso MessagingPlan se mueven al recurso del espacio de nombres (principal) en versiones posteriores de la API. Esta operación no se admite en la versión de API del 01/04/2017.|
|/namespaces/migrate/action|Operación de migración del espacio de nombres.|
|/namespaces/operationresults/read|Obtiene el estado de la operación del espacio de nombres.|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene una lista de descripciones de recursos de configuración de diagnósticos del espacio de nombres|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Obtiene una lista de descripciones de recursos de configuración de diagnósticos del espacio de nombres|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Obtiene una lista de descripciones de recursos de registros del espacio de nombres|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Obtiene una lista de descripciones de recursos de métricas del espacio de nombres|
|/namespaces/queues/authorizationRules/action|Operación para actualizar la cola. Esta operación no se admite en la versión de API del 01/04/2017. Reglas de autorización. Use una llamada PUT para actualizar la regla de autorización.|
|/namespaces/queues/authorizationRules/delete|Operación para eliminar las reglas de autorización de Queue|
|/namespaces/queues/authorizationRules/listkeys/action|Obtiene la cadena de conexión a Queue|
|/namespaces/queues/authorizationRules/read| Obtiene la lista de reglas de autorización de Queue|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Regenera la clave principal o secundaria del recurso|
|/namespaces/queues/authorizationRules/write|Crea reglas de autorización de Queue y actualiza sus propiedades. Los derechos de acceso de las reglas de autorización se pueden actualizar.|
|/namespaces/queues/Delete|Operación para eliminar los recursos de Queue|
|/namespaces/queues/read|Obtiene una lista de descripciones de recursos de Queue|
|/namespaces/queues/write|Crea o actualiza las propiedades de Queue.|
|/namespaces/read|Obtiene la lista de descripción del recurso del espacio de nombres|
|/namespaces/topics/authorizationRules/action|Operación para actualizar el tema. Esta operación no se admite en la versión de API del 01/04/2017. Reglas de autorización. Use una llamada PUT para actualizar la regla de autorización.|
|/namespaces/topics/authorizationRules/delete|Operación para eliminar las reglas de autorización de temas|
|/namespaces/topics/authorizationRules/listkeys/action|Obtiene la cadena de conexión al tema|
|/namespaces/topics/authorizationRules/read| Obtiene la lista de reglas de autorización de temas|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Regenera la clave principal o secundaria del recurso|
|/namespaces/topics/authorizationRules/write|Crea reglas de autorización de temas y actualiza sus propiedades. Los derechos de acceso de las reglas de autorización se pueden actualizar.|
|/namespaces/topics/Delete|Operación para eliminar los recursos de un tema|
|/namespaces/topics/read|Obtiene una lista de descripciones de recursos de tema|
|/namespaces/topics/subscriptions/Delete|Operación para eliminar los recursos de TopicSubscription|
|/namespaces/topics/subscriptions/read|Obtiene una lista de descripciones de recursos de TopicSubscription|
|/namespaces/topics/subscriptions/rules/Delete|Operación para eliminar los recursos de reglas|
|/namespaces/topics/subscriptions/rules/read|Obtiene una lista de descripciones de recursos de reglas|
|/namespaces/topics/subscriptions/rules/write|Crea o actualiza las propiedades de reglas.|
|/namespaces/topics/subscriptions/write|Crear o actualizar las propiedades de TopicSubscription.|
|/namespaces/topics/write|Crea o actualiza las propiedades de tema.|
|/namespaces/write|Crea un recurso de espacio de nombres y actualiza sus propiedades. Las etiquetas y la capacidad del espacio de nombres son las propiedades que se pueden actualizar.|
|/operations/read|Obtiene operaciones.|
|/register/action|Registra la suscripción para el proveedor de recursos de ServiceBus y habilita la creación de recursos de ServiceBus|
|/sku/read|Obtiene una lista de descripciones de recursos de SKU.|
|/sku/regions/read|Obtiene una lista de descripciones de recursos de SkuRegions.|
|/unregister/action|Registra la suscripción para el proveedor de recursos de ServiceBus y habilita la creación de recursos de ServiceBus|

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

| Operación | DESCRIPCIÓN |
|---|---|
|/clusters/applications/delete|Elimina cualquier aplicación.|
|/clusters/applications/read|Lee cualquier aplicación.|
|/clusters/applications/services/delete|Elimina cualquier servicio.|
|/clusters/applications/services/partitions/read|Lee cualquier partición.|
|/clusters/applications/services/partitions/replicas/read|Lee cualquier réplica.|
|/clusters/applications/services/read|Lee cualquier servicio.|
|/clusters/applications/services/statuses/read|Lee cualquier estado del servicio.|
|/clusters/applications/services/write|Crea o actualiza cualquier servicio.|
|/clusters/applications/write|Crea o actualiza cualquier aplicación.|
|/clusters/applicationTypes/delete|Elimina cualquier tipo de aplicación.|
|/clusters/applicationTypes/read|Lee cualquier tipo de aplicación.|
|/clusters/applicationTypes/versions/delete|Elimina cualquier versión de tipo de aplicación.|
|/clusters/applicationTypes/versions/read|Lee cualquier versión de tipo de aplicación.|
|/clusters/applicationTypes/versions/write|Crea o actualiza cualquier versión de tipo de aplicación.|
|/clusters/applicationTypes/write|Crea o actualiza cualquier tipo de aplicación.|
|/clusters/delete|Borra cualquier clúster.|
|/clusters/nodes/read|Lee cualquier nodo.|
|/clusters/read|Lee cualquier clúster.|
|/clusters/statuses/read|Lee cualquier estado del clúster.|
|/clusters/write|Crea o actualiza cualquier clúster.|
|/locations/clusterVersions/read|Lee cualquier versión del clúster.|
|/locations/environments/clusterVersions/read|Lee cualquier versión de clúster para un entorno específico.|
|/locations/operationresults/read|Lee cualquier resultado de la operación.|
|/locations/operations/read|Lee cualquier operación según la ubicación.|
|/operations/read|Lee cualquier operación disponible.|
|/register/action|Registra cualquier acción.|

## <a name="microsoftsolutions"></a>Microsoft.Solutions

| Operación | DESCRIPCIÓN |
|---|---|
|/applicationDefinitions/delete|Quita una definición de aplicación.|
|/applicationDefinitions/read|Recupera una lista de definiciones de aplicación.|
|/applicationDefinitions/write|Agrega o modifica una definición de aplicación.|
|/applications/delete|Quita una aplicación.|
|/applications/read|Recupera una lista de aplicaciones.|
|/applications/write|Crea una aplicación.|
|/locations/operationStatuses/read|Lee el estado de la operación de los recursos.|
|/register/action|Permite registrarse en Solutions.|

## <a name="microsoftsql"></a>Microsoft.Sql

| Operación | DESCRIPCIÓN |
|---|---|
|/checkNameAvailability/action|Comprueba si un nombre de servidor dado está disponible para su aprovisionamiento en todo el mundo según una suscripción determinada.|
|/locations/auditingSettingsAzureAsyncOperation/read|Recupera el resultado de la operación Set para la directiva de auditoría de blobs del servidor extendido.|
|/locations/auditingSettingsOperationResults/read|Recupera el resultado de la operación de establecimiento de directivas de auditoría de blobs del servidor|
|/locations/capabilities/read|Obtiene las capacidades de esta suscripción en una ubicación específica.|
|/locations/databaseAzureAsyncOperation/read|Obtiene el estado de una operación de base de datos.|
|/locations/databaseOperationResults/read|Obtiene el estado de una operación de base de datos.|
|/locations/deletedServerAsyncOperation/read|Obtiene las operaciones en curso del servidor eliminado.|
|/locations/deletedServerOperationResults/read|Obtiene las operaciones en curso del servidor eliminado.|
|/locations/deletedServers/read|Devuelve la lista de servidores eliminados u obtiene las propiedades de un servidor específico eliminado.|
|/locations/deletedServers/recover/action|Recupera un servidor eliminado.|
|/locations/deleteVirtualNetworkOrSubnets/action|Elimina las reglas de red virtual asociadas a una red o subred virtual.|
|/locations/elasticPoolAzureAsyncOperation/read|Obtiene la operación asincrónica de Azure para una operación asincrónica de grupo elástico.|
|/locations/elasticPoolOperationResults/read|Obtiene el resultado de una operación de grupo elástico.|
|/locations/extendedAuditingSettingsAzureAsyncOperation/read|Recupera el resultado de la operación Set para la directiva de auditoría de blobs del servidor extendido.|
|/locations/extendedAuditingSettingsOperationResults/read|Recupera el resultado de la operación Set para la directiva de auditoría de blobs del servidor extendido.|
|/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action|Completa una operación de restauración de base de datos administrada.|
|/locations/managedTransparentDataEncryptionAzureAsyncOperation/read|Obtiene las operaciones en curso para el cifrado de datos transparente de la base de datos administrada.|
|/locations/managedTransparentDataEncryptionOperationResults/read|Obtiene las operaciones en curso para el cifrado de datos transparente de la base de datos administrada.|
|/locations/read|Obtiene las ubicaciones disponibles para una suscripción determinada.|
|/locations/syncAgentOperationResults/read|Recupera el resultado de la operación de recursos del agente de sincronización.|
|/locations/syncDatabaseIds/read|Recupera los identificadores de la base de datos de sincronización de una región y una suscripción determinadas.|
|/locations/syncGroupOperationResults/read|Recupera el resultado de la operación de recursos del grupo de sincronización.|
|/locations/syncMemberOperationResults/read|Recupera el resultado de la operación de recursos del miembro de sincronización.|
|/locations/usages/read|Obtiene una colección de métricas de uso para esta suscripción en una ubicación.|
|/locations/virtualNetworkRulesAzureAsyncOperation/read|Devuelve los detalles de una operación asincrónica de Azure para las reglas de red virtual especificadas. |
|/locations/virtualNetworkRulesOperationResults/read|Devuelve los detalles de una operación para las reglas de red virtual especificadas. |
|/managedInstances/administrators/delete|Elimina un administrador existente de la instancia administrada.|
|/managedInstances/administrators/read|Obtiene una lista de administradores de la instancia administrada.|
|/managedInstances/administrators/write|Crea o actualiza el administrador de la instancia administrada con los parámetros especificados.|
|/managedInstances/databases/delete|Elimina una base de datos administrada ya existente.|
|/managedInstances/databases/read|Obtiene una base de datos administrada ya existente.|
|/managedInstances/databases/securityAlertPolicies/read|Recupera los detalles de la directiva de detección de amenazas en bases de datos que está configurada en una base de datos administrada específica.|
|/managedInstances/databases/securityAlertPolicies/write|Cambia la directiva de detección de amenazas en bases de datos para una base de datos administrada específica.|
|/managedInstances/databases/securityEvents/read|Recupera los eventos de seguridad de una base de datos administrada.|
|/managedInstances/databases/transparentDataEncryption/read|Recupera los detalles del cifrado de datos transparente de una base de datos para una base de datos administrada específica.|
|/managedInstances/databases/transparentDataEncryption/write|Cambia el cifrado de datos transparente de una base de datos para una base de datos administrada específica.|
|/managedInstances/databases/write|Crea una nueva base de datos o actualiza una ya existente.|
|/managedInstances/delete|Elimina una base de datos administrada ya existente.|
|/managedInstances/metricDefinitions/read|Obtiene las definiciones de métricas de la instancia administrada.|
|/managedInstances/metrics/read|Obtiene las métricas de la instancia administrada.|
|/managedInstances/read|Devuelve la lista de instancias administradas u obtiene las propiedades de una instancia administrada específica.|
|/managedInstances/securityAlertPolicies/read|Recupera los detalles de la directiva de detección de amenazas en un servidor administrado que está configurada en un servidor administrado específico.|
|/managedInstances/securityAlertPolicies/write|Cambia la directiva de detección de amenazas en un servidor administrado para un servidor administrado específico.|
|/managedInstances/write|Crea una instancia administrada con los parámetros especificados o actualiza las propiedades o etiquetas de la instancia administrada especificada.|
|/operations/read|Obtiene las operaciones de REST disponibles.|
|/register/action|Registra la suscripción del proveedor de recursos de Microsoft SQL Database y habilita la creación de bases de datos de Microsoft SQL Database.|
|/servers/administratorOperationResults/read|Obtiene las operaciones en curso de los administradores del servidor.|
|/servers/administrators/delete|Elimina el administrador del servidor.|
|/servers/administrators/read|Recupera los detalles del administrador del servidor|
|/servers/administrators/write|Crea o actualiza el administrador del servidor|
|/servers/advisors/read|Devuelve la lista de asesores disponibles para el servidor|
|/servers/advisors/recommendedActions/read|Devuelve una lista de acciones recomendadas del asesor especificado para el servidor|
|/servers/advisors/recommendedActions/write|Aplica la acción recomendada en el servidor|
|/servers/advisors/write|Actualiza el estado de ejecución automática de un asesor en el nivel del servidor.|
|/servers/auditingPolicies/read|Recupera detalles de la directiva predeterminada de auditoría de tablas del servidor configurada en un servidor determinado|
|/servers/auditingPolicies/write|Cambia la directiva predeterminada de auditoría de tablas del servidor para un servidor determinado|
|/servers/auditingSettings/operationResults/read|Recupera el resultado de la operación de establecimiento de directivas de auditoría de blobs del servidor|
|/servers/auditingSettings/read|Recupera detalles de la directiva de auditoría de blobs del servidor configurada en un servidor determinado|
|/servers/auditingSettings/write|Cambia la auditoría de blobs del servidor para un servidor determinado|
|/servers/automaticTuning/read|Devuelve la configuración de optimización automática del servidor.|
|/servers/automaticTuning/write|Actualiza la configuración de optimización automática del servidor y devuelve la configuración actualizada.|
|/servers/backupLongTermRetentionVaults/delete|Elimina un almacén de archivos de copia de seguridad.|
|/servers/backupLongTermRetentionVaults/read|Esta operación se usa para obtener un almacén de retención de copia de seguridad a largo plazo. Devuelve información acerca del almacén registrado en este servidor.|
|/servers/backupLongTermRetentionVaults/write|Esta operación se usa para registrar un almacén de retención de copias de seguridad a largo plazo en un servidor.|
|/servers/communicationLinks/delete|Elimina un vínculo de comunicación del servidor existente.|
|/servers/communicationLinks/read|Devuelve la lista de vínculos de comunicación de un servidor específico.|
|/servers/communicationLinks/write|Crea o actualiza un vínculo de comunicación del servidor.|
|/servers/connectionPolicies/read|Devuelve la lista de directivas de conexión del servidor de un servidor específico.|
|/servers/connectionPolicies/write|Crea o actualiza una directiva de conexión del servidor.|
|/servers/databases/advisors/read|Devuelve la lista de asesores disponibles para la base de datos|
|/servers/databases/advisors/recommendedActions/read|Devuelve una lista de acciones recomendadas del asesor especificado para la base de datos|
|/servers/databases/advisors/recommendedActions/write|Aplica la acción recomendada en la base de datos|
|/servers/databases/advisors/write|Actualiza el estado de ejecución automática de un asesor en el nivel de la base de datos.|
|/servers/databases/auditingPolicies/read|Recupera detalles de la directiva de auditoría de tablas configurada en una base de datos determinada|
|/servers/databases/auditingPolicies/write|Cambia la directiva de auditoría de tablas para una base de datos determinada|
|/servers/databases/auditingSettings/read|Recupera detalles de la directiva de auditoría de blobs configurada en una base de datos determinada|
|/servers/databases/auditingSettings/write|Cambia la directiva de auditoría de blobs para una base de datos determinada|
|/servers/databases/auditRecords/read|Recupera los registros de auditoría de blobs de bases de datos|
|/servers/databases/automaticTuning/read|Devuelve la configuración de optimización automática de una base de datos.|
|/servers/databases/automaticTuning/write|Actualiza la configuración de optimización automática de una base de datos y devuelve la configuración actualizada.|
|/servers/databases/azureAsyncOperation/read|Obtiene el estado de una operación de base de datos.|
|/servers/databases/backupLongTermRetentionPolicies/read|Devuelve la lista de las directivas de archivado de copias de seguridad de una base de datos específica.|
|/servers/databases/backupLongTermRetentionPolicies/write|Crea o actualiza una directiva de archivado de copias de seguridad de bases de datos.|
|/servers/databases/connectionPolicies/read|Recupera detalles de la directiva de conexión configurada en una base de datos determinada|
|/servers/databases/connectionPolicies/write|Cambia la directiva de conexión para una base de datos determinada|
|/servers/databases/dataMaskingPolicies/read|Devuelve la lista de directivas de enmascaramiento de datos de una base de datos.|
|/servers/databases/dataMaskingPolicies/rules/delete|Elimina la regla de la directiva de enmascaramiento de datos de una base de datos determinada.|
|/servers/databases/dataMaskingPolicies/rules/read|Recupera los detalles de la regla de directiva de enmascaramiento de datos configurada en una determinada base de datos|
|/servers/databases/dataMaskingPolicies/rules/write|Cambia la regla de directiva de enmascaramiento de datos de una determinada base de datos|
|/servers/databases/dataMaskingPolicies/write|Cambia la directiva de enmascaramiento de datos de una determinada base de datos|
|/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read|Devuelve la información de pasos de la consulta distribuida de almacenamiento de datos para el identificador del paso seleccionado|
|/servers/databases/dataWarehouseQueries/read|Devuelve la información de la consulta de distribución de almacenamiento de datos para el identificador de la consulta seleccionado|
|/servers/databases/dataWarehouseUserActivities/read|Recupera las actividades de usuario de una instancia de SQL Data Warehouse que incluye las consultas en ejecución y las suspendidas.|
|/servers/databases/delete|Elimina una base de datos existente.|
|/servers/databases/export/action|Exporta Azure SQL Database.|
|/servers/databases/extendedAuditingSettings/read|Recupera los detalles de la directiva de auditoría de blobs extendida y configurada en una base de datos determinada.|
|/servers/databases/extendedAuditingSettings/write|Cambia la directiva de auditoría de blobs extendida para una base de datos determinada.|
|/servers/databases/extensions/read|Obtiene una colección de extensiones para la base de datos.|
|/servers/databases/extensions/write|Cambia la extensión de una base de datos determinada.|
|/servers/databases/geoBackupPolicies/read|Recupera las directivas de copia de seguridad de replicación geográfica para una base de datos determinada.|
|/servers/databases/geoBackupPolicies/write|Crea o actualiza una directiva de replicación geográfica de copias de seguridad de bases de datos.|
|/servers/databases/importExportOperationResults/read|Obtiene las operaciones de importación y exportación en curso.|
|/servers/databases/metricDefinitions/read|Devuelve los tipos de métricas que están disponibles para grupos de bases de datos|
|/servers/databases/metrics/read|Devuelve las métricas de las bases de datos.|
|/servers/databases/move/action|Cambia el nombre de la instancia de Azure SQL Database.|
|/servers/databases/operationResults/read|Obtiene el estado de una operación de base de datos.|
|/servers/databases/operations/cancel/action|Cancela la instancia de Azure SQL Database que tiene pendiente una operación asincrónica que aún no ha finalizado.|
|/servers/databases/operations/read|Devuelve la lista de las operaciones realizadas en la base de datos.|
|/servers/databases/pause/action|Pausa la base de datos de Azure SQL Data Warehouse.|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/servers/databases/providers/Microsoft.Insights/logDefinitions/read|Obtiene los registros disponibles para las bases de datos|
|/servers/databases/providers/Microsoft.Insights/metricDefinitions/read|Devuelve los tipos de métricas que están disponibles para grupos de bases de datos|
|/servers/databases/queryStore/queryTexts/read|Devuelve la colección de los textos de consulta que corresponden a los parámetros especificados.|
|/servers/databases/queryStore/read|Devuelve los valores actuales de configuración del almacén de consultas de la base de datos.|
|/servers/databases/queryStore/write|Actualiza la configuración del almacén de consultas de la base de datos|
|/servers/databases/read|Devuelve la lista de bases de datos u obtiene las propiedades de una base de datos específica.|
|/servers/databases/replicationLinks/delete|Finaliza la relación de replicación a la fuerza con una posible pérdida de datos|
|/servers/databases/replicationLinks/failover/action|Realiza una conmutación por error después de sincronizar todos los cambios desde el servidor principal, haciendo que esta base de datos sea la principal de la relación de replicación y haciendo que el control remoto principal se convierta en secundario.|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Realiza una conmutación por error de forma inmediata con posible pérdida de datos, haciendo que esta base de datos sea la principal de la relación de replicación y haciendo que el control remoto principal se convierta en secundario.|
|/servers/databases/replicationLinks/read|Devuelve detalles acerca de los vínculos de replicación establecidos para una determinada base de datos|
|/servers/databases/replicationLinks/unlink/action|Finaliza la relación de replicación a la fuerza o después de sincronizar con el asociado|
|/servers/databases/replicationLinks/updateReplicationMode/action|Actualiza el modo de replicación para vincular al modo sincrónico o asincrónico|
|/servers/databases/restorePoints/action|Crea un nuevo punto de restauración|
|/servers/databases/restorePoints/read|Devuelve los puntos de restauración de la base de datos.|
|/servers/databases/resume/action|Reanuda la base de datos de Azure SQL Data Warehouse.|
|/servers/databases/schemas/read|Recupera la lista de esquemas de una base de datos.|
|/servers/databases/schemas/tables/columns/read|Recuperar la lista de columnas de una tabla|
|/servers/databases/schemas/tables/columns/sensitivityLabels/delete|Elimina la etiqueta de confidencialidad de una columna determinada.|
|/servers/databases/schemas/tables/columns/sensitivityLabels/read|Obtiene la etiqueta de confidencialidad de una columna determinada.|
|/servers/databases/schemas/tables/columns/sensitivityLabels/write|Crea o actualiza la etiqueta de confidencialidad de una columna determinada.|
|/servers/databases/schemas/tables/read|Recupera la lista de tablas de una base de datos.|
|/servers/databases/schemas/tables/recommendedIndexes/read|Recupera la lista de recomendaciones de índice de una base de datos|
|/servers/databases/schemas/tables/recommendedIndexes/write|Aplica la recomendación de índice|
|/servers/databases/securityAlertPolicies/read|Recupera detalles de la directiva de detección de amenazas configurada en una base de datos determinada|
|/servers/databases/securityAlertPolicies/write|Cambia la directiva de detección de amenazas para una base de datos determinada|
|/servers/databases/securityMetrics/read|Obtiene una colección de métricas de seguridad de una base de datos.|
|/servers/databases/sensitivityLabels/read|Enumera las etiquetas de confidencialidad de una base de datos determinada.|
|/servers/databases/serviceTierAdvisors/read|Devuelve sugerencias acerca de cómo escalar o reducir verticalmente la base de datos en función de las estadísticas de ejecución de consultas para mejorar el rendimiento o reducir los costos|
|/servers/databases/syncGroups/cancelSync/action|Cancela la sincronización del grupo de sincronización.|
|/servers/databases/syncGroups/delete|Elimina un grupo de sincronización existente.|
|/servers/databases/syncGroups/hubSchemas/read|Devuelve la lista de esquemas de la base de datos central de sincronización.|
|/servers/databases/syncGroups/logs/read|Devuelve la lista de registros del grupo de sincronización.|
|/servers/databases/syncGroups/read|Devuelve la lista de grupos de sincronización u obtiene las propiedades de un grupo de sincronización específico.|
|/servers/databases/syncGroups/refreshHubSchema/action|Actualiza el esquema de la base de datos central de sincronización.|
|/servers/databases/syncGroups/refreshHubSchemaOperationResults/read|Recupera el resultado de la operación de actualización del esquema de la base de datos central de sincronización.|
|/servers/databases/syncGroups/syncMembers/delete|Elimina un miembro de sincronización existente.|
|/servers/databases/syncGroups/syncMembers/read|Devuelve la lista de miembros de sincronización u obtiene las propiedades de un miembro de sincronización específico.|
|/servers/databases/syncGroups/syncMembers/refreshSchema/action|Actualiza el esquema de un miembro de sincronización.|
|/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read|Recupera el resultado de la operación de actualización del esquema de un miembro sincronización.|
|/servers/databases/syncGroups/syncMembers/schemas/read|Devuelve la lista de esquemas de la base de datos del miembro de sincronización.|
|/servers/databases/syncGroups/syncMembers/write|Crea un miembro de sincronización con los parámetros especificados o actualiza las propiedades del miembro de sincronización especificado.|
|/servers/databases/syncGroups/triggerSync/action|Desencadena la sincronización del grupo de sincronización.|
|/servers/databases/syncGroups/write|Crea un grupo de sincronización con los parámetros especificados o actualiza las propiedades de un grupo de sincronización especificado.|
|/servers/databases/topQueries/queryText/action|Devuelve el texto de Transact-SQL para el identificador de la consulta seleccionado|
|/servers/databases/topQueries/read|Devuelve estadísticas agregadas de tiempo de ejecución para la consulta seleccionada en el período de tiempo seleccionado|
|/servers/databases/topQueries/statistics/read|Devuelve estadísticas agregadas de tiempo de ejecución para la consulta seleccionada en el período de tiempo seleccionado|
|/servers/databases/transparentDataEncryption/operationResults/read|Obtiene las operaciones en curso para el cifrado de datos transparente.|
|/servers/databases/transparentDataEncryption/read|Recupera el estado y los detalles de la característica de seguridad de cifrado de datos transparente para una base de datos determinada|
|/servers/databases/transparentDataEncryption/write|Cambia el estado del cifrado de datos transparente.|
|/servers/databases/upgradeDataWarehouse/action|Actualiza la base de datos de Azure SQL Data Warehouse.|
|/servers/databases/usages/read|Obtiene información sobre los usos de Azure SQL Database.|
|/servers/databases/vulnerabilityAssessments/delete|Quita la valoración de vulnerabilidades de una base de datos determinada.|
|/servers/databases/vulnerabilityAssessments/read|Recupera los detalles de la valoración de vulnerabilidades configurada en una base de datos determinada.|
|/servers/databases/vulnerabilityAssessments/rules/baselines/delete|Quita la línea de base de la regla de valoración de vulnerabilidades de una base de datos determinada.|
|/servers/databases/vulnerabilityAssessments/rules/baselines/read|Obtiene la línea de base de la regla de valoración de vulnerabilidades de una base de datos determinada.|
|/servers/databases/vulnerabilityAssessments/rules/baselines/write|Cambia la línea de base de la regla de valoración de vulnerabilidades de una base de datos determinada.|
|/servers/databases/vulnerabilityAssessments/scans/action|Ejecuta un análisis de la base datos de evaluación de vulnerabilidad.|
|/servers/databases/vulnerabilityAssessments/scans/export/action|Convierte un resultado del análisis existente en un formato legible. Si ya existe no ocurre nada.|
|/servers/databases/vulnerabilityAssessments/scans/read|Devuelve la lista de los registros del análisis de evaluación de vulnerabilidades u obtiene el registro del análisis para el id. del análisis específico.|
|/servers/databases/vulnerabilityAssessments/write|Cambia la valoración de vulnerabilidades de una base de datos determinada.|
|/servers/databases/vulnerabilityAssessmentScans/action|Ejecuta un análisis de la base datos de evaluación de vulnerabilidad.|
|/servers/databases/vulnerabilityAssessmentScans/operationResults/read|Recupera el resultado de la operación Execute correspondiente al análisis de valoración de vulnerabilidades de la base de datos.|
|/servers/databases/vulnerabilityAssessmentSettings/read|Recupera los detalles de la valoración de vulnerabilidades configurada en una base de datos determinada.|
|/servers/databases/vulnerabilityAssessmentSettings/write|Cambia la valoración de vulnerabilidades de una base de datos determinada.|
|/servers/databases/write|Crea una base de datos con los parámetros especificados o actualiza las propiedades o etiquetas de la base de datos especificada.|
|/servers/delete|Elimina un servidor existente.|
|/servers/disasterRecoveryConfiguration/delete|Elimina las configuraciones de recuperación ante desastres existentes de un servidor determinado.|
|/servers/disasterRecoveryConfiguration/failover/action|Realiza una conmutación por error de DisasterRecoveryConfiguration.|
|/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action|Fuerza una conmutación por error de DisasterRecoveryConfiguration.|
|/servers/disasterRecoveryConfiguration/read|Obtiene una colección de configuraciones de recuperación ante desastres que incluyan este servidor.|
|/servers/disasterRecoveryConfiguration/write|Cambia la configuración de recuperación ante desastres del servidor.|
|/servers/elasticPoolEstimates/read|Devuelve una lista de las estimaciones de grupos elásticos ya creados para este servidor|
|/servers/elasticPoolEstimates/write|Crea una nueva estimación de grupos elásticos para la lista de bases de datos proporcionada|
|/servers/elasticPools/advisors/read|Devuelve una lista de asesores disponibles para el grupo elástico|
|/servers/elasticPools/advisors/recommendedActions/read|Devuelve una lista de acciones recomendadas del asesor especificado para el grupo elástico|
|/servers/elasticPools/advisors/recommendedActions/write|Aplica la acción recomendada en el grupo elástico|
|/servers/elasticPools/advisors/write|Actualiza el estado de ejecución automática de un asesor en el nivel del grupo elástico.|
|/servers/elasticPools/databases/read|Obtiene una lista de bases de datos de un grupo elástico.|
|/servers/elasticPools/delete|Elimina un grupo elástico ya existente.|
|/servers/elasticPools/elasticPoolActivity/read|Recupera las actividades y detalles de un grupo de bases de datos elásticas determinado|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Recupera las actividades y detalles de una determinada base de datos que forma parte del grupo de bases de datos elásticas|
|/servers/elasticPools/metricDefinitions/read|Devuelve los tipos de métricas que están disponibles para grupos de bases de datos elásticas|
|/servers/elasticPools/metrics/read|Devuelve las métricas de los grupos de bases de datos elásticas.|
|/servers/elasticPools/operations/cancel/action|Cancela la el grupo elástico de Azure SQL que tiene pendiente una operación asincrónica que aún no ha finalizado.|
|/servers/elasticPools/operations/read|Devuelve la lista de las operaciones realizadas en el grupo elástico.|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read|Devuelve los tipos de métricas que están disponibles para grupos de bases de datos elásticas|
|/servers/elasticPools/read|Recupera los detalles de un grupo elástico de un servidor determinado.|
|/servers/elasticPools/skus/read|Obtiene una colección de SKU disponibles para este grupo elástico.|
|/servers/elasticPools/write|Crea o cambia las propiedades de un grupo elástico.|
|/servers/encryptionProtector/read|Devuelve una lista de protectores de cifrado de servidor u obtiene las propiedades de un protector de cifrado de servidor específico.|
|/servers/encryptionProtector/write|Actualiza las propiedades de un protector de cifrado de servidor específico.|
|/servers/extendedAuditingSettings/read|Recupera los detalles de la directiva de auditoría de blobs del servidor extendido que está configurada en un servidor determinado.|
|/servers/extendedAuditingSettings/write|Cambia la auditoría de blobs del servidor extendido para un servidor determinado.|
|/servers/failoverGroups/delete|Elimina un grupo de conmutación por error existente.|
|/servers/failoverGroups/failover/action|Ejecuta la conmutación por error planeada en un grupo de conmutación por error existente.|
|/servers/failoverGroups/forceFailoverAllowDataLoss/action|Ejecuta la conmutación por error forzada en un grupo de conmutación por error existente.|
|/servers/failoverGroups/read|Devuelve la lista de grupos de conmutación por error u obtiene las propiedades de un grupo de conmutación por error específico.|
|/servers/failoverGroups/write|Crea un grupo de conmutación por error con los parámetros especificados o actualiza las propiedades o etiquetas del grupo de conmutación por error especificado.|
|/servers/firewallRules/delete|Elimina una regla de firewall de servidor existente.|
|/servers/firewallRules/read|Devuelve la lista de reglas de firewall de servidor u obtiene las propiedades de la regla de firewall de servidor especificada.|
|/servers/firewallRules/write|Crea una regla de firewall de servidor con los parámetros especificados, actualiza las propiedades de la regla especificada o sobrescribe todas las reglas existentes con nuevas reglas de firewall de servidor.|
|/servers/import/action|Crea una base de datos nueva en el servidor e implementa el esquema y los datos de un paquete DacPac|
|/servers/importExportOperationResults/read|Obtiene las operaciones de importación y exportación en curso.|
|/servers/keys/delete|Elimina una clave de servidor existente.|
|/servers/keys/read|Devuelve la lista de claves de servidor u obtiene las propiedades de una clave de servidor específica.|
|/servers/keys/write|Crea una clave con los parámetros especificados o actualiza las propiedades o etiquetas de la clave de servidor especificada.|
|/servers/operationResults/read|Obtiene las operaciones de servidor en curso.|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Devuelve los tipos de métricas que están disponibles en los servidores.|
|/servers/read|Devuelve la lista de servidores u obtiene las propiedades de un servidor específico.|
|/servers/recommendedElasticPools/databases/read|Recupera las métricas para los grupos de bases de datos elásticas recomendados para un servidor determinado|
|/servers/recommendedElasticPools/read|Recupera la recomendación para los grupos de bases de datos elásticas para reducir los costos o mejorar el rendimiento en función del historial de uso de los recursos|
|/servers/recoverableDatabases/read|Esta operación se usa para la recuperación ante desastres de una base de datos activa para restaurar la base de datos al último punto bueno de copia de seguridad conocido. Devuelve información acerca de la última copia de seguridad correcta, pero no restaura realmente la base de datos.|
|/servers/restorableDroppedDatabases/read|Obtiene una lista de bases de datos que se han quitado de un servidor determinado y que siguen estando dentro de la directiva de retención.|
|/servers/securityAlertPolicies/operationResults/read|Recupera los resultados de la operación de escritura de directivas de detección de amenazas del servidor.|
|/servers/securityAlertPolicies/read|Recupera detalles de la directiva de detección de amenazas del servidor configurada en un servidor determinado|
|/servers/securityAlertPolicies/write|Cambia la directiva de detección de amenazas del servidor para un servidor determinado.|
|/servers/serviceObjectives/read|Recupera la lista de objetivos de nivel de servicio (también conocida como niveles de rendimiento) disponible en un servidor determinado|
|/servers/syncAgents/delete|Elimina un agente de sincronización existente.|
|/servers/syncAgents/generateKey/action|Genera la clave de registro del agente de sincronización.|
|/servers/syncAgents/linkedDatabases/read|Devuelve la lista de bases de datos de sincronización del agente vinculado.|
|/servers/syncAgents/read|Devuelve la lista de agentes de sincronización u obtiene las propiedades de un agente de sincronización específico.|
|/servers/syncAgents/write|Crea un agente de sincronización con los parámetros especificados o actualiza las propiedades de un agente de sincronización específico.|
|/servers/usages/read|Devuelve la cuota de DTU del servidor y el consumo actual de DTU de todas las bases de datos del servidor|
|/servers/virtualNetworkRules/delete|Elimina una regla de Virtual Network existente.|
|/servers/virtualNetworkRules/read|Devuelve la lista de reglas de red virtual u obtiene las propiedades de una regla de red virtual específica.|
|/servers/virtualNetworkRules/write|Crea una regla de red virtual con los parámetros especificados o actualiza las propiedades o etiquetas de la regla de red virtual especificada.|
|/servers/write|Crea un servidor con los parámetros especificados o actualiza las propiedades o etiquetas del servidor especificado.|
|/unregister/action|Quita el registro a la suscripción del proveedor de recursos de Microsoft SQL Database y habilita la creación de bases de datos de Microsoft SQL Database.|
|/virtualClusters/read|Devuelve la lista de clúster virtuales u obtiene las propiedades de un clúster virtual específico.|
|/virtualClusters/write|Actualiza las etiquetas de los clúster virtuales.|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Operación | DESCRIPCIÓN |
|---|---|
|/checknameavailability/read|Comprueba que el nombre de la cuenta es válido y que no está en uso.|
|/locations/deleteVirtualNetworkOrSubnets/action|Notifica a Microsoft.Storage que se está eliminando una red virtual o subred|
|/operations/read|Sondea el estado de una operación asincrónica.|
|/register/action|Registra la suscripción para el proveedor de recursos de almacenamiento y habilita la creación de cuentas de almacenamiento.|
|/skus/read|Enumera las SKU compatibles con Microsoft.Storage.|
|/storageAccounts/blobServices/containers/clearLegalHold/action|Borra la suspensión legal del contenedor de blobs.|
|/storageAccounts/blobServices/containers/delete|Devuelve el resultado de la eliminación de un contenedor.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/delete|Elimina la directiva de inmutabilidad del contenedor de blobs.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action|Extiende la directiva de inmutabilidad del contenedor de blobs.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action|Bloquea la directiva de inmutabilidad del contenedor de blobs.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/read|Obtiene la directiva de inmutabilidad del contenedor de blobs.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/write|Establece la directiva de inmutabilidad del contenedor de blobs.|
|/storageAccounts/blobServices/containers/read|Devuelve un contenedor o una lista de contenedores.|
|/storageAccounts/blobServices/containers/setLegalHold/action|Establece la suspensión legal del contenedor de blobs.|
|/storageAccounts/blobServices/containers/write|Devuelve el resultado de la colocación o concesión del contenedor de blobs.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read|Obtiene la lista de definiciones de métricas de almacenamiento de Microsoft.|
|/storageAccounts/blobServices/read|Devuelve las propiedades o las estadísticas de Blob service.|
|/storageAccounts/blobServices/write|Devuelve el resultado de las propiedades de colocación de Blob service.|
|/storageAccounts/delete|Agrega una cuenta de almacenamiento existente.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read|Obtiene la lista de definiciones de métricas de almacenamiento de Microsoft.|
|/storageAccounts/listAccountSas/action|Devuelve el token de SAS de la cuenta de almacenamiento especificada.|
|/storageAccounts/listkeys/action|Devuelve las claves de acceso de la cuenta de almacenamiento especificada.|
|/storageAccounts/listServiceSas/action|Devuelve el token de SAS del servicio para la cuenta de almacenamiento especificada.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso.|
|/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read|Obtiene la lista de definiciones de métricas de almacenamiento de Microsoft.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read|Obtiene la lista de definiciones de métricas de almacenamiento de Microsoft.|
|/storageAccounts/queueServices/queues/delete|Devuelve el resultado de la eliminación de una cola.|
|/storageAccounts/queueServices/queues/read|Devuelve una cola o una lista de colas.|
|/storageAccounts/queueServices/queues/write|Devuelve el resultado de escribir una cola.|
|/storageAccounts/queueServices/read|Devuelve las propiedades o las estadísticas de Queue service.|
|/storageAccounts/queueServices/write|Devuelve el resultado de establecer las propiedades de Queue service.|
|/storageAccounts/read|Devuelve la lista de cuentas de almacenamiento u obtiene las propiedades de la cuenta de almacenamiento especificada.|
|/storageAccounts/regeneratekey/action|Regenera las claves de acceso de la cuenta de almacenamiento especificada.|
|/storageAccounts/services/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de la cuenta de almacenamiento.|
|/storageAccounts/storageAccounts/queueServices/providers/ Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read|Obtiene la lista de definiciones de métricas de almacenamiento de Microsoft.|
|/storageAccounts/write|Crea una cuenta de almacenamiento con los parámetros especificados o actualiza las propiedades o etiquetas, o agrega un dominio personalizado para la cuenta de almacenamiento especificada.|
|/usages/read|Devuelve el límite y el recuento actual de utilización de recursos en la suscripción especificada|

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

| Operación | DESCRIPCIÓN |
|---|---|
|/storageSyncServices/delete|Elimina cualquier servicio de sincronización del almacenamiento.|
|/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles para los servicios de sincronización de almacenamiento.|
|/storageSyncServices/read|Lee cualquier servicio de sincronización de almacenamiento.|
|/storageSyncServices/registeredServers/delete|Elimina cualquier servidor registrado.|
|/storageSyncServices/registeredServers/read|Lee cualquier servidor registrado.|
|/storageSyncServices/registeredServers/write|Crea o actualiza cualquier servidor registrado.|
|/storageSyncServices/syncGroups/cloudEndpoints/delete|Eliminar cualquier punto de conexión de nube.|
|/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read|API de ubicación para las llamadas de copia de seguridad asincrónicas.|
|/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action|Llama a esta acción después de realizar la copia de seguridad.|
|/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action|Llama a esta acción después de realizar el proceso de restauración.|
|/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action|Llama a esta acción antes de realizar la copia de seguridad.|
|/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action|Llama a esta acción antes de realizar el proceso de restauración.|
|/storageSyncServices/syncGroups/cloudEndpoints/read|Lee cualquier punto de conexión de nube.|
|/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action|Restaura el latido.|
|/storageSyncServices/syncGroups/cloudEndpoints/write|Crea o actualiza cualquier punto de conexión de nube.|
|/storageSyncServices/syncGroups/delete|Elimina cualquier grupo de sincronización.|
|/storageSyncServices/syncGroups/read|Lee cualquier grupo de sincronización.|
|/storageSyncServices/syncGroups/serverEndpoints/delete|Elimina cualquier punto de conexión del servidor.|
|/storageSyncServices/syncGroups/serverEndpoints/read|Lee cualquier punto de conexión del servidor.|
|/storageSyncServices/syncGroups/serverEndpoints/recallAction/action|Llama a esta acción para recuperar los archivos de un servidor.|
|/storageSyncServices/syncGroups/serverEndpoints/write|Crea o actualiza cualquier punto de conexión del servidor.|
|/storageSyncServices/syncGroups/write|Crea o actualiza cualquier grupo de sincronización.|
|/storageSyncServices/write|Crea o actualiza cualquier servicio de sincronización del almacenamiento.|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Operación | DESCRIPCIÓN |
|---|---|
|/managers/accessControlRecords/delete|Elimina los registros de control de acceso|
|/managers/accessControlRecords/read|Muestra u obtiene el registro de control de acceso|
|/managers/accessControlRecords/write|Crea o actualiza el registro de control de acceso|
|/managers/alerts/read|Muestra u obtiene las alertas|
|/managers/bandwidthSettings/delete|Elimina una configuración de ancho de banda (solo serie 8000) que ya existe|
|/managers/bandwidthSettings/read|Muestra la configuración de ancho de banda (solo serie 8000)|
|/managers/bandwidthSettings/write|Crea una nueva configuración de ancho de banda (solo serie 8000) o actualiza la que ya existe|
|/Managers/certificates/write|La operación Actualizar certificado de recursos permite actualizar el certificado de credencial de recursos o almacenes.|
|/managers/clearAlerts/action|Borra todas las alertas asociadas con el administrador de dispositivos.|
|/managers/cloudApplianceConfigurations/read|Muestra las configuraciones admitidas de la aplicación en la nube|
|/managers/configureDevice/action|Configura un dispositivo|
|/managers/delete|Elimina los administradores de dispositivos|
|/Managers/delete|La operación Delete Vault elimina el recurso de Azure del tipo "almacén" especificado|
|/managers/devices/alertSettings/read|Muestra u obtiene la configuración de las alertas|
|/managers/devices/alertSettings/write|Crea o actualiza la configuración de las alertas|
|/managers/devices/backupPolicies/backup/action|Realiza una copia de seguridad manual para crear una copia de seguridad a petición de todos los volúmenes protegidos por la directiva.|
|/managers/devices/backupPolicies/delete|Elimina una directiva de copia de seguridad existente (solo serie 8000)|
|/managers/devices/backupPolicies/read|Enumera las directivas de copia de seguridad (solo serie 8000)|
|/managers/devices/backupPolicies/schedules/delete|Elimina una programación existente|
|/managers/devices/backupPolicies/schedules/read|Enumera las programaciones|
|/managers/devices/backupPolicies/schedules/write|Crea una nueva programación o actualiza una que ya existe|
|/managers/devices/backupPolicies/write|Crea una nueva directiva de copia de seguridad (solo serie 8000) o actualiza una que ya existe|
|/managers/devices/backups/delete|Elimina el conjunto de copia de seguridad|
|/managers/devices/backups/elements/clone/action|Clona un recurso o volumen compartido mediante un elemento de copia de seguridad.|
|/managers/devices/backups/read|Muestra u obtiene el conjunto de copia de seguridad|
|/managers/devices/backups/restore/action|Restaurar todos los volúmenes a partir de un conjunto de copias de seguridad.|
|/managers/devices/backupScheduleGroups/delete|Elimina los grupos de programación de copias de seguridad|
|/managers/devices/backupScheduleGroups/read|Muestra u obtiene los grupos de programación de copias de seguridad|
|/managers/devices/backupScheduleGroups/write|Crea o actualiza los grupos de programación de copias de seguridad|
|/managers/devices/chapSettings/delete|Elimina la configuración de CHAP|
|/managers/devices/chapSettings/read|Muestra u obtiene la configuración de CHAP|
|/managers/devices/chapSettings/write|Crea o actualiza la configuración de CHAP|
|/managers/devices/deactivate/action|Desactiva un dispositivo.|
|/managers/devices/delete|Elimina los dispositivos|
|/managers/devices/download/action|Descarga actualizaciones para un dispositivo.|
|/managers/devices/failover/action|Conmutación por error del dispositivo.|
|/managers/devices/fileservers/backup/action|Realiza una copia de seguridad de un servidor de archivos.|
|/managers/devices/fileservers/delete|Elimina los servidores de archivos|
|/managers/devices/fileservers/metrics/read|Muestra u obtiene las métricas|
|/managers/devices/fileservers/metricsDefinitions/read|Muestra u obtiene las definiciones de métricas|
|/managers/devices/fileservers/read|Muestra u obtiene los servidores de archivos|
|/managers/devices/fileservers/shares/delete|Elimina los recursos compartidos|
|/managers/devices/fileservers/shares/metrics/read|Muestra u obtiene las métricas|
|/managers/devices/fileservers/shares/metricsDefinitions/read|Muestra u obtiene las definiciones de métricas|
|/managers/devices/fileservers/shares/read|Muestra u obtiene los recursos compartidos|
|/managers/devices/fileservers/shares/write|Crea o actualiza los recursos compartidos|
|/managers/devices/fileservers/write|Crea o actualiza los servidores de archivos|
|/managers/devices/hardwareComponentGroups/changeControllerPowerState/action|Cambia el estado de energía del controlador de los grupos de componentes de hardware|
|/managers/devices/hardwareComponentGroups/read|Muestra los grupos de componentes de hardware|
|/managers/devices/install/action|Instala actualizaciones en un dispositivo.|
|/managers/devices/installUpdates/action|Instala actualizaciones en los dispositivos|
|/managers/devices/iscsiservers/backup/action|Realiza una copia de seguridad de un servidor iSCSI.|
|/managers/devices/iscsiservers/delete|Elimina los servidores de iSCSI|
|/managers/devices/iscsiservers/disks/delete|Elimina los discos|
|/managers/devices/iscsiservers/disks/metrics/read|Muestra u obtiene las métricas|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|Muestra u obtiene las definiciones de métricas|
|/managers/devices/iscsiservers/disks/read|Muestra u obtiene los discos|
|/managers/devices/iscsiservers/disks/write|Crea o actualiza los discos|
|/managers/devices/iscsiservers/metrics/read|Muestra u obtiene las métricas|
|/managers/devices/iscsiservers/metricsDefinitions/read|Muestra u obtiene las definiciones de métricas|
|/managers/devices/iscsiservers/read|Muestra u obtiene los servidores de iSCSI|
|/managers/devices/iscsiservers/write|Crea o actualiza los servidores de iSCSI|
|/managers/devices/jobs/cancel/action|Cancela un trabajo de ejecución|
|/managers/devices/jobs/read|Muestra u obtiene los trabajos|
|/managers/devices/listFailoverSets/action|Obtiene la lista de conjuntos de conmutación por error de un dispositivo existente.|
|/managers/devices/listFailoverTargets/action|Muestra los destinos de conmutación por error de los dispositivos|
|/managers/devices/metrics/read|Muestra u obtiene las métricas|
|/managers/devices/metricsDefinitions/read|Muestra u obtiene las definiciones de métricas|
|/managers/devices/migrationSourceConfigurations/confirmMigration/action|Confirma una migración correcta y la confirma.|
|/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action|Recupera el estado de confirmación de la migración.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action|Recupera el estado del trabajo de estimación de la migración.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action|Recupera el estado de la migración.|
|/managers/devices/migrationSourceConfigurations/import/action|Importa las configuraciones de origen para la migración|
|/managers/devices/migrationSourceConfigurations/startMigration/action|Inicia la migración mediante configuraciones de origen|
|/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action|Inicia un trabajo para calcular la duración del proceso de migración.|
|/managers/devices/networkSettings/read|Muestra u obtiene la configuración de red|
|/managers/devices/networkSettings/write|Crea una nueva configuración de red o actualiza una que ya existe|
|/managers/devices/publicEncryptionKey/action|Muestra la clave de cifrado pública del administrador de dispositivos|
|/managers/devices/publishSupportPackage/action|Publica el paquete de soporte de un dispositivo para la solución de problemas del Soporte técnico de Microsoft.|
|/managers/devices/read|Muestra u obtiene los dispositivos|
|/managers/devices/scanForUpdates/action|Busca actualizaciones de un dispositivo.|
|/managers/devices/securitySettings/read|Muestra la configuración de seguridad|
|/managers/devices/securitySettings/syncRemoteManagementCertificate/action|Sincroniza el certificado de administración remota de un dispositivo.|
|/managers/devices/securitySettings/update/action|Actualiza la configuración de seguridad.|
|/managers/devices/securitySettings/write|Crea una nueva configuración de seguridad o actualiza una que ya existe|
|/managers/devices/sendTestAlertEmail/action|Envía un correo electrónico de alerta de prueba a destinatarios configurados de correo electrónico.|
|/managers/devices/timeSettings/read|Muestra u obtiene la configuración de hora|
|/managers/devices/timeSettings/write|Crea una nueva configuración de hora o actualiza una que ya existe|
|/managers/devices/updateSummary/read|Muestra u obtiene el resumen de actualización|
|/managers/devices/volumeContainers/delete|Elimina los contenedores de volumen existentes (solo para la serie 8000)|
|/managers/devices/volumeContainers/listEncryptionKeys/action|Enumera las claves de cifrado de los contenedores de volumen|
|/managers/devices/volumeContainers/metrics/read|Enumera las métricas|
|/managers/devices/volumeContainers/metricsDefinitions/read|Enumera las definiciones de métricas|
|/managers/devices/volumeContainers/read|Enumera los contenedores de volumen (solo para la serie 8000)|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|Sustituye las claves de cifrado de los contenedores de volumen|
|/managers/devices/volumeContainers/volumes/delete|Elimina un volumen existente|
|/managers/devices/volumeContainers/volumes/metrics/read|Enumera las métricas|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|Enumera las definiciones de métricas|
|/managers/devices/volumeContainers/volumes/read|Enumera los volúmenes|
|/managers/devices/volumeContainers/volumes/write|Crea un nuevo volumen o actualiza uno que ya existe|
|/managers/devices/volumeContainers/write|Crea un nuevo contenedor de volumen (solo para la serie 8000) o actualiza uno que ya existe|
|/managers/devices/write|Crea o actualiza los dispositivos|
|/managers/encryptionSettings/read|Muestra u obtiene la configuración de cifrado|
|/Managers/extendedInformation/delete|La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén?|
|/Managers/extendedInformation/read|La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén?|
|/Managers/extendedInformation/write|La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén?|
|/managers/getActivationKey/action|Obtiene la clave de activación del administrador de dispositivos.|
|/managers/getEncryptionKey/action|Obtiene la clave de cifrado del administrador de dispositivos.|
|/managers/listActivationKey/action|Obtiene la clave de activación del administrador de dispositivos de StorSimple.|
|/managers/listPrivateEncryptionKey/action|Obtiene una clave de cifrado privada de un administrador de dispositivos de StorSimple.|
|/managers/listPublicEncryptionKey/action|Enumera las claves de cifrado públicas de un administrador de dispositivos de StorSimple.|
|/managers/metrics/read|Muestra u obtiene las métricas|
|/managers/metricsDefinitions/read|Muestra u obtiene las definiciones de métricas|
|/managers/provisionCloudAppliance/action|Crea una nueva aplicación en la nube.|
|/managers/read|Enumera u obtiene los administradores de dispositivos|
|/Managers/read|La operación Get Vault obtiene un objeto que representa el recurso de Azure del tipo "almacén"|
|/managers/regenarateRegistationCertificate/action|Regenera el certificado de registro de los administradores de dispositivos.|
|/managers/regenerateActivationKey/action|Regenera la clave de activación del administrador de dispositivos.|
|/managers/storageAccountCredentials/delete|Elimina las credenciales de la cuenta de almacenamiento|
|/managers/storageAccountCredentials/listAccessKey/action|Muestra las claves de acceso de las credenciales de la cuenta de almacenamiento|
|/managers/storageAccountCredentials/read|Muestra u obtiene las credenciales de la cuenta de almacenamiento|
|/managers/storageAccountCredentials/write|Crea o actualiza las credenciales de la cuenta de almacenamiento|
|/managers/storageDomains/delete|Elimina los dominios de almacenamiento|
|/managers/storageDomains/read|Muestra u obtiene los dominios de almacenamiento|
|/managers/storageDomains/write|Crea o actualiza los dominios de almacenamiento|
|/managers/write|Crea o actualiza los administradores de dispositivos|
|/Managers/write|La operación Create Vault crea un recurso de Azure del tipo "almacén"|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Operación | DESCRIPCIÓN |
|---|---|
|/locations/quotas/Read|Lee la cuota de suscripción de Stream Analytics.|
|/operations/Read|Lee las operaciones de Stream Analytics.|
|/Register/action|Registra la suscripción con el proveedor de recursos de Stream Analytics.|
|/streamingjobs/Delete|Elimina el trabajo de Stream Analytics|
|/streamingjobs/functions/Delete|Elimina la función de trabajo de Stream Analytics.|
|/streamingjobs/functions/operationresults/Read|Lee los resultados de la operación de la función de trabajo de Stream Analytics.|
|/streamingjobs/functions/Read|Lee la función de trabajo de Stream Analytics.|
|/streamingjobs/functions/RetrieveDefaultDefinition/action|Recupera la definición predeterminada de una función de trabajo de Stream Analytics.|
|/streamingjobs/functions/Test/action|Prueba la función de trabajo de Stream Analytics.|
|/streamingjobs/functions/Write|Escribe la función de trabajo de Stream Analytics.|
|/streamingjobs/inputs/Delete|Elimina la entrada del trabajo de Stream Analytics|
|/streamingjobs/inputs/operationresults/Read|Lee los resultados de la operación de la entrada del trabajo de Stream Analytics.|
|/streamingjobs/inputs/Read|Lee la entrada del trabajo de Stream Analytics|
|/streamingjobs/inputs/Sample/action|Muestra un ejemplo de la entrada del trabajo de Stream Analytics.|
|/streamingjobs/inputs/Test/action|Prueba la entrada del trabajo de Stream Analytics.|
|/streamingjobs/inputs/Write|Escribe la entrada del trabajo de Stream Analytics|
|/streamingjobs/metricdefinitions/Read|Lee las definiciones de métricas.|
|/streamingjobs/operationresults/Read|Lee los resultados de la operación del trabajo de Stream Analytics.|
|/streamingjobs/outputs/Delete|Elimina la salida del trabajo de Stream Analytics|
|/streamingjobs/outputs/operationresults/Read|Lee los resultados de la operación de la salida del trabajo de Stream Analytics.|
|/streamingjobs/outputs/Read|Lee la salida del trabajo de Stream Analytics|
|/streamingjobs/outputs/Test/action|Prueba la salida del trabajo de Stream Analytics.|
|/streamingjobs/outputs/Write|Escribe la salida del trabajo de Stream Analytics|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Lee la configuración de diagnóstico.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Escribe la configuración de diagnóstico.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|Obtiene los registros disponibles de los trabajos del flujo|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de los trabajos del flujo|
|/streamingjobs/Read|Lee el trabajo de Stream Analytics|
|/streamingjobs/Start/action|Inicia el trabajo de Stream Analytics|
|/streamingjobs/Stop/action|Detiene el trabajo de Stream Analytics|
|/streamingjobs/transformations/Delete|Elimina la transformación de trabajos de Stream Analytics|
|/streamingjobs/transformations/Read|Lee la transformación de trabajos de Stream Analytics|
|/streamingjobs/transformations/Write|Escribe la transformación de trabajos de Stream Analytics|
|/streamingjobs/Write|Escribe el trabajo de Stream Analytics|

## <a name="microsoftsubscription"></a>Microsoft.Subscription

| Operación | DESCRIPCIÓN |
|---|---|
|/SubscriptionDefinitions/read|Obtiene una definición de la suscripción de Azure dentro de un grupo de administración.|
|/SubscriptionDefinitions/write|Crea una definición de la suscripción de Azure.|

## <a name="microsoftsupport"></a>Microsoft.Support

| Operación | DESCRIPCIÓN |
|---|---|
|/register/action|Registra para admitir el proveedor de recursos|
|/supportTickets/read|Obtiene los detalles de las incidencias de soporte técnico (incluido el estado, gravedad, detalles de contacto y comunicaciones) u obtiene la lista de incidencias de soporte técnico de las diversas suscripciones.|
|/supportTickets/write|Crea o actualiza una incidencia de soporte técnico. Puede crear una incidencia de soporte técnico para problemas relacionados con cuestiones técnicas, de facturación, cuotas o de administración de suscripciones. Puede actualizar la gravedad, los detalles de contacto y las comunicaciones de las incidencias de soporte técnico existentes.|

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

| Operación | DESCRIPCIÓN |
|---|---|
|/environments/accesspolicies/delete|Elimina la directiva de acceso.|
|/environments/accesspolicies/read|Obtiene las propiedades de una directiva de acceso.|
|/environments/accesspolicies/write|Crea una nueva directiva de acceso para un entorno o actualiza una directiva de acceso ya existente.|
|/environments/delete|Elimina el entorno.|
|/environments/eventsources/delete|Elimina el origen del evento.|
|/environments/eventsources/eventsources/providers/Microsoft.Insights/ diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de los orígenes de los eventos.|
|/environments/eventsources/read|Obtiene las propiedades de un origen del evento.|
|/environments/eventsources/write|Crea un nuevo origen del evento para un entorno o actualiza un origen del evento ya existente.|
|/environments/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/environments/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/environments/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles para los entornos.|
|/environments/read|Obtiene las propiedades de un entorno.|
|/environments/referencedatasets/delete|Elimina un conjunto de datos de referencia.|
|/environments/referencedatasets/read|Obtiene las propiedades de un conjunto de datos referencia.|
|/environments/referencedatasets/write|Crea un conjunto de datos de referencia para un entorno o actualiza un conjunto de datos de referencia ya existente.|
|/environments/status/read|Obtiene el estado del entorno, y el estado de sus operaciones asociadas como las de entrada.|
|/environments/write|Crea un entorno o actualiza uno ya existente.|
|/register/action|Registra la suscripción del proveedor de recursos de Time Series Insights y habilita la creación de entornos de Time Series Insights.|

## <a name="microsoftweb"></a>microsoft.web

| Operación | DESCRIPCIÓN |
|---|---|
|/apimanagementaccounts/apiacls/read|Obtiene las ACL de API de las cuentas de API Management.|
|/apimanagementaccounts/apis/apiacls/delete|Elimina las ACL de API de las cuentas de API Management.|
|/apimanagementaccounts/apis/apiacls/read|Obtiene las ACL de las API de las cuentas de API Management.|
|/apimanagementaccounts/apis/apiacls/write|Actualiza las ACL de las API de las cuentas de API Management.|
|/apimanagementaccounts/apis/connectionacls/read|Obtiene las ACL de Connection de las API para las cuentas de API Management.|
|/apimanagementaccounts/apis/connections/confirmconsentcode/action|Confirma las conexiones de API de las cuentas de API Management del código de consentimiento.|
|/apimanagementaccounts/apis/connections/connectionacls/delete|Elimina las ACL de Connection de las API para las cuentas de API Management.|
|/apimanagementaccounts/apis/connections/connectionacls/read|Obtiene las ACL de Connection de las conexiones de API para las cuentas de API Management.|
|/apimanagementaccounts/apis/connections/connectionacls/write|Actualiza las ACL de Connection de las conexiones de API para las cuentas de API Management.|
|/apimanagementaccounts/apis/connections/delete|Elimina conexiones de API para las cuentas de API Management.|
|/apimanagementaccounts/apis/connections/getconsentlinks/action|Obtiene los vínculos de consentimiento para conexiones de API de las cuentas de API Management.|
|/apimanagementaccounts/apis/connections/listconnectionkeys/action|Enumera las conexiones de API de las cuentas de API Management para las claves de conexión.|
|/apimanagementaccounts/apis/connections/listsecrets/action|Enumera las conexiones de API de las cuentas de API Management para los secretos.|
|/apimanagementaccounts/apis/connections/read|Obtiene las conexiones de API para las cuentas de API Management.|
|/apimanagementaccounts/apis/connections/write|Actualiza las conexiones de API para las cuentas de API Management.|
|/apimanagementaccounts/apis/delete|Elimina las API de las cuentas de API Management.|
|/apimanagementaccounts/apis/localizeddefinitions/delete|Elimina las definiciones localizadas de API para las cuentas de API Management.|
|/apimanagementaccounts/apis/localizeddefinitions/read|Obtiene las definiciones localizadas de API para las cuentas de API Management.|
|/apimanagementaccounts/apis/localizeddefinitions/write|Actualiza las definiciones localizadas de API para las cuentas de API Management.|
|/apimanagementaccounts/apis/read|Obtiene las API de las cuentas de API Management.|
|/apimanagementaccounts/apis/write|Actualiza las API de las cuentas de API Management.|
|/apimanagementaccounts/connectionacls/read|Obtiene las ACL de Connection de las cuentas de API Management.|
|/availablestacks/read|Obtiene las pilas disponibles.|
|/billingmeters/read|Obtiene la lista de medidores de facturación.|
|/certificates/Delete|Elimina un certificado existente.|
|/certificates/Read|Obtiene la lista de certificados.|
|/certificates/Write|Agrega un nuevo certificado o actualiza uno existente.|
|/checknameavailability/read|Comprueba si el nombre de recurso está disponible.|
|/classicmobileservices/read|Obtiene Mobile Services clásico.|
|/connectionGateways/Delete|Elimina una puerta de enlace de conexión.|
|/connectionGateways/Join/Action|Se une a una puerta de enlace de conexión.|
|/connectiongateways/liststatus/action|Enumera las puertas de enlace de la conexión de estado.|
|/connectionGateways/ListStatus/Action|Enumera los estados de una puertas de enlace de la conexión.|
|/connectionGateways/Move/Action|Mueve una puerta de enlace de conexión.|
|/connectionGateways/Read|Obtiene la lista de puertas de enlace de conexiones.|
|/connectionGateways/Write|Crea o actualiza una puerta de enlace de conexión.|
|/connections/confirmconsentcode/action|Confirma el código de consentimiento de conexiones.|
|/connections/Delete|Elimina una conexión.|
|/connections/Join/Action|Se une a una conexión.|
|/connections/listconsentlinks/action|Enumera los vínculos de consentimiento de las conexiones.|
|/connections/Move/Action|Mueve una conexión.|
|/connections/Read|Obtiene la lista de conexiones.|
|/connections/Write|Crea o actualiza una conexión.|
|/customApis/Delete|Elimina una API personalizada.|
|/customApis/extractApiDefinitionFromWsdl/Action|Extrae la definición de la API de un archivo WSDL.|
|/customApis/Join/Action|Combina una API personalizada.|
|/customApis/listWsdlInterfaces/Action|Enumera las interfaces WSDL de una API personalizada.|
|/customApis/Move/Action|Mueve una API personalizada.|
|/customApis/Read|Obtiene la lista de API personalizadas.|
|/customApis/Write|Crea o actualiza una API personalizada.|
|/deploymentlocations/read|Obtiene las ubicaciones de implementación.|
|/geoRegions/Read|Obtiene la lista de regiones geográficas.|
|/hostingenvironments/capacities/read|Obtiene las funcionalidades de los entornos de hospedaje.|
|/hostingEnvironments/Delete|Elimina un entorno de App Service|
|/hostingenvironments/diagnostics/read|Obtiene los diagnósticos de los entornos de hospedaje.|
|/hostingenvironments/inboundnetworkdependenciesendpoints/read|Obtiene los puntos de conexión de red de todas las dependencias de entrada.|
|/hostingenvironments/metricdefinitions/read|Obtiene las definiciones de métricas de los entornos de hospedaje.|
|/hostingenvironments/multirolepools/metricdefinitions/read|Obtiene las definiciones de métricas de los grupos MultiRole de los entornos de hospedaje.|
|/hostingenvironments/multirolepools/metrics/read|Obtiene las métricas de los grupos MultiRole de los entornos de hospedaje.|
|/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/ metricDefinitions/Read|Obtiene las métricas disponibles para el multirol de App Service Environment.|
|/hostingEnvironments/multiRolePools/Read|Obtiene las propiedades de un grupo de front-end en un entorno de App Service|
|/hostingenvironments/multirolepools/skus/read|Obtiene las SKU de los grupos MultiRole de los entornos de hospedaje.|
|/hostingenvironments/multirolepools/usages/read|Obtiene los usos de los grupos MultiRole de los entornos de hospedaje.|
|/hostingEnvironments/multiRolePools/Write|Crea un nuevo grupo de front-end en un entorno de App Service o actualiza uno que ya existe|
|/hostingenvironments/operations/read|Obtiene las operaciones de los entornos de hospedaje.|
|/hostingenvironments/outboundnetworkdependenciesendpoints/read|Obtiene los puntos de conexión de red de todas las dependencias de salida.|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/hostingEnvironments/Read|Obtiene las propiedades de un entorno de App Service|
|/hostingEnvironments/reboot/Action|Reinicia todas las máquinas de un entorno de App Service|
|/hostingenvironments/resume/action|Reanuda los entornos de hospedaje.|
|/hostingenvironments/serverfarms/read|Obtiene los planes de App Service de los entornos de hospedaje.|
|/hostingenvironments/sites/read|Obtiene las instancias de Web Apps de los entornos de hospedaje.|
|/hostingenvironments/suspend/action|Suspende los entornos de hospedaje.|
|/hostingenvironments/usages/read|Obtiene los usos de los entornos de hospedaje.|
|/hostingenvironments/workerpools/metricdefinitions/read|Obtiene las definiciones de métricas de los grupos de trabajo de los entornos de hospedaje.|
|/hostingenvironments/workerpools/metrics/read|Obtiene las métricas de los grupos de trabajo de los entornos de hospedaje.|
|/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read|Obtiene las métricas disponibles para el conjunto de trabajo de App Service Environment.|
|/hostingEnvironments/workerPools/Read|Obtiene las propiedades de un conjunto de trabajo en un entorno de App Service|
|/hostingenvironments/workerpools/skus/read|Obtiene las SKU de los grupos de trabajo de los entornos de hospedaje.|
|/hostingenvironments/workerpools/usages/read|Obtiene los usos de los grupos de trabajo de los entornos de hospedaje.|
|/hostingEnvironments/workerPools/Write|Crea un nuevo grupo de trabajo en un entorno de App Service o actualiza uno que ya existe|
|/hostingEnvironments/Write|Crea un nuevo entorno de App Service o actualiza uno que ya existe|
|/ishostingenvironmentnameavailable/read|Averigua si el nombre del entorno de hospedaje está disponible.|
|/ishostnameavailable/read|Comprueba si el nombre de host está disponible.|
|/isusernameavailable/read|Comprueba si el nombre de usuario está disponible.|
|/listSitesAssignedToHostName/Read|Obtiene los nombres de sitios asignados al nombre de host.|
|/locations/apioperations/read|Obtiene las operaciones de API de ubicaciones.|
|/locations/connectiongatewayinstallations/read|Obtiene las instalaciones de puertas de enlace de conexiones de ubicaciones.|
|/locations/extractapidefinitionfromwsdl/action|Extrae la definición de API de WSDL para las ubicaciones.|
|/locations/listwsdlinterfaces/action|Enumera las interfaces de WSDL para las ubicaciones.|
|/locations/managedapis/apioperations/read|Obtiene las operaciones de API administradas de las ubicaciones.|
|/locations/managedapis/Join/Action|Combina una API administrada.|
|/locations/managedapis/read|Obtiene las API administradas de ubicaciones.|
|/operations/read|Obtiene operaciones.|
|/publishingusers/read|Obtiene los usuarios de publicación.|
|/publishingusers/write|Actualiza los usuarios de publicación.|
|/recommendations/Read|Obtiene la lista de recomendaciones de las suscripciones.|
|/register/action|Registra el proveedor de recursos de Microsoft.Web de la suscripción.|
|/resourcehealthmetadata/read|Obtiene los metadatos de Resource Health.|
|/serverfarms/capabilities/read|Obtiene las funcionalidades de los planes de App Service.|
|/serverfarms/Delete|Eliminación de un plan de App Service|
|/serverfarms/firstpartyapps/settings/delete|Elimina la configuración de aplicaciones propias de los planes de App Service.|
|/serverfarms/firstpartyapps/settings/read|Obtiene la configuración de aplicaciones propias de los planes de App Service.|
|/serverfarms/firstpartyapps/settings/write|Actualiza la configuración de aplicaciones propias de los planes de App Service.|
|/serverfarms/hybridconnectionnamespaces/relays/delete|Elimina las retransmisiones de espacios de nombres de conexión híbrida de los planes de App Service.|
|/serverfarms/hybridconnectionnamespaces/relays/read|Obtiene las retransmisiones de espacios de nombres de conexión híbrida de los planes de App Service.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|Obtiene las instancias de Web Apps de las retransmisiones de espacios de nombres de conexión híbrida de los planes de App Service.|
|/serverfarms/hybridconnectionplanlimits/read|Obtiene los límites del plan de conexión híbrida de los planes de App Service.|
|/serverfarms/hybridconnectionrelays/read|Obtiene las retransmisiones de conexión híbrida de los planes de App Service.|
|/serverfarms/metricdefinitions/read|Obtiene las definiciones de métricas de los planes de App Service.|
|/serverfarms/metrics/read|Obtiene las métricas de los planes de App Service.|
|/serverfarms/operationresults/read|Obtiene los resultados de la operación de planes de App Service.|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read|Obtiene las métricas disponibles para un plan de App Service.|
|/serverfarms/Read|Obtiene las propiedades de un plan de App Service|
|/serverfarms/restartSites/Action|Reinicia todas las instancias de Web Apps de un plan de App Service|
|/serverfarms/sites/read|Obtiene las instancias de Web Apps de los planes de App Service.|
|/serverfarms/skus/read|Obtiene las SKU de los planes de App Service.|
|/serverfarms/usages/read|Obtiene los usos de los planes de App Service.|
|/serverfarms/virtualnetworkconnections/gateways/write|Actualiza las puertas de enlace de las conexiones de red virtual de los planes de App Service.|
|/serverfarms/virtualnetworkconnections/read|Obtiene las conexiones de red virtual de los planes de App Service.|
|/serverfarms/virtualnetworkconnections/routes/delete|Elimina las rutas de las conexiones de red virtual de los planes de App Service.|
|/serverfarms/virtualnetworkconnections/routes/read|Obtiene las rutas de las conexiones de red virtual de los planes de App Service.|
|/serverfarms/virtualnetworkconnections/routes/write|Actualiza las rutas de las conexiones de red virtual de los planes de App Service.|
|/serverfarms/workers/reboot/action|Reinicia los trabajos de los planes de App Service.|
|/serverfarms/Write|Crea un nuevo plan de App Service o actualiza uno que ya existe|
|/sites/analyzecustomhostname/read|Analiza el nombre de host personalizado.|
|/sites/applySlotConfig/Action|Aplica la configuración de ranuras de la aplicación web desde la ranura de destino a la aplicación web actual|
|/sites/backup/Action|Crea una copia de seguridad de una nueva aplicación web|
|/sites/backup/read|Obtiene una copia de seguridad de Web Apps.|
|/sites/backup/write|Actualiza la copia de seguridad de Web Apps.|
|/sites/backups/delete|Elimina las copias de seguridad de Web Apps.|
|/sites/backups/list/action|Muestra las copias de seguridad de Web Apps.|
|/sites/backups/Read|Obtiene las propiedades de una copia de seguridad de una aplicación web|
|/sites/backups/restore/action|Restaura las copias de seguridad de Web Apps.|
|/sites/config/delete|Elimina la configuración de Web Apps.|
|/sites/config/list/Action|Muestra las opciones confidenciales de seguridad de Web Apps como las credenciales de publicación, la configuración de la aplicación y las cadenas de conexión|
|/sites/config/Read|Obtiene las opciones de configuración de Web Apps|
|/sites/config/Write|Actualiza las opciones de configuración de Web Apps|
|/sites/continuouswebjobs/delete|Elimina WebJobs continuos de Web Apps.|
|/sites/continuouswebjobs/read|Obtiene WebJobs continuos de Web Apps.|
|/sites/continuouswebjobs/start/action|Inicia WebJobs continuos de Web Apps.|
|/sites/continuouswebjobs/stop/action|Detiene WebJobs continuos de Web Apps.|
|/sites/Delete|Eliminación de una aplicación web existente|
|/sites/deployments/delete|Elimina las implementaciones de Web Apps.|
|/sites/deployments/log/read|Obtiene el registro de las implementaciones de Web Apps.|
|/sites/deployments/read|Obtiene las implementaciones de Web Apps.|
|/sites/deployments/write|Actualiza las implementaciones de Web Apps.|
|/sites/diagnostics/analyses/execute/Action|Ejecuta el análisis de diagnósticos de Web Apps.|
|/sites/diagnostics/analyses/read|Obtiene el análisis de diagnósticos de Web Apps.|
|/sites/diagnostics/aspnetcore/read|Obtiene los diagnósticos de Web Apps para la aplicación de ASP.NET Core.|
|/sites/diagnostics/autoheal/read|Obtiene la característica AutoHeal para los diagnósticos de Web Apps.|
|/sites/diagnostics/deployment/read|Obtiene la implementación de los diagnósticos de Web Apps.|
|/sites/diagnostics/deployments/read|Obtiene las implementaciones de los diagnósticos de Web Apps.|
|/sites/diagnostics/detectors/execute/Action|Ejecuta el detector de diagnósticos de Web Apps.|
|/sites/diagnostics/detectors/read|Obtiene el detector de diagnósticos de Web Apps.|
|/sites/diagnostics/failedrequestsperuri/read|Obtiene las solicitudes erróneas de diagnóstico de Web Apps según la URI.|
|/sites/diagnostics/frebanalysis/read|Obtiene el análisis FREB de diagnóstico de Web Apps.|
|/sites/diagnostics/loganalyzer/read|Obtiene el analizador de registros de diagnóstico de Web Apps.|
|/sites/diagnostics/read|Obtiene las categorías de los diagnósticos de Web Apps.|
|/sites/diagnostics/runtimeavailability/read|Obtiene la disponibilidad de tiempo de ejecución de diagnóstico de Web Apps.|
|/sites/diagnostics/servicehealth/read|Obtiene el estado del servicio de diagnóstico de Web Apps.|
|/sites/diagnostics/sitecpuanalysis/read|Obtiene el análisis de CPU del sitio de diagnósticos de Web Apps.|
|/sites/diagnostics/sitecrashes/read|Obtiene los bloqueos del sitio de diagnósticos de Web Apps.|
|/sites/diagnostics/sitelatency/read|Obtiene la latencia del sitio de diagnósticos de Web Apps.|
|/sites/diagnostics/sitememoryanalysis/read|Obtiene el análisis de memoria del sitio de diagnósticos de Web Apps.|
|/sites/diagnostics/siterestartsettingupdate/read|Obtiene la actualización de la configuración de reinicio del sitio de diagnósticos de Web Apps.|
|/sites/diagnostics/siterestartuserinitiated/read|Obtiene el proceso de reinicio del sitio de diagnósticos de Web Apps que realizó el usuario.|
|/sites/diagnostics/siteswap/read|Obtiene el intercambio del sitio de diagnósticos de Web Apps.|
|/sites/diagnostics/threadcount/read|Obtiene el número de subprocesos del sitio de diagnósticos de Web Apps.|
|/sites/diagnostics/workeravailability/read|Obtiene el valor de Workeravailability de diagnóstico de Web Apps.|
|/sites/diagnostics/workerprocessrecycle/read|Obtiene el reciclaje del proceso de trabajo de diagnóstico de Web Apps.|
|/sites/domainownershipidentifiers/read|Obtiene identificadores de propiedad de dominio de Web Apps.|
|/sites/domainownershipidentifiers/write|Actualiza identificadores de propiedad de dominio de Web Apps.|
|/sites/functions/action|Funciones de Web Apps.|
|/sites/functions/delete|Elimina funciones de Web Apps.|
|/sites/functions/listsecrets/action|Muestra secretos de Web Apps.|
|/sites/functions/masterkey/read|Obtiene la clave maestra de las funciones de Web Apps.|
|/sites/functions/read|Obtiene funciones de Web Apps.|
|/sites/functions/token/read|Obtiene el token de las funciones de Web Apps.|
|/sites/functions/write|Actualiza funciones de Web Apps.|
|/sites/hostnamebindings/delete|Elimina enlaces de nombre de host de Web Apps.|
|/sites/hostnamebindings/read|Obtiene enlaces de nombre de host de Web Apps.|
|/sites/hostnamebindings/write|Actualiza enlaces de nombre de host de Web Apps.|
|/sites/hybridconnection/delete|Elimina la conexión híbrida de Web Apps.|
|/sites/hybridconnection/read|Obtiene la conexión híbrida de Web Apps.|
|/sites/hybridconnection/write|Actualiza la conexión híbrida de Web Apps.|
|/sites/hybridconnectionnamespaces/relays/delete|Elimina las retransmisiones de espacios de nombres de conexión híbrida de Web Apps.|
|/sites/hybridconnectionnamespaces/relays/listkeys/action|Enumera las claves de las retransmisiones de espacios de nombres de conexión híbrida de Web Apps.|
|/sites/hybridconnectionnamespaces/relays/read|Obtiene las retransmisiones de espacios de nombres de conexión híbrida de Web Apps.|
|/sites/hybridconnectionnamespaces/relays/write|Actualiza las retransmisiones de espacios de nombres de conexión híbrida de Web Apps.|
|/sites/hybridconnectionrelays/read|Obtiene las retransmisiones de conexiones híbridas de Web Apps.|
|/sites/instances/deployments/delete|Elimina las implementaciones de instancias de Web Apps.|
|/sites/instances/deployments/read|Obtiene implementaciones de instancias de Web Apps.|
|/sites/instances/extensions/log/read|Obtiene el registro de extensiones de instancias de Web Apps.|
|/sites/instances/extensions/read|Obtiene las extensiones de instancias de Web Apps.|
|/sites/instances/processes/delete|Elimina procesos de instancias de Web Apps.|
|/sites/instances/processes/read|Obtiene procesos de instancias de Web Apps.|
|/sites/instances/read|Obtiene instancias de Web Apps.|
|/sites/listsyncfunctiontriggerstatus/action|Enumera las instancias de Web Apps de estado del desencadenador de funciones de sincronización.|
|/sites/metricdefinitions/read|Obtiene las definiciones de métricas de Web Apps.|
|/sites/metrics/read|Obtiene las métricas de Web Apps.|
|/sites/metricsdefinitions/read|Obtiene las definiciones de métricas de Web Apps.|
|/sites/migratemysql/action|Migra Web Apps de MySql.|
|/sites/migratemysql/read|Consigue que Web Apps migre de MySql.|
|/sites/networktrace/action|Realiza un seguimiento de red de las instancias de Web Apps.|
|/sites/newpassword/action|Genera una nueva contraseña para las instancias de Web Apps.|
|/sites/operationresults/read|Obtiene los resultados de la operación de Web Apps.|
|/sites/operations/read|Obtiene las operaciones de Web Apps.|
|/sites/perfcounters/read|Obtiene los contadores de rendimiento de Web Apps.|
|/sites/premieraddons/delete|Elimina complementos Premier de Web Apps.|
|/sites/premieraddons/read|Obtiene complementos Premier de Web Apps.|
|/sites/premieraddons/write|Actualiza complementos Premier de Web Apps.|
|/sites/processes/read|Obtiene los procesos de Web Apps.|
|/sites/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/sites/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/sites/providers/Microsoft.Insights/metricDefinitions/Read|Obtiene las métricas disponibles de Web Apps.|
|/sites/publiccertificates/delete|Elimina los certificados públicos de Web Apps.|
|/sites/publiccertificates/read|Obtiene los certificados públicos de Web Apps.|
|/sites/publiccertificates/write|Actualiza los certificados públicos de Web Apps.|
|/sites/publish/Action|Publica una aplicación web|
|/sites/publishxml/Action|Obtiene el xml del perfil de publicación de una aplicación web|
|/sites/publishxml/read|Obtiene el XML de publicación de Web Apps.|
|/sites/Read|Obtiene las propiedades de una aplicación web|
|/sites/recommendationhistory/read|Obtiene el historial de recomendaciones de Web Apps.|
|/sites/recommendations/disable/action|Deshabilita las recomendaciones de Web Apps.|
|/sites/recommendations/Read|Obtiene la lista de recomendaciones de la aplicación web.|
|/sites/recover/action|Recupera Web Apps.|
|/sites/resetSlotConfig/Action|Restablece la configuración de la aplicación web|
|/sites/resourcehealthmetadata/read|Obtiene los metadatos de Resource Health de Web Apps.|
|/sites/restart/Action|Reinicia una aplicación web|
|/sites/restore/read|Obtiene la restauración de Web Apps.|
|/sites/restore/write|Restaura Web Apps.|
|/sites/siteextensions/delete|Elimina las extensiones de sitio de Web Apps.|
|/sites/siteextensions/read|Obtiene las extensiones de sitio de Web Apps.|
|/sites/siteextensions/write|Actualiza las extensiones de sitio de Web Apps.|
|/sites/slots/analyzecustomhostname/read|Obtiene el nombre de host personalizado del análisis de ranuras de Web Apps.|
|/sites/slots/applySlotConfig/Action|Aplica una configuración de ranuras de la aplicación web desde la ranura de destino a la ranura actual.|
|/sites/slots/backup/Action|Crea una nueva copia de seguridad de ranura de aplicación web.|
|/sites/slots/backup/read|Obtiene las copias de seguridad de ranuras de Web Apps.|
|/sites/slots/backup/write|Actualiza la copia de seguridad de ranuras de Web Apps.|
|/sites/slots/backups/delete|Elimina las copias de seguridad de ranuras de Web Apps.|
|/sites/slots/backups/list/action|Muestra las copias de seguridad de ranuras de Web Apps.|
|/sites/slots/backups/Read|Obtiene las propiedades de una copia de seguridad de las ranuras de una aplicación web|
|/sites/slots/backups/restore/action|Restaura las copias de seguridad de ranuras de Web Apps.|
|/sites/slots/config/delete|Elimina la configuración de ranuras de Web Apps.|
|/sites/slots/config/list/Action|Muestra las opciones confidenciales de seguridad de ranuras de Web Apps como las credenciales de publicación, la configuración de la aplicación y las cadenas de conexión|
|/sites/slots/config/Read|Obtiene las opciones de configuración de ranura de Web Apps|
|/sites/slots/config/Write|Actualiza las opciones de configuración de ranura de Web Apps|
|/sites/slots/continuouswebjobs/delete|Elimina WebJobs continuos de ranuras de Web Apps.|
|/sites/slots/continuouswebjobs/read|Obtiene WebJobs continuos de ranuras de Web Apps.|
|/sites/slots/continuouswebjobs/start/action|Inicia WebJobs continuos de ranuras de Web Apps.|
|/sites/slots/continuouswebjobs/stop/action|Detiene WebJobs continuos de ranuras de Web Apps.|
|/sites/slots/Delete|Elimina una ranura de aplicación web existente|
|/sites/slots/deployments/delete|Elimina las implementaciones de ranuras de Web Apps.|
|/sites/slots/deployments/log/read|Obtiene el registro de implementaciones de ranuras de Web Apps.|
|/sites/slots/deployments/read|Obtiene las implementaciones de ranuras de Web Apps.|
|/sites/slots/deployments/write|Actualiza las implementaciones de ranuras de Web Apps.|
|/sites/slots/diagnostics/analyses/execute/Action|Ejecuta el análisis de diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/analyses/read|Obtiene el análisis de diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/aspnetcore/read|Obtiene los diagnósticos de ranuras de Web Apps para la aplicación de ASP.NET Core.|
|/sites/slots/diagnostics/autoheal/read|Obtiene la característica AutoHeal para los diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/deployment/read|Obtiene la implementación para los diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/deployments/read|Obtiene las implementaciones para los diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/detectors/execute/Action|Ejecuta el detector de diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/detectors/read|Obtiene el detector de diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/frebanalysis/read|Obtiene el análisis FREB de los diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/loganalyzer/read|Obtiene el analizador de registros de diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/read|Obtiene los diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/runtimeavailability/read|Obtiene la disponibilidad de tiempo de ejecución de los diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/servicehealth/read|Obtiene una instancia de Service Health para los diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/sitecpuanalysis/read|Obtiene el análisis de CPU del sitio de diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/sitecrashes/read|Obtiene los bloqueos del sitio de diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/sitelatency/read|Obtiene la latencia del sitio de diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/sitememoryanalysis/read|Obtiene el análisis de memoria del sitio de diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/siterestartsettingupdate/read|Obtiene la actualización de la configuración de reinicio del sitio de diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/siterestartuserinitiated/read|Obtiene proceso de reinicio el sitio de diagnósticos de ranuras de Web Apps que realizó el usuario.|
|/sites/slots/diagnostics/siteswap/read|Obtiene el intercambio del sitio de diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/threadcount/read|Obtiene número de subprocesos del sitio de diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/workeravailability/read|Obtiene el valor de Workeravailability de los diagnósticos de ranuras de Web Apps.|
|/sites/slots/diagnostics/workerprocessrecycle/read|Obtiene el reciclaje del proceso de trabajo de los diagnósticos de ranuras de Web Apps.|
|/sites/slots/domainownershipidentifiers/read|Obtiene los identificadores de propiedad del dominio de ranuras de Web Apps.|
|/sites/slots/hostnamebindings/delete|Elimina enlaces de nombre de host de ranuras de Web Apps.|
|/sites/slots/hostnamebindings/read|Obtiene enlaces de nombre de host de ranuras de Web Apps.|
|/sites/slots/hostnamebindings/write|Actualiza enlaces de nombre de host de ranuras de Web Apps.|
|/sites/slots/hybridconnection/delete|Elimine la conexión híbrida de ranuras de Web Apps.|
|/sites/slots/hybridconnection/read|Obtiene la conexión híbrida de ranuras de Web Apps.|
|/sites/slots/hybridconnection/write|Actualiza la conexión híbrida de ranuras de Web Apps.|
|/sites/slots/hybridconnectionnamespaces/relays/delete|Elimina las retransmisiones de espacios de nombres de conexión híbrida de las ranuras de Web Apps.|
|/sites/slots/hybridconnectionnamespaces/relays/write|Actualiza las retransmisiones de espacios de nombres de conexión híbrida de las ranuras de Web Apps.|
|/sites/slots/hybridconnectionrelays/read|Obtiene las retransmisiones de conexión híbrida de las ranuras de Web Apps.|
|/sites/slots/instances/deployments/read|Obtiene implementaciones de instancias de ranuras de Web Apps.|
|/sites/slots/instances/processes/delete|Elimina los procesos de instancias de las ranuras de Web Apps.|
|/sites/slots/instances/processes/read|Obtiene procesos de instancias de ranuras de Web Apps.|
|/sites/slots/instances/read|Obtiene instancias de ranuras de Web Apps.|
|/sites/slots/metricdefinitions/read|Obtiene las definiciones de métricas de ranuras de Web Apps.|
|/sites/slots/metrics/read|Obtiene las métricas de ranuras de Web Apps.|
|/sites/slots/migratemysql/read|Consigue que las ranuras de Web Apps migren de MySql.|
|/sites/slots/networktrace/action|Realiza un seguimiento de red de las ranuras de Web Apps.|
|/sites/slots/newpassword/action|Genera una nueva contraseña para las ranuras de instancias de Web Apps.|
|/sites/slots/operationresults/read|Obtiene los resultados de la operación de ranuras de Web Apps.|
|/sites/slots/operations/read|Obtiene la operación de ranuras de Web Apps.|
|/sites/slots/perfcounters/read|Obtiene los contadores de rendimiento de las ranuras de Web Apps.|
|/sites/slots/phplogging/read|Obtiene Phplogging de ranuras de Web Apps.|
|/sites/slots/premieraddons/delete|Elimina complementos Premier de ranuras de Web Apps.|
|/sites/slots/premieraddons/read|Obtiene complementos Premier de ranuras de Web Apps.|
|/sites/slots/premieraddons/write|Actualiza complementos Premier de ranuras de Web Apps.|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read|Obtiene las métricas disponibles para la ranura de Web Apps.|
|/sites/slots/publiccertificates/read|Obtiene los certificados públicos de las ranuras de Web Apps.|
|/sites/slots/publiccertificates/write|Crea o actualiza los certificados públicos de las ranuras de Web Apps.|
|/sites/slots/publish/Action|Publica una ranura de aplicación web|
|/sites/slots/publishxml/Action|Obtiene el xml del perfil de publicación de una ranura de aplicación web|
|/sites/slots/Read|Obtiene las propiedades de una ranura de implementación de aplicación web|
|/sites/slots/resetSlotConfig/Action|Restablece la configuración de ranuras de la aplicación web|
|/sites/slots/resourcehealthmetadata/read|Obtiene los metadatos de Resource Health de las ranuras de Web Apps.|
|/sites/slots/restart/Action|Reinicia una ranura de aplicación web|
|/sites/slots/restore/read|Obtiene la restauración de ranuras de Web Apps.|
|/sites/slots/restore/write|Restaura las ranuras de Web Apps.|
|/sites/slots/siteextensions/delete|Elimina las extensiones de sitio de las ranuras de Web Apps.|
|/sites/slots/siteextensions/read|Obtiene las extensiones de sitio de las ranuras de Web Apps.|
|/sites/slots/siteextensions/write|Actualiza las extensiones de sitio de las ranuras de Web Apps.|
|/sites/slots/slotsdiffs/Action|Obtiene las diferencias de configuración entre la aplicación web y las ranuras|
|/sites/slots/slotsswap/Action|Intercambia ranuras de implementación de aplicación web|
|/sites/slots/snapshots/read|Obtiene instantáneas de las ranuras de Web Apps.|
|/sites/slots/sourcecontrols/Delete|Elimina las opciones de configuración de control de código fuente de ranuras de Web Apps|
|/sites/slots/sourcecontrols/Read|Obtiene las opciones de configuración de control de código fuente de ranuras de Web Apps|
|/sites/slots/sourcecontrols/Write|Actualiza las opciones de configuración de control de código fuente de ranuras de Web Apps|
|/sites/slots/start/Action|Inicia una ranura de aplicación web|
|/sites/slots/stop/Action|Detiene una ranura de aplicación web|
|/sites/slots/sync/action|Sincroniza ranuras de Web Apps.|
|/sites/slots/triggeredwebjobs/delete|Elimina WebJobs desencadenados de ranuras de Web Apps.|
|/sites/slots/triggeredwebjobs/read|Obtiene WebJobs desencadenados de ranuras de Web Apps.|
|/sites/slots/triggeredwebjobs/run/action|Ejecuta WebJobs desencadenados de ranuras de Web Apps.|
|/sites/slots/usages/read|Obtiene los usos de ranuras de Web Apps.|
|/sites/slots/virtualnetworkconnections/delete|Elimina conexiones de red virtual de ranuras de Web Apps.|
|/sites/slots/virtualnetworkconnections/gateways/write|Actualiza puertas de enlace de conexiones de red virtual de ranuras de Web Apps.|
|/sites/slots/virtualnetworkconnections/read|Obtiene conexiones de red virtual de ranuras de Web Apps.|
|/sites/slots/virtualnetworkconnections/write|Actualiza conexiones de red virtual de ranuras de Web Apps.|
|/sites/slots/webjobs/read|Obtiene los WebJobs de ranuras de Web Apps.|
|/sites/slots/Write|Crea una nueva ranura de aplicación web o actualiza una ya existente|
|/sites/slotsdiffs/Action|Obtiene las diferencias de configuración entre la aplicación web y las ranuras|
|/sites/slotsswap/Action|Intercambia ranuras de implementación de aplicación web|
|/sites/snapshots/read|Obtiene instantáneas de Web Apps.|
|/sites/sourcecontrols/Delete|Elimina las opciones de configuración de control de código fuente de Web Apps|
|/sites/sourcecontrols/Read|Obtiene las opciones de configuración de control de código fuente de Web Apps|
|/sites/sourcecontrols/Write|Actualiza las opciones de configuración de control de código fuente de Web Apps|
|/sites/start/Action|Inicia una aplicación web|
|/sites/stop/Action|Detiene una aplicación web|
|/sites/sync/action|Sincroniza las instancias de Web Apps.|
|/sites/syncfunctiontriggers/action|Desencadenadores de función de sincronización para Web Apps.|
|/sites/triggeredwebjobs/delete|Elimina WebJobs desencadenados de Web Apps.|
|/sites/triggeredwebjobs/history/read|Obtiene el historial de WebJobs desencadenado en Web Apps.|
|/sites/triggeredwebjobs/read|Obtiene WebJobs desencadenados de Web Apps.|
|/sites/triggeredwebjobs/run/action|Ejecuta WebJobs desencadenados de Web Apps.|
|/sites/usages/read|Obtiene los usos de Web Apps.|
|/sites/virtualnetworkconnections/delete|Elimina conexiones de red virtual de Web Apps.|
|/sites/virtualnetworkconnections/gateways/read|Obtiene puertas de enlace de conexiones de red virtual de Web Apps.|
|/sites/virtualnetworkconnections/gateways/write|Actualiza puertas de enlace de conexiones de red virtual de Web Apps.|
|/sites/virtualnetworkconnections/read|Obtiene conexiones de red virtual de Web Apps.|
|/sites/virtualnetworkconnections/write|Actualiza conexiones de red virtual de Web Apps.|
|/sites/webjobs/read|Obtiene los WebJobs de Web Apps.|
|/sites/Write|Crea una nueva aplicación web o actualiza una ya existente|
|/skus/read|Obtiene las SKU.|
|/sourcecontrols/read|Obtiene los controles de origen.|
|/sourcecontrols/write|Actualiza los controles de origen.|
|/unregister/action|Anula el registro del proveedor de recursos de Microsoft.Web de la suscripción.|
|/validate/action|Valida.|
|/verifyhostingenvironmentvnet/action|Comprueba la red virtual del entorno de hospedaje.|

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

| Operación | DESCRIPCIÓN |
|---|---|
|/components/read|Lee los recursos de las operaciones.|
|/healthInstances/read|Lee los recursos de las operaciones.|
|/Operations/read|Lee los recursos de las operaciones.|
|/workloads/delete|Elimina un recurso de carga de trabajo.|
|/workloads/read|Lee un recurso de carga de trabajo.|
|/workloads/write|Escribe un recurso de carga de trabajo.|

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [crear un rol personalizado](role-based-access-control-custom-roles.md).
- Revise los [roles RBAC integrados](role-based-access-built-in-roles.md).
- Aprenda a administrar las asignaciones de acceso [por usuario](role-based-access-control-manage-assignments.md) o [por recurso](role-based-access-control-configure.md) 
- Aprenda a [visualizar los registros de actividad para auditar las acciones sobre los recursos](~/articles/azure-resource-manager/resource-group-audit.md)
