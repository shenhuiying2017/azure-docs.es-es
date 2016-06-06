<properties
	pageTitle="RBAC: roles integrados | Microsoft Azure"
	description="Este tema describe los roles integrados para el control de acceso basado en rol"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="05/20/2016"
	ms.author="kgremban"/>

#RBAC: Roles integrados

El control de acceso basado en roles de Azure (RBAC) dispone de los siguientes roles integrados que se pueden asignar a usuarios, grupos y servicios. Las definiciones de los roles integrados no se puede modificar. Sin embargo, puede crear [roles personalizados en RBAC de Azure](role-based-access-control-custom-roles.md) para satisfacer las necesidades específicas de su organización.

## Roles de Azure

La tabla siguiente proporciona breves descripciones de los roles integrados. Haga clic en el nombre de rol para ver una lista detallada de las propiedades **actions** y **notactions** del rol. La propiedad **acciones** especifica las acciones permitidas en los recursos de Azure. Las cadenas de acciones pueden utilizar caracteres comodín. La propiedad **notactions** especifica las acciones que se excluyen de las acciones permitidas.

>[AZURE.NOTE] Las definiciones de rol de Azure están en constante evolución. Este artículo se mantiene tan actualizado como sea posible, pero las últimas definiciones de roles puede encontrarlas en Azure PowerShell. Use los cmdlets de `(get-azurermroledefinition "<role name>").actions` o `(get-azurermroledefinition "<role name>").notactions` como corresponda.

