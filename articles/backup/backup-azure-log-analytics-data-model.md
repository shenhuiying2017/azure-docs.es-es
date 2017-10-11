---
title: Modelo de datos de Log Analytics para Azure Backup
description: "En este artículo se explican los detalles del modelo de datos de Log Analytics para los datos de Azure Backup."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: dfd5c73d-0d34-4d48-959e-1936986f9fc0
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 041a8835a1dd185739b23d4073fd5811bb4490b5
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2017
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modelo de datos de Log Analytics para datos de Azure Backup
En este artículo se describe el modelo de datos utilizado para los datos de informes de inserción para Log Analytics. Con este modelo de datos, puede crear consultas personalizadas y paneles y utilizarlos en OMS. 

## <a name="using-azure-backup-data-model"></a>Uso del modelo de datos de Azure Backup
Puede usar los siguientes campos que se proporcionan como parte del modelo de datos para crear objetos visuales, consultas personalizadas y paneles, según sus requisitos.

### <a name="alert"></a>Alerta
Esta tabla proporciona detalles acerca de los campos relacionados con la alerta.

| Campo | Tipo de datos | Descripción |
| --- | --- | --- |
| AlertUniqueId_s |Texto |Identificador único de la alerta generada |
| AlertType_s |Texto |Tipo de la alerta generada por ejemplo, Copia de seguridad |
| AlertStatus_s |Texto |Estado de la alerta, por ejemplo, Activa |
| AlertOccurenceDateTime_s |Fecha y hora |Fecha y hora en que se creó la alerta |
| AlertSeverity_s |Texto |Gravedad de la alerta, por ejemplo, Crítica |
| EventName_s |Texto |Este campo representa el nombre de este evento; es siempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Identificador único del elemento de copia de seguridad a la que pertenece esta alerta |
| SchemaVersion_s |Texto |Este campo indica la versión actual del esquema; es **V1** |
| State_s |Texto |Estado actual del objeto de la alerta por ejemplo, Active o Deleted |
| BackupManagementType_s |Texto |Tipo de proveedor para realizar la copia de seguridad, por ejemplo, IaaSVM o FileFolder al que pertenece esta alerta |
| nombreOperación |Texto |Este campo representa el nombre de la operación actual: Alert |
| Categoría |Texto |Este campo representa la categoría de datos de diagnóstico insertada en Log Analytics; es AzureBackupReport |
| Recurso |Texto |Este es el recurso para el que se están recopilando datos; muestra el nombre del almacén de Recovery Services |
| ProtectedServerUniqueId_s |Texto |Id. exclusivo del elemento protegido al que pertenece esta alerta |
| VaultUniqueId_s |Texto |Id. exclusivo del elemento protegido al que pertenece esta alerta |
| SourceSystem |Texto |Sistema de origen de los datos actuales: Azure |
| ResourceId |Texto |Este campo representa el id. de recurso para el que se están recopilando datos; muestra el id. de recurso del almacén de Recovery Services |
| SubscriptionId |Texto |Este campo representa el id. de suscripción del recurso (almacén RS) para el que se están recopilando datos |
| ResourceGroup |Texto |Este campo representa el grupo de recursos del recurso (almacén RS) para el que se están recopilando datos |
| ResourceProvider |Texto |Este campo representa el proveedor de recursos para el que se están recopilando datos: Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa el tipo del recurso para el que se están recopilando datos: almacenes |

### <a name="backupitem"></a>BackupItem
Esta tabla proporciona detalles acerca de los campos relacionados con el elemento de copia de seguridad.

