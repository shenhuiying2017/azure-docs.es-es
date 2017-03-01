
---
title: "Cambio de la configuración de un clúster de Azure Service Fabric | Microsoft Docs"
description: "En este artículo se describe la configuración de Fabric y las directivas de actualización de Fabric que se pueden personalizar."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: 
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/15/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 1b2e22150f9cea004af4892cd7fa2fb2b59c8787
ms.openlocfilehash: 16e53dbdb4ce6de02a9c8acb2fb1d8a3ac265b8f
ms.lasthandoff: 02/16/2017


---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>Personalización de la configuración de un clúster de Service Fabric y una directiva de actualización de Fabric
En este documento se explica cómo personalizar las diversas opciones de configuración de Fabric y la directiva de actualización de Fabric para el clúster de Service Fabric. Puede personalizarlos en el portal o mediante una plantilla de Azure Resource Manager.

## <a name="fabric-settings-that-you-can-customize"></a>Configuración de Fabric que se puede personalizar
Esta es la configuración de Fabric que se puede personalizar:

### <a name="section-name-diagnostics"></a>Nombre de sección: Diagnostics
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| ConsumerInstances |String |La lista de instancias de consumidor de DCA. |
| ProducerInstances |string |La lista de instancias de productor de DCA. |
| AppEtwTraceDeletionAgeInDays |Int, el valor predeterminado es 3. |Número de días transcurridos los cuales se eliminarán los archivos ETL antiguos que contienen seguimientos de ETW de aplicación. |
| AppDiagnosticStoreAccessRequiresImpersonation |Bool, el valor predeterminado es true. |Si se requerirá o no suplantación para acceder a los almacenes de diagnóstico en nombre de la aplicación. |
| MaxDiskQuotaInMB |Int, el valor predeterminado es 65 536. |Cuota de disco en MB para archivos de registro de Windows Fabric. |
| DiskFullSafetySpaceInMB |Int, el valor predeterminado es 1024. |Espacio en disco restante en MB para proteger contra el uso por DCA. |
| ApplicationLogsFormatVersion |Int, el valor predeterminado es 0. |Versión del formato de registros de aplicación. Los valores admitidos son 0 y 1. La versión 1 incluye más campos del registro de eventos de ETW que la versión 0. |
| ClusterId |Cadena |El id. único del clúster. Se genera cuando se crea el clúster. |
| EnableTelemetry |Bool, el valor predeterminado es true. |Indica si se habilitará o deshabilitará la telemetría. |
| EnableCircularTraceSession |Bool, el valor predeterminado es false. |La marca indica si se deben usar sesiones de seguimiento circulares. |

### <a name="section-name-traceetw"></a>Nombre de sección: Trace/Etw
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| Nivel |Int, el valor predeterminado es 4. |El nivel de seguimiento de eventos puede adoptar los valores 1, 2, 3, 4. Para que sea admitido, debe mantener el nivel de seguimiento en 4. |

### <a name="section-name-performancecounterlocalstore"></a>Nombre de sección: PerformanceCounterLocalStore
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| IsEnabled |Bool, el valor predeterminado es true. |La marca indica si está habilitada la colección de contadores de rendimiento en el nodo local. |
| SamplingIntervalInSeconds |Int, el valor predeterminado es 60. |Intervalo de muestreo para los contadores de rendimiento que se recolectarán. |
| Counters |String |Lista separada por comas de los contadores de rendimiento que se recolectarán. |
| MaxCounterBinaryFileSizeInMB |Int, el valor predeterminado es 1. |Tamaño máximo (en MB) de cada archivo binario de contador de rendimiento. |
| NewCounterBinaryFileCreationIntervalInMinutes |Int, el valor predeterminado es 10. |Intervalo máximo (en segundos) después del cual se crea un nuevo archivo binario de contador de rendimiento. |

### <a name="section-name-setup"></a>Nombre de sección: Setup
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| FabricDataRoot |string |Directorio raíz de datos de Service Fabric. El valor predeterminado para Azure es d:\svcfab. |
| FabricLogRoot |string |Directorio raíz del registro de Service Fabric. Aquí es donde se colocan los seguimientos y registros de SF. |
| ServiceRunAsAccountName |string |El nombre de la cuenta con el que se ejecuta el servicio Fabric Host. |
| ServiceStartupType |Cadena |El tipo de inicio del servicio Fabric Host. |
| SkipFirewallConfiguration |Bool, el valor predeterminado es false. |Especifica si el sistema debe establecer o no la configuración de firewall. Solo aplicable si usa Firewall de Windows. Si usa firewalls de terceros, debe abrir los puertos para que los usen el sistema y las aplicaciones. |

### <a name="section-name-transactionalreplicator"></a>Nombre de sección: TransactionalReplicator
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| MaxCopyQueueSize |Uint, el valor predeterminado es 16384. |Es el valor máximo y define el tamaño inicial de la cola que mantiene operaciones de replicación. Tenga en cuenta que debe ser una potencia de 2. Si durante el tiempo de ejecución la cola crece hasta este tamaño, se limitarán las operaciones entre los replicadores principal y secundario. |
| BatchAcknowledgementInterval | Tiempo en segundos, el valor predeterminado es 0,015. | Especifique el intervalo de tiempo en segundos. Determina la cantidad de tiempo que el replicador espera después de recibir una operación, antes de devolver una confirmación. Las confirmaciones de otras operaciones recibidas durante este período de tiempo se devolverán en un solo mensaje-> de forma que se reduce el tráfico de red pero también posiblemente el rendimiento del replicador. |
| MaxReplicationMessageSize |Uint, el valor predeterminado es 52 428 800. | Tamaño máximo de mensaje de las operaciones de replicación. El valor predeterminado es 50 MB. |
| ReplicatorAddress |Wstring, el valor predeterminado es "localhost:0". | El punto de conexión en forma de cadena -'IP:Port' que usa el replicador de Windows Fabric para establecer conexiones con otras réplicas para enviar o recibir operaciones. |
| InitialPrimaryReplicationQueueSize |Uint, el valor predeterminado es 64. | Este valor define el tamaño inicial de la cola que mantiene las operaciones de replicación en el replicador principal. Tenga en cuenta que debe ser una potencia de 2.|
| MaxPrimaryReplicationQueueSize |Uint, el valor predeterminado es 8192. |Es el número máximo de operaciones que podrían existir en la cola de replicación principal. Tenga en cuenta que debe ser una potencia de 2. |
| MaxPrimaryReplicationQueueMemorySize |Uint, el valor predeterminado es 0. |Es el valor máximo de la cola de replicación principal en bytes. |
| InitialSecondaryReplicationQueueSize |Uint, el valor predeterminado es 64. |Este valor define el tamaño inicial de la cola que mantiene las operaciones de replicación en el replicador secundario. Tenga en cuenta que debe ser una potencia de 2. |
| MaxSecondaryReplicationQueueSize |Uint, el valor predeterminado es 16384. |Es el número máximo de operaciones que podrían existir en la cola de replicación secundaria. Tenga en cuenta que debe ser una potencia de 2. |
| MaxSecondaryReplicationQueueMemorySize |Uint, el valor predeterminado es 0. |Es el valor máximo de la cola de replicación secundaria en bytes. |
| SecondaryClearAcknowledgedOperations |Bool, el valor predeterminado es false. |Controla si las operaciones en el replicador secundario se borran una vez que se confirman en el principal (se vacían en el disco). La configuración de este valor en TRUE puede dar lugar a lecturas de disco adicionales en el nuevo replicador, al tiempo que se actualizan las réplicas después de una conmutación por error. |
| MaxMetadataSizeInKB |Int, el valor predeterminado es 4. |Tamaño máximo de los metadatos de la secuencia de registro. |
| MaxRecordSizeInKB |Uint, el valor predeterminado es 1024. | Tamaño máximo de un registro de secuencia de registro. |
| CheckpointThresholdInMB |Int, el valor predeterminado es 50. |Cuando el uso del registro supera este valor, se inicia un punto de control. |
| MaxAccumulatedBackupLogSizeInMB |Int, el valor predeterminado es 800. |El tamaño máximo acumulado (en MB) de los registros de copia de seguridad de una cadena de registros de copia de seguridad determinada. Las solicitudes de copia de seguridad incremental no se realizarán correctamente si esta genera un registro de copia de seguridad que provoque la acumulación de este tipo de elemento, ya que la copia de seguridad completa tendrá un tamaño superior. En tales casos, el usuario tiene que realizar una copia de seguridad completa. |
| MaxWriteQueueDepthInKB |Int, el valor predeterminado es 0. | Valor entero para la profundidad máxima de la cola de escritura que puede usar el registrador de núcleos, especificado en kilobytes para el registro asociado con esta réplica. Este valor es el número máximo de bytes que pueden estar pendientes durante las actualizaciones del registrador de núcleos. Puede ser 0 para que el registrador de núcleos calcule un valor adecuado, o un múltiplo de 4. |
| SharedLogId |String |Identificador de registro compartido. Es un GUID y debe ser único para cada registro compartido. |
| SharedLogPath |Cadena |Ruta de acceso al registro compartido. Si este valor está vacío, se usa el registro compartido predeterminado. |
| SlowApiMonitoringDuration |Tiempo en segundos, el valor predeterminado es 300. | Especifique la duración de la API antes de que se desencadene el evento de mantenimiento de advertencia.|
| MinLogSizeInMB |Int, el valor predeterminado es 0. |Tamaño mínimo del registro transaccional. El registro no podrá truncarse a un tamaño inferior a este valor. 0 indica que el replicador determinará el tamaño de registro mínimo según otros valores de configuración. Al aumentar este valor, incrementa la posibilidad de hacer copias parciales y copias de seguridad incrementales, ya que se reducen las posibilidades de que se trunquen registros pertinentes. |

