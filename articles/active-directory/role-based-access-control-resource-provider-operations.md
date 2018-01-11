---
title: Operaciones del proveedor de Azure Resource Manager | Microsoft Docs
description: "Los detalles de las operaciones están disponibles en los proveedores de recursos de Microsoft Azure Resource Manager"
services: active-directory
documentationcenter: 
author: jboeshart
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: jaboes
ms.openlocfilehash: 27880402d377701448d095a1295ece875729cd67
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operaciones del proveedor de recursos de Azure Resource Manager

Este documento enumera las operaciones que están disponibles para cada proveedor de recursos de Microsoft Azure Resource Manager. Estos se pueden usar en roles personalizados para proporcionar permisos granulares de control de acceso basado en roles (RBAC) a los recursos de Azure. Tenga en cuenta que esta no es una lista exhaustiva y que se pueden agregar o eliminar operaciones a medida que se actualiza cada proveedor. Las cadenas de operaciones usan el formato `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Si desea consultar una lista exhaustiva y actualizada, use `Get-AzureRmProviderOperation` (en PowerShell) o `az provider operation list` (en la CLI de Azure v2) para mostrar las operaciones de los proveedores de recursos de Azure.

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Operación | Descripción |
|---|---|
|/configuration/action|Actualiza la configuración del inquilino.|
|/services/action|Actualiza una instancia de servicio en el inquilino.|
|/configuration/write|Crea una configuración de inquilino.|
|/configuration/read|Lee la configuración de inquilino.|
|/services/write|Crea una instancia de servicio del inquilino.|
|/services/read|Lee las instancias de servicio del inquilino.|
|/services/delete|Elimina una instancia de servicio del inquilino.|
|/services/servicemembers/action|Crea una instancia de miembro de servicio del inquilino.|
|/services/servicemembers/read|Lee una instancia de miembro de servicio del inquilino.|
|/services/servicemembers/delete|Elimina una instancia de miembro de servicio del inquilino.|
|/services/servicemembers/alerts/read|Lee las alertas de un miembro de servicio.|
|/services/alerts/read|Lee las alertas de un servicio.|
|/services/alerts/read|Lee las alertas de un servicio.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Operación | Descripción |
|---|---|
|/generateRecommendations/action|Genera recomendaciones|
|/suppressions/action|Crea o actualiza supresiones|
|/register/action|Registra la suscripción de Microsoft Advisor|
|/generateRecommendations/read|Obtiene el estado de las recomendaciones generadas|
|/recommendations/read|Lee las recomendaciones|
|/suppressions/read|Obtiene las supresiones|
|/suppressions/delete|Elimina las supresiones|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Operación | Descripción |
|---|---|
|/servers/read|Recupera la información de la instancia de Analysis Server especificada.|
|/servers/write|Crea o actualiza la instancia de Analysis Server especificada.|
|/servers/delete|Elimina la instancia de Analysis Server.|
|/servers/suspend/action|Suspende la instancia de Analysis Server.|
|/servers/resume/action|Reanuda la instancia de Analysis Server.|
|/servers/checkNameAvailability<br>/action|Comprueba que el nombre dado de la instancia de Analysis Server es válido y no está en uso.|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Operación | Descripción |
|---|---|
|/checkNameAvailability/action|Comprueba si el nombre del servicio proporcionado está disponible|
|/register/action|Registra la suscripción para el proveedor de recursos de Microsoft.ApiManagement|
|/unregister/action|Anula el registro de la suscripción para el proveedor de recursos de Microsoft.ApiManagement|
|/service/write|Crea una nueva instancia del servicio API Management|
|/service/read|Lee los metadatos de una instancia del servicio API Management|
|/service/delete|Elimina una instancia del servicio API Management|
|/service/updatehostname/action|Configura, actualiza o elimina los nombres de dominio personalizado de un servicio API Management|
|/service/uploadcertificate/action|Carga el certificado SSL de un servicio API Management|
|/service/backup/action|Realiza una copia de seguridad del servicio API Management en el contenedor especificado de una cuenta de almacenamiento proporcionada por el usuario|
|/service/restore/action|Restaura el servicio API Management desde el contenedor especificado de una cuenta de almacenamiento proporcionada por el usuario|
|/service/managedeployments/action|Cambia SKU y unidades, y agrega o quita las implementaciones regionales del servicio API Management|
|/service/getssotoken/action|Obtiene el token de SSO que se puede usar para iniciar sesión en el portal heredado del servicio API Management como administrador|
|/service/applynetworkconfigurationupdates/action|Actualiza los recursos de Microsoft.ApiManagement que se ejecutan en una instancia de Virtual Network para seleccionar la configuración de red actualizada.|
|/service/operationresults/read|Obtiene el estado actual de una operación de larga duración|
|/service/networkStatus/read|Obtiene el estado del acceso a la red de los recursos.|
|/service/loggers/read|Obtiene una lista de registradores o los detalles de uno de ellos|
|/service/loggers/write|Agrega un nuevo registrador o actualiza los detalles de los registradores existentes|
|/service/loggers/delete|Quita un registrador existente|
|/service/users/read|Obtiene una lista de usuarios registrados o los detalles de la cuenta de un usuario|
|/service/users/write|Registra un nuevo usuario o actualiza los detalles de la cuenta de un usuario existente|
|/service/users/delete|Quita una cuenta de usuario|
|/service/users/generateSsoUrl/action|Genera la dirección URL de SSO. La dirección URL se puede utilizar para acceder al portal de administración|
|/service/users/subscriptions/read|Obtiene una lista de suscripciones de usuario|
|/service/users/keys/read|Obtiene una lista de claves de usuario|
|/service/users/groups/read|Obtiene una lista de grupos de usuario|
|/service/tenant/operationResults/read|Obtiene una lista de resultados de operaciones o el resultado de una operación específica|
|/service/tenant/policy/read|Obtiene la configuración de directivas del inquilino|
|/service/tenant/policy/write|Establece la configuración de directivas del inquilino|
|/service/tenant/policy/delete|Elimina la configuración de directivas del inquilino|
|/service/tenant/configuration/save/action|Crea una confirmación con la instantánea de configuración en la bifurcación especificada del repositorio|
|/service/tenant/configuration/deploy/action|Ejecuta una tarea de implementación para aplicar los cambios de la bifurcación git especificada en la configuración de la base de datos.|
|/service/tenant/configuration/validate/action|Valida los cambios de la bifurcación git especificada|
|/service/tenant/configuration/operationResults/read|Obtiene una lista de resultados de operaciones o el resultado de una operación específica|
|/service/tenant/configuration/syncState/read|Obtiene el estado de la última sincronización de git|
|/service/tenant/access/read|Obtiene detalles de la información de acceso del inquilino|
|/service/tenant/access/write|Actualiza los detalles de la información de acceso del inquilino|
|/service/tenant/access/regeneratePrimaryKey/action|Regenera la clave de acceso principal|
|/service/tenant/access/regenerateSecondaryKey/action|Regenera la clave de acceso secundaria|
|/service/identityProviders/read|Obtiene una lista de proveedores de identidades o detalles del proveedor de identidades|
|/service/identityProviders/write|Crea un nuevo proveedor de identidades o actualiza los detalles de un proveedor ya existente|
|/service/identityProviders/delete|Elimina el proveedor de identidades existente|
|/service/subscriptions/read|Obtiene una lista de suscripciones de productos o detalles de una suscripción de producto|
|/service/subscriptions/write|Suscribe un usuario existente a un producto existente o actualiza los detalles de la suscripción existente. Esta operación se puede usar para renovar la suscripción|
|/service/subscriptions/delete|Elimina la suscripción. Esta operación se puede usar para eliminar la suscripción|
|/service/subscriptions/regeneratePrimaryKey/action|Regenera la clave principal de suscripción|
|/service/subscriptions/regenerateSecondaryKey/action|Regenera la clave secundaria de suscripción|
|/service/backends/read|Obtiene una lista de back-ends o detalles de un back-end|
|/service/backends/write|Agregar un nuevo back-end o actualiza los detalles de un back-end ya existente|
|/service/backends/delete|Elimina un backend existente|
|/service/apis/read|Obtiene una lista de todas las API registradas o detalles de una API|
|/service/apis/write|Crea una nueva API o actualiza los detalles de una API ya existente|
|/service/apis/delete|Elimina una API ya existente|
|/service/apis/policy/read|Obtiene los detalles de configuración de directivas de una API|
|/service/apis/policy/write|Establece los detalles de configuración de directivas de una API|
|/service/apis/policy/delete|Elimina la configuración de directivas de la API|
|/service/apis/operations/read|Obtiene una lista de las operaciones de API existentes o detalles de una operación de API|
|/service/apis/operations/write|Crea una nueva operación de API o actualiza una operación de API ya existente|
|/service/apis/operations/delete|Elimina una operación de API ya existente|
|/service/apis/operations/policy/read|Obtiene los detalles de configuración de directivas de una operación|
|/service/apis/operations/policy/write|Establece los detalles de configuración de directivas de una operación|
|/service/apis/operations/policy/delete|Elimina la configuración de directivas de una operación|
|/service/products/read|Obtiene una lista de productos o detalles de un producto|
|/service/products/write|Crea un nuevo producto o actualiza los detalles de un producto existente|
|/service/products/delete|Elimina un producto ya existente|
|/service/products/subscriptions/read|Obtiene una lista de suscripciones de productos|
|/service/products/apis/read|Obtiene una lista de las API que se agregan a un producto existente|
|/service/products/apis/write|Agrega una API existente a un producto que ya existe|
|/service/products/apis/delete|Elimina una API existente de un producto que ya existe|
|/service/products/policy/read|Obtiene la configuración de directivas de un producto ya existente|
|/service/products/policy/write|Establece la configuración de directivas de un producto ya existente|
|/service/products/policy/delete|Elimina la configuración de directivas de un producto ya existente|
|/service/products/groups/read|Obtiene una lista de grupos de desarrolladores asociados con el producto|
|/service/products/groups/write|Asocia un grupo de desarrolladores ya existente con un producto que ya existe|
|/service/products/groups/delete|Elimina la asociación de un grupo de desarrolladores ya existente con un producto que ya existe|
|/service/openidConnectProviders/read|Obtiene una lista de proveedores de OpenID Connect o detalles del proveedor de OpenID Connect|
|/service/openidConnectProviders/write|Crea un nuevo proveedor de OpenID Connect o actualiza los detalles de un proveedor de OpenID Connect ya existente|
|/service/openidConnectProviders/delete|Elimina un proveedor de OpenID Connect ya existente|
|/service/certificates/read|Obtiene una lista de certificados o detalles de un certificado|
|/service/certificates/write|Agrega un nuevo certificado|
|/service/certificates/delete|Elimina un certificado existente|
|/service/properties/read|Obtiene una lista de todas las propiedades o detalles de una propiedad especificada|
|/service/properties/write|Crea una nueva propiedad o actualiza el valor de una propiedad especificada|
|/service/properties/delete|Elimina una propiedad ya existente|
|/service/groups/read|Obtiene una lista de grupos o detalles de un grupo|
|/service/groups/write|Crea un nuevo grupo o actualiza los detalles de un grupo ya existente|
|/service/groups/delete|Elimina un grupo ya existente|
|/service/groups/users/read|Obtiene una lista de los usuarios del grupo|
|/service/groups/users/write|Agrega un usuario existente a un grupo que ya existe|
|/service/groups/users/delete|Elimina un usuario existente de un grupo que ya existe|
|/service/authorizationServers/read|Obtiene una lista de servidores de autorización o detalles de un servidor de autorización|
|/service/authorizationServers/write|Crea un nuevo servidor de autorización o actualiza los detalles de un servidor de autorización ya existente|
|/service/authorizationServers/delete|Quita un servidor de autorización ya existente|
|/service/reports/bySubscription/read|Obtiene un informe agregado por la suscripción.|
|/service/reports/byRequest/read|Obtiene solicitudes de datos de informes|
|/service/reports/byOperation/read|Obtiene un informe agregado por las operaciones|
|/service/reports/byGeo/read|Obtiene un informe agregado por región geográfica|
|/service/reports/byUser/read|Obtiene un informe agregado por los desarrolladores.|
|/service/reports/byTime/read|Obtiene un informe agregado por períodos de tiempo|
|/service/reports/byApi/read|Obtiene un informe agregado por las API|
|/service/reports/byProduct/read|Obtiene un informe agregado por productos.|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Operación | Descripción |
|---|---|
|/elevateAccess/action|Concede al llamador acceso de administrador de acceso de usuario en el ámbito de inquilinos|
|/classicAdministrators/read|Lee los administradores para la suscripción.|
|/classicAdministrators/write|Agrega o modifica el administrador para una suscripción.|
|/classicAdministrators/delete|Quita al administrador de la suscripción.|
|/locks/read|Obtiene bloqueos en el ámbito especificado.|
|/locks/write|Agrega bloqueos en el ámbito especificado.|
|/locks/delete|Elimina bloqueos en el ámbito especificado.|
|/policyAssignments/read|Obtiene información sobre una asignación de directiva.|
|/policyAssignments/write|Crea una asignación de directiva en el ámbito especificado.|
|/policyAssignments/delete|Elimina una asignación de directiva en el ámbito especificado.|
|/permissions/read|Enumera todos los permisos que tiene el llamador en un ámbito concreto.|
|/roleDefinitions/read|Obtiene información sobre una definición de roles.|
|/roleDefinitions/write|Crea o actualiza una definición de roles personalizada con permisos especificados y ámbitos asignables.|
|/roleDefinitions/delete|Elimina la definición de roles personalizada especificada.|
|/providerOperations/read|Obtiene operaciones para todos los proveedores de recursos que pueden usarse en definiciones de roles.|
|/policyDefinitions/read|Obtiene información sobre una definición de directiva.|
|/policyDefinitions/write|Crea una definición de directiva personalizada.|
|/policyDefinitions/delete|Elimina una definición de directiva.|
|/roleAssignments/read|Obtiene información sobre una asignación de roles.|
|/roleAssignments/write|Crea una asignación de roles en el ámbito especificado.|
|/roleAssignments/delete|Elimine una asignación de roles en el ámbito especificado.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Operación | Descripción |
|---|---|
|/automationAccounts/read|Obtiene una cuenta de Azure Automation|
|/automationAccounts/write|Crea o actualiza una cuenta de Azure Automation|
|/automationAccounts/delete|Elimina una cuenta de Azure Automation|
|/automationAccounts/configurations/readContent/action|Obtiene contenidos de DSC de Azure Automation|
|/automationAccounts/hybridRunbookWorkerGroups/read|Lee los recursos de Hybrid Runbook Worker|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Elimina los recursos de Hybrid Runbook Worker|
|/automationAccounts/jobSchedules/read|Obtiene una programación de trabajos de Azure Automation|
|/automationAccounts/jobSchedules/write|Crea una programación de trabajos de Azure Automation|
|/automationAccounts/jobSchedules/delete|Elimina una programación de trabajos de Azure Automation|
|/automationAccounts/connectionTypes/read|Obtiene un recurso de tipo de conexión de Azure Automation|
|/automationAccounts/connectionTypes/write|Crea un recurso de tipo de conexión de Azure Automation|
|/automationAccounts/connectionTypes/delete|Elimina un recurso de tipo de conexión de Azure Automation|
|/automationAccounts/modules/read|Obtiene un módulo de Azure Automation|
|/automationAccounts/modules/write|Crea o actualiza un módulo de Azure Automation|
|/automationAccounts/modules/delete|Elimina un módulo de Azure Automation|
|/automationAccounts/credentials/read|Obtiene un recurso de credencial de Azure Automation|
|/automationAccounts/credentials/write|Crea o actualiza un recurso de credencial de Azure Automation|
|/automationAccounts/credentials/delete|Elimina un recurso de credencial de Azure Automation|
|/automationAccounts/certificates/read|Obtiene un recurso de credencial de Azure Automation|
|/automationAccounts/certificates/write|Crea o actualiza un recurso de certificado de Azure Automation|
|/automationAccounts/certificates/delete|Elimina un recurso de certificado de Azure Automation|
|/automationAccounts/schedules/read|Obtiene un recurso de programación de Azure Automation|
|/automationAccounts/schedules/write|Crea o actualiza un recurso de programación de Azure Automation|
|/automationAccounts/schedules/delete|Elimina un recurso de programación de Azure Automation|
|/automationAccounts/jobs/read|Obtiene un trabajo de Azure Automation|
|/automationAccounts/jobs/write|Crea un trabajo de Azure Automation|
|/automationAccounts/jobs/stop/action|Detiene un trabajo de Azure Automation|
|/automationAccounts/jobs/suspend/action|Suspende un trabajo de Azure Automation|
|/automationAccounts/jobs/resume/action|Reanuda un trabajo de Azure Automation|
|/automationAccounts/jobs/runbookContent/action|Obtiene el contenido del runbook de Azure Automation en el momento de la ejecución del trabajo|
|/automationAccounts/jobs/output/action|Obtiene la salida de un trabajo|
|/automationAccounts/jobs/read|Obtiene un trabajo de Azure Automation|
|/automationAccounts/jobs/write|Crea un trabajo de Azure Automation|
|/automationAccounts/jobs/stop/action|Detiene un trabajo de Azure Automation|
|/automationAccounts/jobs/suspend/action|Suspende un trabajo de Azure Automation|
|/automationAccounts/jobs/resume/action|Reanuda un trabajo de Azure Automation|
|/automationAccounts/jobs/streams/read|Obtiene un flujo de trabajos de Azure Automation|
|/automationAccounts/connections/read|Obtiene un recurso de conexión de Azure Automation|
|/automationAccounts/connections/write|Crea o actualiza un recurso de conexión de Azure Automation|
|/automationAccounts/connections/delete|Elimina un recurso de conexión de Azure Automation|
|/automationAccounts/variables/read|Lee un recurso de variable en Azure Automation|
|/automationAccounts/variables/write|Crea o actualiza un recurso de variable de Azure Automation|
|/automationAccounts/variables/delete|Elimina un recurso de variable de Azure Automation|
|/automationAccounts/runbooks/readContent/action|Obtiene el contenido de un runbook de Azure Automation|
|/automationAccounts/runbooks/read|Obtiene un runbook de Azure Automation|
|/automationAccounts/runbooks/write|Crea o actualiza un runbook de Azure Automation|
|/automationAccounts/runbooks/delete|Elimina un runbook de Azure Automation|
|/automationAccounts/runbooks/draft/readContent/action|Obtiene el contenido de un borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/writeContent/action|Crea el contenido de un borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/read|Obtiene un borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/publish/action|Publica un borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/undoEdit/action|Deshace las ediciones de un borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/testJob/read|Obtiene un trabajo de prueba del borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/testJob/write|Crea un trabajo de prueba del borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/testJob/stop/action|Detiene un trabajo de prueba del borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Suspende un trabajo de prueba del borrador de runbook de Azure Automation|
|/automationAccounts/runbooks/draft/testJob/resume/action|Reanuda un trabajo de prueba del borrador de runbook de Azure Automation|
|/automationAccounts/webhooks/read|Lee un webhook de Azure Automation|
|/automationAccounts/webhooks/write|Crea o actualiza un webhook de Azure Automation|
|/automationAccounts/webhooks/delete|Elimina un webhook de Azure Automation |
|/automationAccounts/webhooks/generateUri/action|Genera un identificador URI para un webhook de Azure Automation|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

Este proveedor no es un proveedor de ARM completo y no proporciona ninguna operación de ARM.

## <a name="microsoftbatch"></a>Microsoft.Batch

| Operación | Descripción |
|---|---|
|/register/action|Registra la suscripción para el proveedor de recursos de Batch y habilita la creación de cuentas de Batch|
|/batchAccounts/write|Crea una nueva cuenta de Batch o actualiza una cuenta de Batch ya existente|
|/batchAccounts/read|Enumera las cuentas de Batch u obtiene las propiedades de una de ellas|
|/batchAccounts/delete|Elimina una cuenta de Batch|
|/batchAccounts/listkeys/action|Enumera las claves de acceso de una cuenta de Batch|
|/batchAccounts/regeneratekeys/action|Regenera las claves de acceso de una cuenta de Batch|
|/batchAccounts/syncAutoStorageKeys/action|Sincroniza las teclas de acceso para la cuenta de almacenamiento automática configurada para una cuenta de Batch|
|/batchAccounts/applications/read|Enumera las aplicaciones u obtiene las propiedades de una aplicación|
|/batchAccounts/applications/write|Crea una nueva aplicación o actualiza una aplicación ya existente|
|/batchAccounts/applications/delete|Elimina una aplicación|
|/batchAccounts/applications/versions/read|Obtiene las propiedades de un paquete de aplicación|
|/batchAccounts/applications/versions/write|Crea un nuevo paquete de aplicación o actualiza uno ya existente|
|/batchAccounts/applications/versions/activate/action|Activa un paquete de aplicación|
|/batchAccounts/applications/versions/delete|Elimina un paquete de aplicación|
|/locations/quotas/read|Obtiene las cuotas de Batch de la suscripción especificada en la región especificada de Azure|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Operación | Descripción |
|---|---|
|/invoices/read|Enumera las facturas disponibles|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Operación | Descripción |
|---|---|
|/mapApis/Read|Lee una operación|
|/mapApis/Write|Escribe una operación|
|/mapApis/Delete|Elimina una operación|
|/mapApis/regenerateKey/action|Regenera la clave|
|/mapApis/listSecrets/action|Enumera los secretos|
|/mapApis/listSingleSignOnToken/action|Lee el token de autorización de inicio de sesión único del recurso|
|/Operations/read|Descripción de la operación.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Operación | Descripción |
|---|---|
|/checknameavailability/action|Comprueba si un nombre está disponible para su uso con una nueva instancia de Redis Cache|
|/register/action|Registra el proveedor de recursos "Microsoft.Cache" con una suscripción|
|/unregister/action|Anula el registro del proveedor de recursos "Microsoft.Cache" con una suscripción|
|/redis/write|Modifica la configuración de Redis Cache en el portal de administración|
|/redis/read|Visualiza la configuración de Redis Cache en el portal de administración|
|/redis/delete|Elimina la instancia completa de Redis Cache|
|/redis/listKeys/action|Visualiza el valor de las claves de acceso de Redis Cache en el portal de administración|
|/redis/regenerateKey/action|Cambia el valor de las claves de acceso de Redis Cache en el portal de administración|
|/redis/import/action|Importa datos de un formato especificado desde varios blobs en Redis|
|/redis/export/action|Exporta datos de Redis a blobs de almacenamiento prefijados en un formato especificado|
|/redis/forceReboot/action|Fuerza el reinicio de una instancia de memoria caché, posiblemente con pérdida de datos.|
|/redis/stop/action|Detiene una instancia de la memoria caché.|
|/redis/start/action|Inicia una instancia de la memoria caché.|
|/redis/metricDefinitions/read|Obtiene las métricas disponibles para una instancia de Redis Cache|
|/redis/firewallRules/read|Obtiene las reglas de firewall IP de una instancia de Redis Cache|
|/redis/firewallRules/write|Edita las reglas de firewall IP de una instancia de Redis Cache|
|/redis/firewallRules/delete|Elimina las reglas de firewall IP de una instancia de Redis Cache|
|/redis/listUpgradeNotifications/read|Enumera las notificaciones de actualización más recientes para el inquilino de la caché.|
|/redis/linkedservers/read|Obtiene los servidores vinculados asociados con una instancia de Redis Cache.|
|/redis/linkedservers/write|Agrega un servidor vinculado a una instancia de Redis Cache|
|/redis/linkedservers/delete|Elimina un servidor vinculado de una instancia de Redis Cache|
|/redis/patchSchedules/read|Obtiene la programación de una aplicación de revisiones de una instancia de Redis Cache|
|/redis/patchSchedules/write|Modifica la programación de una aplicación de revisiones de una instancia de Redis Cache|
|/redis/patchSchedules/delete|Elimina la programación de una aplicación de revisiones de una instancia de Redis Cache|

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Operación | Descripción |
|---|---|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Aprovisiona una entidad de servicio para entidad de aplicación de servicio|
|/validateCertificateRegistrationInformation/Action|Valida un objeto de compra de certificado sin enviarlo|
|/register/action|Registra el proveedor de recursos de Microsoft Certificates de la suscripción|
|/certificateOrders/Write|Agrega un nuevo certificateOrder o actualiza uno existente|
|/certificateOrders/Delete|Elimina un AppServiceCertificate existente|
|/certificateOrders/Read|Obtiene la lista de CertificateOrders|
|/certificateOrders/reissue/Action|Vuelve a emitir un certificateorder existente|
|/certificateOrders/renew/Action|Renueva un certificateorder existente|
|/certificateOrders/retrieveCertificateActions/Action|Recupera la lista de acciones de certificado|
|/certificateOrders/retrieveEmailHistory/Action|Recupera el historial de correos electrónicos del certificado|
|/certificateOrders/resendEmail/Action|Vuelve a enviar el correo electrónico de certificado|
|/certificateOrders/verifyDomainOwnership/Action|Comprobar la propiedad del dominio|
|/certificateOrders/resendRequestEmails/Action|Vuelve a enviar los mensajes de correo electrónico de solicitud a otra dirección de correo electrónico|
|/certificateOrders/resendRequestEmails/Action|Recupera el sello del sitio de un certificado emitido de App Service|
|/certificateOrders/certificates/Write|Agrega un nuevo certificado o actualiza uno existente|
|/certificateOrders/certificates/Delete|Elimina un certificado existente|
|/certificateOrders/certificates/Read|Obtiene la lista de certificados|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Operación | Descripción |
|---|---|
|/register/action|Realiza el registro en un proceso clásico|
|/checkDomainNameAvailability/action|Comprueba la disponibilidad de un nombre de dominio determinado.|
|/moveSubscriptionResources/action|Mueve todos los recursos clásicos a una suscripción diferente.|
|/validateSubscriptionMoveAvailability/action|Valida la disponibilidad de la suscripción para la operación de movimiento clásico.|
|/operatingSystemFamilies/read|Enumera las familias del sistema operativo invitado disponibles en Microsoft Azure y también muestra las versiones del sistema operativo disponibles para cada familia
|/capabilities/read|Muestra las funcionalidades|
|/operatingSystems/read|Enumera las versiones del sistema operativo invitado que están actualmente disponibles en Microsoft Azure.|
|/resourceTypes/skus/read|Obtiene la lista de Sku para los tipos de recursos admitidos.|
|/domainNames/read|Devuelve los nombres de dominio de los recursos.|
|/domainNames/write|Agrega o modifica los nombres de dominio de los recursos.|
|/domainNames/delete|Quita los nombres de dominio de los recursos.|
|/domainNames/swap/action|Cambia la ranura de ensayo a la de producción.|
|/domainNames/serviceCertificates/read|Devuelve los certificados de servicio usados.|
|/domainNames/serviceCertificates/write|Agrega o modifica los certificados de servicio usados.|
|/domainNames/serviceCertificates/delete|Elimina los certificados de servicio usados.|
|/domainNames/serviceCertificates/operationStatuses/read|Lee el estado de la operación de los certificados de servicio de los nombres de dominio.|
|/domainNames/capabilities/read|Muestra las funcionalidades del nombre de dominio|
|/domainNames/extensions/read|Devuelve las extensiones del nombre de dominio.|
|/domainNames/extensions/write|Agrega las extensiones del nombre de dominio.|
|/domainNames/extensions/delete|Quita las extensiones del nombre de dominio.|
|/domainNames/extensions/operationStatuses/read|Lee el estado de la operación de las extensiones de los nombres de dominio.|
|/domainNames/active/write|Establece el nombre de dominio activo.|
|/domainNames/slots/read|Muestra las ranuras de implementación.|
|/domainNames/slots/write|Crea o actualiza la implementación.|
|/domainNames/slots/delete|Elimina una ranura de implementación concreta.|
|/domainNames/slots/start/action|Inicia una ranura de implementación.|
|/domainNames/slots/stop/action|Suspende la ranura de implementación.|
|/domainNames/slots/operationStatuses/read|Lee el estado de la operación de las ranuras de los nombres de dominio.|
|/domainNames/slots/roles/read|Obtiene el rol de la ranura de implementación.|
|/domainNames/slots/roles/extensionReferences/read|Devuelve la referencia de extensión para el rol de la ranura de implementación.|
|/domainNames/slots/roles/extensionReferences/write|Agrega o modifica la referencia de extensión para el rol de la ranura de implementación.|
|/domainNames/slots/roles/extensionReferences/delete|Quita la referencia de extensión para el rol de la ranura de implementación.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|Lee el estado de la operación de las referencias de extensiones de los roles de las ranuras de nombres de dominio.|
|/domainNames/slots/roles/roleInstances/read|Obtiene la instancia de rol.|
|/domainNames/slots/roles/roleInstances/restart/action|Reinicia las instancias de rol.|
|/domainNames/slots/roles/roleInstances/reimage/action|Restablece la imagen inicial de la instancia de rol.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|Lee el estado de la operación de las instancias de rol de los roles de las ranuras de nombres de dominio.|
|/domainNames/slots/state/start/write|Cambia el estado de la ranura de implementación a detenido.|
|/domainNames/slots/state/stop/write|Cambia el estado de la ranura de implementación a iniciado.|
|/domainNames/slots/upgradeDomain/write|Recorre el dominio de actualización.|
|/domainNames/internalLoadBalancers/read|Obtiene los equilibradores de carga internos.|
|/domainNames/internalLoadBalancers/write|Crea un nuevo equilibrio de carga interno.|
|/domainNames/internalLoadBalancers/delete|Quita un nuevo equilibrio de carga interno.|
|/domainNames/internalLoadBalancers/operationStatuses/read|Lee el estado de la operación de los equilibradores de carga internos de los nombres de dominio.|
|/domainNames/loadBalancedEndpointSets/read|Muestra los conjuntos de puntos de conexión de carga equilibrada|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|Lee el estado de la operación de los conjuntos de puntos de conexión de carga equilibrada de los nombres de dominio.|
|/domainNames/availabilitySets/read|Muestra el conjunto de disponibilidad para el recurso.|
|/quotas/read|Obtiene la cuota de la suscripción.|
|/virtualMachines/read|Recupera una lista de máquinas virtuales.|
|/virtualMachines/write|Agrega o modifica máquinas virtuales.|
|/virtualMachines/delete|Quita máquinas virtuales.|
|/virtualMachines/start/action|Inicie la máquina virtual.|
|/virtualMachines/redeploy/action|Vuelve a implementar la máquina virtual.|
|/virtualMachines/restart/action|Reinicia las máquinas virtuales.|
|/virtualMachines/stop/action|Detiene la máquina virtual.|
|/virtualMachines/shutdown/action|Apaga la máquina virtual.|
|/virtualMachines/attachDisk/action|Conecta un disco de datos a una máquina virtual.|
|/virtualMachines/detachDisk/action|Desconecta un disco de datos de una máquina virtual.|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|Descarga el archivo RDP para la máquina virtual.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/read|Obtiene el grupo de seguridad de red asociado con la interfaz de red.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/write|Agrega un grupo de seguridad de red asociado con la interfaz de red.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/delete|Elimina el grupo de seguridad de red asociado con la interfaz de red.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/operationStatuses/read|Lee el estado de la operación de los grupos de seguridad de red asociados a las máquinas virtuales.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las definiciones de métricas.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Agrega o modifica la configuración de diagnóstico.|
|/virtualMachines/metrics/read|Obtiene las métricas.|
|/virtualMachines/operationStatuses/read|Lee el estado de la operación de las máquinas virtuales.|
|/virtualMachines/extensions/read|Obtiene la extensión de máquina virtual.|
|/virtualMachines/extensions/write|Pone la extensión de máquina virtual.|
|/virtualMachines/extensions/operationStatuses/read|Lee el estado de la operación de las extensiones de máquinas virtuales.|
|/virtualMachines/asyncOperations/read|Obtiene las operaciones asincrónicas posibles|
|/virtualMachines/disks/read|Recupera la lista de discos de datos|
|/virtualMachines/associatedNetworkSecurityGroups/read|Obtiene el grupo de seguridad de red asociado con la máquina virtual.|
|/virtualMachines/associatedNetworkSecurityGroups/write|Agrega un grupo de seguridad de red asociado con la máquina virtual.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|Elimina el grupo de seguridad de red asociado con la máquina virtual.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|Lee el estado de la operación de los grupos de seguridad de red asociados a las máquinas virtuales.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Operación | Descripción |
|---|---|
|/register/action|Realiza el registro en una red clásica|
|/gatewaySupportedDevices/read|Recupera la lista de dispositivos compatibles.|
|/reservedIps/read|Obtiene las IP reservadas|
|/reservedIps/write|Agregar una nueva IP reservada|
|/reservedIps/delete|Elimina una IP reservada.|
|/reservedIps/link/action|Vincula una IP reservada|
|/reservedIps/join/action|Une una IP reservada|
|/reservedIps/operationStatuses/read|Lee el estado de la operación de las IP reservadas.|
|/virtualNetworks/read|Obtiene la red virtual.|
|/virtualNetworks/write|Agrega una nueva red virtual.|
|/virtualNetworks/delete|Elimina la red virtual.|
|/virtualNetworks/peer/action|Empareja una red virtual con otra red virtual.|
|/virtualNetworks/join/action|Une la red virtual.|
|/virtualNetworks/checkIPAddressAvailability/action|Comprueba la disponibilidad de una dirección IP determinada en una red virtual.|
|/virtualNetworks/capabilities/read|Muestra las funcionalidades|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/read|Obtiene el grupo de seguridad de red asociado con la subred.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/write|Agrega un grupo de seguridad de red asociado con la subred.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/delete|Elimina el grupo de seguridad de red asociado con la subred.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/operationStatuses/read|Lee el estado de la operación del grupo de seguridad de red asociado a la subred de red virtual.|
|/virtualNetworks/operationStatuses/read|Lee el estado de la operación de las redes virtuales.|
|/virtualNetworks/gateways/read|Obtiene las puertas de enlace de red virtual.|
|/virtualNetworks/gateways/write|Agrega una puerta de enlace de red virtual.|
|/virtualNetworks/gateways/delete|Elimina la puerta de enlace de red virtual.|
|/virtualNetworks/gateways/startDiagnostics/action|Inicia el diagnóstico de la puerta de enlace de red virtual.|
|/virtualNetworks/gateways/stopDiagnostics/action|Detiene el diagnóstico de la puerta de enlace de red virtual.|
|/virtualNetworks/gateways/downloadDiagnostics/action|Descarga los diagnósticos de la puerta de enlace.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Recupera la clave de servicio del circuito.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|Descarga el script de configuración del dispositivo.|
|/virtualNetworks/gateways/listPackage/action|Muestra el paquete de la puerta de enlace de red virtual.|
|/virtualNetworks/gateways/operationStatuses/read|Lee el estado de la operación de las puertas de enlace de redes virtuales.|
|/virtualNetworks/gateways/packages/read|Obtiene el paquete de la puerta de enlace de red virtual.|
|/virtualNetworks/gateways/connections/read|Recupera la lista de conexiones.|
|/virtualNetworks/gateways/connections/connect/action|Realiza una conexión de puerta de enlace de sitio a sitio.|
|/virtualNetworks/gateways/connections/disconnect/action|Desconecta una conexión de puerta de enlace de sitio a sitio.|
|/virtualNetworks/gateways/connections/test/action|Prueba una conexión de puerta de enlace de sitio a sitio.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|Lee los certificados de cliente revocados.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|Revoca un certificado de cliente.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|Anula la revocación de un certificado de cliente.|
|/virtualNetworks/gateways/clientRootCertificates/read|Busca los certificados raíz de cliente.|
|/virtualNetworks/gateways/clientRootCertificates/write|Carga un nuevo certificado raíz de cliente.|
|/virtualNetworks/gateways/clientRootCertificates/delete|Elimina el certificado de cliente de la puerta de enlace de red virtual.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|Descarga el certificado con huella digital.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|Muestra el paquete de certificado de la puerta de enlace de red virtual.|
|/networkSecurityGroups/read|Obtiene el grupo de seguridad de red.|
|/networkSecurityGroups/write|Agrega un nuevo grupo de seguridad de red.|
|/networkSecurityGroups/delete|Elimina el grupo de seguridad de red.|
|/networkSecurityGroups/operationStatuses/read|Lee el estado de la operación del grupo de seguridad de red.|
|/networkSecurityGroups/securityRules/read|Obtiene la regla de seguridad.|
|/networkSecurityGroups/securityRules/write|Agrega o actualiza una regla de seguridad.|
|/networkSecurityGroups/securityRules/delete|Elimina la regla de seguridad.|
|/networkSecurityGroups/securityRules/operationStatuses/read|Lee el estado de la operación de las reglas de seguridad del grupo de seguridad de red.|
|/quotas/read|Obtiene la cuota de la suscripción.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Operación | Descripción |
|---|---|
|/register/action|Registra elementos en el almacenamiento clásico|
|/checkStorageAccountAvailability/action|Comprueba la disponibilidad de una cuenta de almacenamiento.|
|/capabilities/read|Muestra las funcionalidades|
|/publicImages/read|Obtiene la imagen de máquina virtual pública.|
|/images/read|Devuelve la imagen.|
|/storageAccounts/read|Devuelve la cuenta de almacenamiento con la cuenta especificada.|
|/storageAccounts/write|Agrega una nueva cuenta de almacenamiento.|
|/storageAccounts/delete|Elimina la cuenta de almacenamiento.|
|/storageAccounts/listKeys/action|Enumera las claves de acceso de las cuentas de almacenamiento.|
|/storageAccounts/regenerateKey/action|Regenera las claves de acceso existentes de la cuenta de almacenamiento.|
|/storageAccounts/operationStatuses/read|Lee el estado de la operación de los recursos.|
|/storageAccounts/images/read|Devuelve la imagen de la cuenta de almacenamiento.|
|/storageAccounts/images/delete|Elimina una imagen determinada de la cuenta de almacenamiento.|
|/storageAccounts/disks/read|Devuelve el disco de la cuenta de almacenamiento.|
|/storageAccounts/disks/write|Agrega un disco de cuenta de almacenamiento.|
|/storageAccounts/disks/delete|Elimina un disco de cuenta de almacenamiento determinado.|
|/storageAccounts/disks/operationStatuses/read|Lee el estado de la operación de los recursos.|
|/storageAccounts/osImages/read|Devuelve la imagen del sistema operativo de la cuenta de almacenamiento.|
|/storageAccounts/osImages/delete|Elimina una imagen del sistema operativo de la cuenta de almacenamiento.|
|/storageAccounts/services/read|Obtiene los servicios disponibles.|
|/storageAccounts/services/metricDefinitions/read|Obtiene las definiciones de métricas.|
|/storageAccounts/services/metrics/read|Obtiene las métricas.|
|/storageAccounts/services/diagnosticSettings/read|Obtiene la configuración de diagnóstico.|
|/storageAccounts/services/diagnosticSettings/write|Agrega o modifica la configuración de diagnóstico.|
|/disks/read|Devuelve el disco de la cuenta de almacenamiento.|
|/osImages/read|Devuelve la imagen de sistema operativo.|
|/quotas/read|Obtiene la cuota de la suscripción.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Operación | Descripción |
|---|---|
|/accounts/read|Lee las cuentas de la API.|
|/accounts/write|Escribe las cuentas de la API.|
|/accounts/delete|Elimina las cuentas de la API|
|/accounts/listKeys/action|Enumera las claves|
|/accounts/regenerateKey/action|Regenera una clave|
|/accounts/skus/read|Lee las SKU disponibles para un recurso existente.|
|/accounts/usages/read|Obtiene el uso de cuota para un recurso existente.|
|/Operations/read|Descripción de la operación.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Operación | Descripción |
|---|---|
|/RateCard/read|Devuelve datos de oferta, metadatos de recurso o medidor, y las tarifas de las suscripciones dadas.|
|/UsageAggregates/read|Recupera el consumo de Microsoft Azure por parte de una suscripción. El resultado contiene datos de uso de agregados, información relacionada con la suscripción y los recursos, en un determinado intervalo de tiempo.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Operación | Descripción |
|---|---|
|/register/action|Registra la suscripción con el proveedor de recursos de Microsoft.Compute|
|/restorePointCollections/read|Obtiene las propiedades de una colección de puntos de restauración|
|/restorePointCollections/write|Crea una nueva colección de puntos de restauración o actualiza una ya existente|
|/restorePointCollections/delete|Elimina la colección de puntos de restauración y los puntos de restauración que contiene|
|/restorePointCollections/restorePoints/read|Obtiene las propiedades de un punto de restauración|
|/restorePointCollections/restorePoints/write|Crea un nuevo punto de restauración|
|/restorePointCollections/restorePoints/delete|Elimina el punto de restauración|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Obtiene las propiedades de un punto de restauración junto con los identificadores URI de SAS de blob|
|/virtualMachineScaleSets/read|Obtiene las propiedades de un conjunto de escalado de máquinas virtuales|
|/virtualMachineScaleSets/write|Crea un nuevo conjunto de escalado de máquinas virtuales o actualiza uno ya existente|
|/virtualMachineScaleSets/delete|Elimina el conjunto de escalado de máquinas virtuales|
|/virtualMachineScaleSets/start/action|Inicia las instancias del conjunto de escalado de máquinas virtuales|
|/virtualMachineScaleSets/powerOff/action|Apaga las instancias del conjunto de escalado de máquinas virtuales|
|/virtualMachineScaleSets/restart/action|Reinicia las instancias del conjunto de escalado de máquinas virtuales|
|/virtualMachineScaleSets/deallocate/action|Apaga y libera los recursos de proceso de las instancias del conjunto de escalado de máquinas virtuales |
|/virtualMachineScaleSets/manualUpgrade/action|Actualiza manualmente las instancias al último modelo del conjunto de escalado de máquinas virtuales|
|/virtualMachineScaleSets/scale/action|Reduce o escala horizontalmente el recuento de instancias de un conjunto de escalado de máquinas virtuales ya existente|
|/virtualMachineScaleSets/instanceView/read|Obtiene la vista de instancias del conjunto de escalado de máquinas virtuales|
|/virtualMachineScaleSets/skus/read|Enumera las SKU válidas de un conjunto de escalado de máquinas virtuales ya existente|
|/virtualMachineScaleSets/virtualMachines/read|Recupera las propiedades de una máquina virtual de un conjunto de escalado de máquinas virtuales|
|/virtualMachineScaleSets/virtualMachines/delete|Elimina una máquina virtual específica de un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/start/action|Inicia una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|Apaga una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/restart/action|Reinicia una instancia de máquina virtual en un conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|Apaga y libera los recursos de proceso de una máquina virtual del conjunto de escalado de máquinas virtuales.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|Recupera la vista de instancias de una máquina virtual de un conjunto de escalado de máquinas virtuales.|
|/images/read|Obtiene las propiedades de la imagen|
|/images/write|Crea una nueva imagen o actualiza una ya existente|
|/images/delete|Elimina la imagen|
|/operations/read|Enumera las operaciones disponibles en el proveedor de recursos de Microsoft.Compute|
|/disks/read|Obtiene las propiedades de un disco|
|/disks/write|Crea un nuevo disco o actualiza uno ya existente|
|/disks/delete|Elimina el disco|
|/disks/beginGetAccess/action|Obtiene el URI de SAS del disco de acceso de blob|
|/disks/endGetAccess/action|Revoca el URI de SAS del disco|
|/snapshots/read|Obtiene las propiedades de una instantánea|
|/snapshots/write|Crea una nueva instantánea o actualiza una ya existente|
|/snapshots/delete|Elimina una instantánea|
|/availabilitySets/read|Obtiene las propiedades de un conjunto de disponibilidad|
|/availabilitySets/write|Crea un nuevo conjunto de disponibilidad o actualiza uno ya existente|
|/availabilitySets/delete|Elimina el conjunto de disponibilidad|
|/availabilitySets/vmSizes/read|Enumera los tamaños disponibles para crear o actualizar una máquina virtual del conjunto de disponibilidad|
|/virtualMachines/read|Obtiene las propiedades de una máquina virtual|
|/virtualMachines/write|Crea una nueva máquina virtual o actualiza una ya existente|
|/virtualMachines/delete|Elimina la máquina virtual|
|/virtualMachines/start/action|Inicia la máquina virtual|
|/virtualMachines/powerOff/action|Apaga la máquina virtual. Tenga en cuenta que se seguirá facturando la máquina virtual.|
|/virtualMachines/redeploy/action|Vuelve a implementar la máquina virtual|
|/virtualMachines/restart/action|Reinicia la máquina virtual|
|/virtualMachines/deallocate/action|Apaga la máquina virtual y libera los recursos de proceso|
|/virtualMachines/generalize/action|Establece el estado de la máquina virtual en Generalizado y prepara la máquina virtual para la captura|
|/virtualMachines/capture/action|Captura la máquina virtual mediante la copia de los discos duros virtuales y genera una plantilla que se puede usar para crear máquinas virtuales similares|
|/virtualMachines/convertToManagedDisks/action|Convierte los discos basados en blobs de la máquina virtual en instancias de Managed Disks|
|/virtualMachines/vmSizes/read|Enumera los tamaños disponibles a los que se puede actualizar la máquina virtual|
|/virtualMachines/instanceView/read|Obtiene el estado detallado en tiempo de ejecución de la máquina virtual y sus recursos|
|/virtualMachines/extensions/read|Obtiene las propiedades de una extensión de máquina virtual|
|/virtualMachines/extensions/write|Crea una nueva extensión de máquina virtual o actualiza una ya existente|
|/virtualMachines/extensions/delete|Elimina la extensión de máquina virtual|
|/locations/vmSizes/read|Enumera los tamaños disponibles de máquina virtual en una ubicación|
|/locations/usages/read|Obtiene los límites de servicio y las cantidades de uso actuales de los recursos de proceso de la suscripción en una ubicación|
|/locations/operations/read|Obtiene el estado de una operación asincrónica|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Operación | Descripción |
|---|---|
|/register/action|Registra la suscripción para el proveedor de recursos del registro de contenedor y habilita la creación de registros de contenedor.|
|/checknameavailability/read|Comprueba que el nombre del registro es válido y que no está en uso.|
|/registries/read|Devuelve la lista de los registros de contenedor u obtiene las propiedades del registro de contenedor especificado.|
|/registries/write|Crea un registro de contenedor con los parámetros especificados o actualiza las propiedades o etiquetas del registro de contenedor especificado.|
|/registries/delete|Elimina un registro de contenedor existente.|
|/registries/listCredentials/action|Enumera las credenciales de inicio de sesión del registro de contenedor especificado.|
|/registries/regenerateCredential/action|Regenera las credenciales de inicio de sesión del registro de contenedor especificado.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Operación | Descripción |
|---|---|
|/containerServices/subscriptions/read|Obtiene los servicios de contenedor especificados según la suscripción|
|/containerServices/resourceGroups/read|Obtiene los servicios de contenedor especificados según el grupo de recursos|
|/containerServices/resourceGroups/ContainerServiceName/read|Obtiene el servicio de contenedor especificado|
|/containerServices/resourceGroups/ContainerServiceName/write|Pone o actualiza el servicio de contenedor especificado|
|/containerServices/resourceGroups/ContainerServiceName/delete|Elimina el servicio de contenedor especificado|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Operación | Descripción |
|---|---|
|/updateCommunicationPreference/action|Actualiza las preferencias de comunicación|
|/listCommunicationPreference/action|Enumera las preferencias de comunicación|
|/applications/read|Lee una operación|
|/applications/write|Escribe una operación|
|/applications/write|Escribe una operación|
|/applications/delete|Elimina una operación|
|/applications/listSecrets/action|Enumera secretos|
|/applications/listSingleSignOnToken/action|Lee tokens de inicio de sesión único|
|/operations/read|operaciones de lectura|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Operación | Descripción |
|---|---|
|/hubs/read|Lee todos los concentradores de Azure Customer Insights|
|/hubs/write|Crea o actualiza todos los concentradores de Azure Customer Insights|
|/hubs/delete|Elimina todos los concentradores de Azure Customer Insights|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles del recurso|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|Obtiene los registros disponibles del recurso|
|/hubs/authorizationPolicies/read|Lee todas las directivas de firma de acceso compartido de Azure Customer Insights|
|/hubs/authorizationPolicies/write|Crea o actualiza todas las directivas de firma de acceso compartido de Azure Customer Insights|
|/hubs/authorizationPolicies/delete|Elimina todas las directivas de firma de acceso compartido de Azure Customer Insights|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Regenera la clave principal de la directiva de firma de acceso compartido de Azure Customer Insights|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Regenera la clave secundaria de la directiva de firma de acceso compartido de Azure Customer Insights|
|/hubs/profiles/read|Lee todos los perfiles de Azure Customer Insights|
|/hubs/profiles/write|Escribe todos los perfiles de Azure Customer Insights|
|/hubs/kpi/read|Lee todos los indicadores clave de rendimiento de Azure Customer Insights|
|/hubs/kpi/write|Crea o actualiza todos los indicadores clave de rendimiento de Azure Customer Insights|
|/hubs/kpi/delete|Elimina todos los indicadores clave de rendimiento de Azure Customer Insights|
|/hubs/views/read|Lee todas vistas de aplicación de Azure Customer Insights|
|/hubs/views/write|Crea o actualiza todas las vistas de aplicación de Azure Customer Insights|
|/hubs/views/delete|Elimina todas las vistas de aplicación de Azure Customer Insights|
|/hubs/interactions/read|Lee todas las interacciones de Azure Customer Insights|
|/hubs/interactions/write|Crea o actualiza todas las interacciones de Azure Customer Insights|
|/hubs/roleAssignments/read|Lee todas las asignaciones de control de acceso basado en rol de Azure Customer Insights|
|/hubs/roleAssignments/write|Crea o actualiza todas las asignaciones de control de acceso basado en rol de Azure Customer Insights|
|/hubs/roleAssignments/delete|Elimina todas las asignaciones de control de acceso basado en rol de Azure Customer Insights|
|/hubs/connectors/read|Lee todos los conectores de Azure Customer Insights|
|/hubs/connectors/write|Crea o actualiza todos los conectores de Azure Customer Insights|
|/hubs/connectors/delete|Elimina todos los conectores de Azure Customer Insights|
|/hubs/connectors/mappings/read|Lee todas las asignaciones de conectores de Azure Customer Insights|
|/hubs/connectors/mappings/write|Crea o actualiza todas las asignaciones de conectores de Azure Customer Insights|
|/hubs/connectors/mappings/delete|Elimina todas las asignaciones de conectores de Azure Customer Insights|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Operación | Descripción |
|---|---|
|/checkNameAvailability/action|Comprueba la disponibilidad del nombre del catálogo del inquilino.|
|/catalogs/read|Obtiene las propiedades de los catálogos de la suscripción o grupo de recursos.|
|/catalogs/write|Crea un catálogo o actualiza las etiquetas y propiedades de este.|
|/catalogs/delete|Elimina el catálogo.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Operación | Descripción |
|---|---|
|/datafactories/read|Lee datos de Data Factory.|
|/datafactories/write|Crea o actualiza una instancia de Data Factory|
|/datafactories/delete|Elimina una instancia de Data Factory.|
|/datafactories/datapipelines/read|Lee la canalización.|
|/datafactories/datapipelines/delete|Elimina la canalización.|
|/datafactories/datapipelines/pause/action|Pausa la canalización.|
|/datafactories/datapipelines/resume/action|Reanuda la canalización.|
|/datafactories/datapipelines/update/action|Actualiza la canalización.|
|/datafactories/datapipelines/write|Crea o actualiza la canalización|
|/datafactories/linkedServices/read|Lee un servicio vinculado.|
|/datafactories/linkedServices/delete|Elimina un servicio vinculado.|
|/datafactories/linkedServices/write|Crea o actualiza un servicio vinculado|
|/datafactories/tables/read|Lee una tabla.|
|/datafactories/tables/delete|Elimina una tabla.|
|/datafactories/tables/write|Crea o actualiza una tabla|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Operación | Descripción |
|---|---|
|/accounts/read|Obtiene información acerca de una cuenta de DataLakeAnalytics.|
|/accounts/write|Crea o actualiza la cuenta de DataLakeAnalytics.|
|/accounts/delete|Elimina la cuenta de DataLakeAnalytics.|
|/accounts/firewallRules/read|Obtiene información acerca de una regla de firewall.|
|/accounts/firewallRules/write|Crea o actualiza una regla de firewall.|
|/accounts/firewallRules/delete|Elimina una regla de firewall.|
|/accounts/storageAccounts/read|Obtiene la cuenta de almacenamiento vinculada de la cuenta de DataLakeAnalytics.|
|/accounts/storageAccounts/write|Vincula una cuenta de almacenamiento vinculada a la cuenta de DataLakeAnalytics.|
|/accounts/storageAccounts/delete|Anula la vinculación de una cuenta de almacenamiento a la cuenta de DataLakeAnalytics.|
|/accounts/storageAccounts/Containers/read|Obtiene los contenedores que están en la cuenta de almacenamiento|
|/accounts/storageAccounts/Containers/listSasTokens/action|Enumera los tokens de SAS del contenedor de almacenamiento|
|/accounts/dataLakeStoreAccounts/read|Obtiene la cuenta vinculada de DataLakeStore de la cuenta de DataLakeAnalytics.|
|/accounts/dataLakeStoreAccounts/write|Vincula una cuenta de DataLakeStore a la cuenta de DataLakeAnalytics.|
|/accounts/dataLakeStoreAccounts/delete|Anula la vinculación de una cuenta de DataLakeStore a la cuenta de DataLakeAnalytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Operación | Descripción |
|---|---|
|/accounts/read|Obtiene información acerca de una cuenta de DataLakeStore existente.|
|/accounts/write|Crea una nueva cuenta de DataLakeStore o actualiza una cuenta de DataLakeStore existente.|
|/accounts/delete|Elimina una cuenta de DataLakeStore existente.|
|/accounts/firewallRules/read|Obtiene información acerca de una regla de firewall.|
|/accounts/firewallRules/write|Crea o actualiza una regla de firewall.|
|/accounts/firewallRules/delete|Elimina una regla de firewall.|
|/accounts/trustedIdProviders/read|Obtiene información acerca de un proveedor de identidades de confianza.|
|/accounts/trustedIdProviders/write|Crea o actualiza un proveedor de identidades de confianza.|
|/accounts/trustedIdProviders/delete|Elimina un proveedor de identidades de confianza.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Operación | Descripción |
|---|---|
|/register/action|Registra la suscripción para el proveedor de recursos de IotHub y habilita la creación de recursos de IotHub|
|/checkNameAvailability/Action|Comprueba si el nombre de la instancia de IotHub está disponible|
|/usages/Read|Obtiene los detalles de uso de la suscripción para este proveedor.|
|/operations/Read|Obtiene todas las operaciones de ResourceProvider|
|/iotHubs/Read|Obtiene los recursos de IotHub|
|/iotHubs/Write|Crea o actualiza los recursos de IotHub|
|/iotHubs/Delete|Elimina los recursos de IotHub|
|/iotHubs/listkeys/Action|Obtiene todas las claves de IotHub|
|/iotHubs/exportDevices/Action|Exporta los dispositivos|
|/iotHubs/importDevices/Action|Importa los dispositivos|
|/IotHubs/metricDefinitions/read|Obtiene las métricas disponibles del servicio IotHub|
|/iotHubs/iotHubKeys/listkeys/Action|Obtiene la clave de IotHub para el nombre especificado|
|/iotHubs/iotHubStats/Read|Obtiene las estadísticas de IotHub|
|/iotHubs/quotaMetrics/Read|Obtiene las métricas de cuota|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Crea un grupo de consumidores de EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Obtiene los grupos de consumidores de EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|Elimina los grupos de consumidores de EventHub|
|/iotHubs/routing/routes/$testall/Action|Prueba un mensaje en todas las rutas existentes|
|/iotHubs/routing/routes/$testnew/Action|Prueba un mensaje en una ruta proporcionada de pruebas|
|/IotHubs/diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/IotHubs/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/iotHubs/skus/Read|Obtiene SKU válidas de IotHub|
|/iotHubs/jobs/Read|Obtiene detalles de los trabajos enviados en una determinada instancia de IotHub|
|/iotHubs/routingEndpointsHealth/Read|Obtiene el estado de todos los puntos de conexión de enrutamientos de una instancia de IotHub|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Operación | Descripción |
|---|---|
|/Subscription/register/action|Registra la suscripción|
|/labs/delete|Elimina laboratorios.|
|/labs/read|Lee laboratorios.|
|/labs/write|Agrega o modifica laboratorios.|
|/labs/ListVhds/action|Enumera las imágenes de disco disponibles para la creación de imágenes personalizadas.|
|/labs/GenerateUploadUri/action|Genera un identificador URI para cargar imágenes de disco personalizadas en un laboratorio.|
|/labs/CreateEnvironment/action|Crea máquinas virtuales en un laboratorio.|
|/labs/ClaimAnyVm/action|Reclama una máquina virtual aleatoria en el laboratorio.|
|/labs/ExportResourceUsage/action|Exporta el uso de recursos del laboratorio a una cuenta de almacenamiento|
|/labs/users/delete|Elimina perfiles de usuario.|
|/labs/users/read|Lee perfiles de usuario.|
|/labs/users/write|Agrega o modifica perfiles de usuario.|
|/labs/users/secrets/delete|Elimina secretos.|
|/labs/users/secrets/read|Lee secretos.|
|/labs/users/secrets/write|Agrega o modifica secretos.|
|/labs/users/environments/delete|Elimina entornos.|
|/labs/users/environments/read|Lee entornos.|
|/labs/users/environments/write|Agrega o modifica entornos.|
|/labs/users/disks/delete|Elimina discos.|
|/labs/users/disks/read|Lee discos.|
|/labs/users/disks/write|Agrega o modifica discos.|
|/labs/users/disks/Attach/action|Adjunta y crea la concesión del disco a la máquina virtual.|
|/labs/users/disks/Detach/action|Desasocia y separa la concesión del disco adjuntado a la máquina virtual.|
|/labs/customImages/delete|Elimina imágenes personalizadas.|
|/labs/customImages/read|Lee imágenes personalizadas.|
|/labs/customImages/write|Agrega o modifica imágenes personalizadas.|
|/labs/serviceRunners/delete|Elimina ejecutores del servicio.|
|/labs/serviceRunners/read|Lee ejecutores del servicio.|
|/labs/serviceRunners/write|Agrega o modifica ejecutores del servicio.|
|/labs/artifactSources/delete|Elimina orígenes de artefacto.|
|/labs/artifactSources/read|Lee orígenes de artefacto.|
|/labs/artifactSources/write|Agrega o modifica orígenes de artefacto.|
|/labs/artifactSources/artifacts/read|Lee artefactos.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Genera una plantilla de ARM para el artefacto en cuestión, carga los archivos necesarios en una cuenta de almacenamiento y valida el artefacto generado.|
|/labs/artifactSources/armTemplates/read|Lee las plantillas de Azure Resource Manager.|
|/labs/costs/read|Lee los costos.|
|/labs/costs/write|Agrega o modifica costos.|
|/labs/virtualNetworks/delete|Elimina redes virtuales.|
|/labs/virtualNetworks/read|Lee redes virtuales.|
|/labs/virtualNetworks/write|Agrega o modifica redes virtuales.|
|/labs/formulas/delete|Elimina fórmulas.|
|/labs/formulas/read|Lee fórmulas.|
|/labs/formulas/write|Agrega o modifica fórmulas.|
|/labs/schedules/delete|Elimina programaciones.|
|/labs/schedules/read|Lee programaciones.|
|/labs/schedules/write|Agrega o modifica programaciones.|
|/labs/schedules/Execute/action|Ejecuta una programación.|
|/labs/schedules/ListApplicable/action|Enumera todas las programaciones aplicables|
|/labs/galleryImages/read|Lee imágenes de la galería.|
|/labs/policySets/EvaluatePolicies/action|Evalúa la directiva de laboratorio.|
|/labs/policySets/policies/delete|Elimina directivas.|
|/labs/policySets/policies/read|Lee directivas.|
|/labs/policySets/policies/write|Agrega o modifica directivas.|
|/labs/virtualMachines/delete|Elimina máquinas virtuales.|
|/labs/virtualMachines/read|Lee máquinas virtuales.|
|/labs/virtualMachines/write|Agrega o modifica máquinas virtuales.|
|/labs/virtualMachines/Start/action|Inicia una máquina virtual.|
|/labs/virtualMachines/Stop/action|Detención de una máquina virtual|
|/labs/virtualMachines/ApplyArtifacts/action|Aplica artefactos a la máquina virtual.|
|/labs/virtualMachines/AddDataDisk/action|Conecta un disco de datos nuevo o existente a una máquina virtual.|
|/labs/virtualMachines/DetachDataDisk/action|Desconecta el disco especificado de la máquina virtual.|
|/labs/virtualMachines/Claim/action|Toma la propiedad de una máquina virtual existente|
|/labs/virtualMachines/ListApplicableSchedules/action|Enumera todas las programaciones aplicables|
|/labs/virtualMachines/schedules/delete|Elimina programaciones.|
|/labs/virtualMachines/schedules/read|Lee programaciones.|
|/labs/virtualMachines/schedules/write|Agrega o modifica programaciones.|
|/labs/virtualMachines/schedules/Execute/action|Ejecuta una programación.|
|/labs/notificationChannels/delete|Elimina notificationchannels.|
|/labs/notificationChannels/read|Lee notificationchannels.|
|/labs/notificationChannels/write|Agrega o modifica notificationchannels.|
|/labs/notificationChannels/Notify/action|Envía una notificación al canal proporcionado.|
|/schedules/delete|Elimina programaciones.|
|/schedules/read|Lee programaciones.|
|/schedules/write|Agrega o modifica programaciones.|
|/schedules/Execute/action|Ejecuta una programación.|
|/schedules/Retarget/action|Actualiza un identificador de recurso de destino de la programación.|
|/locations/operations/read|Lee operaciones.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Operación | Descripción |
|---|---|
|/databaseAccountNames/read|Comprueba la disponibilidad del nombre.|
|/databaseAccounts/read|Lee una cuenta de base de datos.|
|/databaseAccounts/write|Actualiza una cuenta de base de datos.|
|/databaseAccounts/listKeys/action|Enumera las claves de una cuenta de base de datos|
|/databaseAccounts/regenerateKey/action|Rota las claves de una cuenta de base de datos|
|/databaseAccounts/listConnectionStrings/action|Obtiene las cadenas de conexión para una cuenta de base de datos|
|/databaseAccounts/changeResourceGroup/action|Cambia el grupo de recursos de una cuenta de base de datos|
|/databaseAccounts/failoverPriorityChange/action|Cambia las prioridades de conmutación por error de las regiones de una cuenta de base de datos. Esto se utiliza para realizar una operación manual de conmutación por error|
|/databaseAccounts/delete|Elimina las cuentas de base de datos.|
|/databaseAccounts/metricDefinitions/read|Lee las definiciones de métricas de la cuenta de base de datos.|
|/databaseAccounts/metrics/read|Lee las métricas de la cuenta de base de datos.|
|/databaseAccounts/usages/read|Lee los usos de la cuenta de base de datos.|
|/databaseAccounts/databases/collections/metricDefinitions/read|Lee las definiciones de métricas de la colección.|
|/databaseAccounts/databases/collections/metrics/read|Lee las métricas de la colección.|
|/databaseAccounts/databases/collections/usages/read|Lee los usos de la colección.|
|/databaseAccounts/databases/metricDefinitions/read|Lee las definiciones de métrica de la base de datos|
|/databaseAccounts/databases/metrics/read|Lee las métricas de base de datos.|
|/databaseAccounts/databases/usages/read|Lee los usos de la base de datos.|
|/databaseAccounts/readonlykeys/read|Lee las claves de solo lectura de la cuenta de base de datos.|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Operación | Descripción |
|---|---|
|/generateSsoRequest/Action|Genera una solicitud para iniciar sesión en el centro de control de dominios.|
|/validateDomainRegistrationInformation/Action|Valida un objeto de compra de dominio sin enviarlo|
|/checkDomainAvailability/Action|Comprueba si un dominio está disponible para su compra|
|/listDomainRecommendations/Action|Recupera las recomendaciones de dominio de lista en función de las palabras clave|
|/register/action|Registra el proveedor de recursos de Microsoft Domains de la suscripción|
|/domains/Read|Obtiene la lista de dominios|
|/domains/Write|Agrega un nuevo dominio o actualiza uno existente|
|/domains/Delete|Elimina un dominio ya existente.|
|/domains/operationresults/Read|Obtiene una operación de dominio|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Operación | Descripción |
|---|---|
|/lcsprojects/read|Muestra los proyectos de Microsoft Dynamics Lifecycle Services que pertenecen a un usuario|
|/lcsprojects/write|Crea y actualiza los proyectos de Microsoft Dynamics Lifecycle Services que pertenecen al usuario. Solo se pueden actualizar las propiedades de nombre y descripción. No se puede actualizar la suscripción y ubicación asociadas con el proyecto después de la creación|
|/lcsprojects/delete|Elimina los proyectos de Microsoft Dynamics Lifecycle Services que pertenecen al usuario|
|/lcsprojects/clouddeployments/read|Muestra las implementaciones de evaluación de Microsoft Dynamics AX 2012 R3 de un proyecto de Microsoft Dynamics Lifecycle Services que pertenece a un usuario|
|/lcsprojects/clouddeployments/write|Crea las implementaciones de evaluación de Microsoft Dynamics AX 2012 R3 de un proyecto de Microsoft Dynamics Lifecycle Services que pertenece a un usuario. Las implementaciones se pueden administrar desde el portal de administración de Azure|
|/lcsprojects/connectors/read|Lee los conectores que pertenecen a un proyecto de Microsoft Dynamics Lifecycle Services|
|/lcsprojects/connectors/write|Crea y actualiza los conectores que pertenecen a un proyecto de Microsoft Dynamics Lifecycle Services|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Operación | Descripción |
|---|---|
|/checkNameAvailability/action|Comprueba la disponibilidad del espacio de nombres en una suscripción dada.|
|/register/action|Registra la suscripción para el proveedor de recursos de EventHub y habilita la creación de recursos de EventHub|
|/namespaces/write|Crea un recurso de espacio de nombres y actualiza sus propiedades. Las etiquetas y el estado del espacio de nombres son las propiedades que se pueden actualizar.|
|/namespaces/read|Obtiene la lista de descripción del recurso del espacio de nombres|
|/namespaces/Delete|Elimina el recurso del espacio de nombres|
|/namespaces/metricDefinitions/read|Obtiene una lista de descripciones de recursos de métricas del espacio de nombres|
|/namespaces/authorizationRules/read|Obtiene la lista de descripciones de reglas de autorización de espacios de nombres.|
|/namespaces/authorizationRules/write|Crea reglas de autorización en el nivel del espacio de nombres y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/namespaces/authorizationRules/delete|Elimina la regla de autorización del espacio de nombres. No se puede eliminar la regla predeterminada de autorización del espacio de nombres. |
|/namespaces/authorizationRules/listkeys/action|Obtiene la cadena de conexión al espacio de nombres|
|/namespaces/authorizationRules/regenerateKeys/action|Regenera la clave principal o secundaria del recurso|
|/namespaces/eventhubs/write|Crea o actualiza propiedades de EventHub.|
|/namespaces/eventhubs/read|Obtiene una lista de descripciones de recursos de EventHub|
|/namespaces/eventhubs/Delete|Operación para eliminar los recursos de EventHub|
|/namespaces/eventHubs/consumergroups/write|Crea o actualiza las propiedades de ConsumerGroup.|
|/namespaces/eventHubs/consumergroups/read|Obtiene una lista de descripciones de recursos de ConsumerGroup|
|/namespaces/eventHubs/consumergroups/Delete|Operación para eliminar un recurso de ConsumerGroup|
|/namespaces/eventhubs/authorizationRules/read| Obtiene la lista de reglas de autorización de EventHub|
|/namespaces/eventhubs/authorizationRules/write|Crea reglas de autorización de EventHub y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/namespaces/eventhubs/authorizationRules/delete|Operación para eliminar las reglas de autorización de EventHub|
|/namespaces/eventhubs/authorizationRules/listkeys/action|Obtiene la cadena de conexión a EventHub|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|Regenera la clave principal o secundaria del recurso|
|/namespaces/diagnosticSettings/read|Obtiene una lista de descripciones de recursos de configuración de diagnósticos del espacio de nombres|
|/namespaces/diagnosticSettings/write|Obtiene una lista de descripciones de recursos de configuración de diagnósticos del espacio de nombres|
|/namespaces/logDefinitions/read|Obtiene una lista de descripciones de recursos de registros del espacio de nombres|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Operación | Descripción |
|---|---|
|/providers/features/read|Obtiene la característica de una suscripción de un proveedor de recursos determinado.|
|/providers/features/register/action|Registra la característica de una suscripción de un proveedor de recursos determinado.|
|/features/read|Obtiene las características de una suscripción.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Operación | Descripción |
|---|---|
|/clusters/write|Crea o actualiza un clúster de HDInsight|
|/clusters/read|Obtiene detalles sobre el clúster de HDInsight|
|/clusters/delete|Elimina un clúster de HDInsight|
|/clusters/changerdpsetting/action|Cambia la configuración de RDP de un clúster de HDInsight|
|/clusters/configurations/action|Actualiza la configuración de un clúster de HDInsight|
|/clusters/configurations/read|Obtiene las configuraciones de un clúster de HDInsight|
|/clusters/roles/resize/action|Cambia el tamaño de un clúster de HDInsight|
|/locations/capabilities/read|Obtiene las funcionalidades de la suscripción|
|/locations/checkNameAvailability/read|Comprueba la disponibilidad del nombre|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Operación | Descripción |
|---|---|
|/register/action|Registra la suscripción del proveedor de recursos de Import/Export y habilita la creación de trabajos de Import/Export.|
|/jobs/write|Crea un trabajo con los parámetros especificados o actualiza las propiedades o etiquetas del trabajo especificado.|
|/jobs/read|Obtiene las propiedades del trabajo especificado o devuelve la lista de trabajos.|
|/jobs/listBitLockerKeys/action|Obtiene las claves de BitLocker para el trabajo especificado.|
|/jobs/delete|Elimina un trabajo existente.|
|/locations/read|Obtiene las propiedades de la ubicación especificada o devuelve la lista de ubicaciones.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Operación | Descripción |
|---|---|
|/Register/Action|Registra el proveedor de Microsoft Insights|
|/AlertRules/Write|Escribe en una configuración de regla de alerta|
|/AlertRules/Delete|Elimina una configuración de regla de alerta|
|/AlertRules/Read|Lee una configuración de regla de alerta|
|/AlertRules/Activated/Action|Regla de alerta activada|
|/AlertRules/Resolved/Action|Regla de alerta resuelta|
|/AlertRules/Throttled/Action|Se limita la regla de alerta|
|/AlertRules/Incidents/Read|Lee una configuración de incidentes de regla de alerta|
|/MetricDefinitions/Read|Lee definiciones de métricas|
|/eventtypes/values/Read|Lee valores de tipo de evento de administración|
|/eventtypes/digestevents/Read|Lee un resumen de tipos de evento de administración|
|/Metrics/Read|Lee métricas|
|/LogProfiles/Write|Escribe en una configuración de perfil de registro|
|/LogProfiles/Delete|Elimina la configuración de perfiles de registro|
|/LogProfiles/Read|Lee perfiles de registro|
|/AutoscaleSettings/Write|Escribe en una configuración de opciones de escalado automático|
|/AutoscaleSettings/Delete|Elimina una configuración de opciones de escalado automático|
|/AutoscaleSettings/Read|Lee una configuración de opciones de escalado automático|
|/AutoscaleSettings/Scaleup/Action|Operación de escalado vertical de autoescala|
|/AutoscaleSettings/Scaledown/Action|Operación de reducción vertical de autoescala|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|Lee definiciones de métricas|
|/ActivityLogAlerts/Activated/Action|Se desencadena la alerta del registro de actividad|
|/DiagnosticSettings/Write|Escribe la configuración de opciones de diagnóstico|
|/DiagnosticSettings/Delete|Elimina la configuración de opciones de diagnóstico|
|/DiagnosticSettings/Read|Lee una opción de configuración de diagnóstico|
|/LogDefinitions/Read|Lee definiciones de registro|
|/ExtendedDiagnosticSettings/Write|Escribe en la configuración de opciones de diagnóstico extendida|
|/ExtendedDiagnosticSettings/Delete|Elimina la configuración de opciones de diagnóstico extendida|
|/ExtendedDiagnosticSettings/Read|Lee una configuración de opciones de diagnóstico extendida|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Operación | Descripción |
|---|---|
|/register/action|Registra una suscripción|
|/checkNameAvailability/read|Comprueba que un nombre de almacén de claves es válido y que no está en uso|
|/vaults/read|Ve las propiedades de un almacén de claves|
|/vaults/write|Crea un nuevo almacén de claves o actualiza las propiedades de uno que ya existe|
|/vaults/delete|Elimina un almacén de claves|
|/vaults/deploy/action|Permite el acceso a secretos en un almacén de claves al implementar recursos de Azure|
|/vaults/secrets/read|Ve las propiedades de un secreto, pero no su valor|
|/vaults/secrets/write|Crea un secreto nuevo o actualiza el valor de uno que ya existe|
|/vaults/accessPolicies/write|Actualiza una directiva de acceso mediante combinación o sustitución, o agrega una nueva directiva de acceso a un almacén.|
|/deletedVaults/read|Ve las propiedades de los almacenes de claves eliminados temporalmente|
|/locations/operationResults/read|Comprueba el resultado de una operación de larga ejecución|
|/locations/deletedVaults/read|Ve las propiedades de un almacén de claves eliminado temporalmente|
|/locations/deletedVaults/purge/action|Purga un almacén de claves eliminado temporalmente|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Operación | Descripción |
|---|---|
|/workflows/read|Lee el flujo de trabajo.|
|/workflows/write|Crea o actualiza el flujo de trabajo.|
|/workflows/delete|Elimina el flujo de trabajo.|
|/workflows/run/action|Inicia una ejecución del flujo de trabajo.|
|/workflows/disable/action|Deshabilita el flujo de trabajo.|
|/workflows/enable/action|Habilita el flujo de trabajo.|
|/workflows/validate/action|Valida el flujo de trabajo.|
|/workflows/move/action|Mueve el flujo de trabajo desde su identificador de suscripción, grupo de recursos o nombre existentes a un identificador de suscripción, grupo de recursos o nombre diferentes.|
|/workflows/listSwagger/action|Obtiene las definiciones de Swagger del flujo de trabajo.|
|/workflows/regenerateAccessKey/action|Vuelve a generar los secretos de la clave de acceso.|
|/workflows/listCallbackUrl/action|Obtiene la dirección URL de devolución de llamada del flujo de trabajo.|
|/workflows/versions/read|Lee la versión de flujo de trabajo.|
|/workflows/versions/triggers/listCallbackUrl/action|Obtiene la dirección URL de devolución de llamada para el desencadenador.|
|/workflows/runs/read|Lee la ejecución de flujo de trabajo.|
|/workflows/runs/cancel/action|Cancela la ejecución de un flujo de trabajo.|
|/workflows/runs/actions/read|Lee la acción de ejecución de un flujo de trabajo.|
|/workflows/runs/operations/read|Lee el estado de la operación de ejecución de un flujo de trabajo.|
|/workflows/triggers/read|Lee el desencadenador.|
|/workflows/triggers/run/action|Ejecuta el desencadenador.|
|/workflows/triggers/listCallbackUrl/action|Obtiene la dirección URL de devolución de llamada para el desencadenador.|
|/workflows/triggers/histories/read|Lee los historiales del desencadenador.|
|/workflows/triggers/histories/resubmit/action|Vuelve a enviar el desencadenador del flujo de trabajo.|
|/workflows/accessKeys/read|Lee la clave de acceso.|
|/workflows/accessKeys/write|Crea o actualiza la clave de acceso.|
|/workflows/accessKeys/delete|Elimina la clave de acceso.|
|/workflows/accessKeys/list/action|Enumera los secretos de la clave de acceso.|
|/workflows/accessKeys/regenerate/action|Vuelve a generar los secretos de la clave de acceso.|
|/locations/workflows/validate/action|Valida el flujo de trabajo.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Operación | Descripción |
|---|---|
|/register/action|Registra la suscripción del proveedor de recursos del servicio web Machine Learning y habilita la creación de servicios web.|
|/webServices/action|Crea propiedades del servicio web regional las regiones admitidas|
|/commitmentPlans/read|Lee todos los planes de compromiso de Machine Learning|
|/commitmentPlans/write|Crea o actualiza todos los planes de compromiso de Machine Learning|
|/commitmentPlans/delete|Elimina todos los planes de compromiso de Machine Learning|
|/commitmentPlans/join/action|Combina todos los planes de compromiso de Machine Learning|
|/commitmentPlans/commitmentAssociations/read|Lee todas las asociaciones de planes de compromiso de Machine Learning|
|/commitmentPlans/commitmentAssociations/move/action|Mueve todas las asociaciones de planes de compromiso de Machine Learning|
|/Workspaces/read|Lee todas las áreas de trabajo de Machine Learning|
|/Workspaces/write|Crea o actualiza todas las áreas de trabajo de Machine Learning|
|/Workspaces/delete|Elimina todas las áreas de trabajo de Machine Learning|
|/Workspaces/listworkspacekeys/action|Enumera las claves de un área de trabajo de Machine Learning|
|/Workspaces/resyncstoragekeys/action|Vuelve a sincronizar las claves de la cuenta de almacenamiento configurada para un área de trabajo de Machine Learning|
|/webServices/read|Lee todos los servicios web Machine Learning|
|/webServices/write|Crea o actualiza todos los servicios web Machine Learning|
|/webServices/delete|Elimina todos los servicios web Machine Learning|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Operación | Descripción |
|---|---|
|/agreements/offers/plans/read|Devuelve un acuerdo para un elemento determinado de Marketplace|
|/agreements/offers/plans/sign/action|Firma un acuerdo para un elemento determinado de Marketplace|
|/agreements/offers/plans/cancel/action|Cancela un acuerdo para un elemento determinado de Marketplace|

## <a name="microsoftmedia"></a>Microsoft.Media

| Operación | Descripción |
|---|---|
|/mediaservices/read||
|/mediaservices/write||
|/mediaservices/delete||
|/mediaservices/regenerateKey/action||
|/mediaservices/listKeys/action||
|/mediaservices/syncStorageKeys/action||

## <a name="microsoftnetwork"></a>Microsoft.Network

| Operación | Descripción |
|---|---|
|/register/action|Registra la suscripción|
|/unregister/action|Anula el registro de la suscripción|
|/checkTrafficManagerNameAvailability/action|Comprueba la disponibilidad de un nombre DNS relativo de Traffic Manager.|
|/dnszones/read|Obtiene la zona DNS, en formato JSON. Entre las propiedades de la zona se incluyen tags, etag, numberOfRecordSets y maxNumberOfRecordSets. Tenga en cuenta que este comando no recupera los conjuntos de registros dentro de la zona.|
|/dnszones/write|Crea o actualiza una zona DNS en un grupo de recursos.  Se utiliza para actualizar las etiquetas de un recurso de zona DNS. Tenga en cuenta que este comando no se puede usar para crear o actualizar conjuntos de registros dentro de la zona.|
|/dnszones/delete|Elimina la zona DNS, en formato JSON. Entre las propiedades de la zona se incluyen tags, etag, numberOfRecordSets y maxNumberOfRecordSets.|
|/dnszones/MX/read|Obtiene el conjunto de registros del tipo "MX", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag.|
|/dnszones/MX/write|Crea o actualiza un conjunto de registros del tipo "MX" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros.|
|/dnszones/MX/delete|Quita el conjunto de registros de un determinado nombre y del tipo "MX" de una zona DNS.|
|/dnszones/NS/read|Obtiene el conjunto de registros de DNS del tipo NS|
|/dnszones/NS/write|Crea o actualiza el conjunto de registros de DNS del tipo NS|
|/dnszones/NS/delete|Elimina el conjunto de registros de DNS del tipo NS|
|/dnszones/AAAA/read|Obtiene el conjunto de registros del tipo "AAAA", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag.|
|/dnszones/AAAA/write|Crea o actualiza un conjunto de registros del tipo "AAAA" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros.|
|/dnszones/AAAA/delete|Quita el conjunto de registros de un determinado nombre y del tipo "AAAA" de una zona DNS.|
|/dnszones/CNAME/read|Obtiene el conjunto de registros del tipo "CNAME", en formato JSON. El conjunto de registros contiene el TTL, las etiquetas y ETag.|
|/dnszones/CNAME/write|Crea o actualiza un conjunto de registros del tipo "CNAME" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros.|
|/dnszones/CNAME/delete|Quita el conjunto de registros de un determinado nombre y del tipo "CNAME" de una zona DNS.|
|/dnszones/SOA/read|Obtiene el conjunto de registros de DNS del tipo SOA|
|/dnszones/SOA/write|Crea o actualiza el conjunto de registros de DNS del tipo SOA|
|/dnszones/SRV/read|Obtiene el conjunto de registros del tipo "SRV", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag.|
|/dnszones/SRV/write|Crea o actualiza el conjunto de registros del tipo SRV|
|/dnszones/SRV/delete|Quita el conjunto de registros de un determinado nombre y del tipo "SRV" de una zona DNS.|
|/dnszones/PTR/read|Obtiene el conjunto de registros del tipo "PTR", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag.|
|/dnszones/PTR/write|Crea o actualiza un conjunto de registros del tipo "PTR" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros.|
|/dnszones/PTR/delete|Quita el conjunto de registros de un determinado nombre y del tipo "PTR" de una zona DNS.|
|/dnszones/A/read|Obtiene el conjunto de registros del tipo "A", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag.|
|/dnszones/A/write|Crea o actualiza un conjunto de registros del tipo "A" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros.|
|/dnszones/A/delete|Quita el conjunto de registros de un determinado nombre y del tipo "A" de una zona DNS.|
|/dnszones/TXT/read|Obtiene el conjunto de registros del tipo "TXT", en formato JSON. El conjunto de registros contiene una lista de registros, así como el TTL, las etiquetas y ETag.|
|/dnszones/TXT/write|Crea o actualiza un conjunto de registros del tipo "TXT" dentro de una zona DNS. Los registros especificados reemplazarán a los registros actuales en el conjunto de registros.|
|/dnszones/TXT/delete|Quita el conjunto de registros de un determinado nombre y del tipo "TXT" de una zona DNS.|
|/dnszones/recordsets/read|Obtiene los conjuntos de registros de DNS entre los tipos|
|/networkInterfaces/read|Obtiene una definición de interfaz de red. |
|/networkInterfaces/write|Crea una interfaz de red o actualiza una interfaz de red existente. |
|/networkInterfaces/join/action|Une una máquina virtual a una interfaz de red|
|/networkInterfaces/delete|Elimina una interfaz de red|
|/networkInterfaces/effectiveRouteTable/action|Obtiene la tabla de rutas configuradas en la interfaz de red de la máquina virtual|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|Obtiene los grupos de seguridad de red configurados en la interfaz de red de la máquina virtual|
|/networkInterfaces/loadBalancers/read|Obtiene todos los equilibradores de carga de los que forma parte la interfaz de red|
|/networkInterfaces/ipconfigurations/read|Obtiene una definición de configuración de dirección IP de la interfaz de red. |
|/publicIPAddresses/read|Obtiene una definición de la dirección ip pública.|
|/publicIPAddresses/write|Crea una dirección Ip pública o actualiza una que ya existe. |
|/publicIPAddresses/delete|Elimina una dirección IP pública.|
|/publicIPAddresses/join/action|Se une a una dirección IP pública|
|/routeFilters/read|Obtiene una definición de filtro de rutas|
|/routeFilters/join/action|Se une a un filtro de ruta|
|/routeFilters/delete|Elimina una definición de filtro de rutas|
|/routeFilters/write|Crea un filtro de rutas o actualiza uno que ya existe|
|/routeFilters/rules/read|Obtiene una definición de regla de filtro de rutas|
|/routeFilters/rules/write|Crea una regla de filtro de rutas o actualiza una que ya existe|
|/routeFilters/rules/delete|Elimina una definición de regla de filtro de rutas|
|/networkWatchers/read|Obtiene la definición de Network Watcher|
|/networkWatchers/write|Crea una instancia de Network Watcher o actualiza una que ya existe|
|/networkWatchers/delete|Elimina una instancia de Network Watcher|
|/networkWatchers/configureFlowLog/action|Configura el registro de flujos para un recurso de destino.|
|/networkWatchers/ipFlowVerify/action|Devuelve un mensaje indicando si se permite o deniega el paquete en o desde un destino determinado.|
|/networkWatchers/nextHop/action|Para un destino y una dirección IP de destino especificados, devuelve el tipo y la dirección IP del próximo salto.|
|/networkWatchers/queryFlowLogStatus/action|Obtiene el estado del flujo de registros de un recurso.|
|/networkWatchers/queryTroubleshootResult/action|Obtiene el resultado de la operación de solución de problemas ejecutada anteriormente o de la que está actualmente en ejecución.|
|/networkWatchers/securityGroupView/action|Visualiza las reglas de grupos de seguridad de red configuradas y eficaces aplicadas a una máquina virtual.|
|/networkWatchers/topology/action|Obtiene una vista de nivel de red de los recursos y sus relaciones en un grupo de recursos.|
|/networkWatchers/troubleshoot/action|Inicia la solución de problemas en un recurso de redes de Azure.|
|/networkWatchers/packetCaptures/queryStatus/action|Obtiene información acerca de las propiedades y el estado de un recurso de captura de paquetes.|
|/networkWatchers/packetCaptures/stop/action|Detiene la sesión de captura de paquetes en ejecución.|
|/networkWatchers/packetCaptures/read|Obtiene la definición de captura de paquetes|
|/networkWatchers/packetCaptures/write|Crea una captura de paquetes|
|/networkWatchers/packetCaptures/delete|Elimina una captura de paquetes|
|/loadBalancers/read|Obtiene una definición del equilibrador de carga|
|/loadBalancers/write|Crea un equilibrador de carga o actualiza uno que ya existe|
|/loadBalancers/delete|Elimina un equilibrador de carga|
|/loadBalancers/networkInterfaces/read|Obtiene referencias a todas las interfaces de red en un equilibrador de carga|
|/loadBalancers/loadBalancingRules/read|Obtiene una definición de regla de equilibrado de carga del equilibrador de carga|
|/loadBalancers/backendAddressPools/read|Obtiene una definición de grupo de direcciones de back-end del equilibrador de carga|
|/loadBalancers/backendAddressPools/join/action|Se une a un grupo de direcciones de back-end del equilibrador de carga|
|/loadBalancers/inboundNatPools/read|Obtiene una definición de conjuntos NAT de entrada del equilibrador de carga|
|/loadBalancers/inboundNatPools/join/action|Se une a conjuntos NAT de entrada del equilibrador de carga|
|/loadBalancers/inboundNatRules/read|Obtiene una definición de reglas NAT de entrada del equilibrador de carga|
|/loadBalancers/inboundNatRules/write|Crea una regla NAT de entrada del equilibrador de carga o actualiza una que ya existe|
|/loadBalancers/inboundNatRules/delete|Elimina una regla NAT de entrada del equilibrador de carga|
|/loadBalancers/inboundNatRules/join/action|Se une a una regla NAT de entrada del equilibrador de carga|
|/loadBalancers/outboundNatRules/read|Obtiene una definición de reglas NAT de salida del equilibrador de carga|
|/loadBalancers/probes/read|Obtiene un sondeo del equilibrador de carga|
|/loadBalancers/virtualMachines/read|Obtiene referencias a todas las máquinas virtuales de un equilibrador de carga|
|/loadBalancers/frontendIPConfigurations/read|Obtiene una definición de configuración de dirección IP de front-end del equilibrador de carga|
|/trafficManagerGeographicHierarchies/read|Obtiene la jerarquía geográfica de Traffic Manager que contiene las regiones que se pueden usar con el método de enrutamiento del tráfico geográfico|
|/bgpServiceCommunities/read|Obtiene las comunidades de servicio de BGP|
|/applicationGatewayAvailableWafRuleSets/read|Obtiene los conjuntos de reglas WAF disponibles en Application Gateway|
|/virtualNetworks/read|Obtiene la definición de red virtual|
|/virtualNetworks/write|Crea una red virtual o actualiza una que ya existe|
|/virtualNetworks/delete|Elimina una red virtual|
|/virtualNetworks/peer/action|Empareja una red virtual con otra red virtual|
|/virtualNetworks/virtualNetworkPeerings/read|Obtiene una definición de emparejamiento de redes virtuales|
|/virtualNetworks/virtualNetworkPeerings/write|Crea un emparejamiento de redes virtuales o actualiza uno que ya existe|
|/virtualNetworks/virtualNetworkPeerings/delete|Elimina un emparejamiento de redes virtuales|
|/virtualNetworks/subnets/read|Obtiene una definición de subred de red virtual|
|/virtualNetworks/subnets/write|Crea una subred de red virtual o actualiza una que ya existe|
|/virtualNetworks/subnets/delete|Elimina una subred de red virtual|
|/virtualNetworks/subnets/join/action|Se une a una red virtual|
|/virtualNetworks/subnets/joinViaServiceTunnel/action|Une un recurso como una cuenta de almacenamiento o una instancia de SQL Database a una subred habilitada para tunelización de servicio.|
|/virtualNetworks/subnets/virtualMachines/read|Obtiene referencias a todas las máquinas virtuales de una subred de red virtual|
|/virtualNetworks/checkIpAddressAvailability/read|Comprueba si la dirección IP está disponible en la red virtual especificada|
|/virtualNetworks/virtualMachines/read|Obtiene referencias a todas las máquinas virtuales de una red virtual|
|/expressRouteServiceProviders/read|Obtiene los proveedores de servicios de ExpressRoute|
|/dnsoperationresults/read|Obtiene los resultados de una operación DNS|
|/localnetworkgateways/read|Obtiene LocalNetworkGateway|
|/localnetworkgateways/write|Crea o actualiza una LocalNetworkGateway existente|
|/localnetworkgateways/delete|Elimina LocalNetworkGateway|
|/trafficManagerProfiles/read|Obtiene la configuración del perfil de Traffic Manager. Esto incluye la configuración de DNS, la de enrutamiento de tráfico, la de supervisión de puntos de conexión y la lista de puntos de conexión que este perfil de Traffic Manager enruta.|
|/trafficManagerProfiles/write|Crea un perfil de Traffic Manager, o modifica la configuración de un perfil de Traffic Manager que ya existe. Esto incluye la habilitación o deshabilitación de un perfil y la modificación de la configuración de DNS, la de enrutamiento de tráfico o la de supervisión de puntos de conexión. Los puntos de conexión que enruta el perfil de Traffic Manager se pueden agregar, quitar, habilitar o deshabilitar.|
|/trafficManagerProfiles/delete|Elimina el perfil de Traffic Manager. Se perderán todos los valores asociados con el perfil de Traffic Manager y el perfil ya no podrá usarse para enrutar el tráfico.|
|/dnsoperationstatuses/read|Obtiene el estado de una operación DNS |
|/operations/read|Obtiene las operaciones disponibles|
|/expressRouteCircuits/read|Obtiene un ExpressRouteCircuit|
|/expressRouteCircuits/write|Crea o actualiza un ExpressRouteCircuit existente|
|/expressRouteCircuits/delete|Elimina un ExpressRouteCircuit|
|/expressRouteCircuits/stats/read|Obtiene un estado de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/read|Obtiene un emparejamiento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/write|Crea o actualiza un emparejamiento de ExpressRouteCircuit existente|
|/expressRouteCircuits/peerings/delete|Elimina un emparejamiento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/arpTables/action|Obtiene un ArpTable de emparejamiento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/routeTables/action|Obtiene un RouteTable de emparejamiento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/<br>routeTablesSummary/action|Obtiene un resumen de RouteTable de emparejamiento de ExpressRouteCircuit|
|/expressRouteCircuits/peerings/stats/read|Obtiene estadísticas de emparejamiento de ExpressRouteCircuit|
|/expressRouteCircuits/authorizations/read|Obtiene una autorización de ExpressRouteCircuit|
|/expressRouteCircuits/authorizations/write|Crea o actualiza una autorización de ExpressRouteCircuit existente|
|/expressRouteCircuits/authorizations/delete|Elimina una autorización de ExpressRouteCircuit|
|/connections/read|Obtiene VirtualNetworkGatewayConnection|
|/connections/write|Crea o actualiza una VirtualNetworkGatewayConnection existente|
|/connections/delete|Elimina VirtualNetworkGatewayConnection|
|/connections/sharedKey/read|Obtiene el valor de SharedKey de VirtualNetworkGatewayConnection|
|/connections/sharedKey/write|Crea o actualiza una SharedKey de VirtualNetworkGatewayConnection existente|
|/networkSecurityGroups/read|Obtiene una definición de grupo de seguridad de red|
|/networkSecurityGroups/write|Crea un grupo de seguridad de red o actualiza uno que ya existe|
|/networkSecurityGroups/delete|Elimina un grupo de seguridad de red|
|/networkSecurityGroups/join/action|Se une a un grupo de seguridad de red|
|/networkSecurityGroups/defaultSecurityRules/read|Obtiene una definición de regla de seguridad predeterminada|
|/networkSecurityGroups/securityRules/read|Obtiene una definición de regla de seguridad|
|/networkSecurityGroups/securityRules/write|Crea una regla de seguridad o actualiza una que ya existe|
|/networkSecurityGroups/securityRules/delete|Elimina una regla de seguridad|
|/applicationGateways/read|Obtiene una instancia de Application Gateway|
|/applicationGateways/write|Crea una instancia de Application Gateway o actualiza una que ya existe|
|/applicationGateways/delete|Elimina una instancia de Application Gateway|
|/applicationGateways/backendhealth/action|Obtiene el estado de back-end de una instancia de Application Gateway|
|/applicationGateways/start/action|Inicia una instancia de Application Gateway|
|/applicationGateways/stop/action|Detiene una instancia de Application Gateway|
|/applicationGateways/backendAddressPools/join/action|Se une a un grupo de direcciones de back-end de Application Gateway|
|/routeTables/read|Obtiene una definición de tabla de rutas|
|/routeTables/write|Crea una tabla de rutas o actualiza una que ya existe|
|/routeTables/delete|Elimina una definición de tabla de rutas|
|/routeTables/join/action|Se une a una tabla de rutas|
|/routeTables/routes/read|Obtiene una definición de rutas|
|/routeTables/routes/write|Crea una ruta o actualiza una que ya existe|
|/routeTables/routes/delete|Elimina una definición de rutas|
|/locations/operationResults/read|Obtiene el resultado de una operación POST o DELETE asincrónica|
|/locations/checkDnsNameAvailability/read|Comprueba si la etiqueta DNS está disponible en la ubicación especificada|
|/locations/usages/read|Obtiene las métricas de uso de los recursos|
|/locations/operations/read|Obtiene el recurso de operaciones que representa el estado de una operación asincrónica|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Operación | Descripción |
|---|---|
|/register/action|Registra la suscripción para el proveedor de recursos de NotificationHubs y habilita la creación de espacios de nombres y NotificationHubs|
|/CheckNamespaceAvailability/action|Comprueba si un nombre de recurso determinado de espacio de nombres está disponible en el servicio NotificationHub.|
|/Namespaces/write|Crea un recurso de espacio de nombres y actualiza sus propiedades. Las etiquetas y el estado del espacio de nombres son las propiedades que se pueden actualizar.|
|/Namespaces/read|Obtiene la lista de descripción del recurso del espacio de nombres|
|/Namespaces/Delete|Elimina el recurso del espacio de nombres|
|/Namespaces/authorizationRules/action|Obtiene la lista de descripciones de reglas de autorización de espacios de nombres.|
|/Namespaces/CheckNotificationHubAvailability/action|Comprueba si un nombre de NotificationHub determinado está disponible dentro de un espacio de nombres.|
|/Namespaces/authorizationRules/write|Crea reglas de autorización en el nivel del espacio de nombres y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/Namespaces/authorizationRules/read|Obtiene la lista de descripciones de reglas de autorización de espacios de nombres.|
|/Namespaces/authorizationRules/delete|Elimina la regla de autorización del espacio de nombres. No se puede eliminar la regla predeterminada de autorización del espacio de nombres. |
|/Namespaces/authorizationRules/listkeys/action|Obtiene la cadena de conexión al espacio de nombres|
|/Namespaces/authorizationRules/regenerateKeys/action|Namespace Authorization Rule Regenerate Primary/SecondaryKey especifica la clave que se debe regenerar|
|/Namespaces/NotificationHubs/write|Crea un Centro de notificaciones y actualiza sus propiedades. Sus propiedades incluyen principalmente las credenciales PNS. Reglas de autorización y TTL|
|/Namespaces/NotificationHubs/read|Obtiene una lista de descripciones de recursos del Centro de notificaciones|
|/Namespaces/NotificationHubs/Delete|Elimina un recurso del Centro de notificaciones|
|/Namespaces/NotificationHubs/authorizationRules/action|Obtiene la lista de reglas de autorización del Centro de notificaciones|
|/Namespaces/NotificationHubs/pnsCredentials/action|Obtiene todas las credenciales PNS del Centro de notificaciones. Esto incluye las credenciales WNS, MPNS, APNS, GCM y Baidu|
|/Namespaces/NotificationHubs/debugSend/action|Envía una notificación de inserción de prueba.|
|/Namespaces/NotificationHubs/metricDefinitions/read|Obtiene una lista de descripciones de recursos de métricas del espacio de nombres|
|/Namespaces/NotificationHubs/<br>authorizationRules/write|Crea reglas de autorización del Centro de notificaciones y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/Namespaces/NotificationHubs/<br>authorizationRules/read|Obtiene la lista de reglas de autorización del Centro de notificaciones|
|/Namespaces/NotificationHubs/<br>authorizationRules/delete|Elimina las reglas de autorización del Centro de notificaciones|
|/Namespaces/NotificationHubs/<br>authorizationRules/listkeys/action|Obtiene la cadena de conexión al Centro de notificaciones|
|/Namespaces/NotificationHubs/<br>authorizationRules/regenerateKeys/action|Notification Hub Authorization Rule Regenerate Primary/SecondaryKey especifica la clave que se debe regenerar|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Operación | Descripción |
|---|---|
|/register/action|Registra una suscripción en un proveedor de recursos.|
|/linkTargets/read|Enumera las cuentas existentes que no están asociadas a una suscripción de Azure. Para vincular esta suscripción de Azure a un área de trabajo, use un identificador de cliente devuelto por esta operación en la propiedad correspondiente de la operación Create Workspace.|
|/workspaces/write|Crea una nueva área de trabajo o vincula a un área de trabajo que ya existe proporcionando el identificador de cliente de esta.|
|/workspaces/read|Obtiene un área de trabajo existente|
|/workspaces/delete|Elimina un área de trabajo. Si el área de trabajo se vinculó a un área de trabajo ya existente en el momento de la creación, no se eliminará el área de trabajo a la que está vinculado.|
|/workspaces/generateregistrationcertificate/action|Genera el registro de certificado para el área de trabajo. Este certificado se usa para conectar Microsoft System Center Operation Manager al área de trabajo.|
|/workspaces/sharedKeys/action|Recupera las claves compartidas del área de trabajo. Estas claves se utilizan para conectar los agentes de Microsoft Operational Insights al área de trabajo.|
|/workspaces/search/action|Ejecuta una consulta de búsqueda|
|/workspaces/datasources/read|Obtiene los orígenes de datos en un área de trabajo.|
|/workspaces/datasources/write|Crea o actualiza los orígenes de datos en un área de trabajo.|
|/workspaces/datasources/delete|Elimina los orígenes de datos en un área de trabajo.|
|/workspaces/managementGroups/read|Obtiene los nombres y metadatos de los grupos de administración de System Center Operations Manager conectados a esta área de trabajo.|
|/workspaces/schema/read|Obtiene el esquema de búsqueda del área de trabajo.  El esquema de búsqueda incluye los campos expuestos y sus tipos.|
|/workspaces/usages/read|Obtiene los datos de uso de un área de trabajo incluida la cantidad de datos que esta lee.|
|/workspaces/intelligencepacks/read|Enumera todos los Intelligence Packs que están visibles para un área de trabajo determinada y también muestra si el paquete está habilitado o deshabilitado para esa área de trabajo.|
|/workspaces/intelligencepacks/enable/action|Habilita un Intelligence Pack para un área de trabajo determinado.|
|/workspaces/intelligencepacks/disable/action|Deshabilita un Intelligence Pack para un área de trabajo determinado.|
|/workspaces/sharedKeys/read|Recupera las claves compartidas del área de trabajo. Estas claves se utilizan para conectar los agentes de Microsoft Operational Insights al área de trabajo.|
|/workspaces/savedSearches/read|Obtiene una consulta de búsqueda guardada|
|/workspaces/savedSearches/write|Crea una consulta de búsqueda guardada|
|/workspaces/savedSearches/delete|Elimina una consulta de búsqueda guardada|
|/workspaces/storageinsightconfigs/write|Crea una nueva configuración de almacenamiento. Estas configuraciones se usan para extraer datos de una ubicación de una cuenta de almacenamiento existente.|
|/workspaces/storageinsightconfigs/read|Obtiene una configuración de almacenamiento.|
|/workspaces/storageinsightconfigs/delete|Elimina una configuración de almacenamiento. Esto impide a Microsoft Operational Insights leer los datos de la cuenta de almacenamiento.|
|/workspaces/configurationScopes/read|Obtiene el ámbito de configuración|
|/workspaces/configurationScopes/write|Establece el ámbito de configuración|
|/workspaces/configurationScopes/delete|Elimina el ámbito de configuración|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Operación | Descripción |
|---|---|
|/register/action|Registra una suscripción en un proveedor de recursos.|
|/solutions/write|Crea una nueva solución OMS|
|/solutions/read|Obtiene una solución OMS ya existente|
|/solutions/delete|Elimina una solución OMS ya existente|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Operación | Descripción |
|---|---|
|/Vaults/backupJobsExport/action|Exporta trabajos|
|/Vaults/write|La operación Create Vault crea un recurso de Azure del tipo "almacén"|
|/Vaults/read|La operación Get Vault obtiene un objeto que representa el recurso de Azure del tipo "almacén"|
|/Vaults/delete|La operación Delete Vault elimina el recurso de Azure del tipo "almacén" especificado|
|/Vaults/refreshContainers/read|Actualiza la lista de contenedores|
|/Vaults/backupJobsExport/operationResults/read|Devuelve el resultado de la operación del trabajo de exportación.|
|/Vaults/backupOperationResults/read|Devuelve el resultado de la operación de Backup para el almacén de Recovery Services.|
|/Vaults/monitoringAlerts/read|Obtiene las alertas del almacén de Recovery Services.|
|/Vaults/monitoringAlerts/{uniqueAlertId}/read|Obtiene los detalles de la alerta.|
|/Vaults/backupSecurityPIN/read|Devuelve la información del PIN de seguridad del almacén de Recovery Services.|
|/vaults/replicationEvents/read|Lee todos los eventos|
|/Vaults/backupProtectableItems/read|Devuelve una lista de todos los elementos que se pueden proteger.|
|/vaults/replicationFabrics/read|Lee todos los tejidos|
|/vaults/replicationFabrics/write|Crea o actualiza todos los tejidos|
|/vaults/replicationFabrics/remove/action|Quita el tejido|
|/vaults/replicationFabrics/checkConsistency/action|Comprueba la coherencia del tejido|
|/vaults/replicationFabrics/delete|Elimina todos los tejidos|
|/vaults/replicationFabrics/renewcertificate/action||
|/vaults/replicationFabrics/deployProcessServerImage/action|Implementa la imagen del servidor de proceso|
|/vaults/replicationFabrics/reassociateGateway/action|Vuelve a asociar la puerta de enlace|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>read|Lee todos los proveedores de Recovery Services|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>remove/action|Quita los proveedores de Recovery Services|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>delete|Elimina todos los proveedores de Recovery Services|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>refreshProvider/action|Actualiza el proveedor|
|/vaults/replicationFabrics/replicationStorageClassifications/read|Lee todas las clasificaciones de almacenamiento|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/read|Lee todas las asignaciones de clasificaciones de almacenamiento|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/write|Crea o actualiza todas las asignaciones de clasificaciones de almacenamiento|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/delete|Elimina todas las asignaciones de clasificaciones de almacenamiento|
|/vaults/replicationFabrics/replicationvCenters/read|Lee todos los trabajos|
|/vaults/replicationFabrics/replicationvCenters/write|Crea o actualiza todos los trabajos|
|/vaults/replicationFabrics/replicationvCenters/delete|Elimina todos los trabajos|
|/vaults/replicationFabrics/replicationNetworks/read|Lee todas las redes|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/read|Lee todas las asignaciones de redes|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/write|Crea o actualiza todas las asignaciones de redes|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/delete|Elimina todas las asignaciones de redes|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>read|Lee todos los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>discoverProtectableItem/action|Detecta los elementos que se pueden proteger|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>write|Crea o actualiza todos los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>remove/action|Elimina los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>switchprotection/action|Cambia los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectableItems/read|Lee todos los elementos que se pueden proteger|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/read|Lee todas las asignaciones de los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/write|Crea o actualiza todas las asignaciones de los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/remove/action|Quita las asignaciones de los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/delete|Elimina todas las asignaciones de los contenedores de protección|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/read|Lee todos los elementos protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/write|Crea o actualiza todos los elementos protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/delete|Elimina todos los elementos protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/remove/action|Quita los elementos protegidos|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/plannedFailover/action|Conmutación por error planeada|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/unplannedFailover/action|Conmutación por error|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailover/action|Test Failover|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailoverCleanup/action|Prueba la limpieza de la conmutación por error|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/failoverCommit/action|Confirma la conmutación por error|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/reProtect/action|Vuelva a proteger el elemento protegido|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/updateMobilityService/action|Actualiza Mobility Service|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/repairReplication/action|Repara una replicación|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/applyRecoveryPoint/action|Aplica un punto de recuperación|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/recoveryPoints/read|Lee los puntos de recuperación de todas las replicaciones|
|/vaults/replicationPolicies/read|Lee todas las directivas|
|/vaults/replicationPolicies/write|Crea o actualiza todas las directivas|
|/vaults/replicationPolicies/delete|Elimina todas las directivas|
|/vaults/replicationRecoveryPlans/read|Lee todos los planes de recuperación|
|/vaults/replicationRecoveryPlans/write|Crea o actualiza todos los planes de recuperación|
|/vaults/replicationRecoveryPlans/delete|Elimina todos los planes de recuperación|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Plan de recuperación de conmutación por error planeado|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Plan de recuperación de conmutación por error|
|/vaults/replicationRecoveryPlans/testFailover/action|Prueba el plan de recuperación de conmutación por error|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Prueba el plan de recuperación de limpieza de la conmutación por error|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Plan de recuperación de confirmación de la conmutación por error|
|/vaults/replicationRecoveryPlans/reProtect/action|Vuelve a proteger el plan de recuperación|
|/Vaults/extendedInformation/read|La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén?|
|/Vaults/extendedInformation/write|La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén?|
|/Vaults/extendedInformation/delete|La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén?|
|/Vaults/backupManagementMetaData/read|Devuelve los metadatos de administración de Backup para el almacén de Recovery Services.|
|/Vaults/backupProtectionContainers/read|Devuelve todos los contenedores que pertenecen a la suscripción|
|/Vaults/backupFabrics/operationResults/read|Devuelve el estado de la operación|
|/Vaults/backupFabrics/protectionContainers/read|Devuelve todos los contenedores registrados|
|/Vaults/backupFabrics/protectionContainers/<br>operationResults/read|Obtiene los resultados de la operación realizada en el contenedor de protección.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/read|Devuelve detalles de objeto del elemento protegido|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/write|Crea un elemento protegido de copia de seguridad|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/delete|Elimina los elementos protegidos|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/backup/action|Hace una copia de seguridad del elemento protegido.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationResults/read|Obtiene el resultado de la operación realizada en los elementos protegidos.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationStatus/read|Devuelve el estado de la operación realizada en los elementos protegidos.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/read|Obtiene los puntos de recuperación de los elementos protegidos.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>restore/action|Restaura los puntos de recuperación de los elementos protegidos.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>provisionInstantItemRecovery/action|Aprovisiona una recuperación de elementos instantánea para los elementos protegidos|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>revokeInstantItemRecovery/action|Revoca la recuperación de elementos instantánea para los elementos protegidos|
|/Vaults/usages/read|Devuelve los detalles de uso de un almacén de Recovery Services.|
|/vaults/usages/read|Lee todos los usos de almacén|
|/Vaults/certificates/write|La operación Actualizar certificado de recursos permite actualizar el certificado de credencial de recursos o almacenes.|
|/Vaults/tokenInfo/read|Devuelve la información del token del almacén de Recovery Services.|
|/vaults/replicationAlertSettings/read|Lee la configuración de todas las alertas|
|/vaults/replicationAlertSettings/write|Crea o actualiza la configuración de todas las alertas|
|/Vaults/backupOperations/read|Devuelve el estado de la operación de Backup para el almacén de Recovery Services.|
|/Vaults/storageConfig/read|Devuelve la configuración de almacenamiento del almacén de Recovery Services.|
|/Vaults/storageConfig/write|Actualiza la configuración de almacenamiento del almacén de Recovery Services.|
|/Vaults/backupUsageSummaries/read|Devuelve resúmenes de los elementos y servidores protegidos para un almacén de Recovery Services.|
|/Vaults/backupProtectedItems/read|Devuelve la lista de todos los elementos protegidos.|
|/Vaults/backupconfig/vaultconfig/read|Devuelve la configuración del almacén de Recovery Services.|
|/Vaults/backupconfig/vaultconfig/write|Actualiza la configuración del almacén de Recovery Services.|
|/Vaults/registeredIdentities/write|La operación Registrar contenedor de servicios se puede usar para registrar un contenedor con servicio de recuperación.|
|/Vaults/registeredIdentities/read|La operación Obtener contenedores se puede usar para obtener los contenedores registrados para un recurso.|
|/Vaults/registeredIdentities/delete|La operación Anular registro de contenedor se puede usar para eliminar el registro de un contenedor.|
|/Vaults/registeredIdentities/operationResults/read|La operación Obtener resultados de la operación se puede usar para obtener el estado y el resultado de la operación enviada de forma asincrónica|
|/vaults/replicationJobs/read|Lee todos los trabajos|
|/vaults/replicationJobs/cancel/action|Cancela un trabajo|
|/vaults/replicationJobs/restart/action|Reinicia un trabajo|
|/vaults/replicationJobs/resume/action|Reanuda un trabajo|
|/Vaults/backupPolicies/read|Devuelve todas las directivas de protección|
|/Vaults/backupPolicies/write|Crea una directiva de protección|
|/Vaults/backupPolicies/delete|Elimina una directiva de protección|
|/Vaults/backupPolicies/operationResults/read|Obtiene los resultados de la operación de directiva.|
|/Vaults/backupPolicies/operationStatus/read|Obtiene el estado de la operación de directiva.|
|/Vaults/vaultTokens/read|La operación Token de almacén se puede usar para obtener el token de almacén de las operaciones back-end a nivel de almacén.|
|/Vaults/monitoringConfigurations/notificationConfiguration/read|Obtiene la configuración de notificaciones del almacén de Recovery Services.|
|/Vaults/backupJobs/read|Devuelve todos los objetos de trabajo|
|/Vaults/backupJobs/cancel/action|Cancela el trabajo|
|/Vaults/backupJobs/operationResults/read|Devuelve el resultado de la operación de trabajo.|
|/locations/allocateStamp/action|AllocateStamp es una operación interna que el servicio usa|
|/locations/allocatedStamp/read|GetAllocatedStamp es una operación interna que el servicio usa|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Operación | Descripción |
|---|---|
|/checkNamespaceAvailability/action|Comprueba la disponibilidad del espacio de nombres en una suscripción dada.|
|/register/action|Registra la suscripción para el proveedor de recursos de Relay y habilita la creación de recursos de Relay|
|/namespaces/write|Crea un recurso de espacio de nombres y actualiza sus propiedades. Las etiquetas y el estado del espacio de nombres son las propiedades que se pueden actualizar.|
|/namespaces/read|Obtiene la lista de descripción del recurso del espacio de nombres|
|/namespaces/Delete|Elimina el recurso del espacio de nombres|
|/namespaces/authorizationRules/write|Crea reglas de autorización en el nivel del espacio de nombres y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/namespaces/authorizationRules/delete|Elimina la regla de autorización del espacio de nombres. No se puede eliminar la regla predeterminada de autorización del espacio de nombres. |
|/namespaces/authorizationRules/listkeys/action|Obtiene la cadena de conexión al espacio de nombres|
|/namespaces/HybridConnections/write|Crea o actualiza las propiedades de HybridConnection.|
|/namespaces/HybridConnections/read|Obtiene una lista de descripciones de recursos de HybridConnection|
|/namespaces/HybridConnections/Delete|Operación para eliminar el recurso de HybridConnection|
|/namespaces/HybridConnections/authorizationRules/write|Crea reglas de autorización de HybridConnection y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/namespaces/HybridConnections/authorizationRules/delete|Operación para eliminar las reglas de autorización de HybridConnection|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|Obtiene la cadena de conexión a HybridConnection|
|/namespaces/WcfRelays/write|Crea o actualiza las propiedades de WcfRelay.|
|/namespaces/WcfRelays/read|Obtiene una lista de descripciones de recursos de WcfRelay|
|/namespaces/WcfRelays/Delete|Operación para eliminar los recursos de WcfRelay|
|/namespaces/WcfRelays/authorizationRules/write|Crea reglas de autorización de WcfRelay y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/namespaces/WcfRelays/authorizationRules/delete|Operación para eliminar las reglas de autorización de WcfRelay|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|Obtiene la cadena de conexión a WcfRelay|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Operación | Descripción |
|---|---|
|/AvailabilityStatuses/read|Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado|
|/AvailabilityStatuses/current/read|Obtiene el estado de disponibilidad del recurso especificado|

## <a name="microsoftresources"></a>Microsoft.Resources

| Operación | Descripción |
|---|---|
|/checkResourceName/action|Comprueba la validez del nombre de recurso.|
|/providers/read|Obtiene la lista de proveedores.|
|/subscriptions/read|Obtiene la lista de suscripciones.|
|/subscriptions/operationresults/read|Obtiene los resultados de la operación de suscripción.|
|/subscriptions/providers/read|Obtiene o enumera los proveedores de recursos.|
|/subscriptions/tagNames/read|Obtiene o enumera las etiquetas de suscripción.|
|/subscriptions/tagNames/write|Agrega una etiqueta de suscripción.|
|/subscriptions/tagNames/delete|Elimina una etiqueta de suscripción.|
|/subscriptions/tagNames/tagValues/read|Obtiene o enumera los valores de las etiquetas de suscripción.|
|/subscriptions/tagNames/tagValues/write|Agrega un valor de etiqueta de suscripción.|
|/subscriptions/tagNames/tagValues/delete|Elimina el valor de una etiqueta de suscripción.|
|/subscriptions/resources/read|Obtiene recursos de una suscripción.|
|/subscriptions/resourceGroups/read|Obtiene o enumera los grupos de recursos.|
|/subscriptions/resourceGroups/write|Crea o actualiza un grupo de recursos.|
|/subscriptions/resourceGroups/delete|Elimina un grupo de recursos y todos sus recursos.|
|/subscriptions/resourceGroups/moveResources/action|Mueve recursos de un grupo de recursos a otro.|
|/subscriptions/resourceGroups/validateMoveResources/action|Valida el movimiento de recursos de un grupo de recursos a otro.|
|/subscriptions/resourcegroups/resources/read|Obtiene los recursos del grupo de recursos.|
|/subscriptions/resourcegroups/deployments/read|Obtiene o enumera implementaciones.|
|/subscriptions/resourcegroups/deployments/write|Crea o actualiza una implementación.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|Obtiene o enumera los estados de la operación de implementación.|
|/subscriptions/resourcegroups/deployments/operations/read|Obtiene o enumera las operaciones de implementación.|
|/subscriptions/locations/read|Obtiene la lista de ubicaciones admitidas.|
|/links/read|Obtiene o enumera los vínculos de recursos.|
|/links/write|Crea o actualiza un vínculo de recursos.|
|/links/delete|Elimina un vínculo de recursos.|
|/tenants/read|Obtiene la lista de inquilinos.|
|/resources/read|Obtiene la lista de recursos en función de los filtros.|
|/deployments/read|Obtiene o enumera implementaciones.|
|/deployments/write|Crea o actualiza una implementación.|
|/deployments/delete|Elimina una implementación.|
|/deployments/cancel/action|Cancela una implementación.|
|/deployments/validate/action|Valida una implementación.|
|/deployments/operations/read|Obtiene o enumera las operaciones de implementación.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Operación | Descripción |
|---|---|
|/jobcollections/read|Obtiene una colección de trabajos|
|/jobcollections/write|Crea o actualiza una colección de trabajos.|
|/jobcollections/delete|Elimina una colección de trabajos.|
|/jobcollections/enable/action|Habilita una colección de trabajos.|
|/jobcollections/disable/action|Deshabilita una colección de trabajos.|
|/jobcollections/jobs/read|Obtiene un trabajo.|
|/jobcollections/jobs/write|Crea o actualiza el trabajo.|
|/jobcollections/jobs/delete|Elimina el trabajo.|
|/jobcollections/jobs/run/action|Ejecuta el trabajo.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Genera una definición de Logic Apps basada en un trabajo de Scheduler.|
|/jobcollections/jobs/jobhistories/read|Obtiene el historial de trabajos.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Operación | Descripción |
|---|---|
|/register/action|Registra la suscripción para el proveedor de recursos de búsqueda y habilita la creación de servicios de búsqueda.|
|/checkNameAvailability/action|Comprueba la disponibilidad del nombre del servicio.|
|/searchServices/write|Crea o actualiza el servicio de búsqueda.|
|/searchServices/read|Lee el servicio de búsqueda.|
|/searchServices/delete|Elimina el servicio de búsqueda.|
|/searchServices/start/action|Inicia el servicio de búsqueda.|
|/searchServices/stop/action|Detiene el servicio de búsqueda.|
|/searchServices/listAdminKeys/action|Lee las claves de administración.|
|/searchServices/regenerateAdminKey/action|Regenera la clave de administración.|
|/searchServices/createQueryKey/action|Crea la clave de consulta.|
|/searchServices/queryKey/read|Lee las claves de consulta.|
|/searchServices/queryKey/delete|Elimina las claves de consulta.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Operación | Descripción |
|---|---|
|/jitNetworkAccessPolicies/read|Obtiene las directivas just-in-time de acceso de red|
|/jitNetworkAccessPolicies/write|Crea una nueva directiva just-in-time de acceso de red o actualiza una que ya existe|
|/jitNetworkAccessPolicies/initiate/action|Inicia una directiva de acceso de red just-in-time|
|/securitySolutionsReferenceData/read|Obtiene los datos de referencia de las soluciones de seguridad|
|/securityStatuses/read|Obtiene el estado de seguridad de los recursos de Azure|
|/webApplicationFirewalls/read|Obtiene los firewalls de aplicaciones web|
|/webApplicationFirewalls/write|Crea un nuevo firewall de aplicaciones web o actualiza uno que ya existe|
|/webApplicationFirewalls/delete|Elimina un firewall de aplicaciones web|
|/securitySolutions/read|Obtiene las soluciones de seguridad|
|/securitySolutions/write|Crea una nueva solución de seguridad o actualiza una ya existente|
|/securitySolutions/delete|Elimina una solución de seguridad|
|/tasks/read|Obtiene todas las recomendaciones de seguridad disponibles|
|/tasks/dismiss/action|Descarta una recomendación de seguridad|
|/tasks/activate/action|Activa una recomendación de seguridad|
|/policies/read|Obtiene la directiva de seguridad|
|/policies/write|Actualiza la directiva de seguridad|
|/applicationWhitelistings/read|Obtiene la lista de permitidos de la aplicación|
|/applicationWhitelistings/write|Crea una nueva lista de permitidos de la aplicación o actualiza una ya existente|

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

| Operación | Descripción |
|---|---|
|/subscriptions/write|Crea o actualiza una suscripción|
|/gateways/write|Crea o actualiza una puerta de enlace|
|/gateways/delete|Elimina una puerta de enlace|
|/gateways/read|Obtiene una puerta de enlace|
|/gateways/regenerateprofile/action|Regenera el perfil de la puerta de enlace|
|/gateways/upgradetolatest/action|Actualiza la puerta de enlace a la versión más reciente|
|/nodes/write|Crea o actualiza un nodo|
|/nodes/delete|Elimina un nodo|
|/nodes/read|Obtiene un nodo|
|/sessions/write|Crea o actualiza una sesión|
|/sessions/read|Obtiene una sesión|
|/sessions/delete|Elimina una sesión|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Operación | Descripción |
|---|---|
|/checkNameAvailability/action|Comprueba la disponibilidad del espacio de nombres en una suscripción dada.|
|/register/action|Registra la suscripción para el proveedor de recursos de ServiceBus y habilita la creación de recursos de ServiceBus|
|/namespaces/write|Crea un recurso de espacio de nombres y actualiza sus propiedades. Las etiquetas y el estado del espacio de nombres son las propiedades que se pueden actualizar.|
|/namespaces/read|Obtiene la lista de descripción del recurso del espacio de nombres|
|/namespaces/Delete|Elimina el recurso del espacio de nombres|
|/namespaces/metricDefinitions/read|Obtiene una lista de descripciones de recursos de métricas del espacio de nombres|
|/namespaces/authorizationRules/write|Crea reglas de autorización en el nivel del espacio de nombres y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/namespaces/authorizationRules/read|Obtiene la lista de descripciones de reglas de autorización de espacios de nombres.|
|/namespaces/authorizationRules/delete|Elimina la regla de autorización del espacio de nombres. No se puede eliminar la regla predeterminada de autorización del espacio de nombres. |
|/namespaces/authorizationRules/listkeys/action|Obtiene la cadena de conexión al espacio de nombres|
|/namespaces/authorizationRules/regenerateKeys/action|Regenera la clave principal o secundaria del recurso|
|/namespaces/diagnosticSettings/read|Obtiene una lista de descripciones de recursos de configuración de diagnósticos del espacio de nombres|
|/namespaces/diagnosticSettings/write|Obtiene una lista de descripciones de recursos de configuración de diagnósticos del espacio de nombres|
|/namespaces/queues/write|Crea o actualiza las propiedades de Queue.|
|/namespaces/queues/read|Obtiene una lista de descripciones de recursos de Queue|
|/namespaces/queues/Delete|Operación para eliminar los recursos de Queue|
|/namespaces/queues/authorizationRules/write|Crea reglas de autorización de Queue y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/namespaces/queues/authorizationRules/read| Obtiene la lista de reglas de autorización de Queue|
|/namespaces/queues/authorizationRules/delete|Operación para eliminar las reglas de autorización de Queue|
|/namespaces/queues/authorizationRules/listkeys/action|Obtiene la cadena de conexión a Queue|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Regenera la clave principal o secundaria del recurso|
|/namespaces/logDefinitions/read|Obtiene una lista de descripciones de recursos de registros del espacio de nombres|
|/namespaces/topics/write|Crea o actualiza las propiedades de tema.|
|/namespaces/topics/read|Obtiene una lista de descripciones de recursos de tema|
|/namespaces/topics/Delete|Operación para eliminar los recursos de un tema|
|/namespaces/topics/authorizationRules/write|Crea reglas de autorización de temas y actualiza sus propiedades. Se pueden actualizar los derechos de acceso de las reglas de autorización, la clave principal y la clave secundaria.|
|/namespaces/topics/authorizationRules/read| Obtiene la lista de reglas de autorización de temas|
|/namespaces/topics/authorizationRules/delete|Operación para eliminar las reglas de autorización de temas|
|/namespaces/topics/authorizationRules/listkeys/action|Obtiene la cadena de conexión al tema|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Regenera la clave principal o secundaria del recurso|
|/namespaces/topics/subscriptions/write|Crear o actualizar las propiedades de TopicSubscription.|
|/namespaces/topics/subscriptions/read|Obtiene una lista de descripciones de recursos de TopicSubscription|
|/namespaces/topics/subscriptions/Delete|Operación para eliminar los recursos de TopicSubscription|
|/namespaces/topics/subscriptions/rules/write|Crea o actualiza las propiedades de reglas.|
|/namespaces/topics/subscriptions/rules/read|Obtiene una lista de descripciones de recursos de reglas|
|/namespaces/topics/subscriptions/rules/Delete|Operación para eliminar los recursos de reglas|

## <a name="microsoftsql"></a>Microsoft.Sql

| Operación | Descripción |
|---|---|
|/servers/read|Devuelve una lista de servidores de un grupo de recursos en una suscripción|
|/servers/write|Crea un servidor nuevo o modifica las propiedades del servidor existente en un grupo de recursos de una suscripción|
|/servers/delete|Elimina un servidor y todas las bases de datos y grupos elásticos que contiene|
|/servers/import/action|Crea una base de datos nueva en el servidor e implementa el esquema y los datos de un paquete DacPac|
|/servers/upgrade/action|Habilita las nuevas funcionalidades disponibles en la versión más reciente del servidor y especifica la asignación de conversión de edición de las bases de datos|
|/servers/VulnerabilityAssessmentScans/action|Ejecuta un análisis del servidor de evaluación de vulnerabilidad|
|/servers/operationResults/read|Esta operación se usa para seguir el progreso de la actualización del servidor desde la versión inferior a la superior|
|/servers/operationResults/delete|Anula la actualización de la versión del servidor en curso|
|/servers/securityAlertPolicies/read|Recupera detalles de la directiva de detección de amenazas del servidor configurada en un servidor determinado|
|/servers/securityAlertPolicies/write|Cambia la detección de amenazas del servidor para un servidor determinado|
|/servers/securityAlertPolicies/operationResults/read|Recupera los resultados de la operación de establecimiento de directivas de detección de amenazas del servidor|
|/servers/administrators/read|Recupera los detalles del administrador del servidor|
|/servers/administrators/write|Crea o actualiza el administrador del servidor|
|/servers/administrators/delete|Elimina el administrador del servidor del servidor|
|/servers/recoverableDatabases/read|Esta operación se usa para la recuperación ante desastres de una base de datos activa para restaurar la base de datos al último punto bueno de copia de seguridad conocido. Devuelve información acerca de la última copia de seguridad buena pero no restaura realmente la base de datos.|
|/servers/serviceObjectives/read|Recupera la lista de objetivos de nivel de servicio (también conocida como niveles de rendimiento) disponible en un servidor determinado|
|/servers/firewallRules/read|Recupera los detalles de regla de firewall del servidor|
|/servers/firewallRules/write|Crea o actualiza una regla de firewall del servidor que controla el intervalo de direcciones IP a las que se permite conectarse al servidor|
|/servers/firewallRules/delete|Elimina una regla de firewall del servidor|
|/servers/administratorOperationResults/read|Recupera los resultados de la operación del administrador del servidor|
|/servers/recommendedElasticPools/read|Recupera la recomendación para los grupos de bases de datos elásticas para reducir los costos o mejorar el rendimiento en función del historial de uso de los recursos|
|/servers/recommendedElasticPools/metrics/read|Recupera las métricas para los grupos de bases de datos elásticas recomendados para un servidor determinado|
|/servers/recommendedElasticPools/databases/read|Recupera bases de datos que deben agregarse a grupos de bases de datos elásticas recomendados para un servidor determinado|
|/servers/elasticPools/read|Recupera los detalles del grupo de bases de datos elásticas de un servidor determinado|
|/servers/elasticPools/write|Crea un nuevo grupo de bases de datos elásticas o cambia las propiedades de uno que ya existe|
|/servers/elasticPools/delete|Elimina un grupo de bases de datos elásticas que ya existe|
|/servers/elasticPools/operationResults/read|Recupera los detalles de una operación de un grupo de bases de datos elásticas determinado|
|/servers/elasticPools/providers/Microsoft.Insights/<br>metricDefinitions/read|Devuelve los tipos de métricas que están disponibles para grupos de bases de datos elásticas|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/servers/elasticPools/metrics/read|Devuelve las métricas de utilización de recursos de un grupo de bases de datos elásticas|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Recupera las actividades y detalles de una determinada base de datos que forma parte del grupo de bases de datos elásticas|
|/servers/elasticPools/advisors/read|Devuelve una lista de asesores disponibles para el grupo elástico|
|/servers/elasticPools/advisors/write|Actualiza el estado de ejecución automática de un asesor en el nivel del grupo elástico.|
|/servers/elasticPools/advisors/recommendedActions/read|Devuelve una lista de acciones recomendadas del asesor especificado para el grupo elástico|
|/servers/elasticPools/advisors/recommendedActions/write|Aplica la acción recomendada en el grupo elástico|
|/servers/elasticPools/elasticPoolActivity/read|Recupera las actividades y detalles de un grupo de bases de datos elásticas determinado|
|/servers/elasticPools/databases/read|Recupera la lista y los detalles de las bases de datos que forman parte de un grupo de bases de datos elásticas de un servidor determinado|
|/servers/auditingPolicies/read|Recupera detalles de la directiva predeterminada de auditoría de tablas del servidor configurada en un servidor determinado|
|/servers/auditingPolicies/write|Cambia la directiva predeterminada de auditoría de tablas del servidor para un servidor determinado|
|/servers/disasterRecoveryConfiguration/operationResults/read|Obtiene los resultados de la operación de configuración de recuperación ante desastres|
|/servers/advisors/read|Devuelve la lista de asesores disponibles para el servidor|
|/servers/advisors/write|Actualiza el estado de ejecución automática de un asesor en el nivel del servidor.|
|/servers/advisors/recommendedActions/read|Devuelve una lista de acciones recomendadas del asesor especificado para el servidor|
|/servers/advisors/recommendedActions/write|Aplica la acción recomendada en el servidor|
|/servers/usages/read|Devuelve la cuota de DTU del servidor y el consumo actual de DTU de todas las bases de datos del servidor|
|/servers/elasticPoolEstimates/read|Devuelve una lista de las estimaciones de grupos elásticos ya creados para este servidor|
|/servers/elasticPoolEstimates/write|Crea una nueva estimación de grupos elásticos para la lista de bases de datos proporcionada|
|/servers/auditingSettings/read|Recupera detalles de la directiva de auditoría de blobs del servidor configurada en un servidor determinado|
|/servers/auditingSettings/write|Cambia la auditoría de blobs del servidor para un servidor determinado|
|/servers/auditingSettings/operationResults/read|Recupera el resultado de la operación de establecimiento de directivas de auditoría de blobs del servidor|
|/servers/backupLongTermRetentionVaults/read|Esta operación se usa para obtener un almacén de retención de copia de seguridad a largo plazo. Devuelve información acerca del almacén registrado en este servidor.|
|/servers/backupLongTermRetentionVaults/write|Registra un almacén de retención de copia de seguridad a largo plazo|
|/servers/restorableDroppedDatabases/read|Recupera una lista de bases de datos que se han quitado de un servidor determinado que siguen estando dentro de la directiva de retención. Esta operación devuelve una lista de las bases de datos y sus metadatos asociados, como la fecha de eliminación.|
|/servers/databases/read|Devuelve una lista de servidores de un grupo de recursos en una suscripción|
|/servers/databases/write|Crea un servidor nuevo o modifica las propiedades del servidor existente en un grupo de recursos de una suscripción|
|/servers/databases/delete|Elimina un servidor y todas las bases de datos y grupos elásticos que contiene|
|/servers/databases/export/action|Crea una base de datos nueva en el servidor e implementa el esquema y los datos de un paquete DacPac|
|/servers/databases/VulnerabilityAssessmentScans/action|Ejecuta un análisis de la base datos de evaluación de vulnerabilidad.|
|/servers/databases/pause/action|Pausa una base de datos de edición de DataWarehouse|
|/servers/databases/resume/action|Reanuda una base de datos de edición de DataWarehouse|
|/servers/databases/operationResults/read|Esta operación se usa para seguir el progreso de operaciones de base de datos de ejecución prolongada, como el escalado.|
|/servers/databases/replicationLinks/read|Devuelve detalles acerca de los vínculos de replicación establecidos para una determinada base de datos|
|/servers/databases/replicationLinks/delete|Finaliza la relación de replicación a la fuerza con una posible pérdida de datos|
|/servers/databases/replicationLinks/unlink/action|Finaliza la relación de replicación a la fuerza o después de sincronizar con el asociado|
|/servers/databases/replicationLinks/failover/action|Realiza una conmutación por error después de sincronizar todos los cambios desde el servidor principal, haciendo que esta base de datos sea la principal de la relación de replicación y haciendo que el control remoto principal se convierta en secundario|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Realiza una conmutación por error de forma inmediata con posible pérdida de datos, haciendo que esta base de datos sea la principal de la relación de replicación y haciendo que el control remoto principal se convierta en secundario|
|/servers/databases/replicationLinks/updateReplicationMode/action|Actualiza el modo de replicación para vincular al modo sincrónico o asincrónico|
|/servers/databases/replicationLinks/operationResults/read|Obtiene el estado de operaciones de larga duración en vínculos de replicación de la base de datos|
|/servers/databases/dataMaskingPolicies/read|Recupera detalles de la directiva de enmascaramiento de datos configurada en una base de datos determinada|
|/servers/databases/dataMaskingPolicies/write|Cambia la directiva de enmascaramiento de datos de una determinada base de datos|
|/servers/databases/dataMaskingPolicies/rules/read|Recupera los detalles de la regla de directiva de enmascaramiento de datos configurada en una determinada base de datos|
|/servers/databases/dataMaskingPolicies/rules/write|Cambia la regla de directiva de enmascaramiento de datos de una determinada base de datos|
|/servers/databases/securityAlertPolicies/read|Recupera detalles de la directiva de detección de amenazas configurada en una base de datos determinada|
|/servers/databases/securityAlertPolicies/write|Cambia la directiva de detección de amenazas para una base de datos determinada|
|/servers/databases/providers/Microsoft.Insights/<br>metricDefinitions/read|Devuelve los tipos de métricas que están disponibles para grupos de bases de datos|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/read|Obtiene la configuración de diagnóstico del recurso|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico del recurso|
|/servers/databases/providers/Microsoft.Insights/<br>logDefinitions/read|Obtiene los registros disponibles para las bases de datos|
|/servers/databases/topQueries/read|Devuelve estadísticas agregadas de tiempo de ejecución para la consulta seleccionada en el período de tiempo seleccionado|
|/servers/databases/topQueries/queryText/read|Devuelve el texto de Transact-SQL para el identificador de la consulta seleccionado|
|/servers/databases/topQueries/statistics/read|Devuelve estadísticas agregadas de tiempo de ejecución para la consulta seleccionada en el período de tiempo seleccionado|
|/servers/databases/connectionPolicies/read|Recupera detalles de la directiva de conexión configurada en una base de datos determinada|
|/servers/databases/connectionPolicies/write|Cambia la directiva de conexión para una base de datos determinada|
|/servers/databases/metrics/read|Devuelve las métricas de utilización de recursos de base de datos|
|/servers/databases/auditRecords/read|Recupera los registros de auditoría de blobs de bases de datos|
|/servers/databases/transparentDataEncryption/read|Recupera el estado y los detalles de la característica de seguridad de cifrado de datos transparente para una base de datos determinada|
|/servers/databases/transparentDataEncryption/write|Habilita o deshabilita el cifrado de datos transparente para una base de datos|
|/servers/databases/transparentDataEncryption/operationResults/read|Recupera el estado y los detalles de la característica de seguridad de cifrado de datos transparente para una base de datos determinada|
|/servers/databases/auditingPolicies/read|Recupera detalles de la directiva de auditoría de tablas configurada en una base de datos determinada|
|/servers/databases/auditingPolicies/write|Cambia la directiva de auditoría de tablas para una base de datos determinada|
|/servers/databases/dataWarehouseQueries/read|Devuelve la información de la consulta de distribución de almacenamiento de datos para el identificador de la consulta seleccionado|
|/servers/databases/dataWarehouseQueries/<br>dataWarehouseQuerySteps/read|Devuelve la información de pasos de la consulta distribuida de almacenamiento de datos para el identificador del paso seleccionado|
|/servers/databases/serviceTierAdvisors/read|Devuelve sugerencias acerca de cómo escalar o reducir verticalmente la base de datos en función de las estadísticas de ejecución de consultas para mejorar el rendimiento o reducir los costos|
|/servers/databases/advisors/read|Devuelve la lista de asesores disponibles para la base de datos|
|/servers/databases/advisors/write|Actualiza el estado de ejecución automática de un asesor en el nivel de la base de datos.|
|/servers/databases/advisors/recommendedActions/read|Devuelve una lista de acciones recomendadas del asesor especificado para la base de datos|
|/servers/databases/advisors/recommendedActions/write|Aplica la acción recomendada en la base de datos|
|/servers/databases/usages/read|Devuelve el tamaño máximo de la base de datos que se puede alcanzar y el tamaño actual ocupado por los datos|
|/servers/databases/queryStore/read|Devuelve los valores actuales de configuración del almacén de consultas de la base de datos|
|/servers/databases/queryStore/write|Actualiza la configuración del almacén de consultas de la base de datos|
|/servers/databases/auditingSettings/read|Recupera detalles de la directiva de auditoría de blobs configurada en una base de datos determinada|
|/servers/databases/auditingSettings/write|Cambia la directiva de auditoría de blobs para una base de datos determinada|
|/servers/databases/schemas/tables/recommendedIndexes/read|Recupera la lista de recomendaciones de índice de una base de datos|
|/servers/databases/schemas/tables/recommendedIndexes/write|Aplica la recomendación de índice|
|/servers/databases/schemas/tables/columns/read|Recuperar la lista de columnas de una tabla|
|/servers/databases/missingindexes/read|Devuelve sugerencias acerca de los índices de base de datos que debe crear, modificar o eliminar con el fin de mejorar el rendimiento de las consultas|
|/servers/databases/missingindexes/write|Usa la recomendación de índice de base de datos en una base de datos determinada|
|/servers/databases/importExportOperationResults/read|Devuelve detalles sobre una operación de importación o exportación de base de datos desde un DacPac ubicado en la cuenta de almacenamiento|
|/servers/importExportOperationResults/read|Devuelve la lista con los detalles de las operaciones de importación de base de datos desde una cuenta de almacenamiento de un servidor determinado|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Operación | Descripción |
|---|---|
|/register/action|Registra la suscripción para el proveedor de recursos de almacenamiento y habilita la creación de cuentas de almacenamiento.|
|/checknameavailability/read|Comprueba que el nombre de la cuenta es válido y que no está en uso.|
|/storageAccounts/write|Crea una cuenta de almacenamiento con los parámetros especificados o actualiza las propiedades o etiquetas, o agrega un dominio personalizado para la cuenta de almacenamiento especificada.|
|/storageAccounts/delete|Agrega una cuenta de almacenamiento existente.|
|/storageAccounts/listkeys/action|Devuelve las claves de acceso de la cuenta de almacenamiento especificada.|
|/storageAccounts/regeneratekey/action|Regenera las claves de acceso de la cuenta de almacenamiento especificada.|
|/storageAccounts/read|Devuelve la lista de cuentas de almacenamiento u obtiene las propiedades de la cuenta de almacenamiento especificada.|
|/storageAccounts/listAccountSas/action|Devuelve el token de SAS de la cuenta de almacenamiento especificada.|
|/storageAccounts/listServiceSas/action|Token SAS del servicio de almacenamiento|
|/storageAccounts/services/diagnosticSettings/write|Crea o actualiza la configuración de diagnóstico de la cuenta de almacenamiento.|
|/skus/read|Enumera las SKU compatibles con Microsoft.Storage.|
|/usages/read|Devuelve el límite y el recuento actual de utilización de recursos en la suscripción especificada|
|/operations/read|Sondea el estado de una operación asincrónica.|
|/locations/deleteVirtualNetworkOrSubnets/action|Notifica a Microsoft.Storage que se está eliminando una red virtual o subred|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Operación | Descripción |
|---|---|
|/managers/clearAlerts/action|Borra todas las alertas asociadas con el administrador de dispositivos.|
|/managers/getActivationKey/action|Obtiene la clave de activación del administrador de dispositivos.|
|/managers/regenerateActivationKey/action|Regenera la clave de activación del administrador de dispositivos.|
|/managers/regenarateRegistationCertificate/action|Regenera el certificado de registro de los administradores de dispositivos.|
|/managers/getEncryptionKey/action|Obtiene la clave de cifrado del administrador de dispositivos.|
|/managers/read|Enumera u obtiene los administradores de dispositivos|
|/managers/delete|Elimina los administradores de dispositivos|
|/managers/write|Crea o actualiza los administradores de dispositivos|
|/managers/configureDevice/action|Configura un dispositivo|
|/managers/listActivationKey/action|Obtiene la clave de activación del administrador de dispositivos de StorSimple.|
|/managers/listPublicEncryptionKey/action|Enumera las claves de cifrado públicas de un administrador de dispositivos de StorSimple.|
|/managers/listPrivateEncryptionKey/action|Obtiene una clave de cifrado privada de un administrador de dispositivos de StorSimple.|
|/managers/provisionCloudAppliance/action|Crea una nueva aplicación en la nube.|
|/Managers/write|La operación Create Vault crea un recurso de Azure del tipo "almacén"|
|/Managers/read|La operación Get Vault obtiene un objeto que representa el recurso de Azure del tipo "almacén"|
|/Managers/delete|La operación Delete Vault elimina el recurso de Azure del tipo "almacén" especificado|
|/managers/storageAccountCredentials/write|Crea o actualiza las credenciales de la cuenta de almacenamiento|
|/managers/storageAccountCredentials/read|Muestra u obtiene las credenciales de la cuenta de almacenamiento|
|/managers/storageAccountCredentials/delete|Elimina las credenciales de la cuenta de almacenamiento|
|/managers/storageAccountCredentials/listAccessKey/action|Muestra las claves de acceso de las credenciales de la cuenta de almacenamiento|
|/managers/accessControlRecords/read|Muestra u obtiene el registro de control de acceso|
|/managers/accessControlRecords/write|Crea o actualiza el registro de control de acceso|
|/managers/accessControlRecords/delete|Elimina los registros de control de acceso|
|/managers/metrics/read|Muestra u obtiene las métricas|
|/managers/bandwidthSettings/read|Muestra la configuración de ancho de banda (solo serie 8000)|
|/managers/bandwidthSettings/write|Crea una nueva configuración de ancho de banda (solo serie 8000) o actualiza la que ya existe|
|/managers/bandwidthSettings/delete|Elimina una configuración de ancho de banda (solo serie 8000) que ya existe|
|/Managers/extendedInformation/read|La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén?|
|/Managers/extendedInformation/write|La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén?|
|/Managers/extendedInformation/delete|La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén?|
|/managers/alerts/read|Muestra u obtiene las alertas|
|/managers/storageDomains/read|Muestra u obtiene los dominios de almacenamiento|
|/managers/storageDomains/write|Crea o actualiza los dominios de almacenamiento|
|/managers/storageDomains/delete|Elimina los dominios de almacenamiento|
|/managers/devices/scanForUpdates/action|Busca actualizaciones de un dispositivo.|
|/managers/devices/download/action|Descarga actualizaciones para un dispositivo.|
|/managers/devices/install/action|Instala actualizaciones en un dispositivo.|
|/managers/devices/read|Muestra u obtiene los dispositivos|
|/managers/devices/write|Crea o actualiza los dispositivos|
|/managers/devices/delete|Elimina los dispositivos|
|/managers/devices/deactivate/action|Desactiva un dispositivo.|
|/managers/devices/publishSupportPackage/action|Publica el paquete de soporte de un dispositivo para la solución de problemas del Soporte técnico de Microsoft.|
|/managers/devices/failover/action|Conmutación por error del dispositivo.|
|/managers/devices/sendTestAlertEmail/action|Envía un correo electrónico de alerta de prueba a destinatarios configurados de correo electrónico.|
|/managers/devices/installUpdates/action|Instala actualizaciones en los dispositivos|
|/managers/devices/listFailoverSets/action|Obtiene la lista de conjuntos de conmutación por error de un dispositivo existente.|
|/managers/devices/listFailoverTargets/action|Muestra los destinos de conmutación por error de los dispositivos|
|/managers/devices/publicEncryptionKey/action|Muestra la clave de cifrado pública del administrador de dispositivos|
|/managers/devices/hardwareComponentGroups/<br>read|Muestra los grupos de componentes de hardware|
|/managers/devices/hardwareComponentGroups/<br>changeControllerPowerState/action|Cambia el estado de energía del controlador de los grupos de componentes de hardware|
|/managers/devices/metrics/read|Muestra u obtiene las métricas|
|/managers/devices/chapSettings/write|Crea o actualiza la configuración de CHAP|
|/managers/devices/chapSettings/read|Muestra u obtiene la configuración de CHAP|
|/managers/devices/chapSettings/delete|Elimina la configuración de CHAP|
|/managers/devices/backupScheduleGroups/read|Muestra u obtiene los grupos de programación de copias de seguridad|
|/managers/devices/backupScheduleGroups/write|Crea o actualiza los grupos de programación de copias de seguridad|
|/managers/devices/backupScheduleGroups/delete|Elimina los grupos de programación de copias de seguridad|
|/managers/devices/updateSummary/read|Muestra u obtiene el resumen de actualización|
|/managers/devices/migrationSourceConfigurations/<br>import/action|Importa las configuraciones de origen para la migración|
|/managers/devices/migrationSourceConfigurations/<br>startMigrationEstimate/action|Inicia un trabajo para calcular la duración del proceso de migración.|
|/managers/devices/migrationSourceConfigurations/<br>startMigration/action|Inicia la migración mediante configuraciones de origen|
|/managers/devices/migrationSourceConfigurations/<br>confirmMigration/action|Confirma una migración correcta y la confirma.|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationEstimate/action|Recupera el estado del trabajo de estimación de la migración.|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationStatus/action|Recupera el estado de la migración.|
|/managers/devices/migrationSourceConfigurations/<br>fetchConfirmMigrationStatus/action|Recupera el estado de confirmación de la migración.|
|/managers/devices/alertSettings/read|Muestra u obtiene la configuración de las alertas|
|/managers/devices/alertSettings/write|Crea o actualiza la configuración de las alertas|
|/managers/devices/networkSettings/read|Muestra u obtiene la configuración de red|
|/managers/devices/networkSettings/write|Crea una nueva configuración de red o actualiza una que ya existe|
|/managers/devices/jobs/read|Muestra u obtiene los trabajos|
|/managers/devices/jobs/cancel/action|Cancela un trabajo de ejecución|
|/managers/devices/metricsDefinitions/read|Muestra u obtiene las definiciones de métricas|
|/managers/devices/volumeContainers/write|Crea un nuevo contenedor de volumen (solo para la serie 8000) o actualiza uno que ya existe|
|/managers/devices/volumeContainers/read|Enumera los contenedores de volumen (solo para la serie 8000)|
|/managers/devices/volumeContainers/delete|Elimina los contenedores de volumen existentes (solo para la serie 8000)|
|/managers/devices/volumeContainers/listEncryptionKeys/action|Enumera las claves de cifrado de los contenedores de volumen|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|Sustituye las claves de cifrado de los contenedores de volumen|
|/managers/devices/volumeContainers/metrics/read|Enumera las métricas|
|/managers/devices/volumeContainers/volumes/read|Enumera los volúmenes|
|/managers/devices/volumeContainers/volumes/write|Crea un nuevo volumen o actualiza uno que ya existe|
|/managers/devices/volumeContainers/volumes/delete|Elimina un volumen existente|
|/managers/devices/volumeContainers/volumes/metrics/read|Enumera las métricas|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|Enumera las definiciones de métricas|
|/managers/devices/volumeContainers/metricsDefinitions/read|Enumera las definiciones de métricas|
|/managers/devices/iscsiservers/read|Muestra u obtiene los servidores de iSCSI|
|/managers/devices/iscsiservers/write|Crea o actualiza los servidores de iSCSI|
|/managers/devices/iscsiservers/delete|Elimina los servidores de iSCSI|
|/managers/devices/iscsiservers/backup/action|Realiza una copia de seguridad de un servidor iSCSI.|
|/managers/devices/iscsiservers/metrics/read|Muestra u obtiene las métricas|
|/managers/devices/iscsiservers/disks/read|Muestra u obtiene los discos|
|/managers/devices/iscsiservers/disks/write|Crea o actualiza los discos|
|/managers/devices/iscsiservers/disks/delete|Elimina los discos|
|/managers/devices/iscsiservers/disks/metrics/read|Muestra u obtiene las métricas|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|Muestra u obtiene las definiciones de métricas|
|/managers/devices/iscsiservers/metricsDefinitions/read|Muestra u obtiene las definiciones de métricas|
|/managers/devices/backups/read|Muestra u obtiene el conjunto de copia de seguridad|
|/managers/devices/backups/delete|Elimina el conjunto de copia de seguridad|
|/managers/devices/backups/restore/action|Restaurar todos los volúmenes a partir de un conjunto de copias de seguridad.|
|/managers/devices/backups/elements/clone/action|Clona un recurso o volumen compartido mediante un elemento de copia de seguridad.|
|/managers/devices/backupPolicies/write|Crea una nueva directiva de copia de seguridad (solo serie 8000) o actualiza una que ya existe|
|/managers/devices/backupPolicies/read|Enumera las directivas de copia de seguridad (solo serie 8000)|
|/managers/devices/backupPolicies/delete|Elimina una directiva de copia de seguridad existente (solo serie 8000)|
|/managers/devices/backupPolicies/backup/action|Realiza una copia de seguridad manual para crear una copia de seguridad a petición de todos los volúmenes protegidos por la directiva.|
|/managers/devices/backupPolicies/schedules/write|Crea una nueva programación o actualiza una que ya existe|
|/managers/devices/backupPolicies/schedules/read|Enumera las programaciones|
|/managers/devices/backupPolicies/schedules/delete|Elimina una programación existente|
|/managers/devices/securitySettings/update/action|Actualiza la configuración de seguridad.|
|/managers/devices/securitySettings/read|Muestra la configuración de seguridad|
|/managers/devices/securitySettings/<br>syncRemoteManagementCertificate/action|Sincroniza el certificado de administración remota de un dispositivo.|
|/managers/devices/securitySettings/write|Crea una nueva configuración de seguridad o actualiza una que ya existe|
|/managers/devices/fileservers/read|Muestra u obtiene los servidores de archivos|
|/managers/devices/fileservers/write|Crea o actualiza los servidores de archivos|
|/managers/devices/fileservers/delete|Elimina los servidores de archivos|
|/managers/devices/fileservers/backup/action|Realiza una copia de seguridad de un servidor de archivos.|
|/managers/devices/fileservers/metrics/read|Muestra u obtiene las métricas|
|/managers/devices/fileservers/shares/write|Crea o actualiza los recursos compartidos|
|/managers/devices/fileservers/shares/read|Muestra u obtiene los recursos compartidos|
|/managers/devices/fileservers/shares/delete|Elimina los recursos compartidos|
|/managers/devices/fileservers/shares/metrics/read|Muestra u obtiene las métricas|
|/managers/devices/fileservers/shares/metricsDefinitions/read|Muestra u obtiene las definiciones de métricas|
|/managers/devices/fileservers/metricsDefinitions/read|Muestra u obtiene las definiciones de métricas|
|/managers/devices/timeSettings/read|Muestra u obtiene la configuración de hora|
|/managers/devices/timeSettings/write|Crea una nueva configuración de hora o actualiza una que ya existe|
|/Managers/certificates/write|La operación Actualizar certificado de recursos permite actualizar el certificado de credencial de recursos o almacenes.|
|/managers/cloudApplianceConfigurations/read|Muestra las configuraciones admitidas de la aplicación en la nube|
|/managers/metricsDefinitions/read|Muestra u obtiene las definiciones de métricas|
|/managers/encryptionSettings/read|Muestra u obtiene la configuración de cifrado|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Operación | Descripción |
|---|---|
|/streamingjobs/Start/action|Inicia el trabajo de Stream Analytics|
|/streamingjobs/Stop/action|Detiene el trabajo de Stream Analytics|
|/streamingjobs/Read|Lee el trabajo de Stream Analytics|
|/streamingjobs/Write|Escribe el trabajo de Stream Analytics|
|/streamingjobs/Delete|Elimina el trabajo de Stream Analytics|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|Obtiene las métricas disponibles de los trabajos del flujo|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Lee la configuración de diagnóstico.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Escribe la configuración de diagnóstico.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|Obtiene los registros disponibles de los trabajos del flujo|
|/streamingjobs/transformations/Read|Lee la transformación de trabajos de Stream Analytics|
|/streamingjobs/transformations/Write|Escribe la transformación de trabajos de Stream Analytics|
|/streamingjobs/transformations/Delete|Elimina la transformación de trabajos de Stream Analytics|
|/streamingjobs/inputs/Read|Lee la entrada del trabajo de Stream Analytics|
|/streamingjobs/inputs/Write|Escribe la entrada del trabajo de Stream Analytics|
|/streamingjobs/inputs/Delete|Elimina la entrada del trabajo de Stream Analytics|
|/streamingjobs/outputs/Read|Lee la salida del trabajo de Stream Analytics|
|/streamingjobs/outputs/Write|Escribe la salida del trabajo de Stream Analytics|
|/streamingjobs/outputs/Delete|Elimina la salida del trabajo de Stream Analytics|

## <a name="microsoftsupport"></a>Microsoft.Support

| Operación | Descripción |
|---|---|
|/register/action|Registra para admitir el proveedor de recursos|
|/supportTickets/read|Obtiene los detalles de las incidencias de soporte técnico (incluido el estado, gravedad, detalles de contacto y comunicaciones) u obtiene la lista de incidencias de soporte técnico de las diversas suscripciones.|
|/supportTickets/write|Crea o actualiza una incidencia de soporte técnico. Puede crear una incidencia de soporte técnico para problemas relacionados con cuestiones técnicas, de facturación, cuotas o de administración de suscripciones. Puede actualizar la gravedad, los detalles de contacto y las comunicaciones de las incidencias de soporte técnico existentes.|

## <a name="microsoftweb"></a>Microsoft.Web

| Operación | Descripción |
|---|---|
|/unregister/action|Anula el registro del proveedor de recursos de Microsoft.Web de la suscripción.|
|/validate/action|Valida.|
|/register/action|Registra el proveedor de recursos de Microsoft.Web de la suscripción.|
|/hostingEnvironments/Read|Obtiene las propiedades de un entorno de App Service|
|/hostingEnvironments/Write|Crea un nuevo entorno de App Service o actualiza uno que ya existe|
|/hostingEnvironments/Delete|Elimina un entorno de App Service|
|/hostingEnvironments/reboot/Action|Reinicia todas las máquinas de un entorno de App Service|
|/hostingenvironments/resume/action|Reanuda los entornos de hospedaje.|
|/hostingenvironments/suspend/action|Suspende los entornos de hospedaje.|
|/hostingenvironments/metricdefinitions/read|Obtiene las definiciones de métricas de los entornos de hospedaje.|
|/hostingEnvironments/workerPools/Read|Obtiene las propiedades de un conjunto de trabajo en un entorno de App Service|
|/hostingEnvironments/workerPools/Write|Crea un nuevo grupo de trabajo en un entorno de App Service o actualiza uno que ya existe|
|/hostingenvironments/workerpools/metricdefinitions/read|Obtiene las definiciones de métricas de los grupos de trabajo de los entornos de hospedaje.|
|/hostingenvironments/workerpools/metrics/read|Obtiene las métricas de los grupos de trabajo de los entornos de hospedaje.|
|/hostingenvironments/workerpools/skus/read|Obtiene las SKU de los grupos de trabajo de los entornos de hospedaje.|
|/hostingenvironments/workerpools/usages/read|Obtiene los usos de los grupos de trabajo de los entornos de hospedaje.|
|/hostingenvironments/sites/read|Obtiene las instancias de Web Apps de los entornos de hospedaje.|
|/hostingenvironments/serverfarms/read|Obtiene los planes de App Service de los entornos de hospedaje.|
|/hostingenvironments/usages/read|Obtiene los usos de los entornos de hospedaje.|
|/hostingenvironments/capacities/read|Obtiene las funcionalidades de los entornos de hospedaje.|
|/hostingenvironments/operations/read|Obtiene las operaciones de los entornos de hospedaje.|
|/hostingEnvironments/multiRolePools/Read|Obtiene las propiedades de un grupo de front-end en un entorno de App Service|
|/hostingEnvironments/multiRolePools/Write|Crea un nuevo grupo de front-end en un entorno de App Service o actualiza uno que ya existe|
|/hostingenvironments/multirolepools/metricdefinitions/read|Obtiene las definiciones de métricas de los grupos MultiRole de los entornos de hospedaje.|
|/hostingenvironments/multirolepools/metrics/read|Obtiene las métricas de los grupos MultiRole de los entornos de hospedaje.|
|/hostingenvironments/multirolepools/skus/read|Obtiene las SKU de los grupos MultiRole de los entornos de hospedaje.|
|/hostingenvironments/multirolepools/usages/read|Obtiene los usos de los grupos MultiRole de los entornos de hospedaje.|
|/hostingenvironments/diagnostics/read|Obtiene los diagnósticos de los entornos de hospedaje.|
|/publishingusers/read|Obtiene los usuarios de publicación.|
|/publishingusers/write|Actualiza los usuarios de publicación.|
|/checknameavailability/read|Comprueba si el nombre de recurso está disponible.|
|/geoRegions/Read|Obtiene la lista de regiones geográficas.|
|/sites/Read|Obtiene las propiedades de una aplicación web|
|/sites/Write|Crea una nueva aplicación web o actualiza una ya existente|
|/sites/Delete|Eliminación de una aplicación web existente|
|/sites/backup/Action|Crea una copia de seguridad de una nueva aplicación web|
|/sites/publishxml/Action|Obtiene el xml del perfil de publicación de una aplicación web|
|/sites/publish/Action|Publica una aplicación web|
|/sites/restart/Action|Reinicia una aplicación web|
|/sites/start/Action|Inicia una aplicación web|
|/sites/stop/Action|Detiene una aplicación web|
|/sites/slotsswap/Action|Intercambia ranuras de implementación de aplicación web|
|/sites/slotsdiffs/Action|Obtiene las diferencias de configuración entre la aplicación web y las ranuras|
|/sites/applySlotConfig/Action|Aplica la configuración de ranuras de la aplicación web desde la ranura de destino a la aplicación web actual|
|/sites/resetSlotConfig/Action|Restablece la configuración de la aplicación web|
|/sites/functions/action|Funciones de Web Apps.|
|/sites/listsyncfunctiontriggerstatus/action|Enumera las instancias de Web Apps de estado del desencadenador de funciones de sincronización.|
|/sites/networktrace/action|Realiza un seguimiento de red de las instancias de Web Apps.|
|/sites/newpassword/action|Genera una nueva contraseña para las instancias de Web Apps.|
|/sites/sync/action|Sincroniza las instancias de Web Apps.|
|/sites/operationresults/read|Obtiene los resultados de la operación de Web Apps.|
|/sites/webjobs/read|Obtiene los WebJobs de Web Apps.|
|/sites/backup/read|Obtiene una copia de seguridad de Web Apps.|
|/sites/backup/write|Actualiza la copia de seguridad de Web Apps.|
|/sites/metricdefinitions/read|Obtiene las definiciones de métricas de Web Apps.|
|/sites/metrics/read|Obtiene las métricas de Web Apps.|
|/sites/continuouswebjobs/delete|Elimina WebJobs continuos de Web Apps.|
|/sites/continuouswebjobs/read|Obtiene WebJobs continuos de Web Apps.|
|/sites/continuouswebjobs/start/action|Inicia WebJobs continuos de Web Apps.|
|/sites/continuouswebjobs/stop/action|Detiene WebJobs continuos de Web Apps.|
|/sites/domainownershipidentifiers/read|Obtiene identificadores de propiedad de dominio de Web Apps.|
|/sites/domainownershipidentifiers/write|Actualiza identificadores de propiedad de dominio de Web Apps.|
|/sites/premieraddons/delete|Elimina complementos Premier de Web Apps.|
|/sites/premieraddons/read|Obtiene complementos Premier de Web Apps.|
|/sites/premieraddons/write|Actualiza complementos Premier de Web Apps.|
|/sites/triggeredwebjobs/delete|Elimina WebJobs desencadenados de Web Apps.|
|/sites/triggeredwebjobs/read|Obtiene WebJobs desencadenados de Web Apps.|
|/sites/triggeredwebjobs/run/action|Ejecuta WebJobs desencadenados de Web Apps.|
|/sites/hostnamebindings/delete|Elimina enlaces de nombre de host de Web Apps.|
|/sites/hostnamebindings/read|Obtiene enlaces de nombre de host de Web Apps.|
|/sites/hostnamebindings/write|Actualiza enlaces de nombre de host de Web Apps.|
|/sites/virtualnetworkconnections/delete|Elimina conexiones de red virtual de Web Apps.|
|/sites/virtualnetworkconnections/read|Obtiene conexiones de red virtual de Web Apps.|
|/sites/virtualnetworkconnections/write|Actualiza conexiones de red virtual de Web Apps.|
|/sites/virtualnetworkconnections/gateways/read|Obtiene puertas de enlace de conexiones de red virtual de Web Apps.|
|/sites/virtualnetworkconnections/gateways/write|Actualiza puertas de enlace de conexiones de red virtual de Web Apps.|
|/sites/publishxml/read|Obtiene el XML de publicación de Web Apps.|
|/sites/hybridconnectionrelays/read|Obtiene las retransmisiones de conexiones híbridas de Web Apps.|
|/sites/perfcounters/read|Obtiene los contadores de rendimiento de Web Apps.|
|/sites/usages/read|Obtiene los usos de Web Apps.|
|/sites/slots/Write|Crea una nueva ranura de aplicación web o actualiza una ya existente|
|/sites/slots/Delete|Elimina una ranura de aplicación web existente|
|/sites/slots/backup/Action|Crea una nueva copia de seguridad de ranura de aplicación web.|
|/sites/slots/publishxml/Action|Obtiene el xml del perfil de publicación de una ranura de aplicación web|
|/sites/slots/publish/Action|Publica una ranura de aplicación web|
|/sites/slots/restart/Action|Reinicia una ranura de aplicación web|
|/sites/slots/start/Action|Inicia una ranura de aplicación web|
|/sites/slots/stop/Action|Detiene una ranura de aplicación web|
|/sites/slots/slotsswap/Action|Intercambia ranuras de implementación de aplicación web|
|/sites/slots/slotsdiffs/Action|Obtiene las diferencias de configuración entre la aplicación web y las ranuras|
|/sites/slots/applySlotConfig/Action|Aplica una configuración de ranuras de la aplicación web desde la ranura de destino a la ranura actual.|
|/sites/slots/resetSlotConfig/Action|Restablece la configuración de ranuras de la aplicación web|
|/sites/slots/Read|Obtiene las propiedades de una ranura de implementación de aplicación web|
|/sites/slots/newpassword/action|Genera una nueva contraseña para las ranuras de instancias de Web Apps.|
|/sites/slots/sync/action|Sincroniza ranuras de Web Apps.|
|/sites/slots/operationresults/read|Obtiene los resultados de la operación de ranuras de Web Apps.|
|/sites/slots/webjobs/read|Obtiene los WebJobs de ranuras de Web Apps.|
|/sites/slots/backup/write|Actualiza la copia de seguridad de ranuras de Web Apps.|
|/sites/slots/metricdefinitions/read|Obtiene las definiciones de métricas de ranuras de Web Apps.|
|/sites/slots/metrics/read|Obtiene las métricas de ranuras de Web Apps.|
|/sites/slots/continuouswebjobs/delete|Elimina WebJobs continuos de ranuras de Web Apps.|
|/sites/slots/continuouswebjobs/read|Obtiene WebJobs continuos de ranuras de Web Apps.|
|/sites/slots/continuouswebjobs/start/action|Inicia WebJobs continuos de ranuras de Web Apps.|
|/sites/slots/continuouswebjobs/stop/action|Detiene WebJobs continuos de ranuras de Web Apps.|
|/sites/slots/premieraddons/delete|Elimina complementos Premier de ranuras de Web Apps.|
|/sites/slots/premieraddons/read|Obtiene complementos Premier de ranuras de Web Apps.|
|/sites/slots/premieraddons/write|Actualiza complementos Premier de ranuras de Web Apps.|
|/sites/slots/triggeredwebjobs/delete|Elimina WebJobs desencadenados de ranuras de Web Apps.|
|/sites/slots/triggeredwebjobs/read|Obtiene WebJobs desencadenados de ranuras de Web Apps.|
|/sites/slots/triggeredwebjobs/run/action|Ejecuta WebJobs desencadenados de ranuras de Web Apps.|
|/sites/slots/hostnamebindings/delete|Elimina enlaces de nombre de host de ranuras de Web Apps.|
|/sites/slots/hostnamebindings/read|Obtiene enlaces de nombre de host de ranuras de Web Apps.|
|/sites/slots/hostnamebindings/write|Actualiza enlaces de nombre de host de ranuras de Web Apps.|
|/sites/slots/phplogging/read|Obtiene Phplogging de ranuras de Web Apps.|
|/sites/slots/virtualnetworkconnections/delete|Elimina conexiones de red virtual de ranuras de Web Apps.|
|/sites/slots/virtualnetworkconnections/read|Obtiene conexiones de red virtual de ranuras de Web Apps.|
|/sites/slots/virtualnetworkconnections/write|Actualiza conexiones de red virtual de ranuras de Web Apps.|
|/sites/slots/virtualnetworkconnections/gateways/write|Actualiza puertas de enlace de conexiones de red virtual de ranuras de Web Apps.|
|/sites/slots/usages/read|Obtiene los usos de ranuras de Web Apps.|
|/sites/slots/hybridconnection/delete|Elimine la conexión híbrida de ranuras de Web Apps.|
|/sites/slots/hybridconnection/read|Obtiene la conexión híbrida de ranuras de Web Apps.|
|/sites/slots/hybridconnection/write|Actualiza la conexión híbrida de ranuras de Web Apps.|
|/sites/slots/config/Read|Obtiene las opciones de configuración de ranura de Web Apps|
|/sites/slots/config/list/Action|Muestra las opciones confidenciales de seguridad de ranuras de Web Apps como las credenciales de publicación, la configuración de la aplicación y las cadenas de conexión|
|/sites/slots/config/Write|Actualiza las opciones de configuración de ranura de Web Apps|
|/sites/slots/config/delete|Elimina la configuración de ranuras de Web Apps.|
|/sites/slots/instances/read|Obtiene instancias de ranuras de Web Apps.|
|/sites/slots/instances/processes/read|Obtiene procesos de instancias de ranuras de Web Apps.|
|/sites/slots/instances/deployments/read|Obtiene implementaciones de instancias de ranuras de Web Apps.|
|/sites/slots/sourcecontrols/Read|Obtiene las opciones de configuración de control de código fuente de ranuras de Web Apps|
|/sites/slots/sourcecontrols/Write|Actualiza las opciones de configuración de control de código fuente de ranuras de Web Apps|
|/sites/slots/sourcecontrols/Delete|Elimina las opciones de configuración de control de código fuente de ranuras de Web Apps|
|/sites/slots/restore/read|Obtiene la restauración de ranuras de Web Apps.|
|/sites/slots/analyzecustomhostname/read|Obtiene el nombre de host personalizado del análisis de ranuras de Web Apps.|
|/sites/slots/backups/Read|Obtiene las propiedades de una copia de seguridad de las ranuras de una aplicación web|
|/sites/slots/backups/list/action|Muestra las copias de seguridad de ranuras de Web Apps.|
|/sites/slots/backups/restore/action|Restaura las copias de seguridad de ranuras de Web Apps.|
|/sites/slots/deployments/delete|Elimina las implementaciones de ranuras de Web Apps.|
|/sites/slots/deployments/read|Obtiene las implementaciones de ranuras de Web Apps.|
|/sites/slots/deployments/write|Actualiza las implementaciones de ranuras de Web Apps.|
|/sites/slots/deployments/log/read|Obtiene el registro de implementaciones de ranuras de Web Apps.|
|/sites/hybridconnection/delete|Elimina la conexión híbrida de Web Apps.|
|/sites/hybridconnection/read|Obtiene la conexión híbrida de Web Apps.|
|/sites/hybridconnection/write|Actualiza la conexión híbrida de Web Apps.|
|/sites/recommendationhistory/read|Obtiene el historial de recomendaciones de Web Apps.|
|/sites/recommendations/Read|Obtiene la lista de recomendaciones de la aplicación web.|
|/sites/recommendations/disable/action|Deshabilita las recomendaciones de Web Apps.|
|/sites/config/Read|Obtiene las opciones de configuración de Web Apps|
|/sites/config/list/Action|Muestra las opciones confidenciales de seguridad de Web Apps como las credenciales de publicación, la configuración de la aplicación y las cadenas de conexión|
|/sites/config/Write|Actualiza las opciones de configuración de Web Apps|
|/sites/config/delete|Elimina la configuración de Web Apps.|
|/sites/instances/read|Obtiene instancias de Web Apps.|
|/sites/instances/processes/delete|Elimina procesos de instancias de Web Apps.|
|/sites/instances/processes/read|Obtiene procesos de instancias de Web Apps.|
|/sites/instances/deployments/read|Obtiene implementaciones de instancias de Web Apps.|
|/sites/sourcecontrols/Read|Obtiene las opciones de configuración de control de código fuente de Web Apps|
|/sites/sourcecontrols/Write|Actualiza las opciones de configuración de control de código fuente de Web Apps|
|/sites/sourcecontrols/Delete|Elimina las opciones de configuración de control de código fuente de Web Apps|
|/sites/restore/read|Obtiene la restauración de Web Apps.|
|/sites/analyzecustomhostname/read|Analiza el nombre de host personalizado.|
|/sites/backups/Read|Obtiene las propiedades de una copia de seguridad de una aplicación web|
|/sites/backups/list/action|Muestra las copias de seguridad de Web Apps.|
|/sites/backups/restore/action|Restaura las copias de seguridad de Web Apps.|
|/sites/snapshots/read|Obtiene instantáneas de Web Apps.|
|/sites/functions/delete|Elimina funciones de Web Apps.|
|/sites/functions/listsecrets/action|Muestra secretos de Web Apps.|
|/sites/functions/read|Obtiene funciones de Web Apps.|
|/sites/functions/write|Actualiza funciones de Web Apps.|
|/sites/deployments/delete|Elimina las implementaciones de Web Apps.|
|/sites/deployments/read|Obtiene las implementaciones de Web Apps.|
|/sites/deployments/write|Actualiza las implementaciones de Web Apps.|
|/sites/deployments/log/read|Obtiene el registro de las implementaciones de Web Apps.|
|/sites/diagnostics/read|Obtiene diagnósticos de Web Apps.|
|/sites/diagnostics/workerprocessrecycle/read|Obtiene el reciclaje del proceso de trabajo de diagnóstico de Web Apps.|
|/sites/diagnostics/workeravailability/read|Obtiene el valor de Workeravailability de diagnóstico de Web Apps.|
|/sites/diagnostics/runtimeavailability/read|Obtiene la disponibilidad de tiempo de ejecución de diagnóstico de Web Apps.|
|/sites/diagnostics/cpuanalysis/read|Obtiene el valor de Cpuanalysis de diagnósticos de Web Apps.|
|/sites/diagnostics/servicehealth/read|Obtiene el estado del servicio de diagnóstico de Web Apps.|
|/sites/diagnostics/frebanalysis/read|Obtiene el análisis FREB de diagnóstico de Web Apps.|
|/availablestacks/read|Obtiene las pilas disponibles.|
|/isusernameavailable/read|Comprueba si el nombre de usuario está disponible.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Read|Obtiene la lista de API.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Write|Agrega una nueva API o actualiza una que ya existe.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Delete|Elimina una API existente.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Read|Obtiene la lista de conexiones.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Write|Guarda una conexión nueva o actualiza una ya existente.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Delete|Elimina una conexión existente.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Read|Lee ConnectionAcls|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Write|Agrega o actualiza ConnectionAcl|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Delete|Elimina ConnectionAcl|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connectionAcls/Read|Lee ConnectionAcls para la API|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Read|Lee ConnectionAcls|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Write|Crea o actualiza las listas ACL de la API|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Delete|Elimina las listas ACL de la API|
|/serverfarms/Read|Obtiene las propiedades de un plan de App Service|
|/serverfarms/Write|Crea un nuevo plan de App Service o actualiza uno que ya existe|
|/serverfarms/Delete|Eliminación de un plan de App Service|
|/serverfarms/restartSites/Action|Reinicia todas las instancias de Web Apps de un plan de App Service|
|/serverfarms/operationresults/read|Obtiene los resultados de la operación de planes de App Service.|
|/serverfarms/capabilities/read|Obtiene las funcionalidades de los planes de App Service.|
|/serverfarms/metricdefinitions/read|Obtiene las definiciones de métricas de los planes de App Service.|
|/serverfarms/metrics/read|Obtiene las métricas de los planes de App Service.|
|/serverfarms/hybridconnectionplanlimits/read|Obtiene los límites del plan de conexión híbrida de los planes de App Service.|
|/serverfarms/virtualnetworkconnections/read|Obtiene las conexiones de red virtual de los planes de App Service.|
|/serverfarms/virtualnetworkconnections/routes/delete|Elimina las rutas de las conexiones de red virtual de los planes de App Service.|
|/serverfarms/virtualnetworkconnections/routes/read|Obtiene las rutas de las conexiones de red virtual de los planes de App Service.|
|/serverfarms/virtualnetworkconnections/routes/write|Actualiza las rutas de las conexiones de red virtual de los planes de App Service.|
|/serverfarms/virtualnetworkconnections/gateways/write|Actualiza las puertas de enlace de las conexiones de red virtual de los planes de App Service.|
|/serverfarms/firstpartyapps/settings/delete|Elimina la configuración de aplicaciones propias de los planes de App Service.|
|/serverfarms/firstpartyapps/settings/read|Obtiene la configuración de aplicaciones propias de los planes de App Service.|
|/serverfarms/firstpartyapps/settings/write|Actualiza la configuración de aplicaciones propias de los planes de App Service.|
|/serverfarms/sites/read|Obtiene las instancias de Web Apps de los planes de App Service.|
|/serverfarms/workers/reboot/action|Reinicia los trabajos de los planes de App Service.|
|/serverfarms/hybridconnectionrelays/read|Obtiene las retransmisiones de conexión híbrida de los planes de App Service.|
|/serverfarms/skus/read|Obtiene las SKU de los planes de App Service.|
|/serverfarms/usages/read|Obtiene los usos de los planes de App Service.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|Obtiene las instancias de Web Apps de las retransmisiones de espacios de nombres de conexión híbrida de los planes de App Service.|
|/ishostnameavailable/read|Comprueba si el nombre de host está disponible.|
|/connectionGateways/Read|Obtiene la lista de puertas de enlace de conexiones.|
|/connectionGateways/Write|Crea o actualiza una puerta de enlace de conexión.|
|/connectionGateways/Delete|Elimina una puerta de enlace de conexión.|
|/connectionGateways/Join/Action|Se une a una puerta de enlace de conexión.|
|/classicmobileservices/read|Obtiene Mobile Services clásico.|
|/skus/read|Obtiene las SKU.|
|/certificates/Read|Obtiene la lista de certificados.|
|/certificates/Write|Agrega un nuevo certificado o actualiza uno existente.|
|/certificates/Delete|Elimina un certificado existente.|
|/operations/read|Obtiene operaciones.|
|/recommendations/Read|Obtiene la lista de recomendaciones de las suscripciones.|
|/ishostingenvironmentnameavailable/read|Averigua si el nombre del entorno de hospedaje está disponible.|
|/apiManagementAccounts/Read|Obtiene la lista de ApiManagementAccounts.|
|/apiManagementAccounts/Write|Agrega una nueva ApiManagementAccount o actualiza una que ya existe|
|/apiManagementAccounts/Delete|Elimina una ApiManagementAccount existente|
|/apiManagementAccounts/connectionAcls/Read|Obtiene la lista de ACL de conexiones.|
|/apiManagementAccounts/apiAcls/Read|Lee ConnectionAcls|
|/connections/Read|Obtiene la lista de conexiones.|
|/connections/Write|Crea o actualiza una conexión.|
|/connections/Delete|Elimina una conexión.|
|/connections/Join/Action|Se une a una conexión.|
|/connections/confirmconsentcode/action|Confirma el código de consentimiento de conexiones.|
|/connections/listconsentlinks/action|Enumera los vínculos de consentimiento de las conexiones.|
|/deploymentlocations/read|Obtiene las ubicaciones de implementación.|
|/sourcecontrols/read|Obtiene los controles de origen.|
|/sourcecontrols/write|Actualiza los controles de origen.|
|/managedhostingenvironments/read|Obtiene los entornos de hospedaje administrados.|
|/managedhostingenvironments/sites/read|Obtiene las instancias de Web Apps de los entornos de hospedaje administrados.|
|/managedhostingenvironments/serverfarms/read|Obtiene los planes de App Service de los entornos de hospedaje administrados.|
|/locations/managedapis/read|Obtiene las API administradas de ubicaciones.|
|/locations/apioperations/read|Obtiene las operaciones de API de ubicaciones.|
|/locations/connectiongatewayinstallations/read|Obtiene las instalaciones de puertas de enlace de conexiones de ubicaciones.|
|/listSitesAssignedToHostName/Read|Obtiene los nombres de sitios asignados al nombre de host.|

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [crear un rol personalizado](role-based-access-control-custom-roles.md).
- Revise los [roles RBAC integrados](role-based-access-built-in-roles.md).
- Aprenda a administrar las asignaciones de acceso [por usuario](role-based-access-control-manage-assignments.md) o [por recurso](role-based-access-control-configure.md) 
- Aprenda a [visualizar los registros de actividad para auditar las acciones sobre los recursos](~/articles/azure-resource-manager/resource-group-audit.md)