| Campo | Tipo de datos | Descripción |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa el nombre de este evento; es siempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |Identificador exclusivo del elemento de copia de seguridad |
| BackupItemId_s |Texto |Identificador de elemento de copia de seguridad |
| BackupItemName_s |Texto |Nombre de elemento de copia de seguridad |
| BackupItemFriendlyName_s |Texto |Nombre descriptivo del elemento de copia de seguridad |
| BackupItemType_s |Texto |Tipo de elemento de copia de seguridad, por ejemplo, VM o FileFolder |
| ProtectedServerName_s |Texto |Nombre del servidor protegido al que pertenece el elemento de copia de seguridad |
| ProtectionState_s |Texto |Estado actual de protección del elemento copia de seguridad, por ejemplo, Protected o ProtectionStopped |
| SchemaVersion_s |Texto |Este campo indica la versión actual del esquema; es **V1** |
| State_s |Texto |Estado actual del objeto del elemento de copia de seguridad, por ejemplo, Active o Deleted |
| BackupManagementType_s |Texto |Tipo de proveedor para realizar la copia de seguridad, por ejemplo, IaaSVM o FileFolder al que pertenece este elemento de copia de seguridad |
| nombreOperación |Texto |Este campo representa el nombre de la operación actual: BackupItem |
| Categoría |Texto |Este campo representa la categoría de datos de diagnóstico insertada en Log Analytics; es AzureBackupReport |
| Recurso |Texto |Este es el recurso para el que se están recopilando datos; muestra el nombre del almacén de Recovery Services |
| SourceSystem |Texto |Sistema de origen de los datos actuales: Azure |
| ResourceId |Texto |Este campo representa el id. de recurso para el que se están recopilando datos; muestra el id. de recurso del almacén de Recovery Services |
| SubscriptionId |Texto |Este campo representa el id. de suscripción del recurso (almacén RS) para el que se están recopilando datos |
| ResourceGroup |Texto |Este campo representa el grupo de recursos del recurso (almacén RS) para el que se están recopilando datos |
| ResourceProvider |Texto |Este campo representa el proveedor de recursos para el que se están recopilando datos: Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa el tipo del recurso para el que se están recopilando datos: almacenes |

### <a name="backupitemassociation"></a>BackupItemAssociation
Esta tabla proporciona detalles acerca de las asociaciones de elementos de copia de seguridad con varias entidades.

| Campo | Tipo de datos | Descripción |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa el nombre de este evento; es siempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |Identificador exclusivo del elemento de copia de seguridad |
| SchemaVersion_s |Texto |Este campo indica la versión actual del esquema; es **V1** |
| State_s |Texto |Estado actual del objeto de la asociación del elemento de copia de seguridad, por ejemplo, Active o Deleted |
| BackupManagementType_s |Texto |Tipo de proveedor para realizar la copia de seguridad, por ejemplo, IaaSVM o FileFolder al que pertenece este elemento de copia de seguridad |
| nombreOperación |Texto |Este campo representa el nombre de la operación actual: BackupItemAssociation |
| Categoría |Texto |Este campo representa la categoría de datos de diagnóstico insertada en Log Analytics; es AzureBackupReport |
| Recurso |Texto |Este es el recurso para el que se están recopilando datos; muestra el nombre del almacén de Recovery Services |
| PolicyUniqueId_g |Texto |Identificador único para identificar la directiva a la que se asocia el elemento de copia de seguridad |
| ProtectedServerUniqueId_s |Texto |Identificador exclusivo del servidor protegido al que pertenece el elemento de copia de seguridad |
| VaultUniqueId_s |Texto |Identificador exclusivo del almacén al que pertenece el elemento de copia de seguridad |
| SourceSystem |Texto |Sistema de origen de los datos actuales: Azure |
| ResourceId |Texto |Este campo representa el id. de recurso para el que se están recopilando datos; muestra el id. de recurso del almacén de Recovery Services |
| SubscriptionId |Texto |Este campo representa el id. de suscripción del recurso (almacén RS) para el que se están recopilando datos |
| ResourceGroup |Texto |Este campo representa el grupo de recursos del recurso (almacén RS) para el que se están recopilando datos |
| ResourceProvider |Texto |Este campo representa el proveedor de recursos para el que se están recopilando datos: Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa el tipo del recurso para el que se están recopilando datos: almacenes |

### <a name="job"></a>Trabajo
Esta tabla proporciona detalles acerca de los campos relacionados con los trabajos.