### <a name="section-name-fabricclient"></a>Nombre de sección: FabricClient
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| NodeAddresses |Wstring, el valor predeterminado es "". |Una colección de direcciones (cadenas de conexión) en diferentes nodos que se pueden usar para comunicarse con el servicio de nomenclatura. Inicialmente el cliente se conecta mediante la selección de una de las direcciones de manera aleatoria. Si se proporciona más de una cadena de conexión y la conexión no se establece por un error de comunicación o de tiempo de espera, el cliente cambia al uso de la siguiente dirección de manera secuencial. Consulte la sección de reintentos de direcciones del servicio de nomenclatura para más información sobre la semántica de reintentos. |
| ConnectionInitializationTimeout |Tiempo en segundos, el valor predeterminado es 2. |Especifique el intervalo de tiempo en segundos. El intervalo de tiempo de espera de conexión para cada vez que el cliente intenta abrir una conexión a la puerta de enlace. |
| PartitionLocationCacheLimit |Int, el valor predeterminado es 100 000. |Número de particiones almacenadas en caché para la resolución del servicio (se establece en 0 para un número ilimitado). |
| ServiceChangePollInterval |Tiempo en segundos, el valor predeterminado es 120. |Especifique el intervalo de tiempo en segundos. El intervalo entre sondeos consecutivos del servicio cambia del cliente a la puerta de enlace para las devoluciones de llamada registradas de notificaciones de cambio de servicio. |
| KeepAliveIntervalInSeconds |Int, el valor predeterminado es 20. |El intervalo dentro del cual el transporte de FabricClient envía mensajes persistentes a la puerta de enlace. Para 0; keepAlive está deshabilitado. Debe ser un valor positivo. |
| HealthOperationTimeout. |Tiempo en segundos, el valor predeterminado es 120. |Especifique el intervalo de tiempo en segundos. El tiempo de espera de un mensaje de informe enviado al administrador de mantenimiento. |
| HealthReportSendInterval. |Tiempo en segundos, el valor predeterminado es 30. |Especifique el intervalo de tiempo en segundos. El intervalo dentro del cual el componente de informes envía informes de mantenimiento acumulados al administrador de mantenimiento. |
| HealthReportRetrySendInterval |Tiempo en segundos, el valor predeterminado es 30. |Especifique el intervalo de tiempo en segundos. El intervalo dentro del cual el componente de informes reenvía informes de mantenimiento acumulados al administrador de mantenimiento. |
| RetryBackoffInterval |Tiempo en segundos, el valor predeterminado es 3. |Especifique el intervalo de tiempo en segundos. El intervalo de interrupción antes de volver a intentar la operación. |
| MaxFileSenderThreads |Uint, el valor predeterminado es 10. |El número máximo de archivos que se transfieren en paralelo. |

### <a name="section-name-common"></a>Nombre de sección: Common
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| PerfMonitorInterval |Tiempo en segundos, el valor predeterminado es 1. |Especifique el intervalo de tiempo en segundos. Intervalo de supervisión del rendimiento. La configuración en 0 o un valor negativo deshabilita la supervisión. |

### <a name="section-name-healthmanager"></a>Nombre de sección: HealthManager
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Bool, el valor predeterminado es false. |Directiva de evaluación de mantenimiento del clúster: habilita la evaluación de mantenimiento de tipos por aplicación. |

### <a name="section-name-fabricnode"></a>Nombre de sección: FabricNode
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| StateTraceInterval |Tiempo en segundos, el valor predeterminado es 300. |Especifique el intervalo de tiempo en segundos. El intervalo del estado de nodo de seguimiento en cada nodo y en los nodos activos en FM/FMM. |

### <a name="section-name-nodedomainids"></a>Nombre de sección: NodeDomainIds
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| UpgradeDomainId |Wstring, el valor predeterminado es "". |Describe el dominio de actualización al que pertenece un nodo. |
| PropertyGroup |NodeFaultDomainIdCollection |Describe los dominios de error a los que pertenece un nodo. El dominio de error se define mediante un URI que describe la ubicación del nodo en el centro de datos.  Los URI de dominio de error tienen el formato fd:/fd/ seguido de un segmento con la ruta de acceso al URI.|

### <a name="section-name-nodeproperties"></a>Nombre de sección: NodeProperties
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| PropertyGroup |NodePropertyCollectionMap |Una colección de pares de clave y valor de cadena para las propiedades de nodo. |

### <a name="section-name-nodecapacities"></a>Nombre de sección: NodeCapacities
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| PropertyGroup |NodeCapacityCollectionMap |Una colección de funcionalidades de nodo para diferentes métricas. |

### <a name="section-name-fabricnode"></a>Nombre de sección: FabricNode
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| StartApplicationPortRange |Int, el valor predeterminado es 0. |Inicio de los puertos de aplicación administrados por el subsistema de hospedaje. Requerido si EndpointFilteringEnabled es true en Hosting. |
| EndApplicationPortRange |Int, el valor predeterminado es 0. |Fin (no inclusivo) de los puertos de aplicación administrados por el subsistema de hospedaje. Requerido si EndpointFilteringEnabled es true en Hosting. |
| ClusterX509StoreName |Wstring, el valor predeterminado es "My". |Nombre del almacén de certificados X.509 que contiene el certificado de clúster para proteger la comunicación dentro del clúster. |
| ClusterX509FindType |Wstring, el valor predeterminado es "FindByThumbprint". |Indica cómo buscar certificados de clúster en el almacén especificado mediante los valores admitidos de ClusterX509StoreName: "FindByThumbprint"; "FindBySubjectName" con "FindBySubjectName"; cuando existen varias coincidencias, se usa la que tiene la expiración más lejana. |
| ClusterX509FindValue |Wstring, el valor predeterminado es "". |Valor de filtro de búsqueda usado para encontrar el certificado de clúster. |
| ClusterX509FindValueSecondary |Wstring, el valor predeterminado es "". |Valor de filtro de búsqueda usado para encontrar el certificado de clúster. |
| ServerAuthX509StoreName |Wstring, el valor predeterminado es "My". |Nombre del almacén de certificados X.509 que contiene el certificado de servidor para el servicio de entrada. |
| ServerAuthX509FindType |Wstring, el valor predeterminado es "FindByThumbprint". |Indica cómo buscar el certificado de servidor en el almacén especificado mediante el valor admitido ServerAuthX509StoreName: FindByThumbprint; FindBySubjectName. |
| ServerAuthX509FindValue |Wstring, el valor predeterminado es "". |Valor de filtro de búsqueda usado para encontrar el certificado de servidor. |
| ServerAuthX509FindValueSecondary |Wstring, el valor predeterminado es "". |Valor de filtro de búsqueda usado para encontrar el certificado de servidor. |
| ClientAuthX509StoreName |Wstring, el valor predeterminado es "My". |Nombre del almacén de certificados X.509 que contiene el certificado para el rol de administrador predeterminado FabricClient. |
| ClientAuthX509FindType |Wstring, el valor predeterminado es "FindByThumbprint". |Indica cómo buscar el certificado en el almacén especificado mediante el valor admitido ClientAuthX509StoreName: FindByThumbprint; FindBySubjectName. |
| ClientAuthX509FindValue |Wstring, el valor predeterminado es "". | Valor de filtro de búsqueda usado para encontrar el certificado para el rol de administrador predeterminado FabricClient. |
| ClientAuthX509FindValueSecondary |Wstring, el valor predeterminado es "". |Valor de filtro de búsqueda usado para encontrar el certificado para el rol de administrador predeterminado FabricClient. |
| UserRoleClientX509StoreName |Wstring, el valor predeterminado es "My". |Nombre del almacén de certificados X.509 que contiene el certificado para el rol de usuario predeterminado FabricClient. |
| UserRoleClientX509FindType |Wstring, el valor predeterminado es "FindByThumbprint". |Indica cómo buscar el certificado en el almacén especificado mediante el valor admitido UserRoleClientX509StoreName: FindByThumbprint; FindBySubjectName. |
| UserRoleClientX509FindValue |Wstring, el valor predeterminado es "". |Valor de filtro de búsqueda usado para encontrar el certificado para el rol de usuario predeterminado FabricClient. |
| UserRoleClientX509FindValueSecondary |Wstring, el valor predeterminado es "". |Valor de filtro de búsqueda usado para encontrar el certificado para el rol de usuario predeterminado FabricClient. |

### <a name="section-name-paas"></a>Nombre de sección: Paas
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| ClusterId |Wstring, el valor predeterminado es "". |Almacén de certificados X509 usado por Fabric para la protección de la configuración. |

### <a name="section-name-fabrichost"></a>Nombre de sección: FabricHost
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| StopTimeout |Tiempo en segundos, el valor predeterminado es 300. |Especifique el intervalo de tiempo en segundos. El tiempo de espera para la activación, desactivación y actualización del servicio hospedado. |
| StartTimeout |Tiempo en segundos, el valor predeterminado es 300. |Especifique el intervalo de tiempo en segundos. Tiempo de espera de inicio de fabricactivationmanager. |
| ActivationRetryBackoffInterval |Tiempo en segundos, el valor predeterminado es 5. |Especifique el intervalo de tiempo en segundos. Intervalo de retroceso en cada error de activación; con cada error de activación continuo, el sistema vuelve a intentar la activación hasta el valor de MaxActivationFailureCount. El intervalo de reintento en cada intento es un producto del error de activación continua y el intervalo de retroceso de activación. |
| ActivationMaxRetryInterval |Tiempo en segundos, el valor predeterminado es 300. |Especifique el intervalo de tiempo en segundos. Intervalo de reintento máximo para la activación. Con cada error continuo, el intervalo de reintento se calcula como Min( ActivationMaxRetryInterval; Recuento continuo de errores * ActivationRetryBackoffInterval). |
| ActivationMaxFailureCount |Int, el valor predeterminado es 10. |Es el recuento máximo que el sistema reintentará la activación que ha generado error antes de desistir. |
| EnableServiceFabricAutomaticUpdates |Bool, el valor predeterminado es false. |Sirve para habilitar la actualización automática de Fabric mediante Windows Update. |
| EnableServiceFabricBaseUpgrade |Bool, el valor predeterminado es false. |Sirve para habilitar la actualización básica en el servidor. |
| EnableRestartManagement |Bool, el valor predeterminado es false. |Sirve para habilitar el reinicio del servidor. |


