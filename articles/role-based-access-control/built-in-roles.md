---
title: Roles integrados para el control de acceso basado en rol (RBAC) en Azure | Microsoft Docs
description: En este tema se describen los roles integrados para el control de acceso basado en rol (RBAC) en Azure. Enumera los valores actions y NotActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/11/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 85a9b3a4f1fc21cbb47d41a61661de38d6fc246d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34160842"
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Roles integrados para el control de acceso basado en roles de Azure
El [control de acceso basado en rol (RBAC)](overview.md) tiene varias definiciones de roles integrados que se pueden asignar a usuarios, grupos y entidades de servicio. Las asignaciones de roles sirven para controlar el acceso a los recursos de Azure. No puede modificar los roles integrados, pero puede crear sus propios [roles personalizados](custom-roles.md) para ajustarse a las necesidades específicas de su organización.

Los roles integrados siempre están en evolución. Para obtener las últimas definiciones de roles, use [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) o [az role definition list](/cli/azure/role/definition#az-role-definition-list).

## <a name="built-in-role-descriptions"></a>Descripciones de rol integrado
En la tabla siguiente se proporcionan breves descripciones de los roles integrados. Haga clic en el nombre del rol para ver la lista de `actions` y `notActions` para cada rol.


| Rol integrado | DESCRIPCIÓN |
| --- | --- |
| [Propietario](#owner) | Permite administrarlo todo, incluso el acceso a los recursos. |
| [Colaborador](#contributor) | Permite administrarlo todo excepto el acceso a los recursos. |
| [Lector](#reader) | Permite ver todo el contenido, pero no realizar cambios. |
| [AcrImageSigner](#acrimagesigner) | Firmante de imagen de ACR |
| [AcrQuarantineReader](#acrquarantinereader) | Lector de datos de cuarentena de ACR |
| [AcrQuarantineWriter](#acrquarantinewriter) | Escritura de datos de cuarentena de ACR |
| [Colaborador de servicio de administración de API](#api-management-service-contributor) | Puede administrar servicios y las API. |
| [Rol del operador del servicio API Management](#api-management-service-operator-role) | Puede administrar el servicio, pero no las API. |
| [Rol de lector del servicio API Management](#api-management-service-reader-role) | Acceso de solo lectura al servicio y las API. |
| [Colaborador de componentes de Application Insights](#application-insights-component-contributor) | Puede administrar los componentes de Application Insights |
| [Depurador de instantáneas de Application Insights](#application-insights-snapshot-debugger) | Concede permiso al usuario para usar las características del depurador de instantáneas de Application Insights. |
| [Operador de trabajos de Automation](#automation-job-operator) | Permite crear y administrar trabajos con los runbooks de Automation. |
| [Operador de Automation](#automation-operator) | Los operadores de automatización pueden iniciar, detener, suspender y reanudar trabajos. |
| [Operador de runbooks de Automation](#automation-runbook-operator) | Permite leer las propiedades de runbook para poder crear trabajos del runbook. |
| [Propietario del registro de Azure Stack](#azure-stack-registration-owner) | Permite administrar los registros de Azure Stack. |
| [Colaborador de copias de seguridad](#backup-contributor) | Permite administrar el servicio de copias de seguridad, pero no puede crear almacenes y conceder acceso a otros usuarios |
| [Operador de copias de seguridad](#backup-operator) | Permite administrar los servicios de copias de seguridad, excepto la eliminación de copias de seguridad, la creación de almacenes y la concesión de acceso a otros usuarios |
| [Lector de copias de seguridad](#backup-reader) | Puede ver servicios de copia de seguridad, pero no puede realizar cambios. |
| [Lector de facturación](#billing-reader) | Permite acceso de lectura a los datos de facturación. |
| [Colaborador de BizTalk](#biztalk-contributor) | Permite administrar los servicios de BizTalk, pero no acceder a ellos. |
| [Colaborador de punto de conexión de CDN](#cdn-endpoint-contributor) | Puede administrar puntos de conexión de CDN, pero no conceder acceso a otros usuarios. |
| [Lector de punto de conexión de CDN](#cdn-endpoint-reader) | Puede ver puntos de conexión de CDN, pero no hacer cambios. |
| [Colaborador de perfil de CDN](#cdn-profile-contributor) | Puede administrar perfiles de CDN y sus puntos de conexión, pero no conceder acceso a otros usuarios. |
| [Lector de perfil de CDN](#cdn-profile-reader) | Puede ver perfiles de CDN y sus puntos de conexión, pero no hacer cambios. |
| [Colaborador de la red clásica](#classic-network-contributor) | Permite administrar las redes clásicas, pero no acceder a ellas. |
| [Colaborador de cuentas de almacenamiento clásico](#classic-storage-account-contributor) | Permite administrar cuentas de almacenamiento clásicas, pero no acceder a ellas. |
| [Rol de servicio de operador de claves de cuentas de almacenamiento clásicas](#classic-storage-account-key-operator-service-role) | Los operadores de claves de cuentas de almacenamiento clásicas pueden enumerar y regenerar claves en cuentas de almacenamiento clásicas |
| [Colaborador de la máquina virtual clásica](#classic-virtual-machine-contributor) | Permite administrar máquinas virtuales clásicas, pero no acceder a ellas, ni tampoco a la red virtual ni a la cuenta de almacenamiento a las que están conectadas. |
| [Colaborador de ClearDB MySQL DB](#cleardb-mysql-db-contributor) | Permite administrar las bases de datos ClearDB MySQL, pero no acceder a ellas. |
| [Rol de lector de cuentas de Cosmos DB](#cosmos-db-account-reader-role) | Puede leer los datos de cuentas de Azure Cosmos DB. Vea [Colaborador de cuenta de DocumentDB](#documentdb-account-contributor) para administrar cuentas de Azure Cosmos DB. |
| [Colaborador de Factoría de datos](#data-factory-contributor) | Crea y administra factorías de datos, así como recursos secundarios dentro de ellas. |
| [Desarrollador de Data Lake Analytics](#data-lake-analytics-developer) | Le permite enviar, supervisar y administrar sus propios trabajos, pero no crear ni eliminar cuentas de Data Lake Analytics. |
| [Purgador de datos](#data-purger) | Puede purgar datos de análisis. |
| [Usuario de DevTest Labs](#devtest-labs-user) | Permite conectarse a sus máquinas virtuales, así como iniciarlas, reiniciarlas y apagarlas, en su instancia de Azure DevTest Labs. |
| [Colaborador de zona DNS](#dns-zone-contributor) | Permite administrar zonas y conjuntos de registros DNS en Azure DNS, pero no controlar los usuarios que tienen acceso. |
| [Colaborador de cuenta de DocumentDB](#documentdb-account-contributor) | Puede administrar cuentas de Azure Cosmos DB. Azure Cosmos DB se llamaba anteriormente DocumentDB. |
| [Colaborador de la cuenta de Sistemas inteligentes](#intelligent-systems-account-contributor) | Permite administrar las cuentas de Intelligent Systems, pero no acceder a ellas. |
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
| [Colaborador de la red](#network-contributor) | Permite administrar redes, pero no acceder a ellas. |
| [Colaborador de la cuenta de NewRelic APM](#new-relic-apm-account-contributor) | Le permite administrar las aplicaciones y cuentas de Application Performance Management de New Relic, pero no acceder a ellas. |
| [Lector y acceso a los datos](#reader-and-data-access) | Permite ver todo el contenido, pero no eliminar ni crear una cuenta de almacenamiento ni un recurso incluido. También permitirá el acceso de lectura o escritura para todos los datos incluidos en una cuenta de almacenamiento a través del acceso a las claves de la cuenta de almacenamiento. |
| [Colaborador de la memoria caché de Redis](#redis-cache-contributor) | Permite administrar cachés de Redis, pero no acceder a ellas. |
| [Colaborador de colecciones de trabajos de Scheduler](#scheduler-job-collections-contributor) | Permite administrar colecciones de trabajos de Scheduler, pero no acceder a ellas. |
| [Colaborador del servicio Search](#search-service-contributor) | Permite administrar los servicios de Búsqueda, pero no acceder a ellos. |
| [Administrador de seguridad](#security-admin) | Solo en Security Center: puede ver las directivas de seguridad, ver los estados de seguridad, editar las directivas de seguridad, ver alertas y recomendaciones, y descartar alertas y recomendaciones |
| [Administrador de seguridad (heredado)](#security-manager-legacy) | Se trata de un rol heredado. En su lugar, use el Administrador de seguridad. |
| [Lector de seguridad](#security-reader) | Solo en Security Center: puede ver las recomendaciones y alertas, ver las directivas de seguridad y ver los estados de seguridad, pero no puede realizar cambios |
| [Colaborador de Site Recovery](#site-recovery-contributor) | Permite administrar el servicio Site Recovery, excepto la creación de almacenes y la asignación de roles. |
| [Operador de Site Recovery](#site-recovery-operator) | Permite realizar una conmutación por error o una conmutación por recuperación, pero no otras operaciones de administración de Site Recovery. |
| [Lector de Site Recovery](#site-recovery-reader) | Permite visualizar el estado de Site Recovery, pero no realizar otras operaciones de administración. |
| [Colaborador de Base de datos de SQL](#sql-db-contributor) | Permite administrar las bases de datos de SQL, pero no acceder a ellas. Además, no puede administrar sus directivas relacionadas con la seguridad ni los servidores SQL primarios. |
| [Administrador de seguridad SQL](#sql-security-manager) | Permite administrar las directivas relacionadas con seguridad de bases de datos y servidores SQL, pero no acceder a ellas. |
| [Colaborador de SQL Server](#sql-server-contributor) | Permite administrar bases de datos y servidores SQL, pero no acceder a ellos, ni a sus directivas relacionadas con la seguridad. |
| [Colaborador de la cuenta de almacenamiento](#storage-account-contributor) | Permite administrar cuentas de almacenamiento, pero no acceder a ellas. |
| [Rol de servicio de operador de claves de cuentas de almacenamiento](#storage-account-key-operator-service-role) | Los operadores de claves de cuentas de almacenamiento pueden enumerar y regenerar claves de cuentas de almacenamiento. |
| [Colaborador de la solicitud de soporte técnico](#support-request-contributor) | Permite crear y administrar solicitudes de soporte técnico. |
| [Colaborador de Traffic Manager](#traffic-manager-contributor) | Le permite administrar perfiles de Traffic Manager, pero no controlar los usuarios que tienen acceso a ellos. |
| [Administrador de acceso de usuario](#user-access-administrator) | Permite administrar el acceso de usuario a los recursos de Azure. |
| [Inicio de sesión de administrador de Virtual Machine](#virtual-machine-administrator-login) | - Los usuarios con este rol tienen la posibilidad de iniciar sesión en una máquina virtual con privilegios de administrador de Windows o de usuario raíz de Linux. |
| [Colaborador de la máquina virtual](#virtual-machine-contributor) | Permite administrar máquinas virtuales, pero no acceder a ellas, ni tampoco a la red virtual ni a la cuenta de almacenamiento a las que están conectadas. |
| [Inicio de sesión de usuario de Virtual Machine](#virtual-machine-user-login) | Los usuarios con este rol tienen la posibilidad de iniciar sesión en una máquina virtual como un usuario normal. |
| [Colaborador de plan web](#web-plan-contributor) | Permite administrar los planes web para sitios web, pero no acceder a ellos. |
| [Colaborador de sitio web](#website-contributor) | Permite administrar los sitios web (no planes web), pero no acceder a ellos. |


## <a name="owner"></a>Propietario
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrarlo todo, incluso el acceso a los recursos. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Acciones** |  |
> | * | Crear y administrar recursos de todos los tipos |

## <a name="contributor"></a>Colaborador
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrarlo todo excepto el acceso a los recursos. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Acciones** |  |
> | * | Crear y administrar recursos de todos los tipos |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | No puede eliminar roles ni asignaciones de roles. |
> | Microsoft.Authorization/*/Write | No puede crear roles ni asignaciones de roles. |
> | Microsoft.Authorization/elevateAccess/Action | Concede al llamador acceso de administrador de acceso de usuario en el ámbito de inquilinos |

## <a name="reader"></a>Lector
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite ver todo el contenido, pero no realizar cambios. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Acciones** |  |
> | */read | Leer recursos de todos los tipos, excepto secretos. |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Firmante de imagen de ACR |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Acciones** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Lector de datos de cuarentena de ACR |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Acciones** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Escritura de datos de cuarentena de ACR |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Acciones** |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="api-management-service-contributor"></a>Colaborador de servicio de administración de API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Puede administrar servicios y las API. |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Acciones** |  |
> | Microsoft.ApiManagement/service/* | Crear y administrar servicio API Management |
> | Microsoft.Authorization/*/read | Leer autorización |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="api-management-service-operator-role"></a>Rol del operador del servicio API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Puede administrar el servicio, pero no las API. |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Acciones** |  |
> | Microsoft.ApiManagement/service/*/read | Lectura de instancias del servicio API Management |
> | Microsoft.ApiManagement/service/backup/action | Realiza una copia de seguridad del servicio API Management en el contenedor especificado de una cuenta de almacenamiento proporcionada por el usuario |
> | Microsoft.ApiManagement/service/delete | Elimina una instancia del servicio API Management |
> | Microsoft.ApiManagement/service/managedeployments/action | Cambia SKU y unidades, y agrega o quita las implementaciones regionales del servicio API Management |
> | Microsoft.ApiManagement/service/read | Lectura de los metadatos de una instancia del servicio API Management |
> | Microsoft.ApiManagement/service/restore/action | Restauración del servicio API Management desde el contenedor especificado de una cuenta de almacenamiento proporcionada por el usuario |
> | Microsoft.ApiManagement/service/updatecertificate/action | Carga el certificado SSL de un servicio API Management |
> | Microsoft.ApiManagement/service/updatehostname/action | Configura, actualiza o elimina los nombres de dominio personalizado de un servicio API Management |
> | Microsoft.ApiManagement/service/write | Creación de una nueva instancia del servicio API Management |
> | Microsoft.Authorization/*/read | Leer autorización |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obtiene una lista de claves de usuario |

## <a name="api-management-service-reader-role"></a>Rol de lector del servicio API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Acceso de solo lectura al servicio y las API. |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Acciones** |  |
> | Microsoft.ApiManagement/service/*/read | Lectura de instancias del servicio API Management |
> | Microsoft.ApiManagement/service/read | Lectura de los metadatos de una instancia del servicio API Management |
> | Microsoft.Authorization/*/read | Leer autorización |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obtiene una lista de claves de usuario |

## <a name="application-insights-component-contributor"></a>Colaborador de componentes de Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Puede administrar los componentes de Application Insights |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.Insights/components/* | Crear y administrar componentes de Insights |
> | Microsoft.Insights/webtests/* | Crear y administrar pruebas web |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="application-insights-snapshot-debugger"></a>Depurador de instantáneas de Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Concede permiso al usuario para usar las características del depurador de instantáneas de Application Insights. |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="automation-job-operator"></a>Operador de trabajos de Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite crear y administrar trabajos con los runbooks de Automation. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Automation/automationAccounts/jobs/read | Obtiene un trabajo de Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Reanuda un trabajo de Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Detiene un trabajo de Azure Automation |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lee los recursos de Hybrid Runbook Worker |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtiene un flujo de trabajos de Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende un trabajo de Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/write | Crea un trabajo de Azure Automation |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="automation-operator"></a>Operador de Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Los operadores de automatización pueden iniciar, detener, suspender y reanudar trabajos. |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lee los recursos de Hybrid Runbook Worker |
> | Microsoft.Automation/automationAccounts/jobs/read | Obtiene un trabajo de Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Reanuda un trabajo de Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Detiene un trabajo de Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtiene un flujo de trabajos de Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende un trabajo de Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/write | Crea un trabajo de Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Obtiene una programación de trabajos de Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Crea una programación de trabajos de Azure Automation |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtiene el área de trabajo vinculada a la cuenta de Automation. |
> | Microsoft.Automation/automationAccounts/read | Obtiene una cuenta de Azure Automation |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obtiene un runbook de Azure Automation |
> | Microsoft.Automation/automationAccounts/schedules/read | Obtiene un recurso de programación de Azure Automation |
> | Microsoft.Automation/automationAccounts/schedules/write | Crea o actualiza un recurso de programación de Azure Automation |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Obtiene la salida de un trabajo |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="automation-runbook-operator"></a>Operador de runbooks de Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite leer las propiedades de runbook para poder crear trabajos del runbook. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obtiene un runbook de Azure Automation |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="azure-stack-registration-owner"></a>Propietario del registro de Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar los registros de Azure Stack. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Acciones** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Recupera detalles ampliados de un producto de Marketplace de Azure Stack. |
> | Microsoft.AzureStack/registrations/products/read | Obtiene las propiedades de un producto de Marketplace de Azure Stack. |
> | Microsoft.AzureStack/registrations/read | Obtiene las propiedades de un registro de Azure Stack. |

## <a name="backup-contributor"></a>Colaborador de copias de seguridad
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar el servicio de copias de seguridad, pero no puede crear almacenes y conceder acceso a otros usuarios |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Network/virtualNetworks/read | Obtiene la definición de red virtual |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Administrar los resultados de la operación de administración de copias de seguridad |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Crear y administrar contenedores de copias de seguridad dentro de tejidos de copia de seguridad del almacén de Recovery Services |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Crear y administrar trabajos de copia de seguridad |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exporta trabajos |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Crear y administrar metadatos relacionados con la administración de copias de seguridad |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Crear y administrar resultados de operaciones de administración de copias de seguridad |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Crear y administrar directivas de copia de seguridad |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Crear y administrar elementos de los que se puede realizar una copia de seguridad |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Crear y administrar elementos de los que se ha realizado una copia de seguridad |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Crear y administrar contenedores que incluyen elementos de copia de seguridad |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Crear y administrar certificados relacionados con copias de seguridad en el almacén de Recovery Services |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Crear y administrar información ampliada relacionada con el almacén |
> | Microsoft.RecoveryServices/Vaults/read | La operación Get Vault obtiene un objeto que representa el recurso de Azure del tipo "almacén" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/* | Administrar la operación de detección para capturar contenedores recién creados |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Crear y administrar identidades registradas |
> | Microsoft.RecoveryServices/Vaults/usages/* | Crear y administrar el uso del almacén de Recovery Services |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Devuelve resúmenes de los elementos y servidores protegidos para un almacén de Recovery Services. |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devuelve la lista de cuentas de almacenamiento u obtiene las propiedades de la cuenta de almacenamiento especificada. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp es una operación interna que el servicio usa |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtiene las alertas del almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Devuelve el resultado de la operación del trabajo de exportación. |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="backup-operator"></a>Operador de copias de seguridad
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar los servicios de copias de seguridad, excepto la eliminación de copias de seguridad, la creación de almacenes y la concesión de acceso a otros usuarios |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Network/virtualNetworks/read | Obtiene la definición de red virtual |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Devuelve el estado de la operación |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtiene los resultados de la operación realizada en el contenedor de protección. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Hace una copia de seguridad del elemento protegido. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtiene el resultado de la operación realizada en los elementos protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Devuelve el estado de la operación realizada en los elementos protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devuelve detalles de objeto del elemento protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obtiene los puntos de recuperación de los elementos protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaura los puntos de recuperación de los elementos protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Crea un elemento protegido de copia de seguridad |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Devuelve todos los contenedores registrados |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Crear y administrar trabajos de copia de seguridad |
> | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Cancela el trabajo |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Devuelve el resultado de la operación de trabajo. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Devuelve todos los objetos de trabajo |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exporta trabajos |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Devuelve los metadatos de administración de Backup para el almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Crear y administrar resultados de operaciones de administración de copias de seguridad |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obtiene los resultados de la operación de directiva. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Devuelve todas las directivas de protección |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Crear y administrar elementos de los que se puede realizar una copia de seguridad |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Devuelve una lista de todos los elementos que se pueden proteger. |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Devuelve la lista de todos los elementos protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Devuelve todos los contenedores que pertenecen a la suscripción |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Devuelve resúmenes de los elementos y servidores protegidos para un almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén? |
> | Microsoft.RecoveryServices/Vaults/read | La operación Get Vault obtiene un objeto que representa el recurso de Azure del tipo "almacén" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/* | Administrar la operación de detección para capturar contenedores recién creados |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | La operación Obtener resultados de la operación se puede usar para obtener el estado y el resultado de la operación enviada de forma asincrónica |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | La operación Obtener contenedores se puede usar para obtener los contenedores registrados para un recurso. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | La operación Registrar contenedor de servicios se puede usar para registrar un contenedor con servicio de recuperación. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devuelve los detalles de uso de un almacén de Recovery Services. |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devuelve la lista de cuentas de almacenamiento u obtiene las propiedades de la cuenta de almacenamiento especificada. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Aprovisiona una recuperación de elementos instantánea para los elementos protegidos |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revoca la recuperación de elementos instantánea para los elementos protegidos |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp es una operación interna que el servicio usa |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtiene las alertas del almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Devuelve el resultado de la operación del trabajo de exportación. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
> | Microsoft.RecoveryServices/Vaults/certificates/write | La operación Actualizar certificado de recursos permite actualizar el certificado de credencial de recursos o almacenes. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="backup-reader"></a>Lector de copias de seguridad
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Puede ver servicios de copia de seguridad, pero no puede realizar cambios. |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Devuelve el estado de la operación |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtiene los resultados de la operación realizada en el contenedor de protección. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtiene el resultado de la operación realizada en los elementos protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Devuelve el estado de la operación realizada en los elementos protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devuelve detalles de objeto del elemento protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Devuelve todos los contenedores registrados |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Devuelve el resultado de la operación de trabajo. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Devuelve todos los objetos de trabajo |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exporta trabajos |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Devuelve los metadatos de administración de Backup para el almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Devuelve el resultado de la operación de Backup para el almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obtiene los resultados de la operación de directiva. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Devuelve todas las directivas de protección |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Devuelve la lista de todos los elementos protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Devuelve todos los contenedores que pertenecen a la suscripción |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Devuelve resúmenes de los elementos y servidores protegidos para un almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén? |
> | Microsoft.RecoveryServices/Vaults/read | La operación Get Vault obtiene un objeto que representa el recurso de Azure del tipo "almacén" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | La operación Obtener resultados de la operación se puede usar para obtener el estado y el resultado de la operación enviada de forma asincrónica |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | La operación Obtener contenedores se puede usar para obtener los contenedores registrados para un recurso. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp es una operación interna que el servicio usa |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtiene las alertas del almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obtiene los puntos de recuperación de los elementos protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Devuelve el resultado de la operación del trabajo de exportación. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devuelve los detalles de uso de un almacén de Recovery Services. |

## <a name="billing-reader"></a>Lector de facturación
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite acceso de lectura a los datos de facturación. |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Billing/*/read | Leer la información de facturación |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Management/managementGroups/read | Enumera los grupos de administración del usuario autenticado. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="biztalk-contributor"></a>Colaborador de BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar los servicios de BizTalk, pero no acceder a ellos. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.BizTalkServices/BizTalk/* | Crear y administrar los servicios de BizTalk |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="cdn-endpoint-contributor"></a>Colaborador de punto de conexión de CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Puede administrar puntos de conexión de CDN, pero no conceder acceso a otros usuarios. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="cdn-endpoint-reader"></a>Lector de punto de conexión de CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Puede ver puntos de conexión de CDN, pero no hacer cambios. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="cdn-profile-contributor"></a>Colaborador de perfil de CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Puede administrar perfiles de CDN y sus puntos de conexión, pero no conceder acceso a otros usuarios. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="cdn-profile-reader"></a>Lector de perfil de CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Puede ver perfiles de CDN y sus puntos de conexión, pero no hacer cambios. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="classic-network-contributor"></a>Colaborador de la red clásica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar las redes clásicas, pero no acceder a ellas. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer autorización |
> | Microsoft.ClassicNetwork/* | Crear y administrar redes clásicas |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="classic-storage-account-contributor"></a>Colaborador de cuentas de almacenamiento clásico
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar cuentas de almacenamiento clásicas, pero no acceder a ellas. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer autorización |
> | Microsoft.ClassicStorage/storageAccounts/* | Crear y administrar cuentas de almacenamiento |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="classic-storage-account-key-operator-service-role"></a>Rol de servicio de operador de claves de cuentas de almacenamiento clásicas
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Los operadores de claves de cuentas de almacenamiento clásicas pueden enumerar y regenerar claves en cuentas de almacenamiento clásicas |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Acciones** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Enumera las claves de acceso de las cuentas de almacenamiento. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Regenera las claves de acceso existentes de la cuenta de almacenamiento. |

## <a name="classic-virtual-machine-contributor"></a>Colaborador de la máquina virtual clásica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar máquinas virtuales clásicas, pero no acceder a ellas, ni tampoco a la red virtual ni a la cuenta de almacenamiento a las que están conectadas. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer autorización |
> | Microsoft.ClassicCompute/domainNames/* | Crear y administrar nombres de dominio de proceso clásico |
> | Microsoft.ClassicCompute/virtualMachines/* | Crear y administrar máquinas virtuales |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Vincula una IP reservada |
> | Microsoft.ClassicNetwork/reservedIps/read | Obtiene las IP reservadas |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Une la red virtual. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Obtiene la red virtual. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Devuelve el disco de la cuenta de almacenamiento. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Devuelve la imagen de la cuenta de almacenamiento. |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Enumera las claves de acceso de las cuentas de almacenamiento. |
> | Microsoft.ClassicStorage/storageAccounts/read | Devuelve la cuenta de almacenamiento con la cuenta especificada. |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="cleardb-mysql-db-contributor"></a>Colaborador de ClearDB MySQL DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar las bases de datos ClearDB MySQL, pero no acceder a ellas. |
> | **Id** | 9106cda0-8a86-4e81-b686-29a22c54effe |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | successbricks.cleardb/databases/* | Crear y administrar bases de datos ClearDB MySQL |

## <a name="cosmos-db-account-reader-role"></a>Rol de lector de cuentas de Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Puede leer los datos de cuentas de Azure Cosmos DB. Vea [Colaborador de cuenta de DocumentDB](#documentdb-account-contributor) para administrar cuentas de Azure Cosmos DB. |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles, puede leer permisos dados a cada usuario |
> | Microsoft.DocumentDB/*/read | Leer cualquier colección |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Lee las claves de solo lectura de la cuenta de base de datos. |
> | Microsoft.Insights/MetricDefinitions/read | Lee definiciones de métricas |
> | Microsoft.Insights/Metrics/read | Lee métricas |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="data-factory-contributor"></a>Colaborador de Factoría de datos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Crea y administra factorías de datos, así como recursos secundarios dentro de ellas. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.DataFactory/dataFactories/* | Crear y administrar factorías de datos y recursos secundarios dentro de ellos. |
> | Microsoft.DataFactory/factories/* | Crear y administrar factorías de datos y recursos secundarios dentro de ellos. |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="data-lake-analytics-developer"></a>Desarrollador de Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Le permite enviar, supervisar y administrar sus propios trabajos, pero no crear ni eliminar cuentas de Data Lake Analytics. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Elimina la cuenta de DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Concede permisos para cancelar trabajos que enviaron otros usuarios. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Crea o actualiza una cuenta de DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Crea o actualiza una cuenta vinculada de DataLakeStore en la cuenta de DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Anula la vinculación de una cuenta de DataLakeStore a la cuenta de DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Crea o actualiza una cuenta de almacenamiento vinculada a una cuenta de DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Anula la vinculación de una cuenta de almacenamiento a la cuenta de DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Crea o actualiza una regla de firewall. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Elimina una regla de firewall. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Crea o actualiza una directiva de proceso. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Elimina una directiva de proceso. |

## <a name="data-purger"></a>Purgador de datos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Puede purgar datos de análisis. |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Acciones** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action |  |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Elimina los datos especificados del área de trabajo. |

## <a name="devtest-labs-user"></a>Usuario de DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite conectarse a sus máquinas virtuales, así como iniciarlas, reiniciarlas y apagarlas, en su instancia de Azure DevTest Labs. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Compute/availabilitySets/read | Obtiene las propiedades de un conjunto de disponibilidad |
> | Microsoft.Compute/virtualMachines/*/read | Leer las propiedades de una máquina virtual (tamaños de máquinas virtuales, el estado de tiempo de ejecución, extensiones de máquina virtual, etc.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Apaga la máquina virtual y libera los recursos de proceso |
> | Microsoft.Compute/virtualMachines/read | Obtiene las propiedades de una máquina virtual |
> | Microsoft.Compute/virtualMachines/restart/action | Reinicia la máquina virtual |
> | Microsoft.Compute/virtualMachines/start/action | Inicia la máquina virtual |
> | Microsoft.DevTestLab/*/read | Leer las propiedades de un laboratorio |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Crea máquinas virtuales en un laboratorio. |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Reclama una máquina virtual aleatoria en el laboratorio. |
> | Microsoft.DevTestLab/labs/formulas/delete | Elimina fórmulas. |
> | Microsoft.DevTestLab/labs/formulas/read | Lee fórmulas. |
> | Microsoft.DevTestLab/labs/formulas/write | Agrega o modifica fórmulas. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Evalúa la directiva de laboratorio. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Toma la propiedad de una máquina virtual existente |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Se une a un grupo de direcciones de back-end del equilibrador de carga |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Se une a una regla NAT de entrada del equilibrador de carga |
> | Microsoft.Network/networkInterfaces/*/read | Leer las propiedades de una interfaz de red (por ejemplo, todos los equilibradores de carga de los que forma parte de la interfaz de red) |
> | Microsoft.Network/networkInterfaces/join/action | Une una máquina virtual a una interfaz de red |
> | Microsoft.Network/networkInterfaces/read | Obtiene una definición de interfaz de red.  |
> | Microsoft.Network/networkInterfaces/write | Crea una interfaz de red o actualiza una interfaz de red existente.  |
> | Microsoft.Network/publicIPAddresses/*/read | Leer las propiedades de una dirección IP pública |
> | Microsoft.Network/publicIPAddresses/join/action | Se une a una dirección IP pública |
> | Microsoft.Network/publicIPAddresses/read | Obtiene una definición de la dirección ip pública. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Se une a una red virtual |
> | Microsoft.Resources/deployments/operations/read | Obtiene o enumera las operaciones de implementación. |
> | Microsoft.Resources/deployments/read | Obtiene o enumera implementaciones. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devuelve las claves de acceso de la cuenta de almacenamiento especificada. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Enumera los tamaños disponibles a los que se puede actualizar la máquina virtual |

## <a name="dns-zone-contributor"></a>Colaborador de zona DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar zonas y conjuntos de registros DNS en Azure DNS, pero no controlar los usuarios que tienen acceso. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.Network/dnsZones/* | Crear y administrar registros y zonas DNS |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar vales de soporte técnico |

## <a name="documentdb-account-contributor"></a>Colaborador de cuenta de DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Puede administrar cuentas de Azure Cosmos DB. Azure Cosmos DB se llamaba anteriormente DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.DocumentDb/databaseAccounts/* | Crear y administrar cuentas de Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="intelligent-systems-account-contributor"></a>Colaborador de la cuenta de Sistemas inteligentes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar las cuentas de Intelligent Systems, pero no acceder a ellas. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.IntelligentSystems/accounts/* | Crear y administrar cuentas de sistemas inteligentes |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="key-vault-contributor"></a>Colaborador de almacén de claves
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Le permite administrar almacenes de claves, pero no acceder a ellos. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Purga un almacén de claves eliminado temporalmente |
> | Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Creador de laboratorio
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Le permite crear, administrar y eliminar los laboratorios administrados en sus cuentas de Azure Lab. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Crea un laboratorio en una cuenta de laboratorio. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="log-analytics-contributor"></a>Colaborador de Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Un colaborador de Log Analytics puede leer todos los datos de supervisión y editar la configuración de supervisión. La edición de la configuración de supervisión incluye la posibilidad de añadir la extensión de máquina virtual a las máquinas virtuales, leer las claves de las cuentas de almacenamiento para poder configurar la recopilación de registros de Azure Storage, crear y configurar cuentas de Automation, añadir soluciones y configurar Azure Diagnostics en todos los recursos de Azure. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Acciones** |  |
> | */read | Leer recursos de todos los tipos, excepto secretos. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Enumera las claves de acceso de las cuentas de almacenamiento. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Insights/diagnosticSettings/* | Crea, actualiza o lee la configuración de diagnóstico de Analysis Server. |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devuelve las claves de acceso de la cuenta de almacenamiento especificada. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="log-analytics-reader"></a>Lector de Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Un lector de Log Analytics puede ver y buscar todos los datos de supervisión, así como consultar la configuración de supervisión, incluida la de Azure Diagnostics en todos los recursos de Azure. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Acciones** |  |
> | */read | Leer recursos de todos los tipos, excepto secretos. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Realiza búsquedas mediante el nuevo motor. |
> | Microsoft.OperationalInsights/workspaces/search/action | Ejecuta una consulta de búsqueda |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera las claves compartidas del área de trabajo. Estas claves se utilizan para conectar los agentes de Microsoft Operational Insights al área de trabajo. |

## <a name="logic-app-contributor"></a>Colaborador de aplicación lógica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Le permite administrar aplicaciones lógicas, pero no acceder a ellas. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Enumera las claves de acceso de las cuentas de almacenamiento. |
> | Microsoft.ClassicStorage/storageAccounts/read | Devuelve la cuenta de almacenamiento con la cuenta especificada. |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Insights/diagnosticSettings/* | Crea, actualiza o lee la configuración de diagnóstico de Analysis Server. |
> | Microsoft.Insights/logdefinitions/* | Este permiso es necesario para los usuarios que necesitan acceder a registros de actividades a través del portal. Enumere las categorías de registro del registro de actividad. |
> | Microsoft.Insights/metricDefinitions/* | Leer definiciones de métrica (lista de tipos de métricas disponibles para un recurso). |
> | Microsoft.Logic/* | Administra los recursos de Logic Apps. |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/operationresults/read | Obtiene los resultados de la operación de suscripción. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Devuelve las claves de acceso de la cuenta de almacenamiento especificada. |
> | Microsoft.Storage/storageAccounts/read | Devuelve la lista de cuentas de almacenamiento u obtiene las propiedades de la cuenta de almacenamiento especificada. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | Microsoft.Web/connectionGateways/* | Crea y administra una puerta de enlace de conexión. |
> | Microsoft.Web/connections/* | Crea y administra una conexión. |
> | Microsoft.Web/customApis/* | Crea y administra una API personalizada. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Obtiene las propiedades de un plan de App Service |
> | Microsoft.Web/sites/functions/listSecrets/action | Muestra secretos de Web Apps. |

## <a name="logic-app-operator"></a>Operador de aplicación lógica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Le permite leer, habilitar y deshabilitar aplicaciones lógicas. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/*/read | Permite leer reglas de alerta de Insights. |
> | Microsoft.Insights/diagnosticSettings/*/read | Obtiene la configuración de diagnóstico de Logic Apps. |
> | Microsoft.Insights/metricDefinitions/*/read | Obtiene las métricas disponibles para Logic Apps. |
> | Microsoft.Logic/*/read | Lee los recursos de Logic Apps. |
> | Microsoft.Logic/workflows/disable/action | Deshabilita el flujo de trabajo. |
> | Microsoft.Logic/workflows/enable/action | Habilita el flujo de trabajo. |
> | Microsoft.Logic/workflows/validate/action | Valida el flujo de trabajo. |
> | Microsoft.Resources/deployments/operations/read | Obtiene o enumera las operaciones de implementación. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obtiene los resultados de la operación de suscripción. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | Microsoft.Web/connectionGateways/*/read | Lee las puertas de enlace de conexión. |
> | Microsoft.Web/connections/*/read | Lee las conexiones. |
> | Microsoft.Web/customApis/*/read | Lee la API personalizada. |
> | Microsoft.Web/serverFarms/read | Obtiene las propiedades de un plan de App Service |

## <a name="managed-identity-contributor"></a>Colaborador de identidad administrada
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Le permite crear, leer, actualizar y eliminar identidades asignadas por el usuario. |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Acciones** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="managed-identity-operator"></a>Operador de identidad administrada
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Le permite leer y asignar identidades asignadas por el usuario. |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Acciones** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="monitoring-contributor"></a>Colaborador de supervisión
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Puede leer todos los datos de supervisión y editar la configuración de supervisión. Consulte también [Introducción a roles, permisos y seguridad con Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Acciones** |  |
> | */read | Leer recursos de todos los tipos, excepto secretos. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/AlertRules/* | Reglas de alerta de lectura, escritura y eliminación. |
> | Microsoft.Insights/components/* | Leer, escribir o eliminar los componentes de Application Insights. |
> | Microsoft.Insights/DiagnosticSettings/* | Configuración de diagnóstico de lectura, escritura y eliminación. |
> | Microsoft.Insights/eventtypes/* | Enumerar eventos del registro de actividades (eventos de administración) de una suscripción. Este permiso es aplicable para el acceso mediante programación y mediante el portal al registro de actividades. |
> | Microsoft.Insights/LogDefinitions/* | Este permiso es necesario para los usuarios que necesitan acceder a registros de actividades a través del portal. Enumere las categorías de registro del registro de actividad. |
> | Microsoft.Insights/MetricDefinitions/* | Leer definiciones de métrica (lista de tipos de métricas disponibles para un recurso). |
> | Microsoft.Insights/Metrics/* | Leer las métricas de un recurso. |
> | Microsoft.Insights/Register/Action | Registra el proveedor de Microsoft Insights |
> | Microsoft.Insights/webtests/* | Leer, escribir o eliminar pruebas web de Application Insights. |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Leer, escribir o eliminar paquetes de soluciones de Log Analytics. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Leer, escribir o eliminar búsquedas guardadas de Log Analytics. |
> | Microsoft.OperationalInsights/workspaces/search/action | Ejecuta una consulta de búsqueda |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Recupera las claves compartidas del área de trabajo. Estas claves se utilizan para conectar los agentes de Microsoft Operational Insights al área de trabajo. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Leer, escribir o eliminar configuraciones de visión de almacenamiento de Log Analytics. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Lector de supervisión
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Puede leer todos los datos de supervisión (métricas, registros, etc.). Consulte también [Introducción a roles, permisos y seguridad con Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Acciones** |  |
> | */read | Leer recursos de todos los tipos, excepto secretos. |
> | Microsoft.OperationalInsights/workspaces/search/action | Ejecuta una consulta de búsqueda |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="network-contributor"></a>Colaborador de la red
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar redes, pero no acceder a ellas. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.Network/* | Crear y administrar redes |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="new-relic-apm-account-contributor"></a>Colaborador de la cuenta de NewRelic APM
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Le permite administrar las aplicaciones y cuentas de Application Performance Management de New Relic, pero no acceder a ellas. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | NewRelic.APM/accounts/* |  |

## <a name="reader-and-data-access"></a>Lector y acceso a los datos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite ver todo el contenido, pero no eliminar ni crear una cuenta de almacenamiento ni un recurso incluido. También permitirá el acceso de lectura o escritura para todos los datos incluidos en una cuenta de almacenamiento a través del acceso a las claves de la cuenta de almacenamiento. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Acciones** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devuelve las claves de acceso de la cuenta de almacenamiento especificada. |
> | Microsoft.Storage/storageAccounts/read | Devuelve la lista de cuentas de almacenamiento u obtiene las propiedades de la cuenta de almacenamiento especificada. |

## <a name="redis-cache-contributor"></a>Colaborador de la memoria caché de Redis
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar cachés de Redis, pero no acceder a ellas. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Cache/redis/* | Crear y administrar memorias caché de Redis |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="scheduler-job-collections-contributor"></a>Colaborador de colecciones de trabajos de Scheduler
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar colecciones de trabajos de Scheduler, pero no acceder a ellas. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Scheduler/jobcollections/* | Crear y administrar colecciones de trabajos |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="search-service-contributor"></a>Colaborador del servicio de búsqueda
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar los servicios de Búsqueda, pero no acceder a ellos. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Search/searchServices/* | Crear y administrar servicios de búsqueda |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="security-admin"></a>Administrador de seguridad
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Solo en Security Center: puede ver las directivas de seguridad, ver los estados de seguridad, editar las directivas de seguridad, ver alertas y recomendaciones, y descartar alertas y recomendaciones |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Authorization/policyAssignments/* | Crear y administrar asignaciones de directivas |
> | Microsoft.Authorization/policyDefinitions/* | Crear y administrar definiciones de directivas |
> | Microsoft.Authorization/policySetDefinitions/* | Crear y administrar conjuntos de directivas |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.operationalInsights/workspaces/*/read | Ver datos de Log Analytics |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Security/*/read | Leer directivas y componentes de seguridad |
> | Microsoft.Security/locations/alerts/dismiss/action | Descarta una alerta de seguridad. |
> | Microsoft.Security/locations/alerts/activate/action | Activa una alerta de seguridad. |
> | Microsoft.Security/locations/tasks/dismiss/action | Descarta una recomendación de seguridad |
> | Microsoft.Security/locations/tasks/activate/action | Activa una recomendación de seguridad |
> | Microsoft.Security/policies/write | Actualiza la directiva de seguridad |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="security-manager-legacy"></a>Administrador de seguridad (heredado)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Se trata de un rol heredado. En su lugar, use el Administrador de seguridad. |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.ClassicCompute/*/read | Leer información de configuración de máquinas virtuales clásicas |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Escribir configuración de máquinas virtuales clásicas |
> | Microsoft.ClassicNetwork/*/read | Leer información de configuración acerca de la red clásica |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Security/* | Crear y administrar las directivas y los componentes de seguridad |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="security-reader"></a>Lector de seguridad
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Solo en Security Center: puede ver las recomendaciones y alertas, ver las directivas de seguridad y ver los estados de seguridad, pero no puede realizar cambios |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Acciones** |  |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.operationalInsights/workspaces/*/read | Ver datos de Log Analytics |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Security/*/read | Leer directivas y componentes de seguridad |

## <a name="site-recovery-contributor"></a>Colaborador de Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar el servicio Site Recovery, excepto la creación de almacenes y la asignación de roles. |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.Network/virtualNetworks/read | Obtiene la definición de red virtual |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp es una operación interna que el servicio usa |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp es una operación interna que el servicio usa |
> | Microsoft.RecoveryServices/Vaults/certificates/write | La operación Actualizar certificado de recursos permite actualizar el certificado de credencial de recursos o almacenes. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Crear y administrar información ampliada relacionada con el almacén |
> | Microsoft.RecoveryServices/Vaults/read | La operación Get Vault obtiene un objeto que representa el recurso de Azure del tipo "almacén" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Crear y administrar identidades registradas |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Crea o actualiza la configuración de las alertas de replicación |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Lee todos los eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Crea y administra los tejidos de replicación |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Crea y administra los trabajos de replicación |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Crea y administra las directivas de replicación |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Crea y administra planes de recuperación |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Crea y administra la configuración de almacenamiento del almacén de Recovery Services |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Devuelve la información del token del almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devuelve los detalles de uso de un almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | La operación Token de almacén se puede usar para obtener el token de almacén de las operaciones back-end a nivel de almacén. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Lee las alertas del almacén de Recovery Services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devuelve la lista de cuentas de almacenamiento u obtiene las propiedades de la cuenta de almacenamiento especificada. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="site-recovery-operator"></a>Operador de Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite realizar una conmutación por error o una conmutación por recuperación, pero no otras operaciones de administración de Site Recovery. |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.Network/virtualNetworks/read | Obtiene la definición de red virtual |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp es una operación interna que el servicio usa |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp es una operación interna que el servicio usa |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén? |
> | Microsoft.RecoveryServices/Vaults/read | La operación Get Vault obtiene un objeto que representa el recurso de Azure del tipo "almacén" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | La operación Obtener resultados de la operación se puede usar para obtener el estado y el resultado de la operación enviada de forma asincrónica |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | La operación Obtener contenedores se puede usar para obtener los contenedores registrados para un recurso. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lee la configuración de todas las alertas |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Lee todos los eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Comprueba la coherencia del tejido |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Lee todos los tejidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Vuelve a asociar la puerta de enlace |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renueva un certificado para Fabric. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Lee todas las redes |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Lee todas las asignaciones de redes |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Lee todos los contenedores de protección |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Lee todos los elementos que se pueden proteger |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplica un punto de recuperación |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Confirma la conmutación por error |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Conmutación por error planeada |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Lee todos los elementos protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Lee los puntos de recuperación de todas las replicaciones |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Repara una replicación |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Vuelva a proteger el elemento protegido |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Prueba la limpieza de la conmutación por error |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Conmutación por error |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Actualiza Mobility Service |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Lee todas las asignaciones de los contenedores de protección |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Lee todos los proveedores de Recovery Services |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Actualiza el proveedor |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Lee todas las clasificaciones de almacenamiento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Lee todas las asignaciones de clasificaciones de almacenamiento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Lee todos los trabajos |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Crea y administra los trabajos de replicación |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Lee todas las directivas |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plan de recuperación de confirmación de la conmutación por error |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plan de recuperación de conmutación por error planeado |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Lee todos los planes de recuperación |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Vuelve a proteger el plan de recuperación |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Prueba el plan de recuperación de conmutación por error |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Prueba el plan de recuperación de limpieza de la conmutación por error |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan de recuperación de conmutación por error |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Lee las alertas del almacén de Recovery Services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Devuelve la información del token del almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devuelve los detalles de uso de un almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | La operación Token de almacén se puede usar para obtener el token de almacén de las operaciones back-end a nivel de almacén. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Devuelve la lista de cuentas de almacenamiento u obtiene las propiedades de la cuenta de almacenamiento especificada. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="site-recovery-reader"></a>Lector de Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite visualizar el estado de Site Recovery, pero no realizar otras operaciones de administración. |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp es una operación interna que el servicio usa |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | La operación Obtener información adicional obtiene la información adicional de un objeto que representa el recurso de Azure de tipo ?almacén? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtiene las alertas del almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | La operación Get Vault obtiene un objeto que representa el recurso de Azure del tipo "almacén" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | La operación Obtener resultados de la operación se puede usar para obtener el estado y el resultado de la operación enviada de forma asincrónica |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | La operación Obtener contenedores se puede usar para obtener los contenedores registrados para un recurso. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lee la configuración de todas las alertas |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Lee todos los eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Lee todos los tejidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Lee todas las redes |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Lee todas las asignaciones de redes |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Lee todos los contenedores de protección |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Lee todos los elementos que se pueden proteger |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Lee todos los elementos protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Lee los puntos de recuperación de todas las replicaciones |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Lee todas las asignaciones de los contenedores de protección |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Lee todos los proveedores de Recovery Services |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Lee todas las clasificaciones de almacenamiento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Lee todas las asignaciones de clasificaciones de almacenamiento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Lee todos los trabajos |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Lee todos los trabajos |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Lee todas las directivas |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Lee todos los planes de recuperación |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Devuelve la información del token del almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devuelve los detalles de uso de un almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | La operación Token de almacén se puede usar para obtener el token de almacén de las operaciones back-end a nivel de almacén. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="sql-db-contributor"></a>Colaborador de Base de datos de SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar las bases de datos de SQL, pero no acceder a ellas. Además, no puede administrar sus directivas relacionadas con la seguridad ni los servidores SQL primarios. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Crear y administrar bases de datos SQL |
> | Microsoft.Sql/servers/read | Devuelve la lista de servidores u obtiene las propiedades de un servidor específico. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | **NotActions** |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | No puede editar las directivas de auditoría |
> | Microsoft.Sql/servers/databases/auditingSettings/* | No puede editar la configuración de auditoría |
> | Microsoft.Sql/servers/databases/auditRecords/read | Recupera los registros de auditoría de blobs de bases de datos |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | No puede editar las directivas de conexión |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | No puede editar las directivas de enmascaramiento |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | No puede editar las directivas de alerta de seguridad |
> | Microsoft.Sql/servers/databases/securityMetrics/* | No puede editar las métricas de seguridad |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>Administrador de seguridad SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar las directivas relacionadas con seguridad de bases de datos y servidores SQL, pero no acceder a ellas. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer autorización de Microsoft |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Combina un recurso como una cuenta de almacenamiento o una instancia de SQL Database con una subred. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Sql/servers/auditingPolicies/* | Crear y administrar directivas de auditoría de SQL Server |
> | Microsoft.Sql/servers/auditingSettings/* | Crear y administrar configuración de auditoría de SQL Server |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Crear y administrar directivas de auditoría de bases de datos de SQL Server |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Crear y administrar configuración de auditoría de bases de datos de SQL Server |
> | Microsoft.Sql/servers/databases/auditRecords/read | Leer registros de auditoría |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Crear y administrar directivas de conexión de bases de datos de SQL Server |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Crear y administrar directivas de enmascaramiento de datos de bases de datos de SQL Server |
> | Microsoft.Sql/servers/databases/read | Devuelve la lista de bases de datos u obtiene las propiedades de una base de datos específica. |
> | Microsoft.Sql/servers/databases/schemas/read | Recupera la lista de esquemas de una base de datos. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Recuperar la lista de columnas de una tabla |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Recupera la lista de tablas de una base de datos. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Crear y administrar directivas de alerta de seguridad de bases de datos de SQL Server |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Crear y administrar métricas de seguridad de bases de datos de SQL Server |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Devuelve la lista de servidores u obtiene las propiedades de un servidor específico. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Crear y administrar directivas de alerta de seguridad de SQL Server |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="sql-server-contributor"></a>Colaborador de SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar bases de datos y servidores SQL, pero no acceder a ellos, ni a sus directivas relacionadas con la seguridad. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Crear y administrar servidores de SQL Server |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | **NotActions** |  |
> | Microsoft.Sql/servers/auditingPolicies/* | No puede editar las directivas de auditoría de SQL Server |
> | Microsoft.Sql/servers/auditingSettings/* | No puede editar la configuración de auditoría de SQL Server |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | No puede editar las directivas de auditoría de bases de datos de SQL Server |
> | Microsoft.Sql/servers/databases/auditingSettings/* | No puede editar la configuración de auditoría de bases de datos de SQL Server |
> | Microsoft.Sql/servers/databases/auditRecords/read | No puede leer los registros de auditoría. |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | No puede editar las directivas de conexión de bases de datos de SQL Server |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | No puede editar las directivas de enmascaramiento de datos de bases de datos de SQL Server |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | No puede editar las directivas de alerta de seguridad de bases de datos de SQL Server |
> | Microsoft.Sql/servers/databases/securityMetrics/* | No puede editar las métricas de seguridad de bases de datos de SQL Server |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | No puede editar las directivas de alerta de seguridad de SQL Server |

## <a name="storage-account-contributor"></a>Colaborador de la cuenta de almacenamiento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar cuentas de almacenamiento, pero no acceder a ellas. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer toda la autorización |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Insights/diagnosticSettings/* | Administrar la configuración de diagnóstico |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Combina un recurso como una cuenta de almacenamiento o una instancia de SQL Database con una subred. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Storage/storageAccounts/* | Crear y administrar cuentas de almacenamiento |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="storage-account-key-operator-service-role"></a>Rol de servicio de operador de claves de cuentas de almacenamiento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Los operadores de claves de cuentas de almacenamiento pueden enumerar y regenerar claves de cuentas de almacenamiento. |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Acciones** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Devuelve las claves de acceso de la cuenta de almacenamiento especificada. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenera las claves de acceso de la cuenta de almacenamiento especificada. |

## <a name="support-request-contributor"></a>Colaborador de la solicitud de soporte técnico
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite crear y administrar solicitudes de soporte técnico. |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer autorización |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="traffic-manager-contributor"></a>Colaborador de Traffic Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Le permite administrar perfiles de Traffic Manager, pero no controlar los usuarios que tienen acceso a ellos. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="user-access-administrator"></a>Administrador de acceso de usuario
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar el acceso de usuario a los recursos de Azure. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Acciones** |  |
> | */read | Leer recursos de todos los tipos, excepto secretos. |
> | Microsoft.Authorization/* | Administrar la autorización |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="virtual-machine-administrator-login"></a>Inicio de sesión de administrador de Virtual Machine
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | - Los usuarios con este rol tienen la posibilidad de iniciar sesión en una máquina virtual con privilegios de administrador de Windows o de usuario raíz de Linux. |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Acciones** |  |
> | Microsoft.Network/publicIPAddresses/read | Obtiene una definición de la dirección ip pública. |
> | Microsoft.Network/virtualNetworks/read | Obtiene la definición de red virtual |
> | Microsoft.Network/loadBalancers/read | Obtiene una definición del equilibrador de carga |
> | Microsoft.Network/networkInterfaces/read | Obtiene una definición de interfaz de red.  |
> | Microsoft.Compute/virtualMachines/*/read |  |

## <a name="virtual-machine-contributor"></a>Colaborador de la máquina virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar máquinas virtuales, pero no acceder a ellas, ni tampoco a la red virtual ni a la cuenta de almacenamiento a las que están conectadas. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer autorización |
> | Microsoft.Compute/availabilitySets/* | Crear y administrar conjuntos de disponibilidad de proceso |
> | Microsoft.Compute/locations/* | Crear y administrar ubicaciones de proceso |
> | Microsoft.Compute/virtualMachines/* | Crear y administrar máquinas virtuales |
> | Microsoft.Compute/virtualMachineScaleSets/* | Crear y administrar conjuntos de escalado de máquinas virtuales |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Se une a un grupo de direcciones de back-end de Application Gateway |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Se une a un grupo de direcciones de back-end del equilibrador de carga |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Se une a conjuntos NAT de entrada del equilibrador de carga |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Se une a una regla NAT de entrada del equilibrador de carga |
> | Microsoft.Network/loadBalancers/probes/join/action | Permite usar sondeos de un equilibrador de carga. Por ejemplo, con este permiso, la propiedad healthProbe de un conjunto de escalado de máquinas virtuales puede hacer referencia al sondeo. |
> | Microsoft.Network/loadBalancers/read | Obtiene una definición del equilibrador de carga |
> | Microsoft.Network/locations/* | Crear y administrar ubicaciones de red |
> | Microsoft.Network/networkInterfaces/* | Crear y administrar interfaces de red |
> | Microsoft.Network/networkSecurityGroups/join/action | Se une a un grupo de seguridad de red |
> | Microsoft.Network/networkSecurityGroups/read | Obtiene una definición de grupo de seguridad de red |
> | Microsoft.Network/publicIPAddresses/join/action | Se une a una dirección IP pública |
> | Microsoft.Network/publicIPAddresses/read | Obtiene una definición de la dirección ip pública. |
> | Microsoft.Network/virtualNetworks/read | Obtiene la definición de red virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Se une a una red virtual |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devuelve detalles de objeto del elemento protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Crea un elemento protegido de copia de seguridad |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Crea una intención de protección de la copia de seguridad. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Devuelve todas las directivas de protección |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Crea una directiva de protección |
> | Microsoft.RecoveryServices/Vaults/read | La operación Get Vault obtiene un objeto que representa el recurso de Azure del tipo "almacén" |
> | Microsoft.RecoveryServices/Vaults/usages/read | Devuelve los detalles de uso de un almacén de Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/write | La operación Create Vault crea un recurso de Azure del tipo "almacén" |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Devuelve las claves de acceso de la cuenta de almacenamiento especificada. |
> | Microsoft.Storage/storageAccounts/read | Devuelve la lista de cuentas de almacenamiento u obtiene las propiedades de la cuenta de almacenamiento especificada. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

## <a name="virtual-machine-user-login"></a>Inicio de sesión de usuario de Virtual Machine
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Los usuarios con este rol tienen la posibilidad de iniciar sesión en una máquina virtual como un usuario normal. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Acciones** |  |
> | Microsoft.Network/publicIPAddresses/read | Obtiene una definición de la dirección ip pública. |
> | Microsoft.Network/virtualNetworks/read | Obtiene la definición de red virtual |
> | Microsoft.Network/loadBalancers/read | Obtiene una definición del equilibrador de carga |
> | Microsoft.Network/networkInterfaces/read | Obtiene una definición de interfaz de red.  |
> | Microsoft.Compute/virtualMachines/*/read |  |

## <a name="web-plan-contributor"></a>Colaborador de plan web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar los planes web para sitios web, pero no acceder a ellos. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer autorización |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | Microsoft.Web/serverFarms/* | Crear y administrar granjas de servidores |

## <a name="website-contributor"></a>Colaborador de sitio web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descripción** | Permite administrar los sitios web (no planes web), pero no acceder a ellos. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Acciones** |  |
> | Microsoft.Authorization/*/read | Leer autorización |
> | Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
> | Microsoft.Insights/components/* | Crear y administrar componentes de Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtiene los estados de disponibilidad de todos los recursos en el ámbito especificado |
> | Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtiene o enumera los grupos de recursos. |
> | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
> | Microsoft.Web/certificates/* | Crear y administrar certificados de sitios web |
> | Microsoft.Web/listSitesAssignedToHostName/read | Obtiene los nombres de sitios asignados al nombre de host. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Obtiene las propiedades de un plan de App Service |
> | Microsoft.Web/sites/* | Crear y administrar sitios web (la creación de sitios también requiere permisos de escritura para el plan de App Service asociado) |

## <a name="next-steps"></a>Pasos siguientes

- [Roles personalizados](custom-roles.md)
- [Administración de las asignaciones de roles mediante Azure Portal](role-assignments-portal.md)
- [Permisos en Azure Security Center](../security-center/security-center-permissions.md)