| Campo | Tipo de datos | Descripción |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa el nombre de este evento; es siempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Identificador único del elemento de copia de seguridad a la que pertenece este trabajo |
| SchemaVersion_s |Texto |Este campo indica la versión actual del esquema; es **V1** |
| State_s |Texto |Estado actual del objeto del trabajo, por ejemplo, Active o Deleted |
| BackupManagementType_s |Texto |Tipo de proveedor para realizar la copia de seguridad, por ejemplo, IaaSVM o FileFolder al que pertenece este trabajo |
| nombreOperación |Texto |Este campo representa el nombre de la operación actual: Job |
| Categoría |Texto |Este campo representa la categoría de datos de diagnóstico insertada en Log Analytics; es AzureBackupReport |
| Recurso |Texto |Este es el recurso para el que se están recopilando datos; muestra el nombre del almacén de Recovery Services |
| ProtectedServerUniqueId_s |Texto |Identificador único del elemento protegido al que pertenece este trabajo |
| VaultUniqueId_s |Texto |Identificador único del elemento protegido al que pertenece este trabajo |
| JobOperation_s |Texto |Operación para la que se ejecuta el trabajo, por ejemplo, Backup, Restore o Configure Backup |
| JobStatus_s |Texto |Estado del trabajo terminado, por ejemplo, Completed o Failed |
| JobFailureCode_s |Texto |Cadena del código de error por el que produjo el error del trabajo |
| JobStartDateTime_s |Fecha y hora |Fecha y hora en las que comenzó la ejecución del trabajo |
| BackupStorageDestination_s |Texto |Destino de almacenamiento de almacenamiento de copias de seguridad, por ejemplo, Cloud o Disk  |
| JobDurationInSecs_s | Number |Duración del trabajo total en segundos |
| DataTransferredInMB_s | Number |Datos transferidos en MB para este trabajo|
| JobUniqueId_g |Texto |Identificador único que identifica el trabajo |
| SourceSystem |Texto |Sistema de origen de los datos actuales: Azure |
| ResourceId |Texto |Este campo representa el id. de recurso para el que se están recopilando datos; muestra el id. de recurso del almacén de Recovery Services |
| SubscriptionId |Texto |Este campo representa el id. de suscripción del recurso (almacén RS) para el que se están recopilando datos |
| ResourceGroup |Texto |Este campo representa el grupo de recursos del recurso (almacén RS) para el que se están recopilando datos |
| ResourceProvider |Texto |Este campo representa el proveedor de recursos para el que se están recopilando datos: Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa el tipo del recurso para el que se están recopilando datos: almacenes |

### <a name="policy"></a>Directiva
Esta tabla proporciona detalles acerca de los campos relacionados con las directivas.

| Campo | Tipo de datos | Descripción |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa el nombre de este evento; es siempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica la versión actual del esquema; es **V1** |
| State_s |Texto |Estado actual del objeto de la directiva, por ejemplo, Active o Deleted |
| BackupManagementType_s |Texto |Tipo de proveedor para realizar la copia de seguridad, por ejemplo, IaaSVM o FileFolder al que pertenece esta directiva |
| nombreOperación |Texto |Este campo representa el nombre de la operación actual: Policy |
| Categoría |Texto |Este campo representa la categoría de datos de diagnóstico insertada en Log Analytics; es AzureBackupReport |
| Recurso |Texto |Este es el recurso para el que se están recopilando datos; muestra el nombre del almacén de Recovery Services |
| PolicyUniqueId_g |Texto |Identificador único que identifica la directiva |
| PolicyName_s |Texto |Nombre de la directiva definida |
| BackupFrequency_s |Texto |Frecuencia con la que se ejecutan las copias de seguridad, por ejemplo, a diario o semanalmente |
| BackupTimes_s |Texto |Fecha y hora en que se programan las copias de seguridad |
| BackupDaysOfTheWeek_s |Texto |Días de la semana en los que se han programado copias de seguridad |
| RetentionDuration_s |Número entero |Duración de retención de las copias de seguridad configuradas |
| DailyRetentionDuration_s |Número entero |Duración de retención total de las copias de seguridad configuradas, en días |
| DailyRetentionTimes_s |Texto |Fecha y hora en que se configuró la retención diaria |
| WeeklyRetentionDuration_s |Número decimal |Duración total de la retención semanal de las copias de seguridad configuradas, en semanas |
| WeeklyRetentionTimes_s |Texto |Fecha y hora en que se ha configurado la retención semanal |
| WeeklyRetentionDaysOfTheWeek_s |Texto |Días de la semana seleccionados para la retención semanal |
| MonthlyRetentionDuration_s |Número decimal |Duración de retención total de las copias de seguridad configuradas, en meses |
| MonthlyRetentionTimes_s |Texto |Fecha y hora en que se ha configurado la retención mensual |
| MonthlyRetentionFormat_s |Texto |Tipo de configuración para la retención mensual ,por ejemplo, diariamente si se basa en día, semanalmente si se basa en semana |
| MonthlyRetentionDaysOfTheWeek_s |Texto |Días de la semana seleccionados para la retención mensual |
| MonthlyRetentionWeeksOfTheMonth_s |Texto |Semanas del mes en que se ha configurado la retención mensual, por ejemplo, la primera, la última, etc. |
| YearlyRetentionDuration_s |Número decimal |Duración total de la retención de las copias de seguridad configuradas, en años |
| YearlyRetentionTimes_s |Texto |Fecha y hora en que se ha configurado la retención anual |
| YearlyRetentionMonthsOfTheYear_s |Texto |Meses del año seleccionados para la retención anual |
| YearlyRetentionFormat_s |Texto |Tipo de configuración para la retención anual ,por ejemplo, diariamente si se basa en día, semanalmente si se basa en semana |
| YearlyRetentionDaysOfTheMonth_s |Texto |Fechas del mes seleccionadas para la retención anual |
| SourceSystem |Texto |Sistema de origen de los datos actuales: Azure |
| ResourceId |Texto |Este campo representa el id. de recurso para el que se están recopilando datos; muestra el id. de recurso del almacén de Recovery Services |
| SubscriptionId |Texto |Este campo representa el id. de suscripción del recurso (almacén RS) para el que se están recopilando datos |
| ResourceGroup |Texto |Este campo representa el grupo de recursos del recurso (almacén RS) para el que se están recopilando datos |
| ResourceProvider |Texto |Este campo representa el proveedor de recursos para el que se están recopilando datos: Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa el tipo del recurso para el que se están recopilando datos: almacenes |

### <a name="policyassociation"></a>PolicyAssociation
Esta tabla proporciona detalles acerca de las asociaciones de directivas con varias entidades.

| Campo | Tipo de datos | Descripción |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa el nombre de este evento; es siempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica la versión actual del esquema; es **V1** |
| State_s |Texto |Estado actual del objeto de la directiva, por ejemplo, Active o Deleted |
| BackupManagementType_s |Texto |Tipo de proveedor para realizar la copia de seguridad, por ejemplo, IaaSVM o FileFolder al que pertenece esta directiva |
| nombreOperación |Texto |Este campo representa el nombre de la operación actual: PolicyAssociation |
| Categoría |Texto |Este campo representa la categoría de datos de diagnóstico insertada en Log Analytics; es AzureBackupReport |
| Recurso |Texto |Este es el recurso para el que se están recopilando datos; muestra el nombre del almacén de Recovery Services |
| PolicyUniqueId_g |Texto |Identificador único que identifica la directiva |
| VaultUniqueId_s |Texto |Identificador exclusivo del almacén al que pertenece esta directiva |
| SourceSystem |Texto |Sistema de origen de los datos actuales: Azure |
| ResourceId |Texto |Este campo representa el id. de recurso para el que se están recopilando datos; muestra el id. de recurso del almacén de Recovery Services |
| SubscriptionId |Texto |Este campo representa el id. de suscripción del recurso (almacén RS) para el que se están recopilando datos |
| ResourceGroup |Texto |Este campo representa el grupo de recursos del recurso (almacén RS) para el que se están recopilando datos |
| ResourceProvider |Texto |Este campo representa el proveedor de recursos para el que se están recopilando datos: Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa el tipo del recurso para el que se están recopilando datos: almacenes |

### <a name="protectedserver"></a>ProtectedServer
Esta tabla proporciona detalles acerca de los campos relacionados con el servidor protegido.

| Campo | Tipo de datos | Descripción |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa el nombre de este evento; es siempre AzureBackupCentralReport |
| ProtectedServerName_s |Texto |Nombre del servidor protegido |
| SchemaVersion_s |Texto |Este campo indica la versión actual del esquema; es **V1** |
| State_s |Texto |Estado actual del objeto de servidor protegido, por ejemplo, Active o Deleted |
| BackupManagementType_s |Texto |Tipo de proveedor para realizar la copia de seguridad, por ejemplo, IaaSVM o FileFolder al que pertenece este servidor protegido |
| nombreOperación |Texto |Este campo representa el nombre de la operación actual: ProtectedServer |
| Categoría |Texto |Este campo representa la categoría de datos de diagnóstico insertada en Log Analytics; es AzureBackupReport |
| Recurso |Texto |Este es el recurso para el que se están recopilando datos; muestra el nombre del almacén de Recovery Services |
| ProtectedServerUniqueId_s |Texto |Identificador único del servidor protegido |
| RegisteredContainerId_s |Texto |Identificador de contenedor registrado para copia de seguridad |
| ProtectedServerType_s |Texto |Tipo de servidor protegido del que se realiza la copia de seguridad, por ejemplo, Windows |
| ProtectedServerFriendlyName_s |Texto |Nombre descriptivo del servidor protegido |
| AzureBackupAgentVersion_s |Texto |Número de versión de Azure Backup Agent |
| SourceSystem |Texto |Sistema de origen de los datos actuales: Azure |
| ResourceId |Texto |Este campo representa el id. de recurso para el que se están recopilando datos; muestra el id. de recurso del almacén de Recovery Services |
| SubscriptionId |Texto |Este campo representa el id. de suscripción del recurso (almacén RS) para el que se están recopilando datos |
| ResourceGroup |Texto |Este campo representa el grupo de recursos del recurso (almacén RS) para el que se están recopilando datos |
| ResourceProvider |Texto |Este campo representa el proveedor de recursos para el que se están recopilando datos: Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa el tipo del recurso para el que se están recopilando datos: almacenes |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
Esta tabla proporciona detalles acerca de las asociaciones de servidores protegidos con otras entidades.

| Campo | Tipo de datos | Descripción |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa el nombre de este evento; es siempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica la versión actual del esquema; es **V1** |
| State_s |Texto |Estado actual del objeto de asociación de servidores, por ejemplo, Active o Deleted |
| BackupManagementType_s |Texto |Tipo de proveedor para realizar la copia de seguridad, por ejemplo, IaaSVM o FileFolder al que pertenece este servidor protegido |
| nombreOperación |Texto |Este campo representa el nombre de la operación actual: ProtectedServerAssociation |
| Categoría |Texto |Este campo representa la categoría de datos de diagnóstico insertada en Log Analytics; es AzureBackupReport |
| Recurso |Texto |Este es el recurso para el que se están recopilando datos; muestra el nombre del almacén de Recovery Services |
| ProtectedServerUniqueId_s |Texto |Identificador único del servidor protegido |
| VaultUniqueId_s |Texto |Identificador exclusivo del almacén al que pertenece este servidor protegido |
| SourceSystem |Texto |Sistema de origen de los datos actuales: Azure |
| ResourceId |Texto |Este campo representa el id. de recurso para el que se están recopilando datos; muestra el id. de recurso del almacén de Recovery Services |
| SubscriptionId |Texto |Este campo representa el id. de suscripción del recurso (almacén RS) para el que se están recopilando datos |
| ResourceGroup |Texto |Este campo representa el grupo de recursos del recurso (almacén RS) para el que se están recopilando datos |
| ResourceProvider |Texto |Este campo representa el proveedor de recursos para el que se están recopilando datos: Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa el tipo del recurso para el que se están recopilando datos: almacenes |

### <a name="storage"></a>Almacenamiento
Esta tabla proporciona detalles acerca de los campos relacionados con el almacenamiento.