### <a name="section-name-failovermanager"></a>Nombre de sección: FailoverManager
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| UserReplicaRestartWaitDuration |Tiempo en segundos, el valor predeterminado es 60.0 * 30. |Especifique el intervalo de tiempo en segundos. Cuando una réplica persistente deja de funcionar, Windows Fabric espera esta cantidad de tiempo a que la réplica vuelva antes de crear nuevas réplicas de reemplazo (lo que requeriría una copia del estado). |
| QuorumLossWaitDuration |Tiempo en segundos, el valor predeterminado es MaxValue. |Especifique el intervalo de tiempo en segundos. Es la duración máxima que se permite que una partición esté en estado de pérdida de cuórum. Si después de esta duración la partición sigue en pérdida de cuórum, se recupera de dicha pérdida considerando perdidas las réplicas inactivas. Tenga en cuenta que esto puede provocar una pérdida de datos. |
| UserStandByReplicaKeepDuration |Tiempo en segundos, el valor predeterminado es 3600.0 * 24 * 7. |Especifique el intervalo de tiempo en segundos. Cuando una réplica persistente vuelve de un estado inactivo, puede que ya se haya reemplazado. Este temporizador determina cuánto tiempo el FM mantendrá la réplica en espera antes de descartarla. |
| UserMaxStandByReplicaCount |Int, el valor predeterminado es 1. |El número máximo predeterminado de réplicas en espera que mantiene el sistema para los servicios de usuario. |

### <a name="section-name-namingservice"></a>Nombre de sección: NamingService
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| TargetReplicaSetSize |Int, el valor predeterminado es 7. |El número de conjuntos de réplicas para cada partición del almacén de servicio de nomenclatura. Aumentar el número de conjuntos de réplica aumenta el nivel de confiabilidad de la información del almacén del servicio de nomenclatura al reducirse la oportunidad de que la información se pierda como resultado de errores en los nodos. A cambio, aumenta la carga en Windows Fabric y la cantidad de tiempo que tarda en realizar actualizaciones en los datos de nomenclatura.|
|MinReplicaSetSize | Int, el valor predeterminado es 3. | El número mínimo de réplicas del servicio de nomenclatura en las que es necesario escribir para completar una actualización. Si hay un número menor de réplicas de las que están activas en el sistema, el sistema de confiabilidad rechaza las actualizaciones al almacén del servicio de nomenclatura hasta que las réplicas se restauran. Este valor nunca debe ser mayor que el de TargetReplicaSetSize. |
|ReplicaRestartWaitDuration | Tiempo en segundos, el valor predeterminado es (60.0 * 30).| Especifique el intervalo de tiempo en segundos. Cuando una réplica del servicio de nomenclatura deja de funcionar, el temporizador se inicia.  Cuando expira, el FM comenzará a sustituir las réplicas inactivas (aún no se consideran perdidas). |
|QuorumLossWaitDuration | Tiempo en segundos, el valor predeterminado es MaxValue. | Especifique el intervalo de tiempo en segundos. Cuando un servicio de nomenclatura entra en pérdida de cuórum, este temporizador se inicia.  Cuando expira, el FM considera las réplicas inactivas perdidas e intenta recuperar el cuórum. Tenga en cuenta que esto puede dar lugar a la pérdida de datos. |
|StandByReplicaKeepDuration | Tiempo en segundos, el valor predeterminado es 3600.0 * 2. | Especifique el intervalo de tiempo en segundos. Cuando una réplica del servicio de nomenclatura vuelve de un estado inactivo, puede que ya se haya reemplazado.  Este temporizador determina cuánto tiempo el FM mantendrá la réplica en espera antes de descartarla. |
|PlacementConstraints | Wstring, el valor predeterminado es "". | Restricción de selección de ubicación para el servicio de nomenclatura. |
|ServiceDescriptionCacheLimit | Int, el valor predeterminado es 0. | El número máximo de entradas mantenidas en la caché de descripción del servicio LRU en el almacén del servicio de nomenclatura (se establece en 0 para un número ilimitado). |
|RepairInterval | Tiempo en segundos, el valor predeterminado es 5. | Especifique el intervalo de tiempo en segundos. Intervalo dentro del cual se inicia la reparación de la incoherencia de nomenclatura entre el propietario de la entidad y el propietario del nombre. |
|MaxNamingServiceHealthReports | Int, el valor predeterminado es 10. | El número máximo de operaciones lentas que el almacén del servicio de nomenclatura notifica como incorrectas al mismo tiempo. Si es 0, se envían todas las operaciones lentas. |
| MaxMessageSize |Int, el valor predeterminado es 4*1024*1024. |El tamaño de mensaje máximo para la comunicación del nodo cliente cuando se usa la nomenclatura. Mitigación de ataque de DOS; el valor predeterminado es 4 MB. |
| MaxFileOperationTimeout |Tiempo en segundos, el valor predeterminado es 30. |Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo permitido para la operación del servicio de almacén de archivos. Las solicitudes que especifiquen un tiempo de espera mayor se rechazarán. |
| MaxOperationTimeout |Tiempo en segundos, el valor predeterminado es 600. |Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo permitido para las operaciones de cliente. Las solicitudes que especifiquen un tiempo de espera mayor se rechazarán. |
| MaxClientConnections |Int, el valor predeterminado es 1000. |El número máximo permitido de conexiones de cliente por puerta de enlace. |
| ServiceNotificationTimeout |Tiempo en segundos, el valor predeterminado es 30. |Especifique el intervalo de tiempo en segundos. El tiempo de espera usado al entregar notificaciones de servicio al cliente. |
| MaxOutstandingNotificationsPerClient |Int, el valor predeterminado es 1000. |El número máximo de notificaciones pendientes antes de que la puerta de enlace cierre a la fuerza un registro de cliente. |
| MaxIndexedEmptyPartitions |Int, el valor predeterminado es 1000. |El número máximo de particiones vacías que permanecerán indexadas en la caché de notificaciones para la sincronización de los clientes que se vuelven a conectar. Cualquier partición vacía por encima de este número se quitará del índice en orden ascendente de la versión de búsqueda. Durante la reconexión, los clientes se pueden seguir sincronizando y pueden recibir actualizaciones de particiones vacías que faltan, pero el protocolo de sincronización se encarece. |
| GatewayServiceDescriptionCacheLimit |Int, el valor predeterminado es 0. |El número máximo de entradas mantenidas en la caché de descripción del servicio LRU en la puerta de enlace de nomenclatura (se establece en 0 para un número ilimitado). |
| PartitionCount |Int, el valor predeterminado es 3. |El número de particiones del almacén del servicio de nomenclatura que se creará. Cada partición posee una única clave de partición que corresponde a su índice; de modo que existen [0; PartitionCount] claves de partición. Al aumentar el número de particiones del servicio de nomenclatura, aumenta la escala a la que puede ejecutarse el servicio de nomenclatura ya que se reduce la cantidad media de datos que contiene cualquier conjunto de réplicas de respaldo; a cambio, aumenta la utilización de recursos (dado que se deben mantener las réplicas de servicio PartitionCount*ReplicaSetSize).|

### <a name="section-name-runas"></a>Nombre de sección: RunAs
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| RunAsAccountName |Wstring, el valor predeterminado es "". |Indica el nombre de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser" o "ManagedServiceAccount". Valores válidos son "domain\user" o "user@domain". |
|RunAsAccountType|Wstring, el valor predeterminado es "". |Indica el tipo de cuenta de ejecución. Es necesario para cualquier sección RunAs. Valores válidos son "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|Wstring, el valor predeterminado es "". |Indica la contraseña de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser". |

### <a name="section-name-runasfabric"></a>Nombre de sección: RunAs_Fabric
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| RunAsAccountName |Wstring, el valor predeterminado es "". |Indica el nombre de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser" o "ManagedServiceAccount". Valores válidos son "domain\user" o "user@domain". |
|RunAsAccountType|Wstring, el valor predeterminado es "". |Indica el tipo de cuenta de ejecución. Es necesario para cualquier sección RunAs. Valores válidos son "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|Wstring, el valor predeterminado es "". |Indica la contraseña de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser". |

### <a name="section-name-runashttpgateway"></a>Nombre de sección: RunAs_HttpGateway
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| RunAsAccountName |Wstring, el valor predeterminado es "". |Indica el nombre de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser" o "ManagedServiceAccount". Valores válidos son "domain\user" o "user@domain". |
|RunAsAccountType|Wstring, el valor predeterminado es "". |Indica el tipo de cuenta de ejecución. Es necesario para cualquier sección RunAs. Valores válidos son "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|Wstring, el valor predeterminado es "". |Indica la contraseña de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser". |

### <a name="section-name-runasdca"></a>Nombre de sección: RunAs_DCA
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| RunAsAccountName |Wstring, el valor predeterminado es "". |Indica el nombre de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser" o "ManagedServiceAccount". Valores válidos son "domain\user" o "user@domain". |
|RunAsAccountType|Wstring, el valor predeterminado es "". |Indica el tipo de cuenta de ejecución. Es necesario para cualquier sección RunAs. Valores válidos son "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|Wstring, el valor predeterminado es "". |Indica la contraseña de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser". |

### <a name="section-name-httpgateway"></a>Nombre de sección: HttpGateway
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
|IsEnabled|Bool, el valor predeterminado es false. | Habilita o deshabilita httpgateway. Httpgateway está deshabilitado de forma predeterminada y es necesario establecer esta configuración para habilitarlo. |
|ActiveListeners |Uint, el valor predeterminado es 50. | Número de lecturas para enviar a la cola del servidor HTTP. Controla el número de solicitudes simultáneas que se pueden satisfacer mediante HttpGateway. |
|MaxEntityBodySize |Uint, el valor predeterminado es 4 194 304. |  Proporciona el tamaño máximo del cuerpo que se puede esperar de una solicitud HTTP. El valor predeterminado es 4 MB. Httpgateway no atenderá una solicitud si el tamaño del cuerpo es mayor que su valor. El tamaño mínimo del fragmento de lectura es 4096 bytes. Así que tiene que ser > = 4096. |