| Nombre de rol | Descripción |
| --------- | ----------- |
| [Colaborador de servicio de administración de API](#api-management-service-contributor) | Puede administrar servicios de administración de API |
| [Colaborador de componentes de Application Insights](#application-insights-component-contributor) | Puede administrar los componentes de Application Insights |
| [Operador de automatización](#automation-operator) | Puede iniciar, detener, suspender y reanudar trabajos |
| [Colaborador de BizTalk](#biztalk-contributor) | Puede administrar los servicios de BizTalk |
| [Colaborador de ClearDB MySQL DB](#cleardb-mysql-db-contributor) | Puede administrar bases de datos ClearDB MySQL |
| [Colaborador](#contributor) | Puede administrar todo el contenido, excepto el acceso |
| [Colaborador de Factoría de datos](#data-factory-contributor) | Puede administrar las factorías de datos |
| [Usuario de DevTest Labs](#devtest-labs-user) | Puede ver todo el contenido así como conectar, iniciar, reiniciar y apagar las máquinas virtuales |
| [Colaborador de cuenta de DocumentDB](#documentdb-account-contributor) | Puede administrar cuentas de DocumentDB |
| [Colaborador de la cuenta de Sistemas inteligentes](#intelligent-systems-account-contributor) | Puede administrar cuentas de Sistemas inteligentes |
| [Colaborador de la red](#network-contributor) | Puede administrar todos los recursos de red |
| [Colaborador de la cuenta de NewRelic APM](#new-relic-apm-account-contributor) | Puede administrar aplicaciones y cuentas de New Relic Application Performance Management |
| [Propietario](#owner) | Puede administrar todo el contenido, incluido el acceso |
| [Lector](#reader) | Puede ver todo el contenido, pero no puede realizar cambios |
| [Colaborador de la memoria caché de Redis](#redis-cache-contributor]) | Puede administrar memorias caché en Redis |
| [Colaborador de colecciones de trabajos de Scheduler](#scheduler-job-collections-contributor) | Puede administrar las colecciones de trabajo de Programador |
| [Colaborador del servicio de búsqueda](#search-service-contributor) | Puede administrar los servicios de búsqueda |
| [Administrador de seguridad](#security-manager) | Puede administrar los componentes y las directivas de seguridad, además de las máquinas virtuales |
| [Colaborador de Base de datos de SQL](#sql-db-contributor) | Puede administrar bases de datos SQL, pero no las directivas relacionadas con la seguridad |
| [Administrador de seguridad SQL](#sql-security-manager) | Puede administrar las directivas relacionadas con la seguridad de las bases de datos y los servidores SQL |
| [Colaborador de SQL Server](#sql-server-contributor) | Puede administrar las bases de datos y los servidores SQL, pero no las directivas relacionadas con la seguridad |
| [Colaborador de cuentas de almacenamiento clásico](#classic-storage-account-contributor) | Puede administrar las cuentas de almacenamiento clásico |
| [Colaborador de la cuenta de almacenamiento](#storage-account-contributor) | Puede administrar las cuentas de almacenamiento |
| [Administrador de acceso de usuario](#user-access-administrator) | Puede administrar el acceso de usuarios a los recursos de Azure |
| [Colaborador de la máquina virtual clásica](#classic-virtual-machine-contributor) | Puede administrar máquinas virtuales clásicas, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas |
| [Colaborador de la máquina virtual](#virtual-machine-contributor) | Puede administrar máquinas virtuales, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas |
| [Colaborador de la red clásica](#classic-network-contributor) | Puede administrar IP reservadas y redes virtuales clásicas |
| [Colaborador de plan web](#web-plan-contributor) | Puede administrar planes web |
| [Colaborador de sitio web](#website-contributor) | Puede administrar sitios web, pero no los planes web a los que están conectados |

## Permisos de los roles
Las tablas siguientes describen los permisos específicos concedidos a cada rol. En ellas, se incluyen elementos correspondientes a las categorías **Actions** (para conceder permisos) y **NotActions** (para restringir dichos permisos).

### Colaborador de servicio de administración de API
Puede administrar servicios de administración de API

| **Acciones** | |
| ------- | ------ |
| Microsoft.ApiManagement/Service/* | Crear y administrar los servicios de administración de API |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer roles y asignaciones de roles |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de componente de Application Insights
Puede administrar los componentes de Application Insights

| **Acciones** | |
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Insights/components/* | Crear y administrar componentes de Insights |
| Microsoft.Insights/webtests/* | Crear y administrar pruebas web |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Operador de automatización
Puede iniciar, detener, suspender y reanudar trabajos

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Automation/automationAccounts/jobs/read | Leer trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Reanudar un trabajo de cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Detener un trabajo de cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Leer transmisiones de trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspender un trabajo de cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobs/write | Escribir trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Leer un programa de trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Leer un programa de trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/read | Leer cuentas de automatización |
| Microsoft.Automation/automationAccounts/runbooks/read | Leer runbooks de automatización |
| Microsoft.Automation/automationAccounts/schedules/read | Leer esquemas de cuentas de automatización |
| Microsoft.Automation/automationAccounts/schedules/write | Escribir esquemas de cuentas de automatización |
| Microsoft.Insights/components/* | Crear y administrar componentes de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de BizTalk
Puede administrar los servicios de BizTalk

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.BizTalkServices/BizTalk/* | Crear y administrar los servicios de BizTalk |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de ClearDB MySQL DB
Puede administrar bases de datos ClearDB MySQL

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| successbricks.cleardb/databases/* | Crear y administrar bases de datos ClearDB MySQL |

### Colaborador
Puede administrar todo el contenido, excepto el acceso

| **Acciones** ||
| ------- | ------ |
| * | Crear y administrar recursos de todos los tipos |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Authorization/*/Write | No puede crear roles ni asignaciones de roles |
| Microsoft.Authorization/*/Delete | No puede eliminar roles ni asignaciones de roles |

### Colaborador de factoría de datos
Puede administrar las factorías de datos

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.DataFactory/dataFactories/* | Administración de factorías de datos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Usuario de DevTest Labs
Puede ver todo el contenido así como conectar, iniciar, reiniciar y apagar las máquinas virtuales

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Compute/availabilitySets/read | Leer las propiedades de los conjuntos de disponibilidad |
| Microsoft.Compute/virtualMachines/*/read | Leer las propiedades de una máquina virtual (tamaños de máquinas virtuales, el estado de tiempo de ejecución, extensiones de máquina virtual, etc.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Desasignar máquinas virtuales |
| Microsoft.Compute/virtualMachines/read | Leer las propiedades de una máquina virtual |
| Microsoft.Compute/virtualMachines/restart/action | Reiniciar máquinas virtuales |
| Microsoft.Compute/virtualMachines/start/action | Iniciar máquinas virtuales |
| Microsoft.DevTestLab/*/read | Leer las propiedades de un laboratorio |
| Microsoft.DevTestLab/labs/createEnvironment/action | Crear un entorno de laboratorio |
| Microsoft.DevTestLab/labs/formulas/delete | Eliminar fórmulas |
| Microsoft.DevTestLab/labs/formulas/read | Leer fórmulas |
| Microsoft.DevTestLab/labs/formulas/write | Agregar o modificar fórmulas |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Evaluar directivas de laboratorio |
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

### Colaborador de cuenta de DocumentDB
Puede administrar cuentas de DocumentDB

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.DocumentDb/databaseAccounts/* | Crear y administrar cuentas de DocumentDB |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la cuenta de sistemas inteligentes
Puede administrar cuentas de Sistemas inteligentes

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.IntelligentSystems/accounts/* | Crear y administrar cuentas de sistemas inteligentes |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la red
Puede administrar todos los recursos de red

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Network/* | Crear y administrar redes |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la cuenta de NewRelic APM
Puede administrar aplicaciones y cuentas de New Relic Application Performance Management

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| NewRelic.APM/accounts/* | Crear y administrar cuentas de administración del rendimiento de la aplicación New Relic |

### Propietario
Puede administrar todo el contenido, incluido el acceso

| **Acciones** ||
| ------- | ------ |
| * | Crear y administrar recursos de todos los tipos |

### Lector
Puede ver todo el contenido, pero no puede realizar cambios

| **Acciones** ||
| ------- | ------ |
| **/read | Leer recursos de todos los tipos, excepto secretos. |

### Colaborador de la memoria caché de Redis
Puede administrar memorias caché en Redis

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Cache/redis/* | Crear y administrar memorias caché de Redis |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de colecciones de trabajos de Scheduler
Puede administrar las colecciones de trabajo de Programador

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos | Microsoft.Scheduler/jobcollections/* | Crear y administrar colecciones de trabajos |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador del servicio de búsqueda
Puede administrar los servicios de búsqueda

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos | Microsoft.Search/searchServices/* | Crear y administrar servicios de búsqueda |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Administrador de seguridad
Puede administrar los componentes y las directivas, además de las máquinas virtuales

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.ClassicCompute/*/read | Leer información de configuración para máquinas virtuales de proceso clásico |
| Microsoft.ClassicCompute/virtualMachines/*/write | Escribir configuración para máquinas virtuales |
| Microsoft.ClassicNetwork/*/read | Leer información de configuración acerca de la red clásica |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos | Microsoft.Security/* | Crear y administrar las directivas y los componentes de seguridad |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la base de datos de SQL
Puede administrar bases de datos SQL, pero no las directivas relacionadas con la seguridad

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos | Microsoft.Sql/servers/databases/* | Crear y administrar bases de datos SQL |
| Microsoft.Sql/servers/read | Leer los servidores SQL Server |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | No puede editar las directivas de auditoría |
| Microsoft.Sql/servers/databases/auditingSettings/* | No puede editar la configuración de auditoría |
| Microsoft.Sql/servers/databases/connectionPolicies/* | No puede editar las directivas de conexión |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | No puede editar las directivas de enmascaramiento |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | No puede editar las directivas de alerta de seguridad |
| Microsoft.Sql/servers/databases/securityMetrics/* | No puede editar las métricas de seguridad |

### Administrador de seguridad SQL
Puede administrar las directivas relacionadas con la seguridad de las bases de datos y los servidores SQL

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer autorización de Microsoft |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos | Microsoft.Sql/servers/auditingPolicies/* | Crear y administrar directivas de auditoría de SQL Server |
| Microsoft.Sql/servers/auditingSettings/* | Crear y administrar configuración de auditoría de SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Crear y administrar directivas de auditoría de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/auditingSettings/* | Crear y administrar configuración de auditoría de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Crear y administrar directivas de conexión de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Crear y administrar directivas de enmascaramiento de datos de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/read | Leer bases de datos SQL |
| Microsoft.Sql/servers/databases/schemas/read | Leer esquemas de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Leer columnas de tablas de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/read | Leer tablas de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Crear y administrar directivas de alerta de seguridad de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/securityMetrics/* | Crear y administrar métricas de seguridad de bases de datos de SQL Server |
| Microsoft.Sql/servers/read | Leer los servidores SQL Server |
| Microsoft.Sql/servers/securityAlertPolicies/* | Crear y administrar directivas de alerta de seguridad de SQL Server |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de SQL Server
Puede administrar las bases de datos y los servidores SQL, pero no las directivas relacionadas con la seguridad

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer autorización|
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos | Microsoft.Sql/servers/* | Crear y administrar servidores de SQL Server |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | No puede editar las directivas de auditoría de SQL Server |
| Microsoft.Sql/servers/auditingSettings/* | No puede editar la configuración de auditoría de SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | No puede editar las directivas de auditoría de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/auditingSettings/* | No puede editar la configuración de auditoría de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/connectionPolicies/* | No puede editar las directivas de conexión de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | No puede editar las directivas de enmascaramiento de datos de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | No puede editar las directivas de alerta de seguridad de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/securityMetrics/* | No puede editar las métricas de seguridad de bases de datos de SQL Server |
| Microsoft.Sql/servers/securityAlertPolicies/* | No puede editar las directivas de alerta de seguridad de SQL Server |

### Colaborador de cuentas de almacenamiento clásico
Puede administrar las cuentas de almacenamiento clásico

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.ClassicStorage/storageAccounts/* | Crear y administrar cuentas de almacenamiento |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la cuenta de almacenamiento
Permite administrar cuentas de almacenamiento, pero no el acceso a ellas.

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer toda la autorización |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Insights/diagnosticSettings/* | Administrar la configuración de diagnóstico |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos | Microsoft.Storage/storageAccounts/* | Crear y administrar cuentas de almacenamiento |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Administrador de acceso de usuario
Puede administrar el acceso de usuarios a los recursos de Azure

| **Acciones** ||
| ------- | ------ |
| */read | Leer recursos de todos los tipos, excepto secretos. | | Microsoft.Authorization/* | Leer autorización |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la máquina virtual clásica
Puede administrar máquinas virtuales clásicas, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas

| **Acciones** ||
| ------- | ------ |
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
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la máquina virtual
Puede administrar máquinas virtuales, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Compute/availabilitySets/* | Crear y administrar conjuntos de disponibilidad de proceso |
| Microsoft.Compute/locations/* | Crear y administrar ubicaciones de proceso |
| Microsoft.Compute/virtualMachines/* | Crear y administrar máquinas virtuales |
| Microsoft.Compute/virtualMachineScaleSets/* | Crear y administrar conjuntos de escalado de máquinas virtuales |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Unir grupos de direcciones de back-end de puerta de enlace de aplicaciones de red |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Unirse a los grupos de direcciones de back-end de equilibrador de carga |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Unirse a conjuntos NAT de entrada del equilibrador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Unirse a reglas NAT de entrada del equilibrador de carga |
| Microsoft.Network/loadBalancers/read | Leer equilibradores de carga |
| Microsoft.Network/locations/* | Crear y administrar ubicaciones de red |
| Microsoft.Network/networkInterfaces/* | Crear y administrar interfaces de red |
| Microsoft.Network/networkSecurityGroups/join/action | Unir los grupos de seguridad de red |
| Microsoft.Network/networkSecurityGroups/read | Leer grupos de seguridad de red |
| Microsoft.Network/publicIPAddresses/join/action | Unir las direcciones IP públicas de red |
| Microsoft.Network/publicIPAddresses/read | Leer direcciones IP públicas de red |
| Microsoft.Network/virtualNetworks/read | Leer redes virtuales |
| Microsoft.Network/virtualNetworks/subnets/join/action | Unirse a subredes de redes virtuales |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos | Microsoft.Storage/storageAccounts/listKeys/action | Enumerar claves de cuentas de almacenamiento |
| Microsoft.Storage/storageAccounts/read | Leer cuentas de almacenamiento |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la red clásica
Puede administrar IP reservadas y redes virtuales clásicas

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.ClassicNetwork/* | Crear y administrar redes clásicas |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de plan web
Puede administrar planes web

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| Microsoft.Web/serverFarms/* | Crear y administrar granjas de servidores |

### Colaborador de sitio web
Puede administrar sitios web, pero no los planes web a los que están conectados

| **Acciones** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Insights/components/* | Crear y administrar componentes de Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leer el mantenimiento de los recursos |
| Microsoft.Resources/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos | Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| Microsoft.Web/certificates/* | Crear y administrar certificados de sitios web |
| Microsoft.Web/listSitesAssignedToHostName/read | Leer sitios asignados a un nombre de host |
| Microsoft.Web/serverFarms/join/action | Unir granjas de servidores |
| Microsoft.Web/serverFarms/read | Leer las granjas de servidores |
| Microsoft.Web/sites/* | Crear y administrar sitios web |

## Consulte también
- [Control de acceso basado en roles](role-based-access-control-configure.md): introducción a RBAC en el Portal de Azure.
- [Roles personalizados en RBAC de Azure](role-based-access-control-custom-roles.md): información acerca de cómo crear roles personalizados que se ajusten a sus necesidades de acceso.
- [Creación de un informe del historial de cambios de acceso](role-based-access-control-access-change-history-report.md): seguimiento del cambio de asignaciones de roles en RBAC.
- [Solución de problemas del control de acceso basado en roles](role-based-access-control-troubleshooting.md): sugerencias para resolver problemas frecuentes.

<!---HONumber=AcomDC_0525_2016-->