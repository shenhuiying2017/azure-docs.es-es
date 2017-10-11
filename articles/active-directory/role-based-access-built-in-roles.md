---
title: 'Actions y NotActions: control de acceso basado en roles (RBAC) de Azure | Microsoft Docs'
description: "En este tema se describen los roles integrados para el control de acceso basado en roles (RBAC). Los roles se agregan continuamente, así que compruebe que la documentación esté actualizada."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/28/2017
ms.author: andredm
ms.reviewer: 
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9a5de00793621cfdecea887c53a22d482a25d1b8
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Roles integrados para el control de acceso basado en roles de Azure
El control de acceso basado en roles de Azure (RBAC) dispone de los siguientes roles integrados que se pueden asignar a usuarios, grupos y servicios. Las definiciones de los roles integrados no se puede modificar. Sin embargo, puede crear [roles personalizados en RBAC de Azure](role-based-access-control-custom-roles.md) para satisfacer las necesidades específicas de su organización.

## <a name="roles-in-azure"></a>Roles de Azure
En la tabla siguiente se proporcionan breves descripciones de los roles integrados. Haga clic en el nombre de rol para ver una lista detallada de las propiedades **actions** y **notactions** de dicho rol. La propiedad **actions** especifica las acciones permitidas en los recursos de Azure. Las cadenas de acciones pueden utilizar caracteres comodín. La propiedad **notactions** especifica las acciones que se excluyen de las acciones permitidas.

La acción define qué tipo de operaciones puede realizar en un tipo de recurso determinado. Por ejemplo:
- **Escribir** le permite realizar operaciones PUT, POST, PATCH y DELETE.
- **Leer** le permite realizar operaciones GET.

Este artículo solo trata los distintos roles que existen actualmente. Sin embargo, cuando se asigna un rol a un usuario, puede limitar aún más las acciones permitidas mediante la definición de un ámbito. Esto resulta útil si desea convertir a alguien en colaborador del sitio web, pero solo para un grupo de recursos.

> [!NOTE]
> Las definiciones de rol de Azure están en constante evolución. Este artículo se mantiene tan actualizado como sea posible, pero las últimas definiciones de roles puede encontrarlas en Azure PowerShell. Utilice el cmdlet [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) para ver todos los roles actuales. Puede profundizar en un rol específico mediante `(get-azurermroledefinition "<role name>").actions` o `(get-azurermroledefinition "<role name>").notactions`, según corresponda. Use [AzureRmProviderOperation Get](/powershell/module/azurerm.resources/get-azurermprovideroperation) para ver las operaciones de proveedores de recursos de Azure.