### <a name="section-name-ktllogger"></a>Nombre de sección: KtlLogger
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
|AutomaticMemoryConfiguration |Int, el valor predeterminado es 1. | Marca que indica si los valores de memoria se deben configurar automática y dinámicamente. Si es&0;, la configuración de memoria se usa directamente y no cambia en función de las condiciones del sistema. Si es&1;, la configuración de memoria se realiza automáticamente y puede cambiar según las condiciones del sistema. |
|WriteBufferMemoryPoolMinimumInKB |Int, el valor predeterminado es 8 388 608. |El número de KB para asignar inicialmente al bloque de memoria del búfer de escritura. Use 0 para indicar que no hay límite. El valor predeterminado debe ser coherente con el valor de SharedLogSizeInMB siguiente. |
|WriteBufferMemoryPoolMaximumInKB | Int, el valor predeterminado es 0. |El número de KB que se permite que crezca el bloque de memoria del búfer de escritura. Use 0 para indicar que no hay límite. |
|MaximumDestagingWriteOutstandingInKB | Int, el valor predeterminado es 0. | El número de KB que se permite que vaya por delante el registro compartido con respecto al registro dedicado. Use 0 para indicar que no hay límite.
|SharedLogPath |Wstring, el valor predeterminado es "". | Nombre de archivo y ruta a la ubicación para colocar el contenedor de registros compartidos. Use "" para utilizar la ruta de acceso predeterminada en la raíz de datos de Fabric. |
|SharedLogId |Wstring, el valor predeterminado es "". |GUID único del contenedor de registros compartidos. Use "" si utiliza la ruta de acceso predeterminada en la raíz de datos de Fabric. |
|SharedLogSizeInMB |Int, el valor predeterminado es 8192. | El número de MB para asignar en el contenedor de registros compartidos. |

### <a name="section-name-applicationgatewayhttp"></a>Nombre de sección: ApplicationGateway/Http
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
|IsEnabled |Bool, el valor predeterminado es false. | Habilita o deshabilita HttpApplicationGateway. HttpApplicationGateway está deshabilitado de forma predeterminada y para habilitarlo es necesario establecer esta configuración. |
|NumberOfParallelOperations | Uint, el valor predeterminado es 1000. | Número de lecturas para enviar a la cola del servidor HTTP. Controla el número de solicitudes simultáneas que se pueden satisfacer mediante HttpGateway. |
|DefaultHttpRequestTimeout |Tiempo en segundos. El valor predeterminado es 60. |Especifique el intervalo de tiempo en segundos.  Proporciona el tiempo de espera de solicitud predeterminado para las solicitudes HTTP que se van a procesar en la puerta de enlace de aplicaciones HTTP. |
|ResolveServiceBackoffInterval |Tiempo en segundos, el valor predeterminado es 5. |Especifique el intervalo de tiempo en segundos.  Proporciona el intervalo de retroceso predeterminado antes de reintentar una operación errónea del servicio de resolución. |
|BodyChunkSize |Uint, el valor predeterminado es 4096. |  Proporciona el tamaño del fragmento en bytes usado para leer el cuerpo. |
|GatewayAuthCredentialType |Wstring, el valor predeterminado es "None". | Indica el tipo de credenciales de seguridad que se usarán en el punto de conexión de la puerta de enlace de aplicaciones HTTP. Valores válidos son "None/X509. |
|GatewayX509CertificateStoreName |Wstring, el valor predeterminado es "My". | Nombre del almacén de certificados X.509 que contiene el certificado de puerta de enlace de aplicaciones HTTP. |
|GatewayX509CertificateFindType |Wstring, el valor predeterminado es "FindByThumbprint". | Indica cómo buscar el certificado en el almacén especificado mediante el valor admitido GatewayX509CertificateStoreName: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | Wstring, el valor predeterminado es "". | Valor del filtro de búsqueda usado para encontrar el certificado de puerta de enlace de aplicaciones HTTP. Este certificado se configura en el punto de conexión HTTP y también puede usarse para comprobar la identidad de la aplicación en caso de que la necesiten los servicios. Primero se busca FindValue y, si no existe, se busca FindValueSecondary. |
|GatewayX509CertificateFindValueSecondary | Wstring, el valor predeterminado es "". |Valor del filtro de búsqueda usado para encontrar el certificado de puerta de enlace de aplicaciones HTTP. Este certificado se configura en el punto de conexión HTTP y también puede usarse para comprobar la identidad de la aplicación en caso de que la necesiten los servicios. Primero se busca FindValue y, si no existe, se busca FindValueSecondary.|

### <a name="section-name-management"></a>Nombre de sección: Management
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| ImageStoreConnectionString |SecureString | Cadena de conexión a la raíz de ImageStore. |
| ImageStoreMinimumTransferBPS | Int, el valor predeterminado es 1024. |La velocidad mínima de transferencia entre el clúster e ImageStore. Este valor se usa para determinar el tiempo de espera al acceder al elemento ImageStore externo. Cambie este valor solo si la latencia entre el clúster e ImageStore es alta a fin de dar más tiempo al clúster para que se descargue del elemento ImageStore externo. |
|AzureStorageMaxWorkerThreads | Int, el valor predeterminado es 25. | El número máximo de subprocesos de trabajo en paralelo. |
|AzureStorageMaxConnections | Int, el valor predeterminado es 5000. | El número máximo de conexiones simultáneas a Azure Storage. |
|AzureStorageOperationTimeout | Tiempo en segundos, el valor predeterminado es 6000. | Especifique el intervalo de tiempo en segundos. Tiempo de espera para que finalice la operación xstore. |
|ImageCachingEnabled | Bool, el valor predeterminado es true. | Esta configuración permite habilitar o deshabilitar el almacenamiento en caché. |
|DisableChecksumValidation | Bool, el valor predeterminado es false. | Esta configuración permite habilitar o deshabilitar la validación de suma de comprobación durante el aprovisionamiento de aplicaciones. |
|DisableServerSideCopy | Bool, el valor predeterminado es false. | Esta configuración habilita o deshabilita la copia del lado servidor del paquete de aplicación en ImageStore durante el aprovisionamiento de aplicaciones. |

### <a name="section-name-healthmanagerclusterhealthpolicy"></a>Nombre de sección: HealthManager/ClusterHealthPolicy
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| ConsiderWarningAsError |Bool, el valor predeterminado es false. |Directiva de evaluación de mantenimiento del clúster: las advertencias se tratan como errores. |
| MaxPercentUnhealthyNodes | Int, el valor predeterminado es 0. |Directiva de evaluación de mantenimiento del clúster: porcentaje máximo permitido de nodos incorrectos para que el estado del clúster sea correcto. |
| MaxPercentUnhealthyApplications | Int, el valor predeterminado es 0. |Directiva de evaluación de mantenimiento del clúster: porcentaje máximo permitido de aplicaciones incorrectas para que el estado del clúster sea correcto. |
|MaxPercentDeltaUnhealthyNodes | Int, el valor predeterminado es 10. |Directiva de evaluación de mantenimiento de actualización del clúster: porcentaje máximo permitido de nodos incorrectos delta para que el estado del clúster sea correcto. |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes | Int, el valor predeterminado es 15. |Directiva de evaluación de mantenimiento de actualización del clúster: porcentaje máximo permitido de nodos incorrectos delta en un dominio de actualización para que el estado del clúster sea correcto.|

### <a name="section-name-faultanalysisservice"></a>Nombre de sección: FaultAnalysisService
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| TargetReplicaSetSize |Int, el valor predeterminado es 0. |NOT_PLATFORM_UNIX_START. El valor de TargetReplicaSetSize para FaultAnalysisService. |
| MinReplicaSetSize |Int, el valor predeterminado es 0. |MinReplicaSetSize para FaultAnalysisService. |
| ReplicaRestartWaitDuration |Tiempo en segundos, el valor predeterminado es 60 minutos.|Especifique el intervalo de tiempo en segundos. ReplicaRestartWaitDuration para FaultAnalysisService. |
| QuorumLossWaitDuration | Tiempo en segundos, el valor predeterminado es MaxValue. |Especifique el intervalo de tiempo en segundos. QuorumLossWaitDuration para FaultAnalysisService. |
| StandByReplicaKeepDuration| Tiempo en segundos, el valor predeterminado es (60*24*7) minutos. |Especifique el intervalo de tiempo en segundos. StandByReplicaKeepDuration para FaultAnalysisService. |
| PlacementConstraints | Wstring, el valor predeterminado es "".| PlacementConstraints para FaultAnalysisService. |
| StoredActionCleanupIntervalInSeconds | Int, el valor predeterminado es 3600. |Es la frecuencia con la que se limpiará el almacén.  Solo se quitarán las acciones en estado terminal y que se han completado como mínimo hace CompletedActionKeepDurationInSeconds. |
| CompletedActionKeepDurationInSeconds | Int, el valor predeterminado es 604 800. | El tiempo aproximado que las acciones se mantienen en estado terminal.  También depende de StoredActionCleanupIntervalInSeconds, dado que el trabajo para realizar la limpieza solo se realiza en ese intervalo. 604800 es 7 días. |
| StoredChaosEventCleanupIntervalInSeconds | Int, el valor predeterminado es 3600. |La frecuencia con la que se auditará el almacén de cara a la limpieza; si el número de eventos es superior a 30 000, se activa la limpieza. |