| Campo | Tipo de datos | Descripción |
| --- | --- | --- |
| CloudStorageInBytes_s |Número decimal |Almacenamiento de copia de seguridad en la nube utilizado por las copias de seguridad, calculados basándose en el último valor |
| ProtectedInstances_s |Número decimal |Número de instancias protegidas que se utilizan para calcular el almacenamiento de front-end en la facturación; se calcula en función del valor más reciente |
| EventName_s |Texto |Este campo representa el nombre de este evento; es siempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica la versión actual del esquema; es **V1** |
| State_s |Texto |Estado actual del objeto de almacenamiento, por ejemplo, Active o Deleted |
| BackupManagementType_s |Texto |Tipo de proveedor para realizar la copia de seguridad, por ejemplo, IaaSVM o FileFolder al que pertenece este almacenamiento |
| nombreOperación |Texto |Este campo representa el nombre de la operación actual: Storage |
| Categoría |Texto |Este campo representa la categoría de datos de diagnóstico insertada en Log Analytics; es AzureBackupReport |
| Recurso |Texto |Este es el recurso para el que se están recopilando datos; muestra el nombre del almacén de Recovery Services |
| ProtectedServerUniqueId_s |Texto |Id. exclusivo del servidor protegido para el que se calcula el almacenamiento |
| VaultUniqueId_s |Texto |Se calcula el identificador único del almacén para el almacenamiento |
| SourceSystem |Texto |Sistema de origen de los datos actuales: Azure |
| ResourceId |Texto |Este campo representa el id. de recurso para el que se están recopilando datos; muestra el id. de recurso del almacén de Recovery Services |
| SubscriptionId |Texto |Este campo representa el id. de suscripción del recurso (almacén RS) para el que se están recopilando datos |
| ResourceGroup |Texto |Este campo representa el grupo de recursos del recurso (almacén RS) para el que se están recopilando datos |
| ResourceProvider |Texto |Este campo representa el proveedor de recursos para el que se están recopilando datos: Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa el tipo del recurso para el que se están recopilando datos: almacenes |

### <a name="vault"></a>Almacén
Esta tabla proporciona detalles acerca de los campos relacionados con el almacén.

| Campo | Tipo de datos | Descripción |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa el nombre de este evento; es siempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica la versión actual del esquema; es **V1** |
| State_s |Texto |Estado actual del objeto del almacén por ejemplo, Active o Deleted |
| nombreOperación |Texto |Este campo representa el nombre de la operación actual: Vault |
| Categoría |Texto |Este campo representa la categoría de datos de diagnóstico insertada en Log Analytics; es AzureBackupReport |
| Recurso |Texto |Este es el recurso para el que se están recopilando datos; muestra el nombre del almacén de Recovery Services |
| VaultUniqueId_s |Texto |Id. exclusivo del almacén |
| VaultName_s |Texto |Nombre del almacén |
| AzureDataCenter_s |Texto |Centro de datos donde se encuentra el almacén |
| StorageReplicationType_s |Texto |Tipo de replicación de almacenamiento para el almacén, por ejemplo, GeoRedundant |
| SourceSystem |Texto |Sistema de origen de los datos actuales: Azure |
| ResourceId |Texto |Este campo representa el id. de recurso para el que se están recopilando datos; muestra el id. de recurso del almacén de Recovery Services |
| SubscriptionId |Texto |Este campo representa el id. de suscripción del recurso (almacén RS) para el que se están recopilando datos |
| ResourceGroup |Texto |Este campo representa el grupo de recursos del recurso (almacén RS) para el que se están recopilando datos |
| ResourceProvider |Texto |Este campo representa el proveedor de recursos para el que se están recopilando datos: Microsoft.RecoveryServices |
| ResourceType |Texto |Este campo representa el tipo del recurso para el que se están recopilando datos: almacenes |

## <a name="next-steps"></a>Pasos siguientes
Cuando revise el modelo de datos para crear informes de Azure Backup, puede iniciar la [creación de un panel](../log-analytics/log-analytics-dashboards.md) en Log Analytics y OMS.
