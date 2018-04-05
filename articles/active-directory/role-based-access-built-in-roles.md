---
title: Roles integrados para el control de acceso basado en rol (RBAC) de Azure | Microsoft Docs
description: En este tema se describen los roles integrados para el control de acceso basado en roles (RBAC). Los roles se agregan continuamente, así que compruebe que la documentación esté actualizada.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 0e8f1d97f51fbfedbca1619ef5e7f28a3a0570b9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Roles integrados para el control de acceso basado en roles de Azure
El control de acceso basado en roles de Azure (RBAC) dispone de los siguientes roles integrados que se pueden asignar a usuarios, grupos y servicios. Las definiciones de los roles integrados no se puede modificar. Sin embargo, puede crear [roles personalizados en RBAC de Azure](role-based-access-control-custom-roles.md) para satisfacer las necesidades específicas de su organización.

## <a name="built-in-role-descriptions"></a>Descripciones de rol integrado
En la tabla siguiente se proporcionan breves descripciones de los roles integrados. Haga clic en el nombre de rol para ver una lista detallada de las propiedades **actions** y **notactions** de dicho rol. La propiedad **actions** especifica las acciones permitidas en los recursos de Azure. Las cadenas de acciones pueden utilizar caracteres comodín. La propiedad **notactions** especifica las acciones que se excluyen de las acciones permitidas.

La acción define qué tipo de operaciones puede realizar en un tipo de recurso determinado. Por ejemplo: 
- **Escribir** le permite realizar operaciones PUT, POST, PATCH y DELETE.
- **Leer** le permite realizar operaciones GET.

Este artículo solo trata los distintos roles que existen actualmente. Sin embargo, cuando se asigna un rol a un usuario, puede limitar aún más las acciones permitidas mediante la definición de un ámbito. Esto resulta útil si desea convertir a alguien en colaborador del sitio web, pero solo para un grupo de recursos.

> [!NOTE]
> Las definiciones de rol de Azure están en constante evolución. Este artículo se mantiene tan actualizado como sea posible, pero las últimas definiciones de roles puede encontrarlas en Azure PowerShell. Utilice el cmdlet [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) para ver todos los roles actuales. Puede profundizar en un rol específico mediante `(get-azurermroledefinition "<role name>").actions` o `(get-azurermroledefinition "<role name>").notactions`, según corresponda. Use [AzureRmProviderOperation Get](/powershell/module/azurerm.resources/get-azurermprovideroperation) para ver las operaciones de proveedores de recursos de Azure.