### <a name="section-name-filestoreservice"></a>Nombre de sección: FileStoreService
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| NamingOperationTimeout |Tiempo en segundos, el valor predeterminado es 60. |Especifique el intervalo de tiempo en segundos. El tiempo de espera para realizar la operación de nomenclatura. |
| QueryOperationTimeout | Tiempo en segundos, el valor predeterminado es 60. |Especifique el intervalo de tiempo en segundos. El tiempo de espera para realizar una operación de consulta. |
| MaxCopyOperationThreads | Uint, el valor predeterminado es 0. | El número máximo de archivos paralelos que el replicador secundario puede copiar del principal. "0" == número de núcleos. |
| MaxFileOperationThreads | Uint, el valor predeterminado es 100. | El número máximo de subprocesos paralelos que pueden realizar operaciones de archivos (copiar o mover) en el replicador principal. "0" == número de núcleos. |
| MaxStoreOperations | Uint, el valor predeterminado es 4096. |El número máximo de operaciones paralelas de transacción de almacén que se permiten en el replicador principal. "0" == número de núcleos. |
| MaxRequestProcessingThreads | Uint, el valor predeterminado es 200. |El número máximo de subprocesos paralelos que pueden procesar solicitudes en el replicador principal. "0" == número de núcleos. |
| MaxSecondaryFileCopyFailureThreshold | Uint, el valor predeterminado es 25.| El número máximo de reintentos de copia de archivos en el replicador secundario antes de desistir. |
| AnonymousAccessEnabled | Bool, el valor predeterminado es true. |Habilita o deshabilita el acceso anónimo a los recursos compartidos de FileStoreService. |
| PrimaryAccountType | Wstring, el valor predeterminado es "". |El tipo de cuenta principal de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
| PrimaryAccountUserName | Wstring, el valor predeterminado es "". |El nombre de usuario de la cuenta principal de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
| PrimaryAccountUserPassword | SecureString, el valor predeterminado es empty. |La contraseña de la cuenta principal de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
| FileStoreService | PrimaryAccountNTLMPasswordSecret | SecureString, el valor predeterminado es empty. | El secreto de contraseña que se usa como valor de inicialización para generar la misma contraseña cuando se usa la autenticación NTLM. |
| PrimaryAccountNTLMX509StoreLocation | Wstring, el valor predeterminado es "LocalMachine".| La ubicación del almacén de certificados X509 usada para generar HMAC en PrimaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
| PrimaryAccountNTLMX509StoreName | Wstring, el valor predeterminado es "MY".| El nombre del almacén de certificados X509 usado para generar HMAC en PrimaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
| PrimaryAccountNTLMX509Thumbprint | Wstring, el valor predeterminado es "".|La huella digital del almacén de certificados X509 usada para generar HMAC en PrimaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
| SecondaryAccountType | Wstring, el valor predeterminado es "".| El tipo de cuenta secundaria de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
| SecondaryAccountUserName | Wstring, el valor predeterminado es "".| El nombre de usuario de la cuenta secundaria de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
| SecondaryAccountUserPassword | SecureString, el valor predeterminado es empty. |La contraseña de la cuenta secundaria de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL.  |
| SecondaryAccountNTLMPasswordSecret | SecureString, el valor predeterminado es empty. | El secreto de contraseña que se usa como valor de inicialización para generar la misma contraseña cuando se usa la autenticación NTLM. |
| SecondaryAccountNTLMX509StoreLocation | Wstring, el valor predeterminado es "LocalMachine". |La ubicación del almacén de certificados X509 usada para generar HMAC en SecondaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
| SecondaryAccountNTLMX509StoreName | Wstring, el valor predeterminado es "MY". |El nombre del almacén de certificados X509 usado para generar HMAC en SecondaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
| SecondaryAccountNTLMX509Thumbprint | Wstring, el valor predeterminado es "".| La huella digital del almacén de certificados X509 usada para generar HMAC en SecondaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |

### <a name="section-name-imagestoreservice"></a>Nombre de sección: ImageStoreService
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| Enabled |Bool, el valor predeterminado es false. |La marca "Enabled" para ImageStoreService. |
| TargetReplicaSetSize | Int, el valor predeterminado es 7. |TargetReplicaSetSize para ImageStoreService. |
| MinReplicaSetSize | Int, el valor predeterminado es 3. |MinReplicaSetSize para ImageStoreService. |
| ReplicaRestartWaitDuration | Tiempo en segundos, el valor predeterminado es 60.0 * 30. | Especifique el intervalo de tiempo en segundos. ReplicaRestartWaitDuration para ImageStoreService. |
| QuorumLossWaitDuration | Tiempo en segundos, el valor predeterminado es MaxValue. | Especifique el intervalo de tiempo en segundos. QuorumLossWaitDuration para ImageStoreService. |
| StandByReplicaKeepDuration | Tiempo en segundos, el valor predeterminado es 3600.0 * 2. | Especifique el intervalo de tiempo en segundos. StandByReplicaKeepDuration para ImageStoreService. |
| PlacementConstraints | Wstring, el valor predeterminado es "". | PlacementConstraints para ImageStoreService. |
| ClientUploadTimeout | Tiempo en segundos, el valor predeterminado es 1800. |Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de carga de nivel superior para el servicio de almacén de imágenes. |
| ClientCopyTimeout | Tiempo en segundos, el valor predeterminado es 1800. | Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de copia de nivel superior para el servicio de almacén de imágenes. |
| ClientDownloadTimeout | Tiempo en segundos, el valor predeterminado es 1800. | Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de descarga de nivel superior para el servicio de almacén de imágenes. |
| ClientListTimeout | Tiempo en segundos, el valor predeterminado es 600. | Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de lista de nivel superior para el servicio de almacén de imágenes. |
| ClientDefaultTimeout | Tiempo en segundos, el valor predeterminado es 180. | Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera para todas las solicitudes que no son de carga ni de descarga (por ejemplo, existe; eliminar) al servicio del almacén de imágenes. |

### <a name="section-name-imagestoreclient"></a>Nombre de sección: ImageStoreClient
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| ClientUploadTimeout |Tiempo en segundos, el valor predeterminado es 1800. | Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de carga de nivel superior para el servicio de almacén de imágenes. |
| ClientCopyTimeout | Tiempo en segundos, el valor predeterminado es 1800. | Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de copia de nivel superior para el servicio de almacén de imágenes. |
|ClientDownloadTimeout | Tiempo en segundos, el valor predeterminado es 1800. | Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de descarga de nivel superior para el servicio de almacén de imágenes. |
|ClientListTimeout | Tiempo en segundos, el valor predeterminado es 600. |Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de lista de nivel superior para el servicio de almacén de imágenes. |
|ClientDefaultTimeout | Tiempo en segundos, el valor predeterminado es 180. | Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera para todas las solicitudes que no son de carga ni de descarga (por ejemplo, existe; eliminar) al servicio del almacén de imágenes. |

### <a name="section-name-tokenvalidationservice"></a>Nombre de sección: TokenValidationService
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| Proveedores |Wstring, el valor predeterminado es "DSTS". |Lista separada por comas de proveedores de validación de tokens para habilitar (proveedores válidos son: DSTS; AAD). Actualmente solo se puede habilitar un único proveedor cada vez. |

### <a name="section-name-upgradeorchestrationservice"></a>Nombre de sección: UpgradeOrchestrationService
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| TargetReplicaSetSize |Int, el valor predeterminado es 0. |TargetReplicaSetSize para UpgradeOrchestrationService. |
| MinReplicaSetSize |Int, el valor predeterminado es 0. | MinReplicaSetSize para UpgradeOrchestrationService.
| ReplicaRestartWaitDuration | Tiempo en segundos, el valor predeterminado es 60 minutos.| Especifique el intervalo de tiempo en segundos. ReplicaRestartWaitDuration para UpgradeOrchestrationService. |
| QuorumLossWaitDuration | Tiempo en segundos, el valor predeterminado es MaxValue. | Especifique el intervalo de tiempo en segundos. QuorumLossWaitDuration para UpgradeOrchestrationService. |
| StandByReplicaKeepDuration | Tiempo en segundos, el valor predeterminado es 60*24*7 minutos. | Especifique el intervalo de tiempo en segundos. StandByReplicaKeepDuration para UpgradeOrchestrationService. |
| PlacementConstraints | Wstring, el valor predeterminado es "". | PlacementConstraints para UpgradeOrchestrationService. |
| AutoupgradeEnabled | Bool, el valor predeterminado es true. | Sondeo automático y acción de actualización basados en un archivo de estado de objetivo. |
| UpgradeApprovalRequired | Bool, el valor predeterminado es false. | La configuración para crear actualizaciones de código requiere la aprobación del administrador antes de continuar. |

### <a name="section-name-upgradeservice"></a>Nombre de sección: UpgradeService
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| PlacementConstraints |Wstring, el valor predeterminado es "". |PlacementConstraints para el servicio de actualización. |
| TargetReplicaSetSize | Int, el valor predeterminado es 3. | TargetReplicaSetSize para UpgradeService. |
| MinReplicaSetSize | Int, el valor predeterminado es 2. | MinReplicaSetSize para UpgradeService. |
| CoordinatorType | Wstring, el valor predeterminado es "WUTest".| CoordinatorType para UpgradeService. |
| BaseUrl | Wstring, el valor predeterminado es "". |BaseUrl para UpgradeService. |
| ClusterId | Wstring, el valor predeterminado es "". | ClusterId para UpgradeService. |
| X509StoreName | Wstring, el valor predeterminado es "My".| X509StoreName para UpgradeService. |
| X509StoreLocation | Wstring, el valor predeterminado es "". | X509StoreLocation para UpgradeService. |
| X509FindType | Wstring, el valor predeterminado es "".| X509FindType para UpgradeService. |
| X509FindValue | Wstring, el valor predeterminado es "". | X509FindValue para UpgradeService. |
| X509SecondaryFindValue | Wstring, el valor predeterminado es "". | X509SecondaryFindValue para UpgradeService. |
| OnlyBaseUpgrade | Bool, el valor predeterminado es false. | OnlyBaseUpgrade para UpgradeService. |
| TestCabFolder | Wstring, el valor predeterminado es "". | TestCabFolder para UpgradeService. |