| Nombre de rol | Description |
| --- | --- |
| [Colaborador de servicio de administración de API](#api-management-service-contributor) |Puede administrar el servicio API Management y las API |
| [Rol del operador del servicio API Management](#api-management-service-operator-role) | Puede administrar el servicio API Management, pero no las propias API |
| [Rol de lector del servicio API Management](#api-management-service-reader-role) | Acceso de solo lectura a las API y el servicio API Management |
| [Colaborador de componentes de Application Insights](#application-insights-component-contributor) |Puede administrar los componentes de Application Insights |
| [Operador de automatización](#automation-operator) |Puede iniciar, detener, suspender y reanudar trabajos |
| [Colaborador de copias de seguridad](#backup-contributor) | Puede administrar copias de seguridad en el almacén de Recovery Services |
| [Operador de copias de seguridad](#backup-operator) | Puede administrar copias de seguridad, excepto su eliminación, en el almacén de Recovery Services |
| [Lector de copias de seguridad](#backup-reader) | Puede ver todos los servicios de administración de copias de seguridad  |
| [Lector de facturación](#billing-reader) | Puede ver toda la información de facturación  |
| [Colaborador de BizTalk](#biztalk-contributor) |Puede administrar los servicios de BizTalk |
| [Colaborador de ClearDB MySQL DB](#cleardb-mysql-db-contributor) |Puede administrar bases de datos ClearDB MySQL |
| [Colaborador](#contributor) |Puede administrar todo el contenido, excepto el acceso |
| [Colaborador de Factoría de datos](#data-factory-contributor) |Puede crear y administrar factorías de datos y recursos secundarios dentro de ellos. |
| [Usuario de DevTest Labs](#devtest-labs-user) |Puede ver todo el contenido así como conectar, iniciar, reiniciar y apagar las máquinas virtuales |
| [Colaborador de zona DNS](#dns-zone-contributor) |Puede administrar registros y zonas DNS |
| [Colaborador de la cuenta de Azure Cosmos DB](#documentdb-account-contributor) |Puede administrar cuentas de Azure Cosmos DB |
| [Colaborador de la cuenta de Sistemas inteligentes](#intelligent-systems-account-contributor) |Puede administrar cuentas de Sistemas inteligentes |
| Colaborador de aplicación lógica | Puede administrar todos los aspectos de una aplicación lógica, pero no crear una nueva. |
| Operador de aplicación lógica |Puede iniciar y detener los flujos de trabajo definidos en una aplicación lógica. |
| [Lector de supervisión](#monitoring-reader) |Puede leer todos los datos de supervisión |
| [Colaborador de supervisión](#monitoring-contributor) |Puede leer los datos de supervisión y editar la configuración de supervisión |
| [Colaborador de la red](#network-contributor) |Puede administrar todos los recursos de red |
| [Colaborador de la cuenta de NewRelic APM](#new-relic-apm-account-contributor) |Puede administrar aplicaciones y cuentas de New Relic Application Performance Management |
| [Propietario](#owner) |Puede administrar todo el contenido, incluido el acceso |
| [Lector](#reader) |Puede ver todo el contenido, pero no puede realizar cambios |
| [Colaborador de la memoria caché de Redis](#redis-cache-contributor) |Puede administrar memorias caché en Redis |
| [Colaborador de colecciones de trabajos de Scheduler](#scheduler-job-collections-contributor) |Puede administrar las colecciones de trabajo de Programador |
| [Colaborador del servicio de búsqueda](#search-service-contributor) |Puede administrar los servicios de búsqueda |
| [Administrador de seguridad](#security-manager) |Puede administrar los componentes y las directivas de seguridad, además de las máquinas virtuales |
| [Colaborador de Site Recovery](#site-recovery-contributor) | Puede administrar Site Recovery en el almacén de Recovery Services |
| [Operador de Site Recovery](#site-recovery-operator) | Puede administrar operaciones de conmutación por error y conmutación por recuperación de Site Recovery en el almacén de Recovery Services |
| [Lector de Site Recovery](#site-recovery-reader) | Puede ver todas las operaciones de administración de Site Recovery  |
| [Colaborador de Base de datos de SQL](#sql-db-contributor) |Puede administrar bases de datos SQL, pero no las directivas relacionadas con la seguridad. |
| [Administrador de seguridad SQL](#sql-security-manager) |Puede administrar las directivas relacionadas con la seguridad de las bases de datos y los servidores SQL. |
| [Colaborador de SQL Server](#sql-server-contributor) |Puede administrar las bases de datos y los servidores SQL, pero no las directivas relacionadas con la seguridad |
| [Colaborador de cuentas de almacenamiento clásico](#classic-storage-account-contributor) |Puede administrar las cuentas de almacenamiento clásico |
| [Colaborador de la cuenta de almacenamiento](#storage-account-contributor) |Puede administrar las cuentas de almacenamiento |
| [Colaborador de la solicitud de soporte técnico](#support-request-contributor) | Puede crear y administrar solicitudes de soporte técnico |
| [Administrador de acceso de usuario](#user-access-administrator) |Puede administrar el acceso de usuarios a los recursos de Azure |
| [Colaborador de la máquina virtual clásica](#classic-virtual-machine-contributor) |Puede administrar máquinas virtuales clásicas, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas |
| [Colaborador de la máquina virtual](#virtual-machine-contributor) |Puede administrar máquinas virtuales, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas |
| [Colaborador de la red clásica](#classic-network-contributor) |Puede administrar IP reservadas y redes virtuales clásicas |
| [Colaborador de plan web](#web-plan-contributor) |Puede administrar planes web |
| [Colaborador de sitio web](#website-contributor) |Puede administrar sitios web, pero no los planes web a los que están conectados |

## <a name="role-permissions"></a>Permisos de los roles
Las tablas siguientes describen los permisos específicos concedidos a cada rol. En ellas, se incluyen elementos correspondientes a las categorías **Actions** (para conceder permisos) y **NotActions** (para restringir dichos permisos).

### <a name="api-management-service-contributor"></a>Colaborador de servicio de administración de API
Puede administrar servicios de administración de API

| **Acciones** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/* |Crear y administrar servicio API Management |
| Microsoft.Authorization/*/read |Leer autorización |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer roles y asignaciones de roles |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="api-management-service-operator-role"></a>Rol del operador del servicio API Management
Puede administrar servicios de administración de API

| **Acciones** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/*/read | Lectura de instancias del servicio API Management |
| Microsoft.ApiManagement/Service/backup/action | Copia de seguridad del servicio API Management en el contenedor especificado de una cuenta de almacenamiento proporcionada por el usuario |
| Microsoft.ApiManagement/Service/delete | Eliminación de una instancia del servicio API Management |
| Microsoft.ApiManagement/Service/managedeployments/action | Cambio de SKU/unidades; adición o eliminación de las implementaciones regionales del servicio API Management |
| Microsoft.ApiManagement/Service/read | Lectura de los metadatos de una instancia del servicio API Management |
| Microsoft.ApiManagement/Service/restore/action | Restauración del servicio API Management desde el contenedor especificado de una cuenta de almacenamiento proporcionada por el usuario |
| Microsoft.ApiManagement/Service/updatehostname/action | Configuración, actualización o eliminación de los nombres de dominio personalizado de un servicio API Management |
| Microsoft.ApiManagement/Service/write | Creación de una nueva instancia del servicio API Management |
| Microsoft.Authorization/*/read |Leer autorización |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer roles y asignaciones de roles |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="api-management-service-reader-role"></a>Rol de lector del servicio API Management
Puede administrar servicios de administración de API

| **Acciones** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/*/read | Lectura de instancias del servicio API Management |
| Microsoft.ApiManagement/Service/read | Lectura de los metadatos de una instancia del servicio API Management |
| Microsoft.Authorization/*/read |Leer autorización |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer roles y asignaciones de roles |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="application-insights-component-contributor"></a>Colaborador de componentes de Application Insights
Puede administrar los componentes de Application Insights

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.Insights/components/* |Crear y administrar componentes de Insights |
| Microsoft.Insights/webtests/* |Crear y administrar pruebas web |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="automation-operator"></a>Operador de automatización
Puede iniciar, detener, suspender y reanudar trabajos

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.Automation/automationAccounts/jobs/read |Leer trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobs/resume/action |Reanudar un trabajo de cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobs/stop/action |Detener un trabajo de cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobs/streams/read |Leer transmisiones de trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobs/suspend/action |Suspender un trabajo de cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobs/write |Escribir trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobSchedules/read |Leer un programa de trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobSchedules/write |Leer un programa de trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/read |Leer cuentas de automatización |
| Microsoft.Automation/automationAccounts/runbooks/read |Leer runbooks de automatización |
| Microsoft.Automation/automationAccounts/schedules/read |Leer esquemas de cuentas de automatización |
| Microsoft.Automation/automationAccounts/schedules/write |Escribir esquemas de cuentas de automatización |
| Microsoft.Insights/components/* |Crear y administrar componentes de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="backup-contributor"></a>Colaborador de copias de seguridad
Puede administrar todas las acciones de administración de copias de seguridad, excepto la creación del almacén de Recovery Services y la concesión de acceso a otros usuarios

| **Acciones** | |
| --- | --- |
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
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Storage/storageAccounts/read | Leer cuentas de almacenamiento |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="backup-operator"></a>Operador de copias de seguridad
Puede administrar todas las acciones de administración de copias de seguridad, excepto la eliminación de copias de seguridad y la concesión de acceso a otros usuarios

| **Acciones** | |
| --- | --- |
| Microsoft.Network/virtualNetworks/read | Leer redes virtuales |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Leer resultados de la operación de administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Leer los resultados de la operación en los contenedores de protección |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Realizar la operación de copia de seguridad a petición en un elemento del que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Leer el resultado de la operación realizada en un elemento del que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationStatus/read | Leer el estado de la operación realizada en un elemento del que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Leer elementos de los que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Leer punto de recuperación de un elemento del que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Realizar una operación de restauración con un punto de recuperación de un elemento del que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Crear un elemento de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Leer contenedores que incluyen un elemento de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Crear y administrar trabajos de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabajos de copia de seguridad a un archivo de Excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Leer metadatos relacionados con la administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Crear y administrar resultados de operaciones de administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Leer resultados de las operaciones realizadas en directivas de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Leer directivas de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Crear y administrar elementos de los que se puede realizar una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Leer elementos de los que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Leer contenedores de los que se ha hecho una copia de seguridad, que incluyen elementos de copia de seguridad |
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
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### <a name="backup-reader"></a>Lector de copias de seguridad
Puede supervisar la administración de copias de seguridad en el almacén de Recovery Services

| **Acciones** | |
| --- | --- |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read  | Leer resultados de la operación de administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read  | Leer los resultados de la operación en los contenedores de protección |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read  | Leer el resultado de la operación realizada en un elemento del que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationStatus/read  | Leer el estado de la operación realizada en un elemento del que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read  | Leer elementos de los que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read  | Leer contenedores que incluyen un elemento de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read  | Leer resultados de los trabajos de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupJobs/read  | Leer trabajos de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabajos de copia de seguridad a un archivo de Excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read  | Leer metadatos relacionados con la administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read  | Leer resultados de la operación de administración de copias de seguridad |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read  | Leer resultados de las operaciones realizadas en directivas de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read  | Leer directivas de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read  |  Leer elementos de los que se ha hecho una copia de seguridad |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read  | Leer contenedores de los que se ha hecho una copia de seguridad, que incluyen elementos de copia de seguridad |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read  | Leer información ampliada relacionada con el almacén |
| Microsoft.RecoveryServices/Vaults/read  | Leer almacenes de Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read  | Leer el resultado de una operación de detección para capturar contenedores recién creados |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read  | Leer resultados de la operación realizada en elementos registrados del almacén |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read  | Leer elementos registrados del almacén |
| Microsoft.RecoveryServices/Vaults/usages/read  |  Leer uso del almacén de Recovery Services |

### <a name="billing-reader"></a>Lector de facturación
Puede ver toda la información de facturación

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.Billing/*/read |Leer la información de facturación |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="biztalk-contributor"></a>Colaborador de BizTalk
Puede administrar los servicios de BizTalk

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.BizTalkServices/BizTalk/* |Crear y administrar los servicios de BizTalk |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="cleardb-mysql-db-contributor"></a>Colaborador de ClearDB MySQL DB
Puede administrar bases de datos ClearDB MySQL

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |
| successbricks.cleardb/databases/* |Crear y administrar bases de datos ClearDB MySQL |

### <a name="contributor"></a>Colaborador
Puede administrar todo el contenido, excepto el acceso

| **Acciones** |  |
| --- | --- |
| * |Crear y administrar recursos de todos los tipos |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete |No puede eliminar roles ni asignaciones de roles |
| Microsoft.Authorization/*/Write |No puede crear roles ni asignaciones de roles |

### <a name="data-factory-contributor"></a>Colaborador de Factoría de datos
Crear y administrar factorías de datos y recursos secundarios dentro de ellos.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.DataFactory/dataFactories/* |Crear y administrar factorías de datos y recursos secundarios dentro de ellos. |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="devtest-labs-user"></a>Usuario de DevTest Labs
Puede ver todo el contenido así como conectar, iniciar, reiniciar y apagar las máquinas virtuales

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.Compute/availabilitySets/read |Leer las propiedades de los conjuntos de disponibilidad |
| Microsoft.Compute/virtualMachines/*/read |Leer las propiedades de una máquina virtual (tamaños de máquinas virtuales, el estado de tiempo de ejecución, extensiones de máquina virtual, etc.) |
| Microsoft.Compute/virtualMachines/deallocate/action |Desasignar máquinas virtuales |
| Microsoft.Compute/virtualMachines/read |Leer las propiedades de una máquina virtual |
| Microsoft.Compute/virtualMachines/restart/action |Reiniciar máquinas virtuales |
| Microsoft.Compute/virtualMachines/start/action |Iniciar máquinas virtuales |
| Microsoft.DevTestLab/*/read |Leer las propiedades de un laboratorio |
| Microsoft.DevTestLab/labs/createEnvironment/action |Crear un entorno de laboratorio |
| Microsoft.DevTestLab/labs/formulas/delete |Eliminar fórmulas |
| Microsoft.DevTestLab/labs/formulas/read |Leer fórmulas |
| Microsoft.DevTestLab/labs/formulas/write |Agregar o modificar fórmulas |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action |Evaluar directivas de laboratorio |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |Unirse a un grupo de direcciones de back-end del equilibrador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |Unirse a una regla NAT de entrada del equilibrador de carga |
| Microsoft.Network/networkInterfaces/*/read |Leer las propiedades de una interfaz de red (por ejemplo, todos los equilibradores de carga de los que forma parte de la interfaz de red) |
| Microsoft.Network/networkInterfaces/join/action |Unirse a una máquina virtual a una interfaz de red |
| Microsoft.Network/networkInterfaces/read |Leer interfaces de red |
| Microsoft.Network/networkInterfaces/write |Escribir interfaces de red |
| Microsoft.Network/publicIPAddresses/*/read |Leer las propiedades de una dirección IP pública |
| Microsoft.Network/publicIPAddresses/join/action |Unirse a una dirección IP pública |
| Microsoft.Network/publicIPAddresses/read |Leer direcciones IP públicas de red |
| Microsoft.Network/virtualNetworks/subnets/join/action |Unirse a una red virtual |
| Microsoft.Resources/deployments/operations/read |Leer operaciones de implementación |
| Microsoft.Resources/deployments/read |Leer implementaciones |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Storage/storageAccounts/listKeys/action |Enumerar claves de cuentas de almacenamiento |

### <a name="dns-zone-contributor"></a>Colaborador de zona DNS
Puede administrar registros y zonas DNS.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/\*/read |Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/\* |Crear y administrar reglas de alertas |
| Microsoft.Network/dnsZones/\* |Crear y administrar registros y zonas DNS |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el estado de los recursos |
| Microsoft.Resources/deployments/\* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/\* |Crear y administrar vales de soporte técnico |

### <a name="azure-cosmos-db-account-contributor"></a>Colaborador de la cuenta de Azure Cosmos DB
Puede administrar cuentas de Azure Cosmos DB

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.DocumentDb/databaseAccounts/* |Crear y administrar cuentas de DocumentDB |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="intelligent-systems-account-contributor"></a>Colaborador de la cuenta de Sistemas inteligentes
Puede administrar cuentas de Sistemas inteligentes

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.IntelligentSystems/accounts/* |Crear y administrar cuentas de sistemas inteligentes |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="monitoring-reader"></a>Lector de supervisión
Puede leer todos los datos de supervisión (métricas, registros, etc.). Consulte también [Introducción a roles, permisos y seguridad con Azure Monitor](/monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Acciones** |  |
| --- | --- |
| */read |Leer recursos de todos los tipos, excepto secretos. |
| Microsoft.OperationalInsights/workspaces/search/action |Buscar datos de Log Analytics |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="monitoring-contributor"></a>Colaborador de supervisión
Puede leer todos los datos de supervisión y editar la configuración de supervisión. Consulte también [Introducción a roles, permisos y seguridad con Azure Monitor](/monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Acciones** |  |
| --- | --- |
| */read |Leer recursos de todos los tipos, excepto secretos. |
| Microsoft.Insights/AlertRules/* |Reglas de alerta de lectura, escritura y eliminación. |
| Microsoft.Insights/components/* |Leer, escribir o eliminar los componentes de Application Insights. |
| Microsoft.Insights/DiagnosticSettings/* |Configuración de diagnóstico de lectura, escritura y eliminación. |
| Microsoft.Insights/eventtypes/* |Enumerar eventos del registro de actividades (eventos de administración) de una suscripción. Este permiso es aplicable para el acceso mediante programación y mediante el portal al registro de actividades. |
| Microsoft.Insights/LogDefinitions/* |Este permiso es necesario para los usuarios que necesitan acceder a registros de actividades a través del portal. Enumere las categorías de registro del registro de actividad. |
| Microsoft.Insights/MetricDefinitions/* |Leer definiciones de métrica (lista de tipos de métricas disponibles para un recurso). |
| Microsoft.Insights/Metrics/* |Leer las métricas de un recurso. |
| Microsoft.Insights/Register/Action |Registrar el proveedor de Microsoft.Insights. |
| Microsoft.Insights/webtests/* |Leer, escribir o eliminar pruebas web de Application Insights. |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* |Leer, escribir o eliminar paquetes de soluciones de Log Analytics. |
| Microsoft.OperationalInsights/workspaces/savedSearches/* |Leer, escribir o eliminar búsquedas guardadas de Log Analytics. |
| Microsoft.OperationalInsights/workspaces/search/action |Buscar áreas de trabajo de Log Analytics. |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action |Enumerar las claves de un área de trabajo de Log Analytics. |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* |Leer, escribir o eliminar configuraciones de visión de almacenamiento de Log Analytics. |

### <a name="network-contributor"></a>Colaborador de la red
Puede administrar todos los recursos de red

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.Network/* |Crear y administrar redes |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="new-relic-apm-account-contributor"></a>Colaborador de la cuenta de NewRelic APM
Puede administrar aplicaciones y cuentas de New Relic Application Performance Management

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |
| NewRelic.APM/accounts/* |Crear y administrar cuentas de administración del rendimiento de la aplicación New Relic |

### <a name="owner"></a>Propietario
Puede administrar todo el contenido, incluido el acceso

| **Acciones** |  |
| --- | --- |
| * |Crear y administrar recursos de todos los tipos |

### <a name="reader"></a>Lector
Puede ver todo el contenido, pero no puede realizar cambios

| **Acciones** |  |
| --- | --- |
| */read |Leer recursos de todos los tipos, excepto secretos. |

### <a name="redis-cache-contributor"></a>Colaborador de la memoria caché de Redis
Puede administrar memorias caché en Redis

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.Cache/redis/* |Crear y administrar memorias caché de Redis |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="scheduler-job-collections-contributor"></a>Colaborador de colecciones de trabajos de Scheduler
Puede administrar las colecciones de trabajo de Programador

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Scheduler/jobcollections/* |Crear y administrar colecciones de trabajos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="search-service-contributor"></a>Colaborador del servicio de búsqueda
Puede administrar los servicios de búsqueda

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Search/searchServices/* |Crear y administrar servicios de búsqueda |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="security-manager"></a>Administrador de seguridad
Puede administrar los componentes y las directivas de seguridad, además de las máquinas virtuales

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.ClassicCompute/*/read |Leer información de configuración para máquinas virtuales de proceso clásico |
| Microsoft.ClassicCompute/virtualMachines/*/write |Escribir configuración para máquinas virtuales |
| Microsoft.ClassicNetwork/*/read |Leer información de configuración acerca de la red clásica |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Security/* |Crear y administrar las directivas y los componentes de seguridad |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="site-recovery-contributor"></a>Colaborador de Site Recovery
Puede administrar todas las acciones de administración de Site Recovery, excepto la creación del almacén de Recovery Services y la asignación de acceso a otros usuarios

| **Acciones** | |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Network/virtualNetworks/read | Leer redes virtuales |
| Microsoft.RecoveryServices/Vaults/certificates/write | Actualiza el certificado de credenciales del almacén |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Crear y administrar información ampliada relacionada con el almacén |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/*  | Lee las alertas del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read  | Lee la configuración de notificaciones del almacén de Recovery Services |
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
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Storage/storageAccounts/read | Leer cuentas de almacenamiento |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="site-recovery-operator"></a>Operador de Site Recovery
Puede realizar una conmutación por error y por recuperación, pero no puede realizar otras acciones de administración de Site Recovery ni asignar acceso a otros usuarios

| **Acciones** | |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Network/virtualNetworks/read | Leer redes virtuales |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Leer información ampliada relacionada con el almacén |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/*  | Lee las alertas del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read  | Lee la configuración de notificaciones del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/read | Lee almacenes de Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Administrar la operación de detección para capturar contenedores recién creados |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Lee el estado de la operación y el resultado de una operación enviada |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Lee los contenedores registrados de un recurso |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lee la configuración de alertas de replicación |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Lee eventos de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Comprueba la coherencia del tejido |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Lee el tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ reassociateGateway/action | Vuelve a asociar la puerta de enlace de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renueva el certificado de tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Lee las redes del tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | Lee la asignación de redes del tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Lee los contenedores de protección |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Obtiene una lista de todos los elementos que se pueden proteger |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ applyRecoveryPoint/action | Aplica un punto de recuperación específico |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ failoverCommit/action | Confirma la conmutación por error de un elemento conmutado por error |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ plannedFailover/action | Inicia la conmutación por error planeada de un elemento protegido |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Obtiene la lista de todos los elementos protegidos |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Obtiene la lista de los puntos de recuperación disponibles |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ repairReplication/action | Repara la replicación de un elemento protegido |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/reProtect/action | Inicia la reprotección de un elemento protegido|
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/action | Inicia la conmutación por error de prueba de un elemento protegido |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ testFailoverCleanup/action | Inicia la limpieza de una conmutación por error de prueba |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ unplannedFailover/action | Inicia la conmutación por error no planeada de un elemento protegido |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ updateMobilityService/action | Actualiza Mobility Service |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Lee las asignaciones de los contenedores de protección |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Lee los proveedores de Recovery Services |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/action | Actualiza los proveedores de Recovery Services |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Lee las clasificaciones de almacenamiento del tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | Lee las asignaciones de clasificaciones de almacenamiento |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Lee la información de vCenter registrada |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Crea y administra los trabajos de replicación |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Lee las directivas de replicación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ failoverCommit/action | Confirma la conmutación por error del plan de recuperación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ plannedFailover/action | Inicia la conmutación por error de un plan de recuperación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Lee los planes de recuperación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Inicia la reprotección de un plan de recuperación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Inicia la conmutación por error de prueba de un plan de recuperación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ testFailoverCleanup/action | Inicia la limpieza de una conmutación por error de prueba de un plan de recuperación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ unplannedFailover/action | Inicia la conmutación por error no planeada de un plan de recuperación |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Lee la configuración de almacenamiento de un almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Lee la información del token del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/usages/read | Lee los detalles de uso de un almacén de Recovery Services |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Storage/storageAccounts/read | Leer cuentas de almacenamiento |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### <a name="site-recovery-reader"></a>Lector de Site Recovery
Puede supervisar el estado de recuperación de Site Recovery en el almacén de Recovery Services y generar incidencias de soporte técnico

| **Acciones** | |
| --- | --- |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read  | Leer información ampliada relacionada con el almacén |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read  | Lee las alertas del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read  | Lee la configuración de notificaciones del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/read  | Lee almacenes de Recovery Services |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read  | Administrar la operación de detección para capturar contenedores recién creados |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read  | Lee el estado de la operación y el resultado de una operación enviada |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read  | Lee los contenedores registrados de un recurso |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lee la configuración de alertas de replicación |
| Microsoft.RecoveryServices/vaults/replicationEvents/read  | Lee eventos de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read  | Lee el tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read  | Lee las redes del tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read  | Lee la asignación de redes del tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read  |  Lee los contenedores de protección |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read  | Obtiene una lista de todos los elementos que se pueden proteger |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read  | Obtiene la lista de todos los elementos protegidos |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read  | Obtiene la lista de los puntos de recuperación disponibles |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read  | Lee las asignaciones de los contenedores de protección |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read  | Lee los proveedores de Recovery Services |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read  | Lee las clasificaciones de almacenamiento del tejido de replicación |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read  |  Lee las asignaciones de clasificaciones de almacenamiento |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read  |  Lee la información de vCenter registrada |
| Microsoft.RecoveryServices/vaults/replicationJobs/read  |  Lee el estado de los trabajos de replicación |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read  |  Lee las directivas de replicación |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read  |  Lee los planes de recuperación |
| Microsoft.RecoveryServices/Vaults/storageConfig/read  |  Lee la configuración de almacenamiento de un almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read  |  Lee la información del token del almacén de Recovery Services |
| Microsoft.RecoveryServices/Vaults/usages/read  |  Lee los detalles de uso de un almacén de Recovery Services |
| Microsoft.Support/*  |  Crear y administrar incidencias de soporte técnico |

### <a name="sql-db-contributor"></a>Colaborador de Base de datos de SQL
Puede administrar bases de datos SQL, pero no las directivas relacionadas con la seguridad.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Sql/servers/databases/* |Crear y administrar bases de datos SQL |
| Microsoft.Sql/servers/read |Leer los servidores SQL Server |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* |No puede editar las directivas de auditoría |
| Microsoft.Sql/servers/databases/auditingSettings/* |No puede editar la configuración de auditoría |
| Microsoft.Sql/servers/databases/auditRecords/read |No puede leer los registros de auditoría. |
| Microsoft.Sql/servers/databases/connectionPolicies/* |No puede editar las directivas de conexión |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |No puede editar las directivas de enmascaramiento |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |No puede editar las directivas de alerta de seguridad |
| Microsoft.Sql/servers/databases/securityMetrics/* |No puede editar las métricas de seguridad |

### <a name="sql-security-manager"></a>Administrador de seguridad SQL
Puede administrar las directivas relacionadas con la seguridad de las bases de datos y los servidores SQL.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer autorización de Microsoft |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Sql/servers/auditingPolicies/* |Crear y administrar directivas de auditoría de SQL Server |
| Microsoft.Sql/servers/auditingSettings/* |Crear y administrar configuración de auditoría de SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Crear y administrar directivas de auditoría de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/auditingSettings/* |Crear y administrar configuración de auditoría de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/auditRecords/read |Leer registros de auditoría |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Crear y administrar directivas de conexión de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Crear y administrar directivas de enmascaramiento de datos de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/read |Leer bases de datos SQL |
| Microsoft.Sql/servers/databases/schemas/read |Leer esquemas de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read |Leer columnas de tablas de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/read |Leer tablas de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Crear y administrar directivas de alerta de seguridad de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/securityMetrics/* |Crear y administrar métricas de seguridad de bases de datos de SQL Server |
| Microsoft.Sql/servers/read |Leer los servidores SQL Server |
| Microsoft.Sql/servers/securityAlertPolicies/* |Crear y administrar directivas de alerta de seguridad de SQL Server |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="sql-server-contributor"></a>Colaborador de SQL Server
Puede administrar las bases de datos y los servidores SQL, pero no las directivas relacionadas con su seguridad

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer autorización |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Sql/servers/* |Crear y administrar servidores de SQL Server |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* |No puede editar las directivas de auditoría de SQL Server |
| Microsoft.Sql/servers/auditingSettings/* |No puede editar la configuración de auditoría de SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* |No puede editar las directivas de auditoría de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/auditingSettings/* |No puede editar la configuración de auditoría de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/auditRecords/read |No puede leer los registros de auditoría. |
| Microsoft.Sql/servers/databases/connectionPolicies/* |No puede editar las directivas de conexión de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |No puede editar las directivas de enmascaramiento de datos de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |No puede editar las directivas de alerta de seguridad de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/securityMetrics/* |No puede editar las métricas de seguridad de bases de datos de SQL Server |
| Microsoft.Sql/servers/securityAlertPolicies/* |No puede editar las directivas de alerta de seguridad de SQL Server |

### <a name="classic-storage-account-contributor"></a>Colaborador de cuentas de almacenamiento clásico
Puede administrar las cuentas de almacenamiento clásico

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer autorización |
| Microsoft.ClassicStorage/storageAccounts/* |Crear y administrar cuentas de almacenamiento |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="storage-account-contributor"></a>Colaborador de la cuenta de almacenamiento
Puede administrar cuentas de almacenamiento, pero no acceder a ellas.

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer toda la autorización |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alerta de Insights |
| Microsoft.Insights/diagnosticSettings/* |Administrar la configuración de diagnóstico |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Storage/storageAccounts/* |Crear y administrar cuentas de almacenamiento |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="support-request-contributor"></a>Colaborador de la solicitud de soporte técnico
Puede crear y administrar incidencias de soporte técnico en el ámbito de la suscripción

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer roles y asignaciones de roles |

### <a name="user-access-administrator"></a>Administrador de acceso de usuario
Puede administrar el acceso de usuarios a los recursos de Azure

| **Acciones** |  |
| --- | --- |
| */read |Leer recursos de todos los tipos, excepto secretos. |
| Microsoft.Authorization/* |Administrar la autorización |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="classic-virtual-machine-contributor"></a>Colaborador de la máquina virtual clásica
Puede administrar máquinas virtuales clásicas, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer autorización |
| Microsoft.ClassicCompute/domainNames/* |Crear y administrar nombres de dominio de proceso clásico |
| Microsoft.ClassicCompute/virtualMachines/* |Crear y administrar máquinas virtuales |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action |Unir los grupos de seguridad de red |
| Microsoft.ClassicNetwork/reservedIps/link/action |Vincular IP reservadas |
| Microsoft.ClassicNetwork/reservedIps/read |Leer direcciones IP reservadas |
| Microsoft.ClassicNetwork/virtualNetworks/join/action |Unirse a redes virtuales |
| Microsoft.ClassicNetwork/virtualNetworks/read |Leer redes virtuales |
| Microsoft.ClassicStorage/storageAccounts/disks/read |Leer discos de cuentas de almacenamiento |
| Microsoft.ClassicStorage/storageAccounts/images/read |Leer imágenes de cuentas de almacenamiento |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action |Enumerar claves de cuentas de almacenamiento |
| Microsoft.ClassicStorage/storageAccounts/read |Leer cuentas de almacenamiento clásico |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="virtual-machine-contributor"></a>Colaborador de la máquina virtual
Puede administrar máquinas virtuales, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer autorización |
| Microsoft.Compute/availabilitySets/* |Crear y administrar conjuntos de disponibilidad de proceso |
| Microsoft.Compute/locations/* |Crear y administrar ubicaciones de proceso |
| Microsoft.Compute/virtualMachines/* |Crear y administrar máquinas virtuales |
| Microsoft.Compute/virtualMachineScaleSets/* |Crear y administrar conjuntos de escalado de máquinas virtuales |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alerta de Insights |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action |Unir grupos de direcciones de back-end de puerta de enlace de aplicaciones de red |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |Unirse a los grupos de direcciones de back-end de equilibrador de carga |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action |Unirse a conjuntos NAT de entrada del equilibrador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |Unir las reglas NAT de entrada del equilibrador de carga |
| Microsoft.Network/loadBalancers/read |Leer equilibradores de carga |
| Microsoft.Network/locations/* |Crear y administrar ubicaciones de red |
| Microsoft.Network/networkInterfaces/* |Crear y administrar interfaces de red |
| Microsoft.Network/networkSecurityGroups/join/action |Unir los grupos de seguridad de red |
| Microsoft.Network/networkSecurityGroups/read |Leer grupos de seguridad de red |
| Microsoft.Network/publicIPAddresses/join/action |Unir las direcciones IP públicas de red |
| Microsoft.Network/publicIPAddresses/read |Leer direcciones IP públicas de red |
| Microsoft.Network/virtualNetworks/read |Leer redes virtuales |
| Microsoft.Network/virtualNetworks/subnets/join/action |Unirse a subredes de redes virtuales |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Storage/storageAccounts/listKeys/action |Enumerar claves de cuentas de almacenamiento |
| Microsoft.Storage/storageAccounts/read |Leer cuentas de almacenamiento |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="classic-network-contributor"></a>Colaborador de la red clásica
Puede administrar IP reservadas y redes virtuales clásicas

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer autorización |
| Microsoft.ClassicNetwork/* |Crear y administrar redes clásicas |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |

### <a name="web-plan-contributor"></a>Colaborador de plan web
Puede administrar planes web

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer autorización |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |
| Microsoft.Web/serverFarms/* |Crear y administrar granjas de servidores |

### <a name="website-contributor"></a>Colaborador de sitio web
Puede administrar sitios web, pero no los planes web a los que están conectados

| **Acciones** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leer autorización |
| Microsoft.Insights/alertRules/* |Crear y administrar reglas de alerta de Insights |
| Microsoft.Insights/components/* |Crear y administrar componentes de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* |Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leer grupos de recursos |
| Microsoft.Support/* |Crear y administrar incidencias de soporte técnico |
| Microsoft.Web/certificates/* |Crear y administrar certificados de sitios web |
| Microsoft.Web/listSitesAssignedToHostName/read |Leer sitios asignados a un nombre de host |
| Microsoft.Web/serverFarms/join/action |Unir granjas de servidores |
| Microsoft.Web/serverFarms/read |Leer las granjas de servidores |
| Microsoft.Web/sites/* |Crear y administrar sitios web (la creación de sitios también requiere permisos de escritura para el plan de App Service asociado) |

## <a name="see-also"></a>Consulte también
* [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](role-based-access-control-configure.md): introducción a RBAC en el Portal de Azure.
* [Roles personalizados en RBAC de Azure](role-based-access-control-custom-roles.md): aprenda a crear roles personalizados para satisfacer sus necesidades de acceso.
* [Creación de un informe del historial de cambios de acceso](role-based-access-control-access-change-history-report.md): seguimiento del cambio de asignaciones de roles en RBAC.
* [Solución de problemas del control de acceso basado en roles](role-based-access-control-troubleshooting.md): sugerencias para resolver problemas frecuentes.