| Rol integrado | DESCRIPCIÓN |
| --- | --- |
| [Propietario](#owner) | Puede administrar todo el contenido, incluido el acceso |
| [Colaborador](#contributor) | Puede administrar todo el contenido, excepto el acceso |
| [Lector](#reader) | Puede ver todo el contenido, pero no puede realizar cambios |
| [Colaborador de servicio de administración de API](#api-management-service-contributor) | Puede administrar servicios de administración de API |
| [Rol del operador del servicio API Management](#api-management-service-operator-role) | Puede administrar servicios de administración de API |
| [Rol de lector del servicio API Management](#api-management-service-reader-role) | Puede administrar servicios de administración de API |
| [Colaborador de componentes de Application Insights](#application-insights-component-contributor) | Puede administrar los componentes de Application Insights |
| [Depurador de instantáneas de Application Insights](#application-insights-snapshot-debugger) | Concede permiso al usuario para usar las características del depurador de instantáneas de Application Insights. |
| [Operador de trabajos de Automation](#automation-job-operator) | Permite crear y administrar trabajos con los runbooks de Automation. |
| [Operador de Automation](#automation-operator) | Puede iniciar, detener, suspender y reanudar trabajos |
| [Operador de runbooks de Automation](#automation-runbook-operator) | Permite leer las propiedades de runbook para poder crear trabajos del runbook. |
| [Propietario del registro de Azure Stack](#azure-stack-registration-owner) | Permite administrar los registros de Azure Stack. |
| [Colaborador de copias de seguridad](#backup-contributor) | Puede administrar todas las acciones de administración de copias de seguridad, excepto la creación del almacén de Recovery Services y la concesión de acceso a otros usuarios |
| [Operador de copias de seguridad](#backup-operator) | Puede administrar todas las acciones de administración de copias de seguridad, excepto la eliminación de copias de seguridad y la concesión de acceso a otros usuarios |
| [Lector de copias de seguridad](#backup-reader) | Puede supervisar la administración de copias de seguridad en el almacén de Recovery Services |
| [Lector de facturación](#billing-reader) | Puede ver toda la información de facturación |
| [Colaborador de BizTalk](#biztalk-contributor) | Puede administrar los servicios de BizTalk |
| [Colaborador de punto de conexión de CDN](#cdn-endpoint-contributor) | Puede administrar puntos de conexión de CDN, pero no conceder acceso a otros usuarios. |
| [Lector de punto de conexión de CDN](#cdn-endpoint-reader) | Puede ver puntos de conexión de CDN, pero no hacer cambios. |
| [Colaborador de perfil de CDN](#cdn-profile-contributor) | Puede administrar perfiles de CDN y sus puntos de conexión, pero no conceder acceso a otros usuarios. |
| [Lector de perfil de CDN](#cdn-profile-reader) | Puede ver perfiles de CDN y sus puntos de conexión, pero no hacer cambios. |
| [Colaborador de la red clásica](#classic-network-contributor) | Puede administrar IP reservadas y redes virtuales clásicas |
| [Colaborador de cuentas de almacenamiento clásico](#classic-storage-account-contributor) | Puede administrar las cuentas de almacenamiento clásico |
| [Rol de servicio de operador de claves de cuentas de almacenamiento clásicas](#classic-storage-account-key-operator-service-role) | Los operadores de claves de cuentas de almacenamiento clásicas pueden enumerar y regenerar claves en cuentas de almacenamiento clásicas |
| [Colaborador de la máquina virtual clásica](#classic-virtual-machine-contributor) | Puede administrar máquinas virtuales clásicas, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas |
| [Colaborador de ClearDB MySQL DB](#cleardb-mysql-db-contributor) | Puede administrar bases de datos ClearDB MySQL |
| [Rol de lector de cuentas de Cosmos DB](#cosmos-db-account-reader-role) | Puede leer los datos de cuentas de Azure Cosmos DB. Vea [Colaborador de cuenta de DocumentDB](#documentdb-account-contributor) para administrar cuentas de Azure Cosmos DB. |
| [Colaborador de Factoría de datos](#data-factory-contributor) | Crear y administrar factorías de datos y recursos secundarios dentro de ellos. |
| [Desarrollador de Data Lake Analytics](#data-lake-analytics-developer) | Le permite enviar, supervisar y administrar sus propios trabajos, pero no crear ni eliminar cuentas de Data Lake Analytics. |
| [Usuario de DevTest Labs](#devtest-labs-user) | Puede ver todo el contenido así como conectar, iniciar, reiniciar y apagar las máquinas virtuales |
| [Colaborador de zona DNS](#dns-zone-contributor) | Puede administrar registros y zonas DNS. |
| [Colaborador de cuenta de DocumentDB](#documentdb-account-contributor) | Puede administrar cuentas de Azure Cosmos DB. Azure Cosmos DB se llamaba anteriormente DocumentDB. |
| [Colaborador de la cuenta de Sistemas inteligentes](#intelligent-systems-account-contributor) | Puede administrar cuentas de Sistemas inteligentes |
| [Colaborador de almacén de claves](#key-vault-contributor) | Le permite administrar almacenes de claves, pero no acceder a ellos. |
| [Creador de laboratorio](#lab-creator) | Le permite crear, administrar y eliminar los laboratorios administrados en sus cuentas de Azure Lab. |
| [Colaborador de Log Analytics](#log-analytics-contributor) | Un colaborador de Log Analytics puede leer todos los datos de supervisión y editar la configuración de supervisión. La edición de la configuración de supervisión incluye la posibilidad de añadir la extensión de máquina virtual a las máquinas virtuales, leer las claves de las cuentas de almacenamiento para poder configurar la recopilación de registros de Azure Storage, crear y configurar cuentas de Automation, añadir soluciones y configurar Azure Diagnostics en todos los recursos de Azure. |
| [Lector de Log Analytics](#log-analytics-reader) | Un lector de Log Analytics puede ver y buscar todos los datos de supervisión, así como consultar la configuración de supervisión, incluida la de Azure Diagnostics en todos los recursos de Azure. |
| [Colaborador de aplicación lógica](#logic-app-contributor) | Le permite administrar aplicaciones lógicas, pero no acceder a ellas. |
| [Operador de aplicación lógica](#logic-app-operator) | Le permite leer, habilitar y deshabilitar aplicaciones lógicas. |
| [Colaborador de identidad administrada](#managed-identity-contributor) | Le permite crear, leer, actualizar y eliminar identidades asignadas por el usuario. |
| [Operador de identidad administrada](#managed-identity-operator) | Le permite leer y asignar identidades asignadas por el usuario. |
| [Colaborador de supervisión](#monitoring-contributor) | Puede leer todos los datos de supervisión y editar la configuración de supervisión. Consulte también [Introducción a roles, permisos y seguridad con Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Lector de supervisión](#monitoring-reader) | Puede leer todos los datos de supervisión (métricas, registros, etc.). Consulte también [Introducción a roles, permisos y seguridad con Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Colaborador de la red](#network-contributor) | Puede administrar todos los recursos de red |
| [Colaborador de la cuenta de NewRelic APM](#new-relic-apm-account-contributor) | Le permite administrar las aplicaciones y cuentas de Application Performance Management de New Relic, pero no acceder a ellas. |
| [Colaborador de la memoria caché de Redis](#redis-cache-contributor) | Puede administrar memorias caché en Redis |
| [Colaborador de colecciones de trabajos de Scheduler](#scheduler-job-collections-contributor) | Puede administrar las colecciones de trabajo de Scheduler |
| [Colaborador del servicio Search](#search-service-contributor) | Puede administrar los servicios de búsqueda |
| [Administrador de seguridad](#security-admin) | Solo en Security Center: puede ver las directivas de seguridad, ver los estados de seguridad, editar las directivas de seguridad, ver alertas y recomendaciones, y descartar alertas y recomendaciones |
| [Administrador de seguridad](#security-manager) | Puede administrar los componentes y las directivas de seguridad, además de las máquinas virtuales |
| [Lector de seguridad](#security-reader) | Solo en Security Center: puede ver las recomendaciones y alertas, ver las directivas de seguridad y ver los estados de seguridad, pero no puede realizar cambios |
| [Colaborador de Site Recovery](#site-recovery-contributor) | Puede administrar todas las acciones de administración de Site Recovery, excepto la creación del almacén de Recovery Services y la asignación de acceso a otros usuarios |
| [Operador de Site Recovery](#site-recovery-operator) | Puede realizar una conmutación por error y por recuperación, pero no puede realizar otras acciones de administración de Site Recovery ni asignar acceso a otros usuarios |
| [Lector de Site Recovery](#site-recovery-reader) | Puede supervisar el estado de recuperación de Site Recovery en el almacén de Recovery Services y generar incidencias de soporte técnico |
| [Colaborador de Base de datos de SQL](#sql-db-contributor) | Puede administrar bases de datos SQL, pero no las directivas relacionadas con la seguridad. |
| [Administrador de seguridad SQL](#sql-security-manager) | Puede administrar las directivas relacionadas con la seguridad de las bases de datos y los servidores SQL. |
| [Colaborador de SQL Server](#sql-server-contributor) | Puede administrar las bases de datos y los servidores SQL, pero no las directivas relacionadas con su seguridad |
| [Colaborador de la cuenta de almacenamiento](#storage-account-contributor) | Puede administrar cuentas de almacenamiento, pero no acceder a ellas. |
| [Rol de servicio de operador de claves de cuentas de almacenamiento](#storage-account-key-operator-service-role) | Los operadores de claves de cuentas de almacenamiento pueden enumerar y regenerar claves de cuentas de almacenamiento. |
| [Colaborador de la solicitud de soporte técnico](#support-request-contributor) | Puede crear y administrar incidencias de soporte técnico en el ámbito de la suscripción |
| [Colaborador de Traffic Manager](#traffic-manager-contributor) | Le permite administrar perfiles de Traffic Manager, pero no controlar los usuarios que tienen acceso a ellos. |
| [Administrador de acceso de usuario](#user-access-administrator) | Puede administrar el acceso de usuarios a los recursos de Azure |
| [Inicio de sesión de administrador de Virtual Machine](#virtual-machine-administrator-login) | Los usuarios con este rol tienen la posibilidad de iniciar sesión en una máquina virtual con privilegios de administrador de Windows o de usuario raíz de Linux. |
| [Colaborador de la máquina virtual](#virtual-machine-contributor) | Puede administrar máquinas virtuales, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas |
| [Inicio de sesión de usuario de Virtual Machine](#virtual-machine-user-login) | Los usuarios con este rol tienen la posibilidad de iniciar sesión en una máquina virtual como un usuario normal. |
| [Colaborador de plan web](#web-plan-contributor) | Puede administrar planes web |
| [Colaborador de sitio web](#website-contributor) | Puede administrar sitios web, pero no los planes web a los que están conectados |

Las tablas siguientes describen los permisos específicos concedidos a cada rol. En ellas, se incluyen elementos correspondientes a las categorías **Actions** (para conceder permisos) y **NotActions** (para restringir dichos permisos).

## <a name="owner"></a>Propietario
Puede administrar todo el contenido, incluido el acceso

| **Acciones** |  |
| --- | --- |
| * | Crear y administrar recursos de todos los tipos |

## <a name="contributor"></a>Colaborador
Puede administrar todo el contenido, excepto el acceso

| **Acciones** |  |
| --- | --- |
| * | Crear y administrar recursos de todos los tipos |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete | No puede eliminar roles ni asignaciones de roles. |
| Microsoft.Authorization/*/Write | No puede crear roles ni asignaciones de roles. |
| Microsoft.Authorization/elevateAccess/Action | Concede al llamador acceso de administrador de acceso de usuario en el ámbito de inquilinos |

## <a name="reader"></a>Lector
Puede ver todo el contenido, pero no puede realizar cambios

| **Acciones** |  |
| --- | --- |
| */read | Leer recursos de todos los tipos, excepto secretos. |

## <a name="api-management-service-contributor"></a>Colaborador de servicio de administración de API
Puede administrar servicios de administración de API

| **Acciones** |  |
| --- | --- |
| Microsoft.ApiManagement/service/* | Crear y administrar servicio API Management |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer roles y asignaciones de roles |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="api-management-service-operator-role"></a>Rol del operador del servicio API Management
Puede administrar servicios de administración de API

| **Acciones** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Lectura de instancias del servicio API Management |
| Microsoft.ApiManagement/service/backup/action | Copia de seguridad del servicio API Management en el contenedor especificado de una cuenta de almacenamiento proporcionada por el usuario |
| Microsoft.ApiManagement/service/delete | Eliminación de una instancia del servicio API Management |
| Microsoft.ApiManagement/service/managedeployments/action | Cambio de SKU/unidades; adición o eliminación de las implementaciones regionales del servicio API Management |
| Microsoft.ApiManagement/service/read | Lectura de los metadatos de una instancia del servicio API Management |
| Microsoft.ApiManagement/service/restore/action | Restauración del servicio API Management desde el contenedor especificado de una cuenta de almacenamiento proporcionada por el usuario |
| Microsoft.ApiManagement/service/updatecertificate/action | Carga el certificado SSL de un servicio API Management |
| Microsoft.ApiManagement/service/updatehostname/action | Configuración, actualización o eliminación de los nombres de dominio personalizado de un servicio API Management |
| Microsoft.ApiManagement/service/write | Creación de una nueva instancia del servicio API Management |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer roles y asignaciones de roles |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Obtiene una lista de claves de usuario |

## <a name="api-management-service-reader-role"></a>Rol de lector del servicio API Management
Puede administrar servicios de administración de API

| **Acciones** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Lectura de instancias del servicio API Management |
| Microsoft.ApiManagement/service/read | Lectura de los metadatos de una instancia del servicio API Management |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer roles y asignaciones de roles |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Obtiene una lista de claves de usuario |

## <a name="application-insights-component-contributor"></a>Colaborador de componentes de Application Insights
Puede administrar los componentes de Application Insights

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Insights/components/* | Crear y administrar componentes de Insights |
| Microsoft.Insights/webtests/* | Crear y administrar pruebas web |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="application-insights-snapshot-debugger"></a>Depurador de instantáneas de Application Insights
Concede permiso al usuario para usar las características del depurador de instantáneas de Application Insights.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Insights/components/*/read |  |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="automation-job-operator"></a>Operador de trabajos de Automation
Permite crear y administrar trabajos con los runbooks de Automation.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Automation/automationAccounts/jobs/read | Obtiene un trabajo de Azure Automation |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Reanuda un trabajo de Azure Automation |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Detiene un trabajo de Azure Automation |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lee los recursos de Hybrid Runbook Worker |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Obtiene un flujo de trabajos de Azure Automation |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende un trabajo de Azure Automation |
| Microsoft.Automation/automationAccounts/jobs/write | Crea un trabajo de Azure Automation |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="automation-operator"></a>Operador de Automation
Puede iniciar, detener, suspender y reanudar trabajos

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Automation/automationAccounts/jobs/read | Leer trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Reanudar un trabajo de cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Detener un trabajo de cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Leer transmisiones de trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspender un trabajo de cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobs/write | Escribir trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Leer un programa de trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Leer un programa de trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtiene el área de trabajo vinculada a la cuenta de Automation. |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lee los recursos de Hybrid Runbook Worker |
| Microsoft.Automation/automationAccounts/read | Leer cuentas de automatización |
| Microsoft.Automation/automationAccounts/runbooks/read | Leer runbooks de automatización |
| Microsoft.Automation/automationAccounts/schedules/read | Leer esquemas de cuentas de automatización |
| Microsoft.Automation/automationAccounts/schedules/write | Escribir esquemas de cuentas de automatización |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="automation-runbook-operator"></a>Operador de runbooks de Automation
Permite leer las propiedades de runbook para poder crear trabajos del runbook.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Automation/automationAccounts/runbooks/read | Obtiene un runbook de Azure Automation |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="azure-stack-registration-owner"></a>Propietario del registro de Azure Stack
Permite administrar los registros de Azure Stack.

| **Acciones** |  |
| --- | --- |
| Microsoft.AzureStack/registrations/products/listDetails/action | Recupera detalles ampliados de un producto de Marketplace de Azure Stack. |
| Microsoft.AzureStack/registrations/products/read | Obtiene las propiedades de un producto de Marketplace de Azure Stack. |
| Microsoft.AzureStack/registrations/read | Obtiene las propiedades de un registro de Azure Stack. |

## <a name="backup-contributor"></a>Colaborador de copias de seguridad
Puede administrar todas las acciones de administración de copias de seguridad, excepto la creación del almacén de Recovery Services y la concesión de acceso a otros usuarios

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Network/virtualNetworks/read | Leer redes virtuales |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Administrar los resultados de la operación de administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Crear y administrar contenedores de copias de seguridad dentro de tejidos de copia de seguridad del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Crear y administrar trabajos de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabajos de copia de seguridad a un archivo de Excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Crear y administrar metadatos relacionados con la administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Crear y administrar resultados de operaciones de administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | Crear y administrar directivas de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Crear y administrar elementos de los que se puede realizar una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Crear y administrar elementos de los que se ha realizado una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Crear y administrar contenedores que incluyen elementos de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/certificates/* | Crear y administrar certificados relacionados con copias de seguridad en el almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Crear y administrar información ampliada relacionada con el almacén |
| Microsoft.RecoveryServices/Vaults/read | Leer almacenes de Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Administrar la operación de detección para capturar contenedores recién creados |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Crear y administrar identidades registradas |
| Microsoft.RecoveryServices/Vaults/usages/* | Crear y administrar el uso del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Devuelve resúmenes de los elementos y servidores protegidos para un almacén de Recovery Services. |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Storage/storageAccounts/read | Leer cuentas de almacenamiento |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp es una operación interna que el servicio usa |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtiene las alertas del almacén de Recovery Services. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Devuelve el resultado de la operación del trabajo de exportación. |
| Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="backup-operator"></a>Operador de copias de seguridad
Puede administrar todas las acciones de administración de copias de seguridad, excepto la eliminación de copias de seguridad y la concesión de acceso a otros usuarios

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Network/virtualNetworks/read | Leer redes virtuales |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Leer resultados de la operación de administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | Leer los resultados de la operación en los contenedores de protección |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/backup/action | Realizar la operación de copia de seguridad a petición en un elemento del que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | Leer el resultado de la operación realizada en un elemento del que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | Devuelve el estado de la operación realizada en los elementos protegidos. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Leer elementos de los que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | Leer punto de recuperación de un elemento del que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/restore/action | Realizar una operación de restauración con un punto de recuperación de un elemento del que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | Crear un elemento de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Leer contenedores que incluyen un elemento de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Crear y administrar trabajos de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Cancela el trabajo |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Devuelve el resultado de la operación de trabajo. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Devuelve todos los objetos de trabajo |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabajos de copia de seguridad a un archivo de Excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Leer metadatos relacionados con la administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Crear y administrar resultados de operaciones de administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Leer resultados de las operaciones realizadas en directivas de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Leer directivas de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Crear y administrar elementos de los que se puede realizar una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Devuelve una lista de todos los elementos que se pueden proteger. |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Leer elementos de los que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Leer contenedores de los que se ha hecho una copia de seguridad, que incluyen elementos de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Devuelve resúmenes de los elementos y servidores protegidos para un almacén de Recovery Services. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Leer información ampliada relacionada con el almacén |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | Escribir información ampliada relacionada con el almacén |
| Microsoft.RecoveryServices/Vaults/read | Leer almacenes de Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Administrar la operación de detección para capturar contenedores recién creados |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Leer resultados de la operación realizada en elementos registrados del almacén |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Leer elementos registrados del almacén |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Escribir elementos registrados en el almacén |
| Microsoft.RecoveryServices/Vaults/usages/read | Leer uso del almacén de Recovery Services |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Storage/storageAccounts/read | Leer cuentas de almacenamiento |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Aprovisiona una recuperación de elementos instantánea para los elementos protegidos |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revoca la recuperación de elementos instantánea para los elementos protegidos |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp es una operación interna que el servicio usa |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtiene las alertas del almacén de Recovery Services. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Devuelve el resultado de la operación del trabajo de exportación. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
| Microsoft.RecoveryServices/Vaults/certificates/write | La operación Actualizar certificado de recursos permite actualizar el certificado de credencial de recursos o almacenes. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="backup-reader"></a>Lector de copias de seguridad
Puede supervisar la administración de copias de seguridad en el almacén de Recovery Services

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Leer resultados de la operación de administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | Leer los resultados de la operación en los contenedores de protección |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | Leer el resultado de la operación realizada en un elemento del que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | Devuelve el estado de la operación realizada en los elementos protegidos. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Leer elementos de los que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Leer contenedores que incluyen un elemento de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Leer resultados de los trabajos de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Leer trabajos de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabajos de copia de seguridad a un archivo de Excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Leer metadatos relacionados con la administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Leer resultados de la operación de administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Leer resultados de las operaciones realizadas en directivas de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Leer directivas de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Leer elementos de los que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Leer contenedores de los que se ha hecho una copia de seguridad, que incluyen elementos de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Devuelve resúmenes de los elementos y servidores protegidos para un almacén de Recovery Services. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Leer información ampliada relacionada con el almacén |
| Microsoft.RecoveryServices/Vaults/read | Leer almacenes de Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Leer el resultado de una operación de detección para capturar contenedores recién creados |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Leer resultados de la operación realizada en elementos registrados del almacén |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Leer elementos registrados del almacén |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp es una operación interna que el servicio usa |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtiene las alertas del almacén de Recovery Services. |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | Obtiene los puntos de recuperación de los elementos protegidos. |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Devuelve el resultado de la operación del trabajo de exportación. |
| Microsoft.RecoveryServices/Vaults/usages/read | Leer uso del almacén de Recovery Services |

## <a name="billing-reader"></a>Lector de facturación
Puede ver toda la información de facturación

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Billing/*/read | Leer la información de facturación |
| Microsoft.Consumption/*/read |  |
| Microsoft.Commerce/*/read |  |
| Microsoft.Management/managementGroups/read | Enumera los grupos de administración del usuario autenticado. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="biztalk-contributor"></a>Colaborador de BizTalk
Puede administrar los servicios de BizTalk

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.BizTalkServices/BizTalk/* | Crear y administrar los servicios de BizTalk |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="cdn-endpoint-contributor"></a>Colaborador de punto de conexión de CDN
Puede administrar puntos de conexión de CDN, pero no conceder acceso a otros usuarios.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/* |  |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="cdn-endpoint-reader"></a>Lector de punto de conexión de CDN
Puede ver puntos de conexión de CDN, pero no hacer cambios.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/*/read |  |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="cdn-profile-contributor"></a>Colaborador de perfil de CDN
Puede administrar perfiles de CDN y sus puntos de conexión, pero no conceder acceso a otros usuarios.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/* |  |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="cdn-profile-reader"></a>Lector de perfil de CDN
Puede ver perfiles de CDN y sus puntos de conexión, pero no hacer cambios.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/*/read |  |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="classic-network-contributor"></a>Colaborador de la red clásica
Puede administrar IP reservadas y redes virtuales clásicas

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.ClassicNetwork/* | Crear y administrar redes clásicas |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="classic-storage-account-contributor"></a>Colaborador de cuentas de almacenamiento clásico
Puede administrar las cuentas de almacenamiento clásico

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.ClassicStorage/storageAccounts/* | Crear y administrar cuentas de almacenamiento |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="classic-storage-account-key-operator-service-role"></a>Rol de servicio de operador de claves de cuentas de almacenamiento clásicas
Los operadores de claves de cuentas de almacenamiento clásicas pueden enumerar y regenerar claves en cuentas de almacenamiento clásicas

| **Acciones** |  |
| --- | --- |
| Microsoft.ClassicStorage/storageAccounts/listkeys/action | Enumera las claves de acceso de las cuentas de almacenamiento. |
| Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Regenera las claves de acceso existentes de la cuenta de almacenamiento. |

## <a name="classic-virtual-machine-contributor"></a>Colaborador de la máquina virtual clásica
Puede administrar máquinas virtuales clásicas, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.ClassicCompute/domainNames/* | Crear y administrar nombres de dominio de proceso clásico |
| Microsoft.ClassicCompute/virtualMachines/* | Crear y administrar máquinas virtuales |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | Unir los grupos de seguridad de red |
| Microsoft.ClassicNetwork/reservedIps/link/action | Vincular IP reservadas |
| Microsoft.ClassicNetwork/reservedIps/read | Leer direcciones IP reservadas |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Unirse a redes virtuales |
| Microsoft.ClassicNetwork/virtualNetworks/read | Leer redes virtuales |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Leer discos de cuentas de almacenamiento |
| Microsoft.ClassicStorage/storageAccounts/images/read | Leer imágenes de cuentas de almacenamiento |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Enumerar claves de cuentas de almacenamiento |
| Microsoft.ClassicStorage/storageAccounts/read | Leer cuentas de almacenamiento clásico |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="cleardb-mysql-db-contributor"></a>Colaborador de ClearDB MySQL DB
Puede administrar bases de datos ClearDB MySQL

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| successbricks.cleardb/databases/* | Crear y administrar bases de datos ClearDB MySQL |

## <a name="cosmos-db-account-reader-role"></a>Rol de lector de cuentas de Cosmos DB
Puede leer los datos de cuentas de Azure Cosmos DB. Vea [Colaborador de cuenta de DocumentDB](#documentdb-account-contributor) para administrar cuentas de Azure Cosmos DB.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles, puede leer permisos dados a cada usuario |
| Microsoft.DocumentDB/*/read | Leer cualquier colección |
| Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Leer el panel de claves de solo lectura |
| Microsoft.Insights/MetricDefinitions/read | Leer definiciones de métricas |
| Microsoft.Insights/Metrics/read | Leer métricas de la cuenta |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="data-factory-contributor"></a>Colaborador de Factoría de datos
Crear y administrar factorías de datos y recursos secundarios dentro de ellos.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.DataFactory/dataFactories/* | Crear y administrar factorías de datos y recursos secundarios dentro de ellos. |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="data-lake-analytics-developer"></a>Desarrollador de Data Lake Analytics
Le permite enviar, supervisar y administrar sus propios trabajos, pero no crear ni eliminar cuentas de Data Lake Analytics.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.BigAnalytics/accounts/* |  |
| Microsoft.DataLakeAnalytics/accounts/* |  |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

| **NotActions** |  |
| --- | --- |
| Microsoft.BigAnalytics/accounts/Delete |  |
| Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
| Microsoft.BigAnalytics/accounts/Write |  |
| Microsoft.DataLakeAnalytics/accounts/Delete | Elimina la cuenta de DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Concede permisos para cancelar trabajos que enviaron otros usuarios. |
| Microsoft.DataLakeAnalytics/accounts/Write | Crea o actualiza una cuenta de DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Crea o actualiza una cuenta vinculada de DataLakeStore en la cuenta de DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Anula la vinculación de una cuenta de DataLakeStore a la cuenta de DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Crea o actualiza una cuenta de almacenamiento vinculada a una cuenta de DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Anula la vinculación de una cuenta de almacenamiento a la cuenta de DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Crea o actualiza una regla de firewall. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Elimina una regla de firewall. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Crea o actualiza una directiva de proceso. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Elimina una directiva de proceso. |

## <a name="devtest-labs-user"></a>Usuario de DevTest Labs
Puede ver todo el contenido así como conectar, iniciar, reiniciar y apagar las máquinas virtuales

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Compute/availabilitySets/read | Leer las propiedades de los conjuntos de disponibilidad |
| Microsoft.Compute/virtualMachines/*/read | Leer las propiedades de una máquina virtual (tamaños de máquinas virtuales, el estado de tiempo de ejecución, extensiones de máquina virtual, etc.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Desasignar máquinas virtuales |
| Microsoft.Compute/virtualMachines/read | Leer las propiedades de una máquina virtual |
| Microsoft.Compute/virtualMachines/restart/action | Reiniciar máquinas virtuales |
| Microsoft.Compute/virtualMachines/start/action | Iniciar máquinas virtuales |
| Microsoft.DevTestLab/*/read | Leer las propiedades de un laboratorio |
| Microsoft.DevTestLab/labs/createEnvironment/action | Crear un entorno de laboratorio |
| Microsoft.DevTestLab/labs/claimAnyVm/action | Reclama una máquina virtual aleatoria en el laboratorio. |
| Microsoft.DevTestLab/labs/formulas/delete | Eliminar fórmulas |
| Microsoft.DevTestLab/labs/formulas/read | Leer fórmulas |
| Microsoft.DevTestLab/labs/formulas/write | Agregar o modificar fórmulas |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Evaluar directivas de laboratorio |
| Microsoft.DevTestLab/labs/virtualMachines/claim/action | Toma la propiedad de una máquina virtual existente |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Unirse a un grupo de direcciones de back-end del equilibrador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Unirse a una regla NAT de entrada del equilibrador de carga |
| Microsoft.Network/networkInterfaces/*/read | Leer las propiedades de una interfaz de red (por ejemplo, todos los equilibradores de carga de los que forma parte de la interfaz de red) |
| Microsoft.Network/networkInterfaces/join/action | Unirse a una máquina virtual a una interfaz de red |
| Microsoft.Network/networkInterfaces/read | Leer interfaces de red |
| Microsoft.Network/networkInterfaces/write | Escribir interfaces de red |
| Microsoft.Network/publicIPAddresses/*/read | Leer las propiedades de una dirección IP pública |
| Microsoft.Network/publicIPAddresses/join/action | Unirse a una dirección IP pública |
| Microsoft.Network/publicIPAddresses/read | Leer direcciones IP públicas de red |
| Microsoft.Network/virtualNetworks/subnets/join/action | Unirse a una red virtual |
| Microsoft.Resources/deployments/operations/read | Leer operaciones de implementación |
| Microsoft.Resources/deployments/read | Leer implementaciones |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Storage/storageAccounts/listKeys/action | Enumerar claves de cuentas de almacenamiento |

| **NotActions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/vmSizes/read | Enumera los tamaños disponibles a los que se puede actualizar la máquina virtual |

## <a name="dns-zone-contributor"></a>Colaborador de zona DNS
Puede administrar registros y zonas DNS.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Network/dnsZones/* | Crear y administrar registros y zonas DNS |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el estado de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar vales de soporte técnico |

## <a name="documentdb-account-contributor"></a>Colaborador de cuenta de DocumentDB
Puede administrar cuentas de Azure Cosmos DB. Azure Cosmos DB se llamaba anteriormente DocumentDB.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.DocumentDb/databaseAccounts/* | Crear y administrar cuentas de Azure Cosmos DB |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="intelligent-systems-account-contributor"></a>Colaborador de la cuenta de Sistemas inteligentes
Puede administrar cuentas de Sistemas inteligentes

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.IntelligentSystems/accounts/* | Crear y administrar cuentas de sistemas inteligentes |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="key-vault-contributor"></a>Colaborador de almacén de claves
Le permite administrar almacenes de claves, pero no acceder a ellos.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.KeyVault/* |  |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

| **NotActions** |  |
| --- | --- |
| Microsoft.KeyVault/locations/deletedVaults/purge/action | Purga un almacén de claves eliminado temporalmente |
| Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Creador de laboratorio
Le permite crear, administrar y eliminar los laboratorios administrados en sus cuentas de Azure Lab.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.ManagedLab/labAccounts/createLab/action | Crea un laboratorio en una cuenta de laboratorio. |
| Microsoft.ManagedLab/labAccounts/*/read |  |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="log-analytics-contributor"></a>Colaborador de Log Analytics
Un colaborador de Log Analytics puede leer todos los datos de supervisión y editar la configuración de supervisión. La edición de la configuración de supervisión incluye la posibilidad de añadir la extensión de máquina virtual a las máquinas virtuales, leer las claves de las cuentas de almacenamiento para poder configurar la recopilación de registros de Azure Storage, crear y configurar cuentas de Automation, añadir soluciones y configurar Azure Diagnostics en todos los recursos de Azure.

| **Acciones** |  |
| --- | --- |
| */read | Leer recursos de todos los tipos, excepto secretos. |
| Microsoft.Automation/automationAccounts/* |  |
| Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Enumera las claves de acceso de las cuentas de almacenamiento. |
| Microsoft.Compute/virtualMachines/extensions/* |  |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Insights/diagnosticSettings/* | Crea, actualiza o lee la configuración de diagnóstico de Analysis Server. |
| Microsoft.OperationalInsights/* |  |
| Microsoft.OperationsManagement/* |  |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
| Microsoft.Storage/storageAccounts/listKeys/action | Devuelve las claves de acceso de la cuenta de almacenamiento especificada. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="log-analytics-reader"></a>Lector de Log Analytics
Un lector de Log Analytics puede ver y buscar todos los datos de supervisión, así como consultar la configuración de supervisión, incluida la de Azure Diagnostics en todos los recursos de Azure.

| **Acciones** |  |
| --- | --- |
| */read | Leer recursos de todos los tipos, excepto secretos. |
| Microsoft.OperationalInsights/workspaces/analytics/query/action | Realiza búsquedas mediante el nuevo motor. |
| Microsoft.OperationalInsights/workspaces/search/action | Ejecuta una consulta de búsqueda |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

| **NotActions** |  |
| --- | --- |
| Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera las claves compartidas del área de trabajo. Estas claves se utilizan para conectar los agentes de Microsoft Operational Insights al área de trabajo. |

## <a name="logic-app-contributor"></a>Colaborador de aplicación lógica
Le permite administrar aplicaciones lógicas, pero no acceder a ellas.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Enumera las claves de acceso de las cuentas de almacenamiento. |
| Microsoft.ClassicStorage/storageAccounts/read | Devuelve la cuenta de almacenamiento con la cuenta especificada. |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Insights/diagnosticSettings/* | Crea, actualiza o lee la configuración de diagnóstico de Analysis Server. |
| Microsoft.Insights/logdefinitions/* | Este permiso es necesario para los usuarios que necesitan acceder a registros de actividades a través del portal. Enumere las categorías de registro del registro de actividad. |
| Microsoft.Insights/metricDefinitions/* | Leer definiciones de métrica (lista de tipos de métricas disponibles para un recurso). |
| Microsoft.Logic/* | Administra los recursos de Logic Apps. |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/operationresults/read | Obtiene los resultados de la operación de suscripción. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Storage/storageAccounts/listkeys/action | Devuelve las claves de acceso de la cuenta de almacenamiento especificada. |
| Microsoft.Storage/storageAccounts/read | Devuelve la lista de cuentas de almacenamiento u obtiene las propiedades de la cuenta de almacenamiento especificada. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| Microsoft.Web/connectionGateways/* | Crea y administra una puerta de enlace de conexión. |
| Microsoft.Web/connections/* | Crea y administra una conexión. |
| Microsoft.Web/customApis/* | Crea y administra una API personalizada. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | Obtiene las propiedades de un plan de App Service |
| Microsoft.Web/sites/functions/listSecrets/action | Muestra secretos de Web Apps. |

## <a name="logic-app-operator"></a>Operador de aplicación lógica
Le permite leer, habilitar y deshabilitar aplicaciones lógicas.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/*/read | Permite leer reglas de alerta de Insights. |
| Microsoft.Insights/diagnosticSettings/*/read | Obtiene la configuración de diagnóstico de Logic Apps. |
| Microsoft.Insights/metricDefinitions/*/read | Obtiene las métricas disponibles para Logic Apps. |
| Microsoft.Logic/*/read | Lee los recursos de Logic Apps. |
| Microsoft.Logic/workflows/disable/action | Deshabilita el flujo de trabajo. |
| Microsoft.Logic/workflows/enable/action | Habilita el flujo de trabajo. |
| Microsoft.Logic/workflows/validate/action | Valida el flujo de trabajo. |
| Microsoft.Resources/deployments/operations/read | Obtiene o enumera las operaciones de implementación. |
| Microsoft.Resources/subscriptions/operationresults/read | Obtiene los resultados de la operación de suscripción. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| Microsoft.Web/connectionGateways/*/read | Lee las puertas de enlace de conexión. |
| Microsoft.Web/connections/*/read | Lee las conexiones. |
| Microsoft.Web/customApis/*/read | Lee la API personalizada. |
| Microsoft.Web/serverFarms/read | Obtiene las propiedades de un plan de App Service |

## <a name="managed-identity-contributor"></a>Colaborador de identidad administrada
Le permite crear, leer, actualizar y eliminar identidades asignadas por el usuario.

| **Acciones** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="managed-identity-operator"></a>Operador de identidad administrada
Le permite leer y asignar identidades asignadas por el usuario.

| **Acciones** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="monitoring-contributor"></a>Colaborador de supervisión
Puede leer todos los datos de supervisión y editar la configuración de supervisión. Consulte también [Introducción a roles, permisos y seguridad con Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Acciones** |  |
| --- | --- |
| */read | Leer recursos de todos los tipos, excepto secretos. |
| Microsoft.AlertsManagement/alerts/* |  |
| Microsoft.AlertsManagement/alertsSummary/* |  |
| Microsoft.Insights/AlertRules/* | Reglas de alerta de lectura, escritura y eliminación. |
| Microsoft.Insights/components/* | Leer, escribir o eliminar los componentes de Application Insights. |
| Microsoft.Insights/DiagnosticSettings/* | Configuración de diagnóstico de lectura, escritura y eliminación. |
| Microsoft.Insights/eventtypes/* | Enumerar eventos del registro de actividades (eventos de administración) de una suscripción. Este permiso es aplicable para el acceso mediante programación y mediante el portal al registro de actividades. |
| Microsoft.Insights/LogDefinitions/* | Este permiso es necesario para los usuarios que necesitan acceder a registros de actividades a través del portal. Enumere las categorías de registro del registro de actividad. |
| Microsoft.Insights/MetricDefinitions/* | Leer definiciones de métrica (lista de tipos de métricas disponibles para un recurso). |
| Microsoft.Insights/Metrics/* | Leer las métricas de un recurso. |
| Microsoft.Insights/Register/Action | Registrar el proveedor de Microsoft.Insights. |
| Microsoft.Insights/webtests/* | Leer, escribir o eliminar pruebas web de Application Insights. |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Leer, escribir o eliminar paquetes de soluciones de Log Analytics. |
| Microsoft.OperationalInsights/workspaces/savedSearches/* | Leer, escribir o eliminar búsquedas guardadas de Log Analytics. |
| Microsoft.OperationalInsights/workspaces/search/action | Buscar áreas de trabajo de Log Analytics. |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action | Enumerar las claves de un área de trabajo de Log Analytics. |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Leer, escribir o eliminar configuraciones de visión de almacenamiento de Log Analytics. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Lector de supervisión
Puede leer todos los datos de supervisión (métricas, registros, etc.). Consulte también [Introducción a roles, permisos y seguridad con Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Acciones** |  |
| --- | --- |
| */read | Leer recursos de todos los tipos, excepto secretos. |
| Microsoft.OperationalInsights/workspaces/search/action | Buscar datos de Log Analytics |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="network-contributor"></a>Colaborador de la red
Puede administrar todos los recursos de red

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Network/* | Crear y administrar redes |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="new-relic-apm-account-contributor"></a>Colaborador de la cuenta de NewRelic APM
Le permite administrar las aplicaciones y cuentas de Application Performance Management de New Relic, pero no acceder a ellas.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| NewRelic.APM/accounts/* |  |

## <a name="redis-cache-contributor"></a>Colaborador de la memoria caché de Redis
Puede administrar memorias caché en Redis

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Cache/redis/* | Crear y administrar memorias caché de Redis |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="scheduler-job-collections-contributor"></a>Colaborador de colecciones de trabajos de Scheduler
Puede administrar las colecciones de trabajo de Scheduler

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Scheduler/jobcollections/* | Crear y administrar colecciones de trabajos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="search-service-contributor"></a>Colaborador del servicio de búsqueda
Puede administrar los servicios de búsqueda

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Search/searchServices/* | Crear y administrar servicios de búsqueda |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="security-admin"></a>Administrador de seguridad
Solo en Security Center: puede ver las directivas de seguridad, ver los estados de seguridad, editar las directivas de seguridad, ver alertas y recomendaciones, y descartar alertas y recomendaciones

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Authorization/policyAssignments/* | Crear y administrar asignaciones de directivas |
| Microsoft.Authorization/policyDefinitions/* | Crear y administrar definiciones de directivas |
| Microsoft.Authorization/policySetDefinitions/* | Crear y administrar conjuntos de directivas |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.operationalInsights/workspaces/*/read | Ver datos de Log Analytics |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Security/*/read | Leer directivas y componentes de seguridad |
| Microsoft.Security/locations/alerts/dismiss/action | Descarta una alerta de seguridad. |
| Microsoft.Security/locations/tasks/dismiss/action | Descarta una recomendación de seguridad |
| Microsoft.Security/policies/write | Actualiza la directiva de seguridad |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="security-manager"></a>Administrador de seguridad
Puede administrar los componentes y las directivas de seguridad, además de las máquinas virtuales

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.ClassicCompute/*/read | Leer información de configuración de máquinas virtuales clásicas |
| Microsoft.ClassicCompute/virtualMachines/*/write | Escribir configuración de máquinas virtuales clásicas |
| Microsoft.ClassicNetwork/*/read | Leer información de configuración acerca de la red clásica |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Security/* | Crear y administrar las directivas y los componentes de seguridad |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="security-reader"></a>Lector de seguridad
Solo en Security Center: puede ver las recomendaciones y alertas, ver las directivas de seguridad y ver los estados de seguridad, pero no puede realizar cambios

| **Acciones** |  |
| --- | --- |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.operationalInsights/workspaces/*/read | Ver datos de Log Analytics |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Security/*/read | Leer directivas y componentes de seguridad |

## <a name="site-recovery-contributor"></a>Colaborador de Site Recovery
Puede administrar todas las acciones de administración de Site Recovery, excepto la creación del almacén de Recovery Services y la asignación de acceso a otros usuarios

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Network/virtualNetworks/read | Leer redes virtuales |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp es una operación interna que el servicio usa |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp es una operación interna que el servicio usa |
| Microsoft.RecoveryServices/Vaults/certificates/write | Actualiza el certificado de credenciales del almacén |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Crear y administrar información ampliada relacionada con el almacén |
| Microsoft.RecoveryServices/Vaults/read | Lee almacenes de Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Administrar la operación de detección para capturar contenedores recién creados |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Crear y administrar identidades registradas |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Crea o actualiza la configuración de las alertas de replicación |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Lee eventos de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | Crea y administra los tejidos de replicación |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Crea y administra los trabajos de replicación |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | Crea y administra las directivas de replicación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Crea y administra planes de recuperación |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Crea y administra la configuración de almacenamiento del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Lee la información del token del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/usages/read | Lee los detalles de uso de un almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | La operación Token de almacén se puede usar para obtener el token de almacén de las operaciones back-end a nivel de almacén. |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Lee las alertas del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Lee la configuración de notificaciones del almacén de Recovery Services |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Storage/storageAccounts/read | Leer cuentas de almacenamiento |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="site-recovery-operator"></a>Operador de Site Recovery
Puede realizar una conmutación por error y por recuperación, pero no puede realizar otras acciones de administración de Site Recovery ni asignar acceso a otros usuarios

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Network/virtualNetworks/read | Leer redes virtuales |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp es una operación interna que el servicio usa |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp es una operación interna que el servicio usa |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Leer información ampliada relacionada con el almacén |
| Microsoft.RecoveryServices/Vaults/read | Lee almacenes de Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Administrar la operación de detección para capturar contenedores recién creados |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Lee el estado de la operación y el resultado de una operación enviada |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Lee los contenedores registrados de un recurso |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lee la configuración de alertas de replicación |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Lee eventos de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Comprueba la coherencia del tejido |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Lee el tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Vuelve a asociar la puerta de enlace de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renueva el certificado de tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Lee las redes del tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | Lee la asignación de redes del tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Lee los contenedores de protección |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Obtiene una lista de todos los elementos que se pueden proteger |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplica un punto de recuperación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Confirma la conmutación por error |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Conmutación por error planeada |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Obtiene la lista de todos los elementos protegidos |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Obtiene la lista de los puntos de recuperación disponibles |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Repara una replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/reProtect/action | Inicia la reprotección de un elemento protegido |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/action | Inicia la conmutación por error de prueba de un elemento protegido |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Prueba la limpieza de la conmutación por error |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Conmutación por error |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Actualiza Mobility Service |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Lee las asignaciones de los contenedores de protección |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Lee los proveedores de Recovery Services |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/action | Actualiza los proveedores de Recovery Services |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Lee las clasificaciones de almacenamiento del tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | Lee las asignaciones de clasificaciones de almacenamiento |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Lee la información de vCenter registrada |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Crea y administra los trabajos de replicación |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Lee las directivas de replicación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Confirma la conmutación por error del plan de recuperación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Inicia la conmutación por error de un plan de recuperación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Lee los planes de recuperación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Inicia la reprotección de un plan de recuperación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Inicia la conmutación por error de prueba de un plan de recuperación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Inicia la limpieza de una conmutación por error de prueba de un plan de recuperación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Inicia la conmutación por error no planeada de un plan de recuperación |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Lee las alertas del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Lee la configuración de notificaciones del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Lee la configuración de almacenamiento de un almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Lee la información del token del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/usages/read | Lee los detalles de uso de un almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | La operación Token de almacén se puede usar para obtener el token de almacén de las operaciones back-end a nivel de almacén. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Storage/storageAccounts/read | Leer cuentas de almacenamiento |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="site-recovery-reader"></a>Lector de Site Recovery
Puede supervisar el estado de recuperación de Site Recovery en el almacén de Recovery Services y generar incidencias de soporte técnico

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp es una operación interna que el servicio usa |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Leer información ampliada relacionada con el almacén |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lee las alertas del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Lee la configuración de notificaciones del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/read | Lee almacenes de Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Administrar la operación de detección para capturar contenedores recién creados |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Lee el estado de la operación y el resultado de una operación enviada |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Lee los contenedores registrados de un recurso |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lee la configuración de alertas de replicación |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Lee eventos de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Lee el tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Lee las redes del tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | Lee la asignación de redes del tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Lee los contenedores de protección |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Obtiene una lista de todos los elementos que se pueden proteger |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Obtiene la lista de todos los elementos protegidos |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Obtiene la lista de los puntos de recuperación disponibles |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Lee las asignaciones de los contenedores de protección |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Lee los proveedores de Recovery Services |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Lee las clasificaciones de almacenamiento del tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | Lee las asignaciones de clasificaciones de almacenamiento |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Lee la información de vCenter registrada |
| Microsoft.RecoveryServices/vaults/replicationJobs/read | Lee el estado de los trabajos de replicación |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Lee las directivas de replicación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Lee los planes de recuperación |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Lee la configuración de almacenamiento de un almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Lee la información del token del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/usages/read | Lee los detalles de uso de un almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | La operación Token de almacén se puede usar para obtener el token de almacén de las operaciones back-end a nivel de almacén. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="sql-db-contributor"></a>Colaborador de Base de datos de SQL
Puede administrar bases de datos SQL, pero no las directivas relacionadas con la seguridad.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/databases/* | Crear y administrar bases de datos SQL |
| Microsoft.Sql/servers/read | Leer los servidores SQL Server |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* | No puede editar las directivas de auditoría |
| Microsoft.Sql/servers/databases/auditingSettings/* | No puede editar la configuración de auditoría |
| Microsoft.Sql/servers/databases/auditRecords/read | No puede leer los registros de auditoría. |
| Microsoft.Sql/servers/databases/connectionPolicies/* | No puede editar las directivas de conexión |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | No puede editar las directivas de enmascaramiento |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | No puede editar las directivas de alerta de seguridad |
| Microsoft.Sql/servers/databases/securityMetrics/* | No puede editar las métricas de seguridad |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>Administrador de seguridad SQL
Puede administrar las directivas relacionadas con la seguridad de las bases de datos y los servidores SQL.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer autorización de Microsoft |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Combina un recurso como una cuenta de almacenamiento o una instancia de SQL Database con una subred. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Sql/servers/auditingPolicies/* | Crear y administrar directivas de auditoría de SQL Server |
| Microsoft.Sql/servers/auditingSettings/* | Crear y administrar configuración de auditoría de SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Crear y administrar directivas de auditoría de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/auditingSettings/* | Crear y administrar configuración de auditoría de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/auditRecords/read | Leer registros de auditoría |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Crear y administrar directivas de conexión de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Crear y administrar directivas de enmascaramiento de datos de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/read | Leer bases de datos SQL |
| Microsoft.Sql/servers/databases/schemas/read | Leer esquemas de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Leer columnas de tablas de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/read | Leer tablas de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Crear y administrar directivas de alerta de seguridad de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/securityMetrics/* | Crear y administrar métricas de seguridad de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/firewallRules/* |  |
| Microsoft.Sql/servers/read | Leer los servidores SQL Server |
| Microsoft.Sql/servers/securityAlertPolicies/* | Crear y administrar directivas de alerta de seguridad de SQL Server |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="sql-server-contributor"></a>Colaborador de SQL Server
Puede administrar las bases de datos y los servidores SQL, pero no las directivas relacionadas con su seguridad

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/* | Crear y administrar servidores de SQL Server |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* | No puede editar las directivas de auditoría de SQL Server |
| Microsoft.Sql/servers/auditingSettings/* | No puede editar la configuración de auditoría de SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | No puede editar las directivas de auditoría de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/auditingSettings/* | No puede editar la configuración de auditoría de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/auditRecords/read | No puede leer los registros de auditoría. |
| Microsoft.Sql/servers/databases/connectionPolicies/* | No puede editar las directivas de conexión de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | No puede editar las directivas de enmascaramiento de datos de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | No puede editar las directivas de alerta de seguridad de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/securityMetrics/* | No puede editar las métricas de seguridad de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/securityAlertPolicies/* | No puede editar las directivas de alerta de seguridad de SQL Server |

## <a name="storage-account-contributor"></a>Colaborador de la cuenta de almacenamiento
Puede administrar cuentas de almacenamiento, pero no acceder a ellas.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer toda la autorización |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Insights/diagnosticSettings/* | Administrar la configuración de diagnóstico |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Combina un recurso como una cuenta de almacenamiento o una instancia de SQL Database con una subred. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Storage/storageAccounts/* | Crear y administrar cuentas de almacenamiento |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="storage-account-key-operator-service-role"></a>Rol de servicio de operador de claves de cuentas de almacenamiento
Los operadores de claves de cuentas de almacenamiento pueden enumerar y regenerar claves de cuentas de almacenamiento.

| **Acciones** |  |
| --- | --- |
| Microsoft.Storage/storageAccounts/listkeys/action | Devuelve las claves de acceso de la cuenta de almacenamiento especificada. |
| Microsoft.Storage/storageAccounts/regeneratekey/action | Regenera las claves de acceso de la cuenta de almacenamiento especificada. |

## <a name="support-request-contributor"></a>Colaborador de la solicitud de soporte técnico
Puede crear y administrar incidencias de soporte técnico en el ámbito de la suscripción

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer roles y asignaciones de roles |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="traffic-manager-contributor"></a>Colaborador de Traffic Manager
Le permite administrar perfiles de Traffic Manager, pero no controlar los usuarios que tienen acceso a ellos.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Network/trafficManagerProfiles/* |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="user-access-administrator"></a>Administrador de acceso de usuario
Puede administrar el acceso de usuarios a los recursos de Azure

| **Acciones** |  |
| --- | --- |
| */read | Leer recursos de todos los tipos, excepto secretos. |
| Microsoft.Authorization/* | Administrar la autorización |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="virtual-machine-administrator-login"></a>Inicio de sesión de administrador de Virtual Machine
-   Los usuarios con este rol tienen la posibilidad de iniciar sesión en una máquina virtual con privilegios de administrador de Windows o de usuario raíz de Linux.

| **Acciones** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="virtual-machine-contributor"></a>Colaborador de la máquina virtual
Puede administrar máquinas virtuales, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Compute/availabilitySets/* | Crear y administrar conjuntos de disponibilidad de proceso |
| Microsoft.Compute/locations/* | Crear y administrar ubicaciones de proceso |
| Microsoft.Compute/virtualMachines/* | Crear y administrar máquinas virtuales |
| Microsoft.Compute/virtualMachineScaleSets/* | Crear y administrar conjuntos de escalado de máquinas virtuales |
| Microsoft.DevTestLab/schedules/* |  |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Unir grupos de direcciones de back-end de puerta de enlace de aplicaciones de red |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Unirse a los grupos de direcciones de back-end de equilibrador de carga |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Unirse a conjuntos NAT de entrada del equilibrador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Unir las reglas NAT de entrada del equilibrador de carga |
| Microsoft.Network/loadBalancers/probes/join/action | Permite usar sondeos de un equilibrador de carga. Por ejemplo, con este permiso, la propiedad healthProbe de un conjunto de escalado de máquinas virtuales puede hacer referencia al sondeo. |
| Microsoft.Network/loadBalancers/read | Leer equilibradores de carga |
| Microsoft.Network/locations/* | Crear y administrar ubicaciones de red |
| Microsoft.Network/networkInterfaces/* | Crear y administrar interfaces de red |
| Microsoft.Network/networkSecurityGroups/join/action | Unir los grupos de seguridad de red |
| Microsoft.Network/networkSecurityGroups/read | Leer grupos de seguridad de red |
| Microsoft.Network/publicIPAddresses/join/action | Unir las direcciones IP públicas de red |
| Microsoft.Network/publicIPAddresses/read | Leer direcciones IP públicas de red |
| Microsoft.Network/virtualNetworks/read | Leer redes virtuales |
| Microsoft.Network/virtualNetworks/subnets/join/action | Unirse a subredes de redes virtuales |
| Microsoft.RecoveryServices/locations/* |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/*/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Devuelve detalles de objeto del elemento protegido |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | Crea un elemento protegido de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Crea una intención de protección de la copia de seguridad. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Devuelve todas las directivas de protección |
| Microsoft.RecoveryServices/Vaults/backupPolicies/write | Crea una directiva de protección |
| Microsoft.RecoveryServices/Vaults/read | La operación Get Vault obtiene un objeto que representa el recurso de Azure del tipo "almacén" |
| Microsoft.RecoveryServices/Vaults/usages/read | Devuelve los detalles de uso de un almacén de Recovery Services. |
| Microsoft.RecoveryServices/Vaults/write | La operación Create Vault crea un recurso de Azure del tipo "almacén" |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Storage/storageAccounts/listKeys/action | Enumerar claves de cuentas de almacenamiento |
| Microsoft.Storage/storageAccounts/read | Leer cuentas de almacenamiento |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="virtual-machine-user-login"></a>Inicio de sesión de usuario de Virtual Machine
Los usuarios con este rol tienen la posibilidad de iniciar sesión en una máquina virtual como un usuario normal.

| **Acciones** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="web-plan-contributor"></a>Colaborador de plan web
Puede administrar planes web

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| Microsoft.Web/serverFarms/* | Crear y administrar granjas de servidores |

## <a name="website-contributor"></a>Colaborador de sitio web
Puede administrar sitios web, pero no los planes web a los que están conectados

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Insights/components/* | Crear y administrar componentes de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| Microsoft.Web/certificates/* | Crear y administrar certificados de sitios web |
| Microsoft.Web/listSitesAssignedToHostName/read | Leer sitios asignados a un nombre de host |
| Microsoft.Web/serverFarms/join/action | Unir granjas de servidores |
| Microsoft.Web/serverFarms/read | Leer las granjas de servidores |
| Microsoft.Web/sites/* | Crear y administrar sitios web (la creación de sitios también requiere permisos de escritura para el plan de App Service asociado) |

## <a name="see-also"></a>Otras referencias
* [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](role-based-access-control-configure.md): introducción a RBAC en Azure Portal.
* [Roles personalizados en RBAC de Azure](role-based-access-control-custom-roles.md): aprenda a crear roles personalizados para satisfacer sus necesidades de acceso.
* [Creación de un informe del historial de cambios de acceso](role-based-access-control-access-change-history-report.md): seguimiento del cambio de asignaciones de roles en RBAC.
* [Solución de problemas del control de acceso basado en roles](role-based-access-control-troubleshooting.md): sugerencias para resolver problemas frecuentes.
* [Permisos en Azure Security Center](../security-center/security-center-permissions.md)