### <a name="section-name-securityclientaccess"></a>Nombre de sección: Security/ClientAccess
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| CreateName |Wstring, el valor predeterminado es "Admin". |Configuración de seguridad para la creación de URI de nomenclatura. |
| DeleteName |Wstring, el valor predeterminado es "Admin". |Configuración de seguridad para la eliminación de URI de nomenclatura. |
| PropertyWriteBatch |Wstring, el valor predeterminado es "Admin". |Configuración de seguridad para operaciones de escritura de la propiedad de nomenclatura. |
| CreateService |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para la creación del servicio. |
| CreateServiceFromTemplate |Wstring, el valor predeterminado es "Admin". |Configuración de seguridad para la creación del servicio desde la plantilla. |
| UpdateService |Wstring, el valor predeterminado es "Admin". |Configuración de seguridad para actualizaciones de servicio. |
| DeleteService  |Wstring, el valor predeterminado es "Admin". |Configuración de seguridad para eliminación de servicio. |
| ProvisionApplicationType |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para aprovisionamiento de tipos de aplicaciones. |
| CreateApplication |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para creación de aplicaciones. |
| DeleteApplication |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para eliminación de aplicaciones. |
| UpgradeApplication |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para iniciar o interrumpir actualizaciones de aplicaciones. |
| RollbackApplicationUpgrade |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para revertir actualizaciones de aplicaciones. |
| UnprovisionApplicationType |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para dejar de aprovisionar tipos de aplicación. |
| MoveNextUpgradeDomain |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para reanudar actualizaciones de aplicaciones con un dominio de actualización explícito. |
| ReportUpgradeHealth |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para reanudar actualizaciones de aplicaciones con el progreso de actualización actual. |
| ReportHealth |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para mantenimiento de informes. |
| ProvisionFabric |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para aprovisionamiento de MSI o del manifiesto de clúster. |
| UpgradeFabric |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para iniciar actualizaciones del clúster. |
| RollbackFabricUpgrade |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para revertir actualizaciones del clúster. |
| UnprovisionFabric |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para dejar de aprovisionar MSI o el manifiesto de clúster. |
| MoveNextFabricUpgradeDomain |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para reanudar actualizaciones del clúster con un dominio de actualización explícito. |
| ReportFabricUpgradeHealth |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para reanudar actualizaciones del clúster con el progreso de actualización actual. |
| StartInfrastructureTask |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para iniciar tareas de infraestructura. |
| FinishInfrastructureTask |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para finalizar tareas de infraestructura. |
| ActivateNode |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para activación de un nodo. |
| DeactivateNode |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para desactivación de un nodo. |
| DeactivateNodesBatch |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para desactivación de varios nodos. |
| RemoveNodeDeactivations |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para revertir la desactivación en varios nodos. |
| GetNodeDeactivationStatus |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para comprobar el estado de desactivación. |
| NodeStateRemoved |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para notificar el estado quitado del nodo. |
| RecoverPartition |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad recuperar una partición. |
| RecoverPartitions |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para recuperar particiones. |
| RecoverServicePartitions |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para recuperar particiones de servicio. |
| RecoverSystemPartitions |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para recuperar particiones de servicio del sistema. |
| ReportFault |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para informes de error. |
| InvokeInfrastructureCommand |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para comandos de administración de tareas de infraestructura. |
| FileContent |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para transferencia de archivos de cliente del almacén de imágenes (externo al clúster). |
| FileDownload |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para inicio de la descarga de archivos de cliente del almacén de imágenes (externo al clúster). |
| InternalList |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para operación de lista de archivos de cliente del almacén de imágenes (interno). |
| Eliminar |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para operación de eliminación del cliente del almacén de imágenes. |
| Cargar |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para operación de carga del cliente del almacén de imágenes. |
| GetStagingLocation |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para recuperación de la ubicación de almacenamiento temporal del cliente del almacén de imágenes. |
| GetStoreLocation |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para recuperación de la ubicación del almacén de clientes del almacén de imágenes. |
| NodeControl |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para inicio, detención y reinicio de nodos. |
| CodePackageControl |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para reiniciar paquetes de código. |
| UnreliableTransportControl |Wstring, el valor predeterminado es "Admin". | Transporte no confiable para agregar y quitar comportamientos. |
| MoveReplicaControl |Wstring, el valor predeterminado es "Admin". | Mueva la réplica. |
| PredeployPackageToNode |Wstring, el valor predeterminado es "Admin". | API previas a la implementación. |
| StartPartitionDataLoss |Wstring, el valor predeterminado es "Admin". | Provoca la pérdida de datos en una partición. |
| StartPartitionQuorumLoss |Wstring, el valor predeterminado es "Admin". | Provoca la pérdida de cuórum en una partición. |
| StartPartitionRestart |Wstring, el valor predeterminado es "Admin". | Reinicia simultáneamente algunas o todas las réplicas de una partición. |
| CancelTestCommand |Wstring, el valor predeterminado es "Admin". | Cancela un comando de prueba específico, si se está ejecutando. |
| StartChaos |Wstring, el valor predeterminado es "Admin". | Inicia Chaos, si no se ha iniciado. |
| StopChaos |Wstring, el valor predeterminado es "Admin". | Detiene Chaos, si se ha iniciado. |
| StartNodeTransition |Wstring, el valor predeterminado es "Admin". | Configuración de seguridad para iniciar una transición de nodo. |
| StartClusterConfigurationUpgrade |Wstring, el valor predeterminado es "Admin". | Produce StartClusterConfigurationUpgrade en una partición. |
| GetUpgradesPendingApproval |Wstring, el valor predeterminado es "Admin". | Produce GetUpgradesPendingApproval en una partición. |
| StartApprovedUpgrades |Wstring, el valor predeterminado es "Admin". | Produce StartApprovedUpgrades en una partición. |
| Ping |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para pings de cliente. |
| Consultar |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para consultas. |
| NameExists |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para comprobaciones de existencia de URI de nomenclatura. |
| EnumerateSubnames |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para enumeración de URI de nomenclatura. |
| EnumerateProperties |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para enumeración de propiedad de nomenclatura. |
| PropertyReadBatch |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para operaciones de lectura de propiedad de nomenclatura. |
| GetServiceDescription |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para notificaciones de servicio de sondeo largo y descripciones de servicio de lectura. |
| ResolveService |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para resolución de servicio basada en reclamaciones. |
| ResolveNameOwner |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para resolver propietario de URI de nomenclatura. |
| ResolvePartition |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para resolver servicios del sistema. |
| ServiceNotifications |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para notificaciones del servicio basadas en eventos. |
| PrefixResolveService |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para resolución de prefijo de servicio basada en reclamaciones. |
| GetUpgradeStatus |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para sondeo de estado de actualización de aplicaciones. |
| GetFabricUpgradeStatus |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para sondeo de estado de actualización del clúster. |
| InvokeInfrastructureQuery |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para consultar tareas de infraestructura. |
| Enumerar |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para operación de lista de archivos de cliente del almacén de imágenes. |
| ResetPartitionLoad |Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para restablecer una carga para una unidad de conmutación por error. |
| ToggleVerboseServicePlacementHealthReporting | Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para alternar informes de mantenimiento detallados de ubicación de servicio. |
| GetPartitionDataLossProgress | Wstring, el valor predeterminado es "Admin"\.|\|User" | Captura el progreso de una llamada de API para invocar la pérdida de datos. |
| GetPartitionQuorumLossProgress | Wstring, el valor predeterminado es "Admin"\.|\|User" | Captura el progreso de una llamada de API para invocar la pérdida de cuórum. |
| GetPartitionRestartProgress | Wstring, el valor predeterminado es "Admin"\.|\|User" | Captura el progreso de una llamada de API para el reinicio de una partición. |
| GetChaosReport | Wstring, el valor predeterminado es "Admin"\.|\|User" | Captura el estado de Chaos dentro de un intervalo de tiempo determinado. |
| GetNodeTransitionProgress | Wstring, el valor predeterminado es "Admin"\.|\|User" | Configuración de seguridad para obtener el progreso en un comando de transición de nodo. |
| GetClusterConfigurationUpgradeStatus | Wstring, el valor predeterminado es "Admin"\.|\|User" | Produce GetClusterConfigurationUpgradeStatus en una partición. |
| GetClusterConfiguration | Wstring, el valor predeterminado es "Admin"\.|\|User" | Produce GetClusterConfiguration en una partición. |

### <a name="section-name-reconfigurationagent"></a>Nombre de sección: ReconfigurationAgent
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| ApplicationUpgradeMaxReplicaCloseDuration | Tiempo en segundos, el valor predeterminado es 900. |Especifique el intervalo de tiempo en segundos. La duración que espera el sistema antes de terminar los hosts de servicio que tienen réplicas que están detenidas al cerrarse. |
| ServiceApiHealthDuration | Tiempo en segundos, el valor predeterminado es 30 minutos. | Especifique el intervalo de tiempo en segundos. ServiceApiHealthDuration define el tiempo que se espera a que una API se ejecute antes de que se notifique como incorrecta. |
| ServiceReconfigurationApiHealthDuration | Tiempo en segundos, el valor predeterminado es 30. | Especifique el intervalo de tiempo en segundos. ServiceReconfigurationApiHealthDuration define el tiempo antes de que un servicio en la reconfiguración se notifique como incorrecto. |
| PeriodicApiSlowTraceInterval | Tiempo en segundos, el valor predeterminado es 5 minutos. | Especifique el intervalo de tiempo en segundos. PeriodicApiSlowTraceInterval define el intervalo por encima del cual el monitor de API vuelve a rastrear las llamadas de API lentas. |
| NodeDeactivationMaxReplicaCloseDuration | Tiempo en segundos, el valor predeterminado es 900. | Especifique el intervalo de tiempo en segundos. Tiempo máximo de espera antes de terminar un host de servicio que está bloqueando la desactivación de nodos. |
| FabricUpgradeMaxReplicaCloseDuration | Tiempo en segundos, el valor predeterminado es 900. | Especifique el intervalo de tiempo en segundos. La duración máxima que espera RA antes de terminar el host de servicio de la réplica que no se está cerrando. |
| IsDeactivationInfoEnabled | Bool, el valor predeterminado es true. | Determina si RA usará la información de desactivación para realizar la reelección principal. En el caso de nuevos clústeres, esta configuración se debe establecer en true; para clústeres existentes que se van a actualizar, consulte las notas de la versión sobre cómo habilitar este valor. |

### <a name="section-name-placementandloadbalancing"></a>Nombre de sección: PlacementAndLoadBalancing
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| TraceCRMReasons |Bool, el valor predeterminado es true. |Especifica si se rastrearán los motivos de los movimientos emitidos por CRM al canal de eventos operativos. |
| ValidatePlacementConstraint | Bool, el valor predeterminado es true. | Especifica si la expresión PlacementConstraint de un servicio se valida o no cuando se actualiza la descripción de un servicio. |
| PlacementConstraintValidationCacheSize | Int, el valor predeterminado es 10 000. | Limita el tamaño de la tabla usada para la validación rápida y el almacenamiento en caché de expresiones de restricciones de ubicación. |
|VerboseHealthReportLimit | Int, el valor predeterminado es 20. | Define el número de veces que una réplica tiene que ir sin colocar antes de que se notifique una advertencia de mantenimiento para ella (si está habilitado el informe de mantenimiento detallado). |
|ConstraintViolationHealthReportLimit | Int, el valor predeterminado es 50. | Define el número de veces que la réplica que infringe la restricción debe estar sin fijar de forma persistente antes de que se realicen diagnósticos y se emitan informes de mantenimiento. |
|DetailedConstraintViolationHealthReportLimit | Int, el valor predeterminado es 200. | Define el número de veces que la réplica que infringe la restricción debe estar sin fijar de forma persistente antes de que se realicen diagnósticos y se emitan informes de mantenimiento detallados. |
|DetailedVerboseHealthReportLimit | Int, el valor predeterminado es 200. | Define el número de veces que una réplica sin colocar tiene que estar sin colocar de forma persistente antes de que se emitan informes de mantenimiento detallados. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, el valor predeterminado es 20. | Define el número de veces consecutivas que los movimientos emitidos por ResourceBalancer se descartan antes de que se realicen diagnósticos y se emitan advertencias de mantenimiento. Negativo: no se emiten advertencias bajo esta condición. |
|DetailedNodeListLimit | Int, el valor predeterminado es 15. | Define el número de nodos por restricción que se incluirán antes del truncamiento en los informes de réplicas sin colocar. |
|DetailedPartitionListLimit | Int, el valor predeterminado es 15. | Define el número de particiones por entrada de diagnóstico que se incluirán para una restricción antes del truncamiento en Diagnostics. |
|DetailedDiagnosticsInfoListLimit | Int, el valor predeterminado es 15. | Define el número de entradas de diagnóstico (con información detallada) que se incluirán por restricción antes del truncamiento en Diagnostics.|
|PLBRefreshGap | Tiempo en segundos, el valor predeterminado es 1. | Especifique el intervalo de tiempo en segundos. Define la cantidad mínima de tiempo que debe transcurrir antes de que PLB actualice el estado de nuevo. |
|MinPlacementInterval | Tiempo en segundos, el valor predeterminado es 1. | Especifique el intervalo de tiempo en segundos. Define la cantidad mínima de tiempo que debe transcurrir antes de dos rondas de selección de ubicación consecutivas. |
|MinConstraintCheckInterval | Tiempo en segundos, el valor predeterminado es 1. | Especifique el intervalo de tiempo en segundos. Define la cantidad mínima de tiempo que debe transcurrir antes de dos rondas de comprobación de restricciones consecutivas. |
|MinLoadBalancingInterval | Tiempo en segundos, el valor predeterminado es 5. | Especifique el intervalo de tiempo en segundos. Define la cantidad mínima de tiempo que debe transcurrir antes de dos rondas de equilibrio de carga consecutivas. |
|BalancingDelayAfterNodeDown | Tiempo en segundos, el valor predeterminado es 120. |Especifique el intervalo de tiempo en segundos. No inicie actividades de equilibrio dentro de este período después de un evento de inactividad de nodos. |
|BalancingDelayAfterNewNode | Tiempo en segundos, el valor predeterminado es 120. |Especifique el intervalo de tiempo en segundos. No inicie actividades de equilibrio dentro de este período después de agregar un nuevo nodo. |
|ConstraintFixPartialDelayAfterNodeDown | Tiempo en segundos, el valor predeterminado es 120. | Especifique el intervalo de tiempo en segundos. No resuelva infracciones FaultDomain y UpgradeDomain dentro de este período después de un evento de inactividad de nodos. |
|ConstraintFixPartialDelayAfterNewNode | Tiempo en segundos, el valor predeterminado es 120. | Especifique el intervalo de tiempo en segundos. No resuelva infracciones de restricciones FaultDomain y UpgradeDomain dentro de este período después de agregar un nuevo nodo. |
|GlobalMovementThrottleThreshold | Uint, el valor predeterminado es 1000. | Número máximo de movimientos permitidos en la fase de equilibrio en el intervalo pasado indicado por GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForPlacement | Uint, el valor predeterminado es 0. | Número máximo de movimientos permitidos en la fase de selección de ubicación en el intervalo pasado indicado por GlobalMovementThrottleCountingInterval.&0; indica sin límite.|
|GlobalMovementThrottleThresholdForBalancing | Uint, el valor predeterminado es 0. | Número máximo de movimientos permitidos en la fase de equilibrio en el intervalo pasado indicado por GlobalMovementThrottleCountingInterval. 0 indica sin límite. |
|GlobalMovementThrottleCountingInterval | Tiempo en segundos, el valor predeterminado es 600. | Especifique el intervalo de tiempo en segundos. Indique la longitud del intervalo pasado para rastrear movimientos de réplicas por dominio (se usa en combinación con GlobalMovementThrottleThreshold). Puede establecerse en 0 para omitir por completo la limitación global. |
|MovementPerPartitionThrottleThreshold | Uint, el valor predeterminado es 50. | No se producen movimientos relacionados con el equilibrio en una partición si el número de estos movimientos para réplicas de esa partición ha alcanzado o superado el valor de MovementPerFailoverUnitThrottleThreshold en el intervalo pasado indicado por MovementPerPartitionThrottleCountingInterval. |
|MovementPerPartitionThrottleCountingInterval | Tiempo en segundos, el valor predeterminado es 600. | Especifique el intervalo de tiempo en segundos. Indique la longitud del intervalo pasado para rastrear movimientos de réplica para cada partición (se usa en combinación con MovementPerPartitionThrottleThreshold). |
|PlacementSearchTimeout | Tiempo en segundos, el valor predeterminado es 0.5. | Especifique el intervalo de tiempo en segundos. Al ubicar servicios, busque como máximo este período antes de devolver un resultado. |
|UseMoveCostReports | Bool, el valor predeterminado es false. | Indica a LB que ignore el elemento de coste de la función de puntuación. Esto da lugar a un número posiblemente grande de movimientos para una ubicación mejor equilibrada. |
|PreventTransientOvercommit | Bool, el valor predeterminado es false. | Determina si PLB cuenta inmediatamente con los recursos que liberarán los movimientos iniciados. De forma predeterminada, PLB puede iniciar movimientos dentro y fuera en el mismo nodo, lo que puede generar confirmaciones en exceso transitorias. Si establece este parámetro en true impedirá esta clase de confirmaciones en exceso y se deshabilitará la desfragmentación a petición (también conocida como placementWithMove). |
|InBuildThrottlingEnabled | Bool, el valor predeterminado es false. | Determina si está habilitada la limitación integrada. |
|InBuildThrottlingAssociatedMetric | Wstring, el valor predeterminado es "". | El nombre de métrica asociado de esta limitación. |
|InBuildThrottlingGlobalMaxValue | Int, el valor predeterminado es 0. |El número máximo de réplicas integradas que se permite a nivel global. |
|SwapPrimaryThrottlingEnabled | Bool, el valor predeterminado es false.| Determina si está habilitada la limitación principal de intercambio. |
|SwapPrimaryThrottlingAssociatedMetric | Wstring, el valor predeterminado es "".| El nombre de métrica asociado de esta limitación. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, el valor predeterminado es 0. | El número máximo de réplicas principales de intercambio que se permiten a nivel global. |
|PlacementConstraintPriority | Int, el valor predeterminado es 0. | Determina la prioridad de la restricción de ubicación: 0: máxima; 1: mínima; negativo: ignorar. |
|PreferredLocationConstraintPriority | Int, el valor predeterminado es 2.| Determina la prioridad de la restricción de ubicación preferida: 0: máxima; 1: mínima; 2: optimización; negativo: ignorar |
|CapacityConstraintPriority | Int, el valor predeterminado es 0. | Determina la prioridad de la restricción de capacidad: 0: máxima; 1: mínima; negativo: ignorar. |
|AffinityConstraintPriority | Int, el valor predeterminado es 0. | Determina la prioridad de la restricción de afinidad: 0: máxima; 1: mínima; negativo: ignorar. |
|FaultDomainConstraintPriority | Int, el valor predeterminado es 0. | Determina la prioridad de la restricción del dominio de error: 0: máxima; 1: mínima; negativo: ignorar. |
|UpgradeDomainConstraintPriority | Int, el valor predeterminado es 1.| Determina la prioridad de la restricción del dominio de actualización: 0: máxima; 1: mínima; negativo: ignorar. |
|ScaleoutCountConstraintPriority | Int, el valor predeterminado es 0. | Determina la prioridad de la restricción de recuento de escalado horizontal: 0: máxima; 1: mínima; negativo: ignorar. |
|ApplicationCapacityConstraintPriority | Int, el valor predeterminado es 0. | Determina la prioridad de la restricción de capacidad: 0: máxima; 1: mínima; negativo: ignorar. |
|MoveParentToFixAffinityViolation | Bool, el valor predeterminado es false. | Valor que determina si se pueden mover las réplicas primarias para corregir restricciones de afinidad.|
|MoveExistingReplicaForPlacement | Bool, el valor predeterminado es true. |Valor que determina si se deben mover las réplicas existentes durante la selección de ubicación. |
|UseSeparateSecondaryLoad | Bool, el valor predeterminado es true. | Valor que determina si se deben usar cargas secundarias diferentes. |
|PlaceChildWithoutParent | Bool, el valor predeterminado es true. | Valor que determina si puede colocarse la réplica de servicio secundaria si no hay ninguna réplica principal activa. |
|PartiallyPlaceServices | Bool, el valor predeterminado es true. | Determina si todas las réplicas de servicio del clúster se colocarán "todo o nada" dados los nodos adecuados limitados para ellas.|
|InterruptBalancingForAllFailoverUnitUpdates | Bool, el valor predeterminado es false. | Determina si cualquier tipo de actualización de las unidades de conmutación por error debe interrumpir la ejecución rápida o lenta del equilibrio. Si se especifica "false", la ejecución del equilibrio se interrumpirá si FailoverUnit: se crea o elimina, tiene réplicas que faltan, ha cambiado la ubicación de la réplica principal o ha cambiado el número de réplicas. La ejecución del equilibrio NO se interrumpirá en otros casos: si FailoverUnit: tiene réplicas adicionales, ha cambiado cualquier marca de réplica, ha cambiado solo la versión de partición o en cualquier otro caso. |

### <a name="section-name-security"></a>Nombre de sección: Seguridad
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| ClusterProtectionLevel |Ninguno o EncryptAndSign |Ninguno (valor predeterminado) para clústeres no seguros, EncryptAndSign para clústeres seguros. |

### <a name="section-name-hosting"></a>Nombre de sección: Hospedaje
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| ServiceTypeRegistrationTimeout |Tiempo en segundos, el valor predeterminado es 300. |Tiempo máximo permitido para que se registre ServiceType con Fabric. |
| ServiceTypeDisableFailureThreshold |Número entero, el valor predeterminado es 1. |Este es el umbral del recuento de errores después del cual se notifica a FailoverManager (FM) que deshabilite el tipo de servicio en ese nodo y pruebe un nodo diferente para la ubicación. |
| ActivationRetryBackoffInterval |Tiempo en segundos, el valor predeterminado es 5. |Intervalo de retroceso en cada error de activación; con cada error de activación continuo, el sistema vuelve a intentar la activación hasta el valor de MaxActivationFailureCount. El intervalo de reintento en cada intento es un producto del error de activación continua y el intervalo de retroceso de activación. |
| ActivationMaxRetryInterval |Tiempo en segundos, el valor predeterminado es 300. |Con cada error de activación continuo, el sistema vuelve a intentar la activación hasta el valor de ActivationMaxFailureCount. ActivationMaxRetryInterval especifica el intervalo de tiempo de espera antes de un reintento después de cada error de activación. |
| ActivationMaxFailureCount |Número entero, el valor predeterminado es 10. |Número de veces que los reintentos del sistema no lograron la activación antes de desistir. |

### <a name="section-name-failovermanager"></a>Nombre de sección: FailoverManager
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| PeriodicLoadPersistInterval |Tiempo en segundos, el valor predeterminado es 10 |Determina la frecuencia con la que FM busca nuevos informes de carga. |

### <a name="section-name-federation"></a>Nombre de sección: Federación
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| LeaseDuration |Tiempo en segundos, el valor predeterminado es 30. |Duración de una concesión entre un nodo y sus vecinos. |
| LeaseDurationAcrossFaultDomain |Tiempo en segundos, el valor predeterminado es 30. |Duración de una concesión entre un nodo y sus vecinos entre dominios de error. |

### <a name="section-name-clustermanager"></a>Nombre de sección: ClusterManager
| **Parámetro** | **Valores permitidos** | **Orientación o breve descripción** |
| --- | --- | --- |
| UpgradeStatusPollInterval |Tiempo en segundos, el valor predeterminado es 60. |La frecuencia de sondeo del estado de actualización de la aplicación. Este valor determina la frecuencia de actualización de cualquier llamada a GetApplicationUpgradeProgress. |
| UpgradeHealthCheckInterval |Tiempo en segundos, el valor predeterminado es 60. |Se comprueba la frecuencia del estado de mantenimiento durante las actualizaciones de una aplicación supervisada. |
| FabricUpgradeStatusPollInterval |Tiempo en segundos, el valor predeterminado es 60. |La frecuencia de sondeo del estado de Service Fabric. Este valor determina la frecuencia de actualización de cualquier llamada a GetFabricUpgradeProgress. |
| FabricUpgradeHealthCheckInterval |Tiempo en segundos, el valor predeterminado es 60. |Se comprueba la frecuencia del estado de mantenimiento durante una actualización de Fabric supervisada. |
|InfrastructureTaskProcessingInterval | Tiempo en segundos, el valor predeterminado es 10 |Especifique el intervalo de tiempo en segundos. El intervalo de procesamiento usado por la máquina de estado de procesamiento de tareas de infraestructura. |
|TargetReplicaSetSize |Int, el valor predeterminado es 7. |TargetReplicaSetSize para ClusterManager. |
|MinReplicaSetSize |Int, el valor predeterminado es 3. |MinReplicaSetSize para ClusterManager. |
|ReplicaRestartWaitDuration |Tiempo en segundos, el valor predeterminado es (60.0 * 30).|Especifique el intervalo de tiempo en segundos. ReplicaRestartWaitDuration para ClusterManager. |
|QuorumLossWaitDuration |Tiempo en segundos, el valor predeterminado es MaxValue. | Especifique el intervalo de tiempo en segundos. QuorumLossWaitDuration para ClusterManager. |
|StandByReplicaKeepDuration | Tiempo en segundos, el valor predeterminado es (3600.0 * 2).|Especifique el intervalo de tiempo en segundos. StandByReplicaKeepDuration para ClusterManager. |
|PlacementConstraints | Wstring, el valor predeterminado es "". |PlacementConstraints para ClusterManager. |
|SkipRollbackUpdateDefaultService | Bool, el valor predeterminado es false. |El CM omitirá la reversión de los servicios predeterminados actualizados durante la reversión de la actualización de aplicaciones. |
|EnableDefaultServicesUpgrade | Bool, el valor predeterminado es false. |Habilita la actualización de servicios predeterminados durante la actualización de aplicaciones. Las descripciones de servicios predeterminados se deben sobrescribir después de la actualización. |
|InfrastructureTaskHealthCheckWaitDuration |Tiempo en segundos, el valor predeterminado es 0.| Especifique el intervalo de tiempo en segundos. La cantidad de tiempo de espera antes de iniciar las comprobaciones de mantenimiento después del posprocesamiento de una tarea de infraestructura. |
|InfrastructureTaskHealthCheckStableDuration | Tiempo en segundos, el valor predeterminado es 0.| Especifique el intervalo de tiempo en segundos. La cantidad de tiempo que se observarán comprobaciones de mantenimiento pasadas consecutivas antes de que el posprocesamiento de una tarea de infraestructura finalice correctamente. Al observarse una comprobación de mantenimiento con error, se restablecerá este temporizador. |
|InfrastructureTaskHealthCheckRetryTimeout | Tiempo en segundos, el valor predeterminado es 60. |Especifique el intervalo de tiempo en segundos. La cantidad de tiempo que se dedicará a reintentar las comprobaciones de mantenimiento con error durante el posprocesamiento de una tarea de infraestructura. Al observarse una comprobación de mantenimiento pasada, se restablecerá este temporizador. |
|ImageBuilderTimeoutBuffer |Tiempo en segundos, el valor predeterminado es 3. |Especifique el intervalo de tiempo en segundos. La cantidad de tiempo que se permitirá que se devuelvan errores de tiempo de espera específicos de Image Builder al cliente. Si este búfer es demasiado pequeño, el cliente agota el tiempo de espera antes que el servidor y obtiene un error genérico de tiempo de espera. |
|MinOperationTimeout | Tiempo en segundos, el valor predeterminado es 60. |Especifique el intervalo de tiempo en segundos. El tiempo de espera mínimo global para procesar internamente las operaciones en ClusterManager. |
|MaxOperationTimeout |Tiempo en segundos, el valor predeterminado es MaxValue. | Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo global para procesar internamente las operaciones en ClusterManager. |
|MaxTimeoutRetryBuffer | Tiempo en segundos, el valor predeterminado es 600. |Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo de funcionamiento cuando se realizan reintentos internos debido a que el tiempo de espera es <Original Timeout>  +  <MaxTimeoutRetryBuffer>. El tiempo de espera adicional se agrega en incrementos de MinOperationTimeout. |
|MaxCommunicationTimeout |Tiempo en segundos, el valor predeterminado es 600. |Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo para la comunicación interna entre ClusterManager y otros servicios del sistema (es decir, el Servicio de nomenclatura, el Administrador de conmutación por error, etc.). Este tiempo de espera debe ser inferior al valor global de MaxOperationTimeout (ya que podría haber muchas comunicaciones entre los componentes del sistema para cada operación de cliente). |
|MaxDataMigrationTimeout |Tiempo en segundos, el valor predeterminado es 600. |Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo para operaciones de recuperación de migración de datos después de que ha tenido lugar una actualización de Fabric. |
|MaxOperationRetryDelay |Tiempo en segundos, el valor predeterminado es 5.| Especifique el intervalo de tiempo en segundos. El retraso máximo de reintentos internos cuando se producen errores. |
|ReplicaSetCheckTimeoutRollbackOverride |Tiempo en segundos, el valor predeterminado es 1200. | Especifique el intervalo de tiempo en segundos. Si ReplicaSetCheckTimeout se establece en el valor máximo de DWORD, entonces se reemplaza por el valor de esta configuración de cara a la reversión. El valor usado para la puesta al día nunca se reemplaza. |
|ImageBuilderJobQueueThrottle |Int, el valor predeterminado es 10. |Limitación de recuento de subprocesos para la cola de trabajo del proxy de Image Builder en solicitudes de aplicación. |

## <a name="next-steps"></a>Pasos siguientes
Lea estos artículos para más información sobre la administración de clúster:

[Agregar o quitar certificados del clúster de Azure ](service-fabric-cluster-security-update-certs-azure.md) 


