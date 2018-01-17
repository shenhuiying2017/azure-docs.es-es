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
ms.date: 1/09/2018
ms.author: chackdan
ms.openlocfilehash: 2e609b205c32d2ea5ca58586e9f8ba9623ef7580
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>Personalización de la configuración de un clúster de Service Fabric y una directiva de actualización de Fabric
En este documento se explica cómo personalizar las diversas opciones de configuración de Fabric y la directiva de actualización de Fabric para el clúster de Service Fabric. Puede personalizarlos en [Azure Portal](https://portal.azure.com) o mediante una plantilla de Azure Resource Manager.

> [!NOTE]
> No todas las opciones están disponibles en el portal. En caso de que un ajuste que se muestra a continuación no esté disponible a través del portal, personalícelo mediante una plantilla de Azure Resource Manager.
> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personalización de la configuración de clústeres mediante plantillas de Resource Manager
Los pasos siguientes muestran cómo agregar un nuevo ajuste *MaxDiskQuotaInMB* a la sección *Diagnostics*.

1. Vaya a https://resources.azure.com.
2. Vaya a su suscripción expandiendo **subscriptions** (suscripciones) -> **resource groups**(grupos de recursos) -> **Microsoft.ServiceFabric** -> **\< el nombre del clúster>**.
3. En la esquina superior derecha, seleccione **Read/Write** (Lectura y escritura).
4. Seleccione **Edit** (Editar), actualice el elemento JSON `fabricSettings` y agregue un nuevo elemento:

```
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

La siguiente es una lista de la configuración de Fabric que puede personalizar, organizada por sección.

### <a name="section-name-diagnostics"></a>Nombre de sección: Diagnostics
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| ConsumerInstances |string | Dinámica |La lista de instancias de consumidor de DCA. |
| ProducerInstances |string | Dinámica |La lista de instancias de productor de DCA. |
| AppEtwTraceDeletionAgeInDays |Int, el valor predeterminado es 3. | Dinámica |Número de días transcurridos los cuales se eliminarán los archivos ETL antiguos que contienen seguimientos de ETW de aplicación. |
| AppDiagnosticStoreAccessRequiresImpersonation |Bool, el valor predeterminado es true. | Dinámica |Si se requerirá o no suplantación para acceder a los almacenes de diagnóstico en nombre de la aplicación. |
| MaxDiskQuotaInMB |Int, el valor predeterminado es 65 536. | Dinámica |Cuota de disco en MB para archivos de registro de Windows Fabric. |
| DiskFullSafetySpaceInMB |Int, el valor predeterminado es 1024. | Dinámica |Espacio en disco restante en MB para proteger contra el uso por DCA. |
| ApplicationLogsFormatVersion |Int, el valor predeterminado es 0. | Dinámica |Versión del formato de registros de aplicación. Los valores admitidos son 0 y 1. La versión 1 incluye más campos del registro de eventos de ETW que la versión 0. |
| ClusterId |string | Dinámica |El id. único del clúster. Se genera cuando se crea el clúster. |
| EnableTelemetry |Bool, el valor predeterminado es true. | Dinámica |Indica si se habilitará o deshabilitará la telemetría. |
| EnableCircularTraceSession |Bool, el valor predeterminado es false. | estática |La marca indica si se deben usar sesiones de seguimiento circulares. |

### <a name="section-name-traceetw"></a>Nombre de sección: Trace/Etw
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| Nivel |Int, el valor predeterminado es 4. | Dinámica |El nivel de seguimiento de eventos puede adoptar los valores 1, 2, 3, 4. Para que sea admitido, debe mantener el nivel de seguimiento en 4. |

### <a name="section-name-performancecounterlocalstore"></a>Nombre de sección: PerformanceCounterLocalStore
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| IsEnabled |Bool, el valor predeterminado es true. | Dinámica |La marca indica si está habilitada la colección de contadores de rendimiento en el nodo local. |
| SamplingIntervalInSeconds |Int, el valor predeterminado es 60. | Dinámica |Intervalo de muestreo para los contadores de rendimiento que se recolectarán. |
| Counters |string | Dinámica |Lista separada por comas de los contadores de rendimiento que se recolectarán. |
| MaxCounterBinaryFileSizeInMB |Int, el valor predeterminado es 1. | Dinámica |Tamaño máximo (en MB) de cada archivo binario de contador de rendimiento. |
| NewCounterBinaryFileCreationIntervalInMinutes |Int, el valor predeterminado es 10. | Dinámica |Intervalo máximo (en segundos) después del cual se crea un nuevo archivo binario de contador de rendimiento. |

### <a name="section-name-setup"></a>Nombre de sección: Setup
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| FabricDataRoot |string | No permitida |Directorio raíz de datos de Service Fabric. El valor predeterminado para Azure es d:\svcfab. |
| FabricLogRoot |string | No permitida |Directorio raíz del registro de Service Fabric. Aquí es donde se colocan los seguimientos y registros de SF. |
| ServiceRunAsAccountName |string | No permitida |El nombre de la cuenta con el que se ejecuta el servicio Fabric Host. |
| SkipFirewallConfiguration |Bool, el valor predeterminado es false. | No permitida |Especifica si el sistema debe establecer o no la configuración de firewall. Solo aplicable si usa Firewall de Windows. Si usa firewalls de terceros, debe abrir los puertos para que los usen el sistema y las aplicaciones. |
|NodesToBeRemoved|string, el valor predeterminado es "".| Dinámica |Nodos que deben quitarse como parte de la actualización de la configuración. (Solo para implementaciones independientes.)|
|ContainerNetworkSetup|bool, el valor predeterminado es FALSE| estática |Establece si se debe configurar una red de contenedores.|
|ContainerNetworkName|string, el valor predeterminado es L""| estática |Nombre de red que se usará al configurar una red de contenedores.|

### <a name="section-name-transactionalreplicator"></a>Nombre de sección: TransactionalReplicator
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| MaxCopyQueueSize |Uint, el valor predeterminado es 16384. | estática |Es el valor máximo y define el tamaño inicial de la cola que mantiene operaciones de replicación. Tenga en cuenta que debe ser una potencia de 2. Si, durante el tiempo de ejecución, la cola crece y alcanza este tamaño, se limitará la operación entre los replicadores principal y secundario. |
| BatchAcknowledgementInterval | Tiempo en segundos, el valor predeterminado es 0,015. | estática | Especifique el intervalo de tiempo en segundos. Determina la cantidad de tiempo que el replicador espera después de recibir una operación, antes de devolver una confirmación. Las confirmaciones de otras operaciones recibidas durante este período de tiempo se devolverán en un solo mensaje-> de forma que se reduce el tráfico de red pero también posiblemente el rendimiento del replicador. |
| MaxReplicationMessageSize |Uint, el valor predeterminado es 52 428 800. | estática | Tamaño máximo de mensaje de las operaciones de replicación. El valor predeterminado es 50 MB. |
| ReplicatorAddress |string, el valor predeterminado es "localhost:0". | estática | El punto de conexión en forma de cadena -'IP:Port' que usa el replicador de Windows Fabric para establecer conexiones con otras réplicas para enviar o recibir operaciones. |
| InitialPrimaryReplicationQueueSize |Uint, el valor predeterminado es 64. | estática |Este valor define el tamaño inicial de la cola que mantiene las operaciones de replicación en el replicador principal. Tenga en cuenta que debe ser una potencia de 2.|
| MaxPrimaryReplicationQueueSize |Uint, el valor predeterminado es 8192. | estática |Es el número máximo de operaciones que podrían existir en la cola de replicación principal. Tenga en cuenta que debe ser una potencia de 2. |
| MaxPrimaryReplicationQueueMemorySize |Uint, el valor predeterminado es 0. | estática |Es el valor máximo de la cola de replicación principal en bytes. |
| InitialSecondaryReplicationQueueSize |Uint, el valor predeterminado es 64. | estática |Este valor define el tamaño inicial de la cola que mantiene las operaciones de replicación en el replicador secundario. Tenga en cuenta que debe ser una potencia de 2. |
| MaxSecondaryReplicationQueueSize |Uint, el valor predeterminado es 16384. | estática |Es el número máximo de operaciones que podrían existir en la cola de replicación secundaria. Tenga en cuenta que debe ser una potencia de 2. |
| MaxSecondaryReplicationQueueMemorySize |Uint, el valor predeterminado es 0. | estática |Es el valor máximo de la cola de replicación secundaria en bytes. |
| SecondaryClearAcknowledgedOperations |Bool, el valor predeterminado es false. | estática |Controla si las operaciones en el replicador secundario se borran una vez que se confirman en el principal (se vacían en el disco). La configuración de este valor en TRUE puede dar lugar a lecturas de disco adicionales en el nuevo replicador, al tiempo que se actualizan las réplicas después de una conmutación por error. |
| MaxMetadataSizeInKB |Int, el valor predeterminado es 4. |No permitida|Tamaño máximo de los metadatos de la secuencia de registro. |
| MaxRecordSizeInKB |Uint, el valor predeterminado es 1024. |No permitida| Tamaño máximo de un registro de secuencia de registro. |
| CheckpointThresholdInMB |Int, el valor predeterminado es 50. |estática|Cuando el uso del registro supera este valor, se inicia un punto de control. |
| MaxAccumulatedBackupLogSizeInMB |Int, el valor predeterminado es 800. |estática|El tamaño máximo acumulado (en MB) de los registros de copia de seguridad de una cadena de registros de copia de seguridad determinada. Las solicitudes de copia de seguridad incremental no se realizarán correctamente si esta genera un registro de copia de seguridad que provoque la acumulación de este tipo de elemento, ya que la copia de seguridad completa tendrá un tamaño superior. En tales casos, el usuario tiene que realizar una copia de seguridad completa. |
| MaxWriteQueueDepthInKB |Int, el valor predeterminado es 0. |No permitida| Valor entero para la profundidad máxima de la cola de escritura que puede usar el registrador de núcleos, especificado en kilobytes para el registro asociado con esta réplica. Este valor es el número máximo de bytes que pueden estar pendientes durante las actualizaciones del registrador de núcleos. Puede ser 0 para que el registrador de núcleos calcule un valor adecuado, o un múltiplo de 4. |
| SharedLogId |string |No permitida|Identificador de registro compartido. Es un GUID y debe ser único para cada registro compartido. |
| SharedLogPath |string |No permitida|Ruta de acceso al registro compartido. Si este valor está vacío, se usa el registro compartido predeterminado. |
| SlowApiMonitoringDuration |Tiempo en segundos, el valor predeterminado es 300. |estática| Especifique la duración de la API antes de que se desencadene el evento de mantenimiento de advertencia.|
| MinLogSizeInMB |Int, el valor predeterminado es 0. |estática|Tamaño mínimo del registro transaccional. El registro no podrá truncarse a un tamaño inferior a este valor. 0 indica que el replicador determinará el tamaño de registro mínimo según otros valores de configuración. Al aumentar este valor, incrementa la posibilidad de hacer copias parciales y copias de seguridad incrementales, ya que se reducen las posibilidades de que se trunquen registros pertinentes. |

### <a name="section-name-fabricclient"></a>Nombre de sección: FabricClient
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| NodeAddresses |string, el valor predeterminado es "". |estática|Una colección de direcciones (cadenas de conexión) en diferentes nodos que se pueden usar para comunicarse con el servicio de nomenclatura. Inicialmente el cliente se conecta mediante la selección de una de las direcciones de manera aleatoria. Si se proporciona más de una cadena de conexión y la conexión no se establece por un error de comunicación o de tiempo de espera, el cliente cambia al uso de la siguiente dirección de manera secuencial. Consulte la sección de reintentos de direcciones del servicio de nomenclatura para más información sobre la semántica de reintentos. |
| ConnectionInitializationTimeout |Tiempo en segundos, el valor predeterminado es 2. |Dinámica|Especifique el intervalo de tiempo en segundos. El intervalo de tiempo de espera de conexión para cada vez que el cliente intenta abrir una conexión a la puerta de enlace. |
| PartitionLocationCacheLimit |Int, el valor predeterminado es 100 000. |estática|Número de particiones almacenadas en caché para la resolución del servicio (se establece en 0 para un número ilimitado). |
| ServiceChangePollInterval |Tiempo en segundos, el valor predeterminado es 120. |Dinámica|Especifique el intervalo de tiempo en segundos. El intervalo entre sondeos consecutivos del servicio cambia del cliente a la puerta de enlace para las devoluciones de llamada registradas de notificaciones de cambio de servicio. |
| KeepAliveIntervalInSeconds |Int, el valor predeterminado es 20. |estática|El intervalo dentro del cual el transporte de FabricClient envía mensajes persistentes a la puerta de enlace. Para 0; keepAlive está deshabilitado. Debe ser un valor positivo. |
| HealthOperationTimeout. |Tiempo en segundos, el valor predeterminado es 120. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera de un mensaje de informe enviado al administrador de mantenimiento. |
| HealthReportSendInterval. |Tiempo en segundos, el valor predeterminado es 30. |Dinámica|Especifique el intervalo de tiempo en segundos. El intervalo dentro del cual el componente de informes envía informes de mantenimiento acumulados al administrador de mantenimiento. |
| HealthReportRetrySendInterval |Tiempo en segundos, el valor predeterminado es 30. |Dinámica|Especifique el intervalo de tiempo en segundos. El intervalo dentro del cual el componente de informes reenvía informes de mantenimiento acumulados al administrador de mantenimiento. |
| RetryBackoffInterval |Tiempo en segundos, el valor predeterminado es 3. |Dinámica|Especifique el intervalo de tiempo en segundos. El intervalo de interrupción antes de volver a intentar la operación. |
| MaxFileSenderThreads |Uint, el valor predeterminado es 10. |estática|El número máximo de archivos que se transfieren en paralelo. |

### <a name="section-name-common"></a>Nombre de sección: Common
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| PerfMonitorInterval |Tiempo en segundos, el valor predeterminado es 1. |Dinámica|Especifique el intervalo de tiempo en segundos. Intervalo de supervisión del rendimiento. La configuración en 0 o un valor negativo deshabilita la supervisión. |

### <a name="section-name-healthmanager"></a>Nombre de sección: HealthManager
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Bool, el valor predeterminado es false. |estática|Directiva de evaluación de mantenimiento del clúster: habilita la evaluación de mantenimiento de tipos por aplicación. |

### <a name="section-name-nodedomainids"></a>Nombre de sección: NodeDomainIds
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| UpgradeDomainId |string, el valor predeterminado es "". |estática|Describe el dominio de actualización al que pertenece un nodo. |
| PropertyGroup |NodeFaultDomainIdCollection |estática|Describe los dominios de error a los que pertenece un nodo. El dominio de error se define mediante un URI que describe la ubicación del nodo en el centro de datos.  Los URI de dominio de error tienen el formato fd:/fd/ seguido de un segmento con la ruta de acceso al URI.|

### <a name="section-name-nodeproperties"></a>Nombre de sección: NodeProperties
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| PropertyGroup |NodePropertyCollectionMap |estática|Una colección de pares de clave y valor de cadena para las propiedades de nodo. |

### <a name="section-name-nodecapacities"></a>Nombre de sección: NodeCapacities
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| PropertyGroup |NodeCapacityCollectionMap |estática|Una colección de funcionalidades de nodo para diferentes métricas. |

### <a name="section-name-fabricnode"></a>Nombre de sección: FabricNode
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| StateTraceInterval |Tiempo en segundos, el valor predeterminado es 300. |estática|Especifique el intervalo de tiempo en segundos. El intervalo del estado de nodo de seguimiento en cada nodo y en los nodos activos en FM/FMM. |
| StartApplicationPortRange |Int, el valor predeterminado es 0. |estática|Inicio de los puertos de aplicación administrados por el subsistema de hospedaje. Requerido si EndpointFilteringEnabled es true en Hosting. |
| EndApplicationPortRange |Int, el valor predeterminado es 0. |estática|Fin (no inclusivo) de los puertos de aplicación administrados por el subsistema de hospedaje. Requerido si EndpointFilteringEnabled es true en Hosting. |
| ClusterX509StoreName |string, el valor predeterminado es "My". |Dinámica|Nombre del almacén de certificados X.509 que contiene el certificado de clúster para proteger la comunicación dentro del clúster. |
| ClusterX509FindType |string, el valor predeterminado es "FindByThumbprint". |Dinámica|Indica cómo buscar certificados de clúster en el almacén especificado mediante los valores admitidos de ClusterX509StoreName: "FindByThumbprint"; "FindBySubjectName" con "FindBySubjectName"; cuando existen varias coincidencias, se usa la que tiene la expiración más lejana. |
| ClusterX509FindValue |string, el valor predeterminado es "". |Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado de clúster. |
| ClusterX509FindValueSecondary |string, el valor predeterminado es "". |Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado de clúster. |
| ServerAuthX509StoreName |string, el valor predeterminado es "My". |Dinámica|Nombre del almacén de certificados X.509 que contiene el certificado de servidor para el servicio de entrada. |
| ServerAuthX509FindType |string, el valor predeterminado es "FindByThumbprint". |Dinámica|Indica cómo buscar el certificado de servidor en el almacén especificado mediante el valor admitido ServerAuthX509StoreName: FindByThumbprint; FindBySubjectName. |
| ServerAuthX509FindValue |string, el valor predeterminado es "". |Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado de servidor. |
| ServerAuthX509FindValueSecondary |string, el valor predeterminado es "". |Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado de servidor. |
| ClientAuthX509StoreName |string, el valor predeterminado es "My". |Dinámica|Nombre del almacén de certificados X.509 que contiene el certificado para el rol de administrador predeterminado FabricClient. |
| ClientAuthX509FindType |string, el valor predeterminado es "FindByThumbprint". |Dinámica|Indica cómo buscar el certificado en el almacén especificado mediante el valor admitido ClientAuthX509StoreName: FindByThumbprint; FindBySubjectName. |
| ClientAuthX509FindValue |string, el valor predeterminado es "". | Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado para el rol de administrador predeterminado FabricClient. |
| ClientAuthX509FindValueSecondary |string, el valor predeterminado es "". |Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado para el rol de administrador predeterminado FabricClient. |
| UserRoleClientX509StoreName |string, el valor predeterminado es "My". |Dinámica|Nombre del almacén de certificados X.509 que contiene el certificado para el rol de usuario predeterminado FabricClient. |
| UserRoleClientX509FindType |string, el valor predeterminado es "FindByThumbprint". |Dinámica|Indica cómo buscar el certificado en el almacén especificado mediante el valor admitido UserRoleClientX509StoreName: FindByThumbprint; FindBySubjectName. |
| UserRoleClientX509FindValue |string, el valor predeterminado es "". |Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado para el rol de usuario predeterminado FabricClient. |
| UserRoleClientX509FindValueSecondary |string, el valor predeterminado es "". |Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado para el rol de usuario predeterminado FabricClient. |

### <a name="section-name-paas"></a>Nombre de sección: Paas
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| ClusterId |string, el valor predeterminado es "". |No permitida|Almacén de certificados X509 usado por Fabric para la protección de la configuración. |

### <a name="section-name-fabrichost"></a>Nombre de sección: FabricHost
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| StopTimeout |Tiempo en segundos, el valor predeterminado es 300. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera para la activación, desactivación y actualización del servicio hospedado. |
| StartTimeout |Tiempo en segundos, el valor predeterminado es 300. |Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo de espera para el inicio de fabricactivationmanager. |
| ActivationRetryBackoffInterval |Tiempo en segundos, el valor predeterminado es 5. |Dinámica|Especifique el intervalo de tiempo en segundos. Intervalo de retroceso en cada error de activación; con cada error de activación continuo, el sistema vuelve a intentar la activación hasta el valor de MaxActivationFailureCount. El intervalo de reintento en cada intento es un producto del error de activación continua y el intervalo de retroceso de activación. |
| ActivationMaxRetryInterval |Tiempo en segundos, el valor predeterminado es 300. |Dinámica|Especifique el intervalo de tiempo en segundos. Intervalo de reintento máximo para la activación. Con cada error continuo, el intervalo de reintento se calcula como Min( ActivationMaxRetryInterval; Recuento continuo de errores * ActivationRetryBackoffInterval). |
| ActivationMaxFailureCount |Int, el valor predeterminado es 10. |Dinámica|Es el recuento máximo que el sistema reintentará la activación que ha generado error antes de desistir. |
| EnableServiceFabricAutomaticUpdates |Bool, el valor predeterminado es false. |Dinámica|Sirve para habilitar la actualización automática de Fabric mediante Windows Update. |
| EnableServiceFabricBaseUpgrade |Bool, el valor predeterminado es false. |Dinámica|Sirve para habilitar la actualización básica en el servidor. |
| EnableRestartManagement |Bool, el valor predeterminado es false. |Dinámica|Sirve para habilitar el reinicio del servidor. |


### <a name="section-name-failovermanager"></a>Nombre de sección: FailoverManager
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| UserReplicaRestartWaitDuration |Tiempo en segundos, el valor predeterminado es 60.0 * 30. |Dinámica|Especifique el intervalo de tiempo en segundos. Cuando una réplica persistente deja de funcionar, Windows Fabric espera esta cantidad de tiempo a que la réplica vuelva antes de crear nuevas réplicas de reemplazo (lo que requeriría una copia del estado). |
| QuorumLossWaitDuration |Tiempo en segundos, el valor predeterminado es MaxValue. |Dinámica|Especifique el intervalo de tiempo en segundos. Es la duración máxima que se permite que una partición esté en estado de pérdida de cuórum. Si después de esta duración la partición sigue en pérdida de cuórum, se recupera de dicha pérdida considerando perdidas las réplicas inactivas. Tenga en cuenta que esto puede provocar una pérdida de datos. |
| UserStandByReplicaKeepDuration |Tiempo en segundos, el valor predeterminado es 3600,0 * 24 * 7 |Dinámica|Especifique el intervalo de tiempo en segundos. Cuando una réplica persistente vuelve de un estado inactivo, puede que ya se haya reemplazado. Este temporizador determina cuánto tiempo el FM mantendrá la réplica en espera antes de descartarla. |
| UserMaxStandByReplicaCount |Int, el valor predeterminado es 1. |Dinámica|El número máximo predeterminado de réplicas en espera que mantiene el sistema para los servicios de usuario. |
| ExpectedClusterSize|int, el valor predeterminado es 1|Dinámica|Cuando el clúster se inicie por primera vez, el FM esperará a que estos nodos informen de su estado para empezar a colocar otros servicios, incluidos los servicios del sistema, como la nomenclatura.  Al aumentar este valor, también aumenta el tiempo que tarda un clúster en iniciarse; pero se impide que los nodos anticipados se sobrecarguen, así como los movimientos adicionales necesarios a medida que haya más nodos conectados.  Generalmente, este valor debe establecerse en una pequeña fracción del tamaño inicial del clúster. |
|ClusterPauseThreshold|int, el valor predeterminado es 1|Dinámica|Si el número de nodos del sistema está por debajo de este valor, la ubicación, el equilibrio de carga y la conmutación por error se detendrán. |
|TargetReplicaSetSize|int, el valor predeterminado es 7|No permitida|Este es el número de réplicas de FM de destino que mantendrá Windows Fabric.  Un número mayor da como resultado una mayor confiabilidad de los datos de FM, pero un menor equilibrio de rendimiento. |
|MinReplicaSetSize|int, el valor predeterminado es 3|No permitida|Este es el tamaño del conjunto de réplicas mínimo para FM.  Si el número de réplicas activas de FM cae por debajo de este valor, el FM rechazará los cambios en el clúster hasta que se recupere al menos el número mínimo de réplicas. |
|ReplicaRestartWaitDuration|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(60.0 * 30)|No permitida|Especifique el intervalo de tiempo en segundos. Valor de ReplicaRestartWaitDuration para FMService. |
|StandByReplicaKeepDuration|Timespan, el valor predeterminado es Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|No permitida|Especifique el intervalo de tiempo en segundos. Valor StandByReplicaKeepDuration para FMService. |
|PlacementConstraints|string, el valor predeterminado es L""|No permitida|Cualquier restricción de ubicación para las réplicas del Administrador de conmutación por error. |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(60.0 * 30)|Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo que se espera que tarde un nodo en actualizarse durante la actualización de Windows Fabric. |
|ExpectedReplicaUpgradeDuration|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(60.0 * 30)|Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo que se espera que tarden todas las réplicas en actualizarse en un nodo durante la actualización de la aplicación. |
|ExpectedNodeDeactivationDuration|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(60.0 * 30)|Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo que se espera que tarde un nodo en completar la desactivación. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, el valor predeterminado es TRUE|Dinámica|Si se establece en true, se permitirá mover las réplicas con un tamaño de conjunto de réplicas de destino de 1 durante la actualización. |
|ReconfigurationTimeLimit|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(300)|Dinámica|Especifique el intervalo de tiempo en segundos. Límite de tiempo para volver a configurar. Una vez transcurrido, se iniciará un informe de estado de advertencia. |
|BuildReplicaTimeLimit|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(3600)|Dinámica|Especifique el intervalo de tiempo en segundos. Límite de tiempo para crear una réplica con estado. Una vez transcurrido, se iniciará un informe de estado de advertencia. |
|CreateInstanceTimeLimit|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(300)|Dinámica|Especifique el intervalo de tiempo en segundos. Límite de tiempo para crear una instancia sin estado. Una vez transcurrido, se iniciará un informe de estado de advertencia. |
|PlacementTimeLimit|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(600)|Dinámica|Especifique el intervalo de tiempo en segundos. Límite de tiempo para alcanzar el número de réplicas de destino. Una vez transcurrido, se iniciará un informe de estado de advertencia. |

### <a name="section-name-namingservice"></a>Nombre de sección: NamingService
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, el valor predeterminado es 7. |No permitida|El número de conjuntos de réplicas para cada partición del almacén de servicio de nomenclatura. Aumentar el número de conjuntos de réplica aumenta el nivel de confiabilidad de la información del almacén del servicio de nomenclatura al reducirse la oportunidad de que la información se pierda como resultado de errores en los nodos. A cambio, aumenta la carga en Windows Fabric y la cantidad de tiempo que tarda en realizar actualizaciones en los datos de nomenclatura.|
|MinReplicaSetSize | Int, el valor predeterminado es 3. |No permitida| El número mínimo de réplicas del servicio de nomenclatura en las que es necesario escribir para completar una actualización. Si hay un número menor de réplicas de las que están activas en el sistema, el sistema de confiabilidad rechaza las actualizaciones al almacén del servicio de nomenclatura hasta que las réplicas se restauran. Este valor nunca debe ser mayor que el de TargetReplicaSetSize. |
|ReplicaRestartWaitDuration | Tiempo en segundos, el valor predeterminado es (60.0 * 30).|No permitida| Especifique el intervalo de tiempo en segundos. Cuando una réplica del servicio de nomenclatura deja de funcionar, el temporizador se inicia.  Cuando expira, el FM comenzará a sustituir las réplicas inactivas (aún no se consideran perdidas). |
|QuorumLossWaitDuration | Tiempo en segundos, el valor predeterminado es MaxValue. |No permitida| Especifique el intervalo de tiempo en segundos. Cuando un servicio de nomenclatura entra en pérdida de cuórum, este temporizador se inicia.  Cuando expira, el FM considera las réplicas inactivas perdidas e intenta recuperar el cuórum. Tenga en cuenta que esto puede dar lugar a la pérdida de datos. |
|StandByReplicaKeepDuration | Tiempo en segundos, el valor predeterminado es 3600.0 * 2. |No permitida| Especifique el intervalo de tiempo en segundos. Cuando una réplica del servicio de nomenclatura se reanuda después de un estado inactivo, puede que ya se haya reemplazado.  Este temporizador determina cuánto tiempo el FM mantendrá la réplica en espera antes de descartarla. |
|PlacementConstraints | string, el valor predeterminado es "". |No permitida| Restricción de selección de ubicación para el servicio de nomenclatura. |
|ServiceDescriptionCacheLimit | Int, el valor predeterminado es 0. |estática| El número máximo de entradas mantenidas en la caché de descripción del servicio LRU en el almacén del servicio de nomenclatura (se establece en 0 para un número ilimitado). |
|RepairInterval | Tiempo en segundos, el valor predeterminado es 5. |estática| Especifique el intervalo de tiempo en segundos. Intervalo dentro del cual se inicia la reparación de la incoherencia de nomenclatura entre el propietario de la entidad y el propietario del nombre. |
|MaxNamingServiceHealthReports | Int, el valor predeterminado es 10. |Dinámica|El número máximo de operaciones lentas que el almacén del servicio de nomenclatura notifica como incorrectas al mismo tiempo. Si es 0, se envían todas las operaciones lentas. |
| MaxMessageSize |Int, el valor predeterminado es 4\*1024\*1024. |estática|El tamaño de mensaje máximo para la comunicación del nodo cliente cuando se usa la nomenclatura. Mitigación de ataque de DOS; el valor predeterminado es 4 MB. |
| MaxFileOperationTimeout |Tiempo en segundos, el valor predeterminado es 30. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo permitido para la operación del servicio de almacén de archivos. Las solicitudes que especifiquen un tiempo de espera mayor se rechazarán. |
| MaxOperationTimeout |Tiempo en segundos, el valor predeterminado es 600. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo permitido para las operaciones de cliente. Las solicitudes que especifiquen un tiempo de espera mayor se rechazarán. |
| MaxClientConnections |Int, el valor predeterminado es 1000. |Dinámica|El número máximo permitido de conexiones de cliente por puerta de enlace. |
| ServiceNotificationTimeout |Tiempo en segundos, el valor predeterminado es 30. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera usado al entregar notificaciones de servicio al cliente. |
| MaxOutstandingNotificationsPerClient |Int, el valor predeterminado es 1000. |Dinámica|El número máximo de notificaciones pendientes antes de que la puerta de enlace cierre a la fuerza un registro de cliente. |
| MaxIndexedEmptyPartitions |Int, el valor predeterminado es 1000. |Dinámica|El número máximo de particiones vacías que permanecerán indexadas en la caché de notificaciones para la sincronización de los clientes que se vuelven a conectar. Cualquier partición vacía por encima de este número se quitará del índice en orden ascendente de la versión de búsqueda. Durante la reconexión, los clientes se pueden seguir sincronizando y pueden recibir actualizaciones de particiones vacías que faltan, pero el protocolo de sincronización se encarece. |
| GatewayServiceDescriptionCacheLimit |Int, el valor predeterminado es 0. |estática|El número máximo de entradas mantenidas en la caché de descripción del servicio LRU en la puerta de enlace de nomenclatura (se establece en 0 para un número ilimitado). |
| PartitionCount |Int, el valor predeterminado es 3. |No permitida|El número de particiones del almacén del servicio de nomenclatura que se creará. Cada partición posee una única clave de partición que corresponde a su índice; de modo que existen [0; PartitionCount] claves de partición. Al aumentar el número de particiones del servicio de nomenclatura, aumenta la escala a la que puede ejecutarse el servicio de nomenclatura ya que se reduce la cantidad media de datos que contiene cualquier conjunto de réplicas de respaldo; a cambio, aumenta la utilización de recursos (dado que se deben mantener las réplicas de servicio PartitionCount*ReplicaSetSize).|

### <a name="section-name-runas"></a>Nombre de sección: RunAs
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| RunAsAccountName |string, el valor predeterminado es "". |Dinámica|Indica el nombre de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser" o "ManagedServiceAccount". Los valores válidos son "domain\user" o "user@domain". |
|RunAsAccountType|string, el valor predeterminado es "". |Dinámica|Indica el tipo de cuenta de ejecución. Es necesario para cualquier sección RunAs. Valores válidos son "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|string, el valor predeterminado es "". |Dinámica|Indica la contraseña de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser". |

### <a name="section-name-runasfabric"></a>Nombre de sección: RunAs_Fabric
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| RunAsAccountName |string, el valor predeterminado es "". |Dinámica|Indica el nombre de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser" o "ManagedServiceAccount". Los valores válidos son "domain\user" o "user@domain". |
|RunAsAccountType|string, el valor predeterminado es "". |Dinámica|Indica el tipo de cuenta de ejecución. Es necesario para cualquier sección RunAs. Valores válidos son "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|string, el valor predeterminado es "". |Dinámica|Indica la contraseña de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser". |

### <a name="section-name-runashttpgateway"></a>Nombre de sección: RunAs_HttpGateway
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| RunAsAccountName |string, el valor predeterminado es "". |Dinámica|Indica el nombre de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser" o "ManagedServiceAccount". Los valores válidos son "domain\user" o "user@domain". |
|RunAsAccountType|string, el valor predeterminado es "". |Dinámica|Indica el tipo de cuenta de ejecución. Es necesario para cualquier sección RunAs. Valores válidos son "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|string, el valor predeterminado es "". |Dinámica|Indica la contraseña de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser". |

### <a name="section-name-runasdca"></a>Nombre de sección: RunAs_DCA
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| RunAsAccountName |string, el valor predeterminado es "". |Dinámica|Indica el nombre de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser" o "ManagedServiceAccount". Los valores válidos son "domain\user" o "user@domain". |
|RunAsAccountType|string, el valor predeterminado es "". |Dinámica|Indica el tipo de cuenta de ejecución. Es necesario para cualquier sección RunAs. Valores válidos son "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|string, el valor predeterminado es "". |Dinámica|Indica la contraseña de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser". |

### <a name="section-name-httpgateway"></a>Nombre de sección: HttpGateway
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|IsEnabled|Bool, el valor predeterminado es false. |estática| Habilita o deshabilita HttpGateway. HttpGateway está deshabilitado de manera predeterminada. |
|ActiveListeners |Uint, el valor predeterminado es 50. |estática| Número de lecturas para enviar a la cola del servidor HTTP. Controla el número de solicitudes simultáneas que se pueden satisfacer mediante HttpGateway. |
|MaxEntityBodySize |Uint, el valor predeterminado es 4 194 304. |Dinámica|Proporciona el tamaño máximo del cuerpo que se puede esperar de una solicitud HTTP. El valor predeterminado es 4 MB. Httpgateway no atenderá una solicitud si el tamaño del cuerpo es mayor que su valor. El tamaño mínimo del fragmento de lectura es 4096 bytes. Así que tiene que ser > = 4096. |
|HttpGatewayHealthReportSendInterval |Tiempo en segundos, el valor predeterminado es 30. |estática|Especifique el intervalo de tiempo en segundos. El intervalo con el que la puerta de enlace HTTP envía los informes de mantenimiento acumulados a Health Manager. |

### <a name="section-name-ktllogger"></a>Nombre de sección: KtlLogger
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, el valor predeterminado es 1. |Dinámica|Marca que indica si los valores de memoria se deben configurar automática y dinámicamente. Si es 0, la configuración de memoria se usa directamente y no cambia en función de las condiciones del sistema. Si es 1, la configuración de memoria se realiza automáticamente y puede cambiar según las condiciones del sistema. |
|WriteBufferMemoryPoolMinimumInKB |Int, el valor predeterminado es 8 388 608. |Dinámica|El número de KB para asignar inicialmente al bloque de memoria del búfer de escritura. Use 0 para indicar que no hay límite. El valor predeterminado debe ser coherente con el valor de SharedLogSizeInMB siguiente. |
|WriteBufferMemoryPoolMaximumInKB | Int, el valor predeterminado es 0. |Dinámica|El número de KB que se permite que crezca el bloque de memoria del búfer de escritura. Use 0 para indicar que no hay límite. |
|MaximumDestagingWriteOutstandingInKB | Int, el valor predeterminado es 0. |Dinámica|El número de KB que se permite que vaya por delante el registro compartido con respecto al registro dedicado. Use 0 para indicar que no hay límite.
|SharedLogPath |string, el valor predeterminado es "". |estática|Nombre de archivo y ruta a la ubicación para colocar el contenedor de registros compartidos. Use "" para utilizar la ruta de acceso predeterminada en la raíz de datos de Fabric. |
|SharedLogId |string, el valor predeterminado es "". |estática|GUID único del contenedor de registros compartidos. Use "" si utiliza la ruta de acceso predeterminada en la raíz de datos de Fabric. |
|SharedLogSizeInMB |Int, el valor predeterminado es 8192. |estática|El número de MB para asignar en el contenedor de registros compartidos. |

### <a name="section-name-applicationgatewayhttp"></a>Nombre de sección: ApplicationGateway/Http
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|IsEnabled |Bool, el valor predeterminado es false. |estática| Habilita o deshabilita HttpApplicationGateway. HttpApplicationGateway está deshabilitado de forma predeterminada y para habilitarlo es necesario establecer esta configuración. |
|NumberOfParallelOperations | Uint, el valor predeterminado es 5000 |estática|Número de lecturas para enviar a la cola del servidor HTTP. Controla el número de solicitudes simultáneas que se pueden satisfacer mediante HttpGateway. |
|DefaultHttpRequestTimeout |Tiempo en segundos. El valor predeterminado es 120 |Dinámica|Especifique el intervalo de tiempo en segundos.  Proporciona el tiempo de espera de solicitud predeterminado para las solicitudes HTTP que se van a procesar en la puerta de enlace de aplicaciones HTTP. |
|ResolveServiceBackoffInterval |Tiempo en segundos, el valor predeterminado es 5. |Dinámica|Especifique el intervalo de tiempo en segundos.  Proporciona el intervalo de retroceso predeterminado antes de reintentar una operación errónea del servicio de resolución. |
|BodyChunkSize |Uint, el valor predeterminado es 16384. |Dinámica| Proporciona el tamaño del fragmento en bytes usado para leer el cuerpo. |
|GatewayAuthCredentialType |string, el valor predeterminado es "None" |estática| Indica el tipo de credenciales de seguridad que se usarán en el punto de conexión de la puerta de enlace de aplicaciones HTTP. Valores válidos son "None/X509. |
|GatewayX509CertificateStoreName |string, el valor predeterminado es "My". |Dinámica| Nombre del almacén de certificados X.509 que contiene el certificado de puerta de enlace de aplicaciones HTTP. |
|GatewayX509CertificateFindType |string, el valor predeterminado es "FindByThumbprint". |Dinámica| Indica cómo buscar el certificado en el almacén especificado mediante el valor admitido GatewayX509CertificateStoreName: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | string, el valor predeterminado es "". |Dinámica| Valor del filtro de búsqueda usado para encontrar el certificado de puerta de enlace de aplicaciones HTTP. Este certificado se configura en el punto de conexión HTTP y también puede usarse para comprobar la identidad de la aplicación en caso de que la necesiten los servicios. Primero se busca FindValue y, si no existe, se busca FindValueSecondary. |
|GatewayX509CertificateFindValueSecondary | string, el valor predeterminado es "". |Dinámica|Valor del filtro de búsqueda usado para encontrar el certificado de puerta de enlace de aplicaciones HTTP. Este certificado se configura en el punto de conexión HTTP y también puede usarse para comprobar la identidad de la aplicación en caso de que la necesiten los servicios. Primero se busca FindValue y, si no existe, se busca FindValueSecondary.|
|HttpRequestConnectTimeout|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(5)|Dinámica|Especifique el intervalo de tiempo en segundos.  Proporciona el tiempo de espera de conexión para las solicitudes HTTP que se envían desde la puerta de enlace de aplicaciones HTTP.  |
|RemoveServiceResponseHeaders|string, el valor predeterminado es L"Date; Server"|estática|Lista de encabezados de respuesta separados por punto y coma o comas que se eliminarán de la respuesta del servicio antes de enviarla al cliente. Si se establece en una cadena vacía, se pasarán todos los encabezados que devuelva el servicio como estén. es decir, no se sobrescribirá la fecha ni el servidor. |
|ApplicationCertificateValidationPolicy|string, el valor predeterminado es L"None"|estática| ApplicationCertificateValidationPolicy: None: no se valida el certificado de servidor; la solicitud se realiza correctamente. ServiceCertificateThumbprints: hace referencia a la configuración ServiceCertificateThumbprints para la lista separada por comas de las huellas digitales de los certificados remotos en los que puede confiar el proxy inverso. ServiceCommonNameAndIssuer: hace referencia a la configuración ServiceCommonNameAndIssuer para el nombre del firmante y la huella digital del emisor de los certificados remotos en los que puede confiar el proxy inverso. |
|ServiceCertificateThumbprints|string, el valor predeterminado es L""|Dinámica| |
|CrlCheckingFlag|uint, el valor predeterminado es 0x40000000 |Dinámica| Marcas para la validación de la cadena de certificados del servicio o aplicación; p. ej., comprobación de CRL 0x10000000 0x20000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT aa376078 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY. Establecer la configuración en 0 deshabilita la comprobación de CRL. dwFlags de CertGetCertificateChain proporciona una lista completa de valores admitidos: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|IgnoreCrlOfflineError|bool, el valor predeterminado es TRUE|Dinámica|Especifica si se ignoran los errores de CRL sin conexión de la verificación del certificado del servicio o la aplicación. |
|SecureOnlyMode|bool, el valor predeterminado es FALSE|Dinámica| SecureOnlyMode: true. El proxy inverso solo reenviará a servicios que publican puntos de conexión seguros. false: el proxy inverso puede reenviar solicitudes a puntos de conexión seguros o no seguros.  |
|ForwardClientCertificate|bool, el valor predeterminado es FALSE|Dinámica| |

### <a name="section-name-applicationgatewayhttpservicecommonnameandissuer"></a>Nombre de sección: ApplicationGateway/Http/ServiceCommonNameAndIssuer
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, el valor predeterminado es None|Dinámica|  |

### <a name="section-name-management"></a>Nombre de sección: Management
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| ImageStoreConnectionString |SecureString |estática|Cadena de conexión a la raíz de ImageStore. |
| ImageStoreMinimumTransferBPS | Int, el valor predeterminado es 1024. |Dinámica|La velocidad mínima de transferencia entre el clúster e ImageStore. Este valor se usa para determinar el tiempo de espera al acceder al elemento ImageStore externo. Cambie este valor solo si la latencia entre el clúster e ImageStore es alta a fin de dar más tiempo al clúster para que se descargue del elemento ImageStore externo. |
|AzureStorageMaxWorkerThreads | Int, el valor predeterminado es 25. |Dinámica|El número máximo de subprocesos de trabajo en paralelo. |
|AzureStorageMaxConnections | Int, el valor predeterminado es 5000. |Dinámica|El número máximo de conexiones simultáneas a Azure Storage. |
|AzureStorageOperationTimeout | Tiempo en segundos, el valor predeterminado es 6000. |Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo de espera para que finalice la operación xstore. |
|ImageCachingEnabled | Bool, el valor predeterminado es true. |estática|Esta configuración permite habilitar o deshabilitar el almacenamiento en caché. |
|DisableChecksumValidation | Bool, el valor predeterminado es false. |estática| Esta configuración permite habilitar o deshabilitar la validación de suma de comprobación durante el aprovisionamiento de aplicaciones. |
|DisableServerSideCopy | Bool, el valor predeterminado es false. |estática|Esta configuración habilita o deshabilita la copia del lado servidor del paquete de aplicación en ImageStore durante el aprovisionamiento de aplicaciones. |

### <a name="section-name-healthmanagerclusterhealthpolicy"></a>Nombre de sección: HealthManager/ClusterHealthPolicy
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| ConsiderWarningAsError |Bool, el valor predeterminado es false. |estática|Directiva de evaluación de mantenimiento del clúster: las advertencias se tratan como errores. |
| MaxPercentUnhealthyNodes | Int, el valor predeterminado es 0. |estática|Directiva de evaluación de mantenimiento del clúster: porcentaje máximo permitido de nodos incorrectos para que el estado del clúster sea correcto. |
| MaxPercentUnhealthyApplications | Int, el valor predeterminado es 0. |estática|Directiva de evaluación de mantenimiento del clúster: porcentaje máximo permitido de aplicaciones incorrectas para que el estado del clúster sea correcto. |

### <a name="section-name-healthmanagerclusterupgradehealthpolicy"></a>Nombre de sección: HealthManager/ClusterUpgradeHealthPolicy
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, el valor predeterminado es 10|estática|Directiva de evaluación de estado de actualización del clúster: porcentaje máximo permitido de nodos incorrectos delta para que el estado del clúster sea correcto. |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, el valor predeterminado es 15|estática|Directiva de evaluación de estado de actualización del clúster: porcentaje máximo permitido de nodos incorrectos delta en un dominio de actualización para que el estado del clúster sea correcto. |

### <a name="section-name-faultanalysisservice"></a>Nombre de sección: FaultAnalysisService
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, el valor predeterminado es 0. |estática|NOT_PLATFORM_UNIX_START. El valor de TargetReplicaSetSize para FaultAnalysisService. |
| MinReplicaSetSize |Int, el valor predeterminado es 0. |estática|MinReplicaSetSize para FaultAnalysisService. |
| ReplicaRestartWaitDuration |Tiempo en segundos, el valor predeterminado es 60 minutos.|estática|Especifique el intervalo de tiempo en segundos. ReplicaRestartWaitDuration para FaultAnalysisService. |
| QuorumLossWaitDuration | Tiempo en segundos, el valor predeterminado es MaxValue. |estática|Especifique el intervalo de tiempo en segundos. QuorumLossWaitDuration para FaultAnalysisService. |
| StandByReplicaKeepDuration| Tiempo en segundos, el valor predeterminado es (60*24*7) minutos. |estática|Especifique el intervalo de tiempo en segundos. StandByReplicaKeepDuration para FaultAnalysisService. |
| PlacementConstraints | string, el valor predeterminado es "".|estática| PlacementConstraints para FaultAnalysisService. |
| StoredActionCleanupIntervalInSeconds | Int, el valor predeterminado es 3600. |estática|Es la frecuencia con la que se limpiará el almacén.  Solo se quitarán las acciones en estado terminal y que se han completado como mínimo hace CompletedActionKeepDurationInSeconds. |
| CompletedActionKeepDurationInSeconds | Int, el valor predeterminado es 604 800. |estática| El tiempo aproximado que las acciones se mantienen en estado terminal.  También depende de StoredActionCleanupIntervalInSeconds, dado que el trabajo para realizar la limpieza solo se realiza en ese intervalo. 604800 es 7 días. |
| StoredChaosEventCleanupIntervalInSeconds | Int, el valor predeterminado es 3600. |estática|La frecuencia con la que se auditará el almacén de cara a la limpieza; si el número de eventos es superior a 30 000, se activa la limpieza. |
|DataLossCheckWaitDurationInSeconds|int, el valor predeterminado es 25|estática|Tiempo total, en segundos, que el sistema esperará para que se produzca la pérdida de datos.  Esto se usa internamente cuando se llama a la API StartPartitionDataLossAsync(). |
|DataLossCheckPollIntervalInSeconds|int, el valor predeterminado es 5|estática|Tiempo entre las comprobaciones que realiza el sistema mientras se espera que se produzca la pérdida de datos.  El número de veces que se comprobará el número de pérdida de datos por cada iteración interna es DataLossCheckWaitDurationInSeconds/this. |
|ReplicaDropWaitDurationInSeconds|int, el valor predeterminado es 600|estática|Este parámetro se usa cuando se llama a la API de pérdida de datos.  Controla cuánto tiempo esperará el sistema a que se elimine una réplica después de invocar la eliminación internamente. |

### <a name="section-name-filestoreservice"></a>Nombre de sección: FileStoreService
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| NamingOperationTimeout |Tiempo en segundos, el valor predeterminado es 60. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera para realizar la operación de nomenclatura. |
| QueryOperationTimeout | Tiempo en segundos, el valor predeterminado es 60. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera para realizar una operación de consulta. |
| MaxCopyOperationThreads | Uint, el valor predeterminado es 0. |Dinámica| El número máximo de archivos paralelos que el replicador secundario puede copiar del principal. "0" == número de núcleos. |
| MaxFileOperationThreads | Uint, el valor predeterminado es 100. |estática| El número máximo de subprocesos paralelos que pueden realizar operaciones de archivos (copiar o mover) en el replicador principal. "0" == número de núcleos. |
| MaxStoreOperations | Uint, el valor predeterminado es 4096. |estática|Número máximo de operaciones paralelas de transacción de almacén que se permiten en el replicador principal. "0" == número de núcleos. |
| MaxRequestProcessingThreads | Uint, el valor predeterminado es 200. |estática|El número máximo de subprocesos paralelos que pueden procesar solicitudes en el replicador principal. "0" == número de núcleos. |
| MaxSecondaryFileCopyFailureThreshold | Uint, el valor predeterminado es 25.|Dinámica|El número máximo de reintentos de copia de archivos en el replicador secundario antes de desistir. |
| AnonymousAccessEnabled | Bool, el valor predeterminado es true. |estática|Habilita o deshabilita el acceso anónimo a los recursos compartidos de FileStoreService. |
| PrimaryAccountType | string, el valor predeterminado es "". |estática|El tipo de cuenta principal de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
| PrimaryAccountUserName | string, el valor predeterminado es "". |estática|El nombre de usuario de la cuenta principal de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
| PrimaryAccountUserPassword | SecureString, el valor predeterminado es empty. |estática|La contraseña de la cuenta principal de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
| PrimaryAccountNTLMPasswordSecret | SecureString, el valor predeterminado es empty. |estática| El secreto de contraseña que se usa como valor de inicialización para generar la misma contraseña cuando se usa la autenticación NTLM. |
| PrimaryAccountNTLMX509StoreLocation | string, el valor predeterminado es "LocalMachine".|estática| La ubicación del almacén de certificados X509 usada para generar HMAC en PrimaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
| PrimaryAccountNTLMX509StoreName | string, el valor predeterminado es "MY".|estática| El nombre del almacén de certificados X509 usado para generar HMAC en PrimaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
| PrimaryAccountNTLMX509Thumbprint | string, el valor predeterminado es "".|estática|La huella digital del almacén de certificados X509 usada para generar HMAC en PrimaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
| SecondaryAccountType | string, el valor predeterminado es "".|estática| El tipo de cuenta secundario de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
| SecondaryAccountUserName | string, el valor predeterminado es "".| estática|El nombre de usuario de la cuenta secundaria de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
| SecondaryAccountUserPassword | SecureString, el valor predeterminado es empty. |estática|La contraseña de la cuenta secundaria de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL.  |
| SecondaryAccountNTLMPasswordSecret | SecureString, el valor predeterminado es empty. |estática| El secreto de contraseña que se usa como valor de inicialización para generar la misma contraseña cuando se usa la autenticación NTLM. |
| SecondaryAccountNTLMX509StoreLocation | string, el valor predeterminado es "LocalMachine". |estática|La ubicación del almacén de certificados X509 usada para generar HMAC en SecondaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
| SecondaryAccountNTLMX509StoreName | string, el valor predeterminado es "MY". |estática|El nombre del almacén de certificados X509 usado para generar HMAC en SecondaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
| SecondaryAccountNTLMX509Thumbprint | string, el valor predeterminado es "".| estática|La huella digital del almacén de certificados X509 usada para generar HMAC en SecondaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
|CommonNameNtlmPasswordSecret|SecureString, el valor predeterminado es Common::SecureString(L"")| estática|Secreto de contraseña que se usa como valor de inicialización para generar la misma contraseña cuando se usa la autenticación NTLM. |
|CommonName1Ntlmx509StoreLocation|string, el valor predeterminado es L"LocalMachine"|estática|Ubicación del almacén del certificado X509 que se usa para generar HMAC en CommonName1NtlmPasswordSecret cuando se usa la autenticación NTLM. |
|CommonName1Ntlmx509StoreName|string, el valor predeterminado es L"MY"| estática|Nombre del almacén del certificado X509 que se usa para generar HMAC en CommonName1NtlmPasswordSecret cuando se usa la autenticación NTLM. |
|CommonName1Ntlmx509CommonName|string, el valor predeterminado es L""|estática| Nombre común del certificado X509 usado para generar HMAC en CommonName1NtlmPasswordSecret cuando se usa la autenticación NTLM. |
|CommonName2Ntlmx509StoreLocation|string, el valor predeterminado es L"LocalMachine"| estática|Ubicación del almacén del certificado X509 que se usa para generar HMAC en CommonName2NtlmPasswordSecret cuando se usa la autenticación NTLM. |
|CommonName2Ntlmx509StoreName|string, el valor predeterminado es L"MY"|estática| Nombre del almacén del certificado X509 que se usa para generar HMAC en CommonName2NtlmPasswordSecret cuando se usa la autenticación NTLM. |
|CommonName2Ntlmx509CommonName|string, el valor predeterminado es L""|estática|Nombre común del certificado X509 que se usa para generar HMAC en CommonName2NtlmPasswordSecret cuando se usa la autenticación NTLM. |

### <a name="section-name-imagestoreservice"></a>Nombre de sección: ImageStoreService
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| habilitado |Bool, el valor predeterminado es false. |estática|La marca "Enabled" para ImageStoreService. Valor predeterminado: false |
| TargetReplicaSetSize | Int, el valor predeterminado es 7. |estática|TargetReplicaSetSize para ImageStoreService. |
| MinReplicaSetSize | Int, el valor predeterminado es 3. |estática|MinReplicaSetSize para ImageStoreService. |
| ReplicaRestartWaitDuration | Tiempo en segundos, el valor predeterminado es 60.0 * 30. |estática|Especifique el intervalo de tiempo en segundos. ReplicaRestartWaitDuration para ImageStoreService. |
| QuorumLossWaitDuration | Tiempo en segundos, el valor predeterminado es MaxValue. |estática| Especifique el intervalo de tiempo en segundos. QuorumLossWaitDuration para ImageStoreService. |
| StandByReplicaKeepDuration | Tiempo en segundos, el valor predeterminado es 3600.0 * 2. |estática| Especifique el intervalo de tiempo en segundos. StandByReplicaKeepDuration para ImageStoreService. |
| PlacementConstraints | string, el valor predeterminado es "". |estática| PlacementConstraints para ImageStoreService. |

### <a name="section-name-imagestoreclient"></a>Nombre de sección: ImageStoreClient
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| ClientUploadTimeout |Tiempo en segundos, el valor predeterminado es 1800. |Dinámica|Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de carga de nivel superior para el servicio de almacén de imágenes. |
| ClientCopyTimeout | Tiempo en segundos, el valor predeterminado es 1800. |Dinámica| Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de copia de nivel superior para el servicio de almacén de imágenes. |
|ClientDownloadTimeout | Tiempo en segundos, el valor predeterminado es 1800. |Dinámica| Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de descarga de nivel superior para el servicio de almacén de imágenes. |
|ClientListTimeout | Tiempo en segundos, el valor predeterminado es 600. |Dinámica|Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de lista de nivel superior para el servicio de almacén de imágenes. |
|ClientDefaultTimeout | Tiempo en segundos, el valor predeterminado es 180. |Dinámica| Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera para todas las solicitudes que no son de carga ni de descarga (por ejemplo, exists o delete) al servicio del almacén de imágenes. |

### <a name="section-name-tokenvalidationservice"></a>Nombre de sección: TokenValidationService
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| Proveedores |string, el valor predeterminado es "DSTS". |estática|Lista separada por comas de proveedores de validación de tokens para habilitar (proveedores válidos son: DSTS; AAD). Actualmente solo se puede habilitar un único proveedor cada vez. |

### <a name="section-name-upgradeorchestrationservice"></a>Nombre de sección: UpgradeOrchestrationService
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, el valor predeterminado es 0. |estática |TargetReplicaSetSize para UpgradeOrchestrationService. |
| MinReplicaSetSize |Int, el valor predeterminado es 0. |estática |MinReplicaSetSize para UpgradeOrchestrationService.
| ReplicaRestartWaitDuration | Tiempo en segundos, el valor predeterminado es 60 minutos.|estática| Especifique el intervalo de tiempo en segundos. ReplicaRestartWaitDuration para UpgradeOrchestrationService. |
| QuorumLossWaitDuration | Tiempo en segundos, el valor predeterminado es MaxValue. |estática| Especifique el intervalo de tiempo en segundos. QuorumLossWaitDuration para UpgradeOrchestrationService. |
| StandByReplicaKeepDuration | Tiempo en segundos, el valor predeterminado es 60*24*7 minutos. |estática| Especifique el intervalo de tiempo en segundos. StandByReplicaKeepDuration para UpgradeOrchestrationService. |
| PlacementConstraints | string, el valor predeterminado es "". |estática| PlacementConstraints para UpgradeOrchestrationService. |
| AutoupgradeEnabled | Bool, el valor predeterminado es true. |estática| Sondeo automático y acción de actualización basados en un archivo de estado de objetivo. |
| UpgradeApprovalRequired | Bool, el valor predeterminado es false. | estática|La configuración para crear actualizaciones de código requiere la aprobación del administrador antes de continuar. |

### <a name="section-name-upgradeservice"></a>Nombre de sección: UpgradeService
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| PlacementConstraints |string, el valor predeterminado es "". |No permitida|PlacementConstraints para el servicio de actualización. |
| TargetReplicaSetSize | Int, el valor predeterminado es 3. |No permitida| TargetReplicaSetSize para UpgradeService. |
| MinReplicaSetSize | Int, el valor predeterminado es 2. |No permitida| MinReplicaSetSize para UpgradeService. |
| CoordinatorType | string, el valor predeterminado es "WUTest".|No permitida|CoordinatorType para UpgradeService. |
| BaseUrl | string, el valor predeterminado es "". |estática|BaseUrl para UpgradeService. |
| ClusterId | string, el valor predeterminado es "". |estática|ClusterId para UpgradeService. |
| X509StoreName | string, el valor predeterminado es "My".|Dinámica|X509StoreName para UpgradeService. |
| X509StoreLocation | string, el valor predeterminado es "". |Dinámica| X509StoreLocation para UpgradeService. |
| X509FindType | string, el valor predeterminado es "".|Dinámica| X509FindType para UpgradeService. |
| X509FindValue | string, el valor predeterminado es "". |Dinámica| X509FindValue para UpgradeService. |
| X509SecondaryFindValue | string, el valor predeterminado es "". |Dinámica| X509SecondaryFindValue para UpgradeService. |
| OnlyBaseUpgrade | Bool, el valor predeterminado es false. |Dinámica|OnlyBaseUpgrade para UpgradeService. |
| TestCabFolder | string, el valor predeterminado es "". |estática| TestCabFolder para UpgradeService. |

### <a name="section-name-security"></a>Nombre de sección: Seguridad
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|ClusterCredentialType|string, el valor predeterminado es L"None"|No permitida|Indica el tipo de credenciales de seguridad que se usarán para proteger el clúster. Los valores válidos son "None/X509/Windows". |
|ServerAuthCredentialType|string, el valor predeterminado es L"None"|estática|Indica el tipo de credenciales de seguridad que se usarán para proteger la comunicación entre FabricClient y el clúster. Los valores válidos son "None/X509/Windows". |
|ClientRoleEnabled|bool, el valor predeterminado es FALSE|estática|Indica si está habilitado el rol de cliente. Cuando se establece en true, se asignan roles a clientes en función de sus identidades. Para V2; habilitar esto significa que los clientes que no estén en AdminClientCommonNames/AdminClientIdentities solo pueden ejecutar operaciones de solo lectura. |
|ClusterCertThumbprints|string, el valor predeterminado es L""|Dinámica|Huellas digitales de certificados que pueden unirse al clúster; lista de nombres separados por comas. |
|ServerCertThumbprints|string, el valor predeterminado es L""|Dinámica|Huellas digitales de certificados de servidor que usa el clúster para comunicarse con los clientes. Los clientes las usan para autenticar el clúster. Lista de nombres separados por comas. |
|ClientCertThumbprints|string, el valor predeterminado es L""|Dinámica|Huellas digitales de certificados que usan los clientes para comunicarse con el clúster. El clúster las usa para autorizar la conexión entrante. Lista de nombres separados por comas. |
|AdminClientCertThumbprints|string, el valor predeterminado es L""|Dinámica|Huellas digitales de certificados que usan los clientes con el rol de administrador. Lista de nombres separados por comas. |
|CrlCheckingFlag|uint, el valor predeterminado es 0x40000000|Dinámica|Marca predeterminada de validación de la cadena de certificados. Puede reemplazarse por la marca específica del componente, p. ej., Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY. Establecer la configuración en 0 deshabilita la comprobación de CRL. dwFlags de CertGetCertificateChain proporciona una lista completa de valores admitidos: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|IgnoreCrlOfflineError|bool, el valor predeterminado es FALSE|Dinámica|Especifica si se omitirán los errores de CRL sin conexión cuando el lado servidor compruebe los certificados de cliente entrantes. |
|IgnoreSvrCrlOfflineError|bool, el valor predeterminado es TRUE|Dinámica|Especifica si se omitirán los errores de CRL sin conexión cuando el lado cliente compruebe los certificados de servidor entrantes; el valor predeterminado es true. Los ataques con certificados de servidor revocados requieren poner en peligro el DNS; más difícil que con certificados de cliente revocados. |
|CrlDisablePeriod|TimeSpan, el valor predeterminado es Common::TimeSpan::FromMinutes(15)|Dinámica|Especifique el intervalo de tiempo en segundos. Durante cuánto tiempo se deshabilita la comprobación de CRL para un certificado especificado después de encontrar un error sin conexión. Especifica si se puede ignorar un error de CRL sin conexión. |
|CrlOfflineHealthReportTtl|TimeSpan, el valor predeterminado es Common::TimeSpan::FromMinutes(1440)|Dinámica|Especifique el intervalo de tiempo en segundos. |
|CertificateHealthReportingInterval|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(3600 * 8)|estática|Especifique el intervalo de tiempo en segundos. Especifica el intervalo para los informes de mantenimiento del certificado. El valor predeterminado es 8 horas. Establecerlo en 0 deshabilita el informe de mantenimiento del certificado. |
|CertificateExpirySafetyMargin|TimeSpan, el valor predeterminado es Common::TimeSpan::FromMinutes(43200)|estática|Especifique el intervalo de tiempo en segundos. Margen de seguridad para la expiración del certificado. El estado de informe de mantenimiento del certificado cambia de Correcto a Advertencia si el periodo de expiración es inferior a este valor. El valor predeterminado es 30 días. |
|ClientClaimAuthEnabled|bool, el valor predeterminado es FALSE|estática|Indica si la autenticación basada en notificaciones está habilitada en los clientes. Si se establece en true, se establece implícitamente ClientRoleEnabled. |
|ClientClaims|string, el valor predeterminado es L""|Dinámica|Todas las notificaciones posibles que se esperan de los clientes para conectarse a la puerta de enlace. Se trata de una lista "OR": ClaimsEntry || ClaimsEntry || ClaimsEntry ... cada valor de ClaimsEntry es una lista "AND": ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue ... |
|AdminClientClaims|string, el valor predeterminado es L""|Dinámica|Todas las notificaciones posibles que se esperan de los clientes de administración. Tiene el mismo formato que ClientClaims. Esta lista se agrega internamente a ClientClaims, por lo que no es necesario agregar también las mismas entradas para ClientClaims. |
|ClusterSpn|string, el valor predeterminado es L""|No permitida|Nombre de entidad de seguridad de servicio del clúster cuando Fabric se ejecuta como un usuario de dominio único (cuenta de usuario de dominio o gMSA). Es el SPN de los agentes de escucha y de los agentes de escucha de concesión de fabric.exe: agentes de escucha de federación; agentes de escucha de replicación interna; agente de escucha del servicio en tiempo de ejecución y agente de escucha de la puerta de enlace de nomenclatura. Debe dejarse vacío cuando Fabric se ejecuta como cuentas de la máquina, en cuyo caso, se conecta el SPN del agente de escucha de proceso lateral desde la dirección de transporte del agente de escucha. |
|ClusterIdentities|string, el valor predeterminado es L""|Dinámica|Identidades de Windows de los nodos del clúster, se usan para la autorización de pertenencia del clúster. Lista separada por comas; cada entrada es un nombre de grupo o de cuenta de dominio. |
|ClientIdentities|string, el valor predeterminado es L""|Dinámica|Identidades de Windows de FabricClient; la puerta de enlace de nomenclatura las usa para autorizar las conexiones entrantes. Lista separada por comas; cada entrada es un nombre de grupo o de cuenta de dominio. Para mayor comodidad; la cuenta que ejecuta fabric.exe se permite automáticamente. Lo mismo sucede con ServiceFabricAllowedUsers y ServiceFabricAdministrators. |
|AdminClientIdentities|string, el valor predeterminado es L""|Dinámica|Identidades de Windows de clientes de Fabric con el rol de administrador; se usa para autorizar las operaciones de Fabric con privilegios. Lista separada por comas; cada entrada es un nombre de grupo o de cuenta de dominio. Para mayor comodidad, a la cuenta que ejecuta fabric.exe se le asigna automáticamente un rol de administrador. Lo mismo sucede con ServiceFabricAdministrators. |
|AADTenantId|string, el valor predeterminado es L""|estática|Id. de inquilino (GUID) |
|AADClusterApplication|string, el valor predeterminado es L""|estática|Nombre de la aplicación API web o identificador que representa el clúster. |
|AADClientApplication|string, el valor predeterminado es L""|estática|Nombre de la aplicación de cliente nativo o identificador que representa a los clientes de Fabric. |
|X509Folder|string,el valor predeterminado es /var/lib/waagent|estática|Carpeta donde se encuentran los certificados X509 y las claves privadas. |
|FabricHostSpn| string, el valor predeterminado es L"" |estática| Nombre de entidad de seguridad de servicio de FabricHost cuando Fabric se ejecuta como usuario de dominio único (cuenta de usuario de dominio o gMSA) y FabricHost se ejecuta en la cuenta de la máquina. Es el SPN del agente de escucha de IPC para FabricHost, que, de forma predeterminada, se debe dejar vacío, ya que FabricHost se ejecuta con la cuenta de la máquina. |
|DisableFirewallRuleForPublicProfile| bool, el valor predeterminado es TRUE | estática|Indica si no se debe habilitar la regla de firewall para el perfil público. |
|DisableFirewallRuleForPrivateProfile| bool, el valor predeterminado es TRUE |estática| Indica si no se debe habilitar la regla de firewall para el perfil privado. | 
|DisableFirewallRuleForDomainProfile| bool, el valor predeterminado es TRUE |estática| Indica si no se debe habilitar la regla de firewall para el perfil de dominio. |
|SettingsX509StoreName| string, el valor predeterminado es L"MY"| Dinámica|Almacén de certificados X509 que usa Fabric para la protección de la configuración. |

### <a name="section-name-securityadminclientx509names"></a>Nombre de sección: Security/AdminClientX509Names
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, el valor predeterminado es None|Dinámica| |

### <a name="section-name-securityclientx509names"></a>Nombre de sección: Security/ClientX509Names
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
PropertyGroup|X509NameMap, el valor predeterminado es None|Dinámica| |

### <a name="section-name-securityclusterx509names"></a>Nombre de sección: Security/ClusterX509Names
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
PropertyGroup|X509NameMap, el valor predeterminado es None|Dinámica| |

### <a name="section-name-securityserverx509names"></a>Nombre de sección: Security/ServerX509Names
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
PropertyGroup|X509NameMap, el valor predeterminado es None|Dinámica| |

### <a name="section-name-securityclientaccess"></a>Nombre de sección: Security/ClientAccess
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| CreateName |string, el valor predeterminado es "Admin". |Dinámica|Configuración de seguridad para la creación de URI de nomenclatura. |
| DeleteName |string, el valor predeterminado es "Admin". |Dinámica|Configuración de seguridad para la eliminación de URI de nomenclatura. |
| PropertyWriteBatch |string, el valor predeterminado es "Admin". |Dinámica|Configuraciones de seguridad para operaciones de escritura de la propiedad de nomenclatura. |
| CreateService |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para la creación del servicio. |
| CreateServiceFromTemplate |string, el valor predeterminado es "Admin". |Dinámica|Configuración de seguridad para la creación de servicios a partir de una plantilla. |
| UpdateService |string, el valor predeterminado es "Admin". |Dinámica|Configuración de seguridad para actualizaciones de servicio. |
| DeleteService  |string, el valor predeterminado es "Admin". |Dinámica|Configuración de seguridad para eliminación de servicio. |
| ProvisionApplicationType |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para aprovisionamiento de tipos de aplicaciones. |
| CreateApplication |string, el valor predeterminado es "Admin". | Dinámica|Configuración de seguridad para creación de aplicaciones. |
| DeleteApplication |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para eliminación de aplicaciones. |
| UpgradeApplication |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para iniciar o interrumpir actualizaciones de aplicaciones. |
| RollbackApplicationUpgrade |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para revertir actualizaciones de aplicaciones. |
| UnprovisionApplicationType |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para dejar de aprovisionar tipos de aplicación. |
| MoveNextUpgradeDomain |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para reanudar actualizaciones de aplicaciones con un dominio de actualización explícito. |
| ReportUpgradeHealth |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para reanudar actualizaciones de aplicaciones con el progreso de actualización actual. |
| ReportHealth |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para mantenimiento de informes. |
| ProvisionFabric |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para aprovisionamiento de MSI o del manifiesto de clúster. |
| UpgradeFabric |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para iniciar actualizaciones del clúster. |
| RollbackFabricUpgrade |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para revertir actualizaciones del clúster. |
| UnprovisionFabric |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para dejar de aprovisionar MSI o el manifiesto de clúster. |
| MoveNextFabricUpgradeDomain |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para reanudar actualizaciones del clúster con un dominio de actualización explícito. |
| ReportFabricUpgradeHealth |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para reanudar actualizaciones del clúster con el progreso de actualización actual. |
| StartInfrastructureTask |string, el valor predeterminado es "Admin". | Dinámica|Configuración de seguridad para iniciar tareas de infraestructura. |
| FinishInfrastructureTask |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para finalizar tareas de infraestructura. |
| ActivateNode |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para activación de un nodo. |
| DeactivateNode |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para desactivación de un nodo. |
| DeactivateNodesBatch |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para desactivación de varios nodos. |
| RemoveNodeDeactivations |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para revertir la desactivación en varios nodos. |
| GetNodeDeactivationStatus |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para comprobar el estado de desactivación. |
| NodeStateRemoved |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para notificar el estado quitado del nodo. |
| RecoverPartition |string, el valor predeterminado es "Admin". | Dinámica|Configuración de seguridad recuperar una partición. |
| RecoverPartitions |string, el valor predeterminado es "Admin". | Dinámica|Configuración de seguridad para recuperar particiones. |
| RecoverServicePartitions |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para recuperar particiones de servicio. |
| RecoverSystemPartitions |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para recuperar particiones de servicio del sistema. |
| ReportFault |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para informes de error. |
| InvokeInfrastructureCommand |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para comandos de administración de tareas de infraestructura. |
| FileContent |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para transferencia de archivos de cliente del almacén de imágenes (externo al clúster). |
| FileDownload |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para inicio de la descarga de archivos de cliente del almacén de imágenes (externo al clúster). |
| InternalList |string, el valor predeterminado es "Admin". | Dinámica|Configuración de seguridad para operación de lista de archivos de cliente del almacén de imágenes (interno). |
| Eliminar |string, el valor predeterminado es "Admin". |Dinámica| Configuraciones de seguridad para la operación de eliminación del cliente del almacén de imágenes. |
| Cargar |string, el valor predeterminado es "Admin". | Dinámica|Configuración de seguridad para operación de carga del cliente del almacén de imágenes. |
| GetStagingLocation |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para recuperación de la ubicación de almacenamiento temporal del cliente del almacén de imágenes. |
| GetStoreLocation |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para recuperación de la ubicación del almacén de clientes del almacén de imágenes. |
| NodeControl |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para inicio, detención y reinicio de nodos. |
| CodePackageControl |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para reiniciar paquetes de código. |
| UnreliableTransportControl |string, el valor predeterminado es "Admin". |Dinámica| Transporte no confiable para agregar y quitar comportamientos. |
| MoveReplicaControl |string, el valor predeterminado es "Admin". | Dinámica|Mueva la réplica. |
| PredeployPackageToNode |string, el valor predeterminado es "Admin". |Dinámica| API previas a la implementación. |
| StartPartitionDataLoss |string, el valor predeterminado es "Admin". |Dinámica| Provoca la pérdida de datos en una partición. |
| StartPartitionQuorumLoss |string, el valor predeterminado es "Admin". |Dinámica| Provoca la pérdida de cuórum en una partición. |
| StartPartitionRestart |string, el valor predeterminado es "Admin". |Dinámica| Reinicia simultáneamente algunas o todas las réplicas de una partición. |
| CancelTestCommand |string, el valor predeterminado es "Admin". |Dinámica| Cancela un comando de prueba específico, si se está ejecutando. |
| StartChaos |string, el valor predeterminado es "Admin". |Dinámica| Inicia Chaos, si no se ha iniciado. |
| StopChaos |string, el valor predeterminado es "Admin". |Dinámica| Detiene Chaos, si se ha iniciado. |
| StartNodeTransition |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para iniciar una transición de nodo. |
| StartClusterConfigurationUpgrade |string, el valor predeterminado es "Admin". |Dinámica| Produce StartClusterConfigurationUpgrade en una partición. |
| GetUpgradesPendingApproval |string, el valor predeterminado es "Admin". |Dinámica| Produce GetUpgradesPendingApproval en una partición. |
| StartApprovedUpgrades |string, el valor predeterminado es "Admin". |Dinámica| Produce StartApprovedUpgrades en una partición. |
| Ping |string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Configuración de seguridad para pings de cliente. |
| Consultar |string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Configuración de seguridad para consultas. |
| NameExists |string, el valor predeterminado es "Admin\.|\|User" | Dinámica|Configuración de seguridad para comprobaciones de existencia de URI de nomenclatura. |
| EnumerateSubnames |string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Configuración de seguridad para enumeración de URI de nomenclatura. |
| EnumerateProperties |string, el valor predeterminado es "Admin\.|\|User" | Dinámica|Configuración de seguridad para enumeración de propiedad de nomenclatura. |
| PropertyReadBatch |string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Configuración de seguridad para operaciones de lectura de propiedad de nomenclatura. |
| GetServiceDescription |string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Configuración de seguridad para notificaciones de servicio de sondeo largo y descripciones de servicio de lectura. |
| ResolveService |string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Configuración de seguridad para resolución de servicio basada en reclamaciones. |
| ResolveNameOwner |string, el valor predeterminado es "Admin\.|\|User" | Dinámica|Configuración de seguridad para resolver propietario de URI de nomenclatura. |
| ResolvePartition |string, el valor predeterminado es "Admin\.|\|User" | Dinámica|Configuración de seguridad para resolver servicios del sistema. |
| ServiceNotifications |string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Configuración de seguridad para notificaciones del servicio basadas en eventos. |
| PrefixResolveService |string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Configuración de seguridad para resolución de prefijo de servicio basada en reclamaciones. |
| GetUpgradeStatus |string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Configuración de seguridad para sondeo de estado de actualización de aplicaciones. |
| GetFabricUpgradeStatus |string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Configuración de seguridad para sondeo de estado de actualización del clúster. |
| InvokeInfrastructureQuery |string, el valor predeterminado es "Admin\.|\|User" | Dinámica|Configuración de seguridad para consultar tareas de infraestructura. |
| Enumerar |string, el valor predeterminado es "Admin\.|\|User" | Dinámica|Configuración de seguridad para operación de lista de archivos de cliente del almacén de imágenes. |
| ResetPartitionLoad |string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Configuración de seguridad para restablecer una carga para una unidad de conmutación por error. |
| ToggleVerboseServicePlacementHealthReporting | string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Configuración de seguridad para alternar informes de mantenimiento detallados de ubicación de servicio. |
| GetPartitionDataLossProgress | string, el valor predeterminado es "Admin\.|\|User" | Dinámica|Captura el progreso de una llamada de API para invocar la pérdida de datos. |
| GetPartitionQuorumLossProgress | string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Captura el progreso de una llamada de API para invocar la pérdida de cuórum. |
| GetPartitionRestartProgress | string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Captura el progreso de una llamada de API para el reinicio de una partición. |
| GetChaosReport | string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Captura el estado de Chaos dentro de un intervalo de tiempo determinado. |
| GetNodeTransitionProgress | string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Configuración de seguridad para obtener el progreso en un comando de transición de nodo. |
| GetClusterConfigurationUpgradeStatus | string, el valor predeterminado es "Admin\.|\|User" |Dinámica| Produce GetClusterConfigurationUpgradeStatus en una partición. |
| GetClusterConfiguration | string, el valor predeterminado es "Admin\.|\|User" | Dinámica|Produce GetClusterConfiguration en una partición. |
|CreateComposeDeployment|string, el valor predeterminado es L"Admin"| Dinámica|Crea una implementación compuesta descrita por archivos compuestos. |
|DeleteComposeDeployment|string, el valor predeterminado es L"Admin"| Dinámica|Elimina la implementación compuesta. |
|UpgradeComposeDeployment|string, el valor predeterminado es L"Admin"| Dinámica|Actualiza la implementación compuesta. |
|ResolveSystemService|string, el valor predeterminado es L"Admin\|\|User"|Dinámica| Configuración de seguridad para resolver servicios del sistema. |
|GetUpgradeOrchestrationServiceState|string, el valor predeterminado es L"Admin"| Dinámica|Induce GetUpgradeOrchestrationServiceState en una partición. |
|SetUpgradeOrchestrationServiceState|string, el valor predeterminado es L"Admin"| Dinámica|Induce SetUpgradeOrchestrationServiceState en una partición. |

### <a name="section-name-reconfigurationagent"></a>Nombre de sección: ReconfigurationAgent
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| ApplicationUpgradeMaxReplicaCloseDuration | Tiempo en segundos, el valor predeterminado es 900. |Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo que esperará el sistema antes de finalizar los hosts de servicio que tienen réplicas detenidas al cerrarse durante la actualización de la aplicación.|
| ServiceApiHealthDuration | Tiempo en segundos, el valor predeterminado es 30 minutos. |Dinámica| Especifique el intervalo de tiempo en segundos. ServiceApiHealthDuration define el tiempo que se espera a que una API se ejecute antes de que se notifique como incorrecta. |
| ServiceReconfigurationApiHealthDuration | Tiempo en segundos, el valor predeterminado es 30. |Dinámica| Especifique el intervalo de tiempo en segundos. ServiceReconfigurationApiHealthDuration define el tiempo que se espera que una API de servicio se ejecute antes de que se notifique como incorrecta. Esto se aplica a las llamadas API que afectan a la disponibilidad.|
| PeriodicApiSlowTraceInterval | Tiempo en segundos, el valor predeterminado es 5 minutos. |Dinámica| Especifique el intervalo de tiempo en segundos. PeriodicApiSlowTraceInterval define el intervalo por encima del cual el monitor de API vuelve a rastrear las llamadas de API lentas. |
| NodeDeactivationMaxReplicaCloseDuration | Tiempo en segundos, el valor predeterminado es 900. |Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo que esperará el sistema antes de finalizar los hosts de servicio que tienen réplicas detenidas al cerrarse durante la desactivación del nodo. |
| FabricUpgradeMaxReplicaCloseDuration | Tiempo en segundos, el valor predeterminado es 900. |Dinámica| Especifique el intervalo de tiempo en segundos. Tiempo que esperará el sistema antes de finalizar los hosts de servicio que tienen réplicas detenidas al cerrarse durante la actualización de Fabric. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(120)|Dinámica|Especifique el intervalo de tiempo en segundos. La duración que espera el sistema antes de terminar los hosts de servicio que tienen réplicas que están detenidas al cerrarse. Si este valor se establece en 0, no se indicará a las réplicas que se cierren.|
|ReplicaChangeRoleFailureRestartThreshold|int, el valor predeterminado es 10|Dinámica| Entero. Especifica el número de errores de la API durante la promoción principal después del cual se aplicará la acción de mitigación automática (reiniciar réplica). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, el valor predeterminado es 2147483647|Dinámica| Entero. Especifica el número de errores de la API durante la promoción principal después del cual se generará un informe de mantenimiento de advertencia.|

### <a name="section-name-placementandloadbalancing"></a>Nombre de sección: PlacementAndLoadBalancing
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| TraceCRMReasons |Bool, el valor predeterminado es true. |Dinámica|Especifica si se rastrearán los motivos de los movimientos emitidos por CRM al canal de eventos operativos. |
| ValidatePlacementConstraint | Bool, el valor predeterminado es true. |Dinámica| Especifica si la expresión PlacementConstraint de un servicio se valida o no cuando se actualiza la descripción de un servicio. |
| PlacementConstraintValidationCacheSize | Int, el valor predeterminado es 10 000. |Dinámica| Limita el tamaño de la tabla usada para la validación rápida y el almacenamiento en caché de expresiones de restricciones de ubicación. |
|VerboseHealthReportLimit | Int, el valor predeterminado es 20. | Dinámica|Define el número de veces que una réplica tiene que ir sin colocar antes de que se notifique una advertencia de mantenimiento para ella (si está habilitado el informe de mantenimiento detallado). |
|ConstraintViolationHealthReportLimit | Int, el valor predeterminado es 50. |Dinámica| Define el número de veces que la réplica que infringe la restricción debe estar sin fijar de forma persistente antes de que se realicen diagnósticos y se emitan informes de mantenimiento. |
|DetailedConstraintViolationHealthReportLimit | Int, el valor predeterminado es 200. |Dinámica| Define el número de veces que la réplica que infringe la restricción debe estar sin fijar de forma persistente antes de que se realicen diagnósticos y se emitan informes de mantenimiento detallados. |
|DetailedVerboseHealthReportLimit | Int, el valor predeterminado es 200. | Dinámica|Define el número de veces que una réplica sin colocar tiene que estar sin colocar de forma persistente antes de que se emitan informes de mantenimiento detallados. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, el valor predeterminado es 20. | Dinámica|Define el número de veces consecutivas que los movimientos emitidos por ResourceBalancer se descartan antes de que se realicen diagnósticos y se emitan advertencias de mantenimiento. Negativo: no se emiten advertencias bajo esta condición. |
|DetailedNodeListLimit | Int, el valor predeterminado es 15. |Dinámica| Define el número de nodos por restricción que se incluirán antes del truncamiento en los informes de réplicas sin colocar. |
|DetailedPartitionListLimit | Int, el valor predeterminado es 15. |Dinámica| Define el número de particiones por entrada de diagnóstico que se incluirán para una restricción antes del truncamiento en Diagnostics. |
|DetailedDiagnosticsInfoListLimit | Int, el valor predeterminado es 15. |Dinámica| Define el número de entradas de diagnóstico (con información detallada) que se incluirán por restricción antes del truncamiento en Diagnostics.|
|PLBRefreshGap | Tiempo en segundos, el valor predeterminado es 1. |Dinámica| Especifique el intervalo de tiempo en segundos. Define la cantidad mínima de tiempo que debe transcurrir antes de que PLB actualice el estado de nuevo. |
|MinPlacementInterval | Tiempo en segundos, el valor predeterminado es 1. |Dinámica| Especifique el intervalo de tiempo en segundos. Define la cantidad mínima de tiempo que debe transcurrir antes de dos rondas de selección de ubicación consecutivas. |
|MinConstraintCheckInterval | Tiempo en segundos, el valor predeterminado es 1. |Dinámica| Especifique el intervalo de tiempo en segundos. Define la cantidad mínima de tiempo que debe transcurrir antes de dos rondas de comprobación de restricciones consecutivas. |
|MinLoadBalancingInterval | Tiempo en segundos, el valor predeterminado es 5. |Dinámica| Especifique el intervalo de tiempo en segundos. Define la cantidad mínima de tiempo que debe transcurrir antes de dos rondas de equilibrio de carga consecutivas. |
|BalancingDelayAfterNodeDown | Tiempo en segundos, el valor predeterminado es 120. |Dinámica|Especifique el intervalo de tiempo en segundos. No inicie actividades de equilibrio dentro de este período después de un evento de inactividad de nodos. |
|BalancingDelayAfterNewNode | Tiempo en segundos, el valor predeterminado es 120. |Dinámica|Especifique el intervalo de tiempo en segundos. No inicie actividades de equilibrio dentro de este período después de agregar un nuevo nodo. |
|ConstraintFixPartialDelayAfterNodeDown | Tiempo en segundos, el valor predeterminado es 120. |Dinámica| Especifique el intervalo de tiempo en segundos. No resuelva infracciones FaultDomain y UpgradeDomain dentro de este período después de un evento de inactividad de nodos. |
|ConstraintFixPartialDelayAfterNewNode | Tiempo en segundos, el valor predeterminado es 120. |Dinámica| Especifique el intervalo de tiempo en segundos. No resuelva infracciones de restricciones FaultDomain y UpgradeDomain dentro de este período después de agregar un nuevo nodo. |
|GlobalMovementThrottleThreshold | Uint, el valor predeterminado es 1000. |Dinámica| Número máximo de movimientos permitidos en la fase de equilibrio en el intervalo pasado indicado por GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForPlacement | Uint, el valor predeterminado es 0. |Dinámica| Número máximo de movimientos permitidos en la fase de selección de ubicación en el intervalo pasado indicado por GlobalMovementThrottleCountingInterval. 0 indica sin límite.|
|GlobalMovementThrottleThresholdForBalancing | Uint, el valor predeterminado es 0. | Dinámica|Número máximo de movimientos permitidos en la fase de equilibrio en el intervalo pasado indicado por GlobalMovementThrottleCountingInterval. 0 indica sin límite. |
|GlobalMovementThrottleCountingInterval | Tiempo en segundos, el valor predeterminado es 600. |estática| Especifique el intervalo de tiempo en segundos. Indique la longitud del intervalo pasado para rastrear movimientos de réplicas por dominio (se usa en combinación con GlobalMovementThrottleThreshold). Puede establecerse en 0 para omitir por completo la limitación global. |
|MovementPerPartitionThrottleThreshold | Uint, el valor predeterminado es 50. |Dinámica| No se producen movimientos relacionados con el equilibrio en una partición si el número de estos movimientos para réplicas de esa partición ha alcanzado o superado el valor de MovementPerFailoverUnitThrottleThreshold en el intervalo pasado indicado por MovementPerPartitionThrottleCountingInterval. |
|MovementPerPartitionThrottleCountingInterval | Tiempo en segundos, el valor predeterminado es 600. |estática| Especifique el intervalo de tiempo en segundos. Indique la longitud del intervalo pasado para rastrear movimientos de réplica para cada partición (se usa en combinación con MovementPerPartitionThrottleThreshold). |
|PlacementSearchTimeout | Tiempo en segundos, el valor predeterminado es 0.5. |Dinámica| Especifique el intervalo de tiempo en segundos. Al ubicar servicios, busque como máximo este período antes de devolver un resultado. |
|UseMoveCostReports | Bool, el valor predeterminado es false. | Dinámica|Indica a LB que ignore el elemento de coste de la función de puntuación. Esto da lugar a un número posiblemente grande de movimientos para una ubicación mejor equilibrada. |
|PreventTransientOvercommit | Bool, el valor predeterminado es false. | Dinámica|Determina si PLB cuenta inmediatamente con los recursos que liberarán los movimientos iniciados. De forma predeterminada, PLB puede iniciar movimientos dentro y fuera en el mismo nodo, lo que puede generar confirmaciones en exceso transitorias. Si establece este parámetro en true, impedirá esta clase de confirmaciones en exceso y se deshabilitará la desfragmentación a petición (también conocida como placementWithMove). |
|InBuildThrottlingEnabled | Bool, el valor predeterminado es false. |Dinámica| Determina si está habilitada la limitación integrada. |
|InBuildThrottlingAssociatedMetric | string, el valor predeterminado es "". |estática| El nombre de métrica asociado de esta limitación. |
|InBuildThrottlingGlobalMaxValue | Int, el valor predeterminado es 0. |Dinámica|El número máximo de réplicas integradas que se permite a nivel global. |
|SwapPrimaryThrottlingEnabled | Bool, el valor predeterminado es false.|Dinámica| Determina si está habilitada la limitación principal de intercambio. |
|SwapPrimaryThrottlingAssociatedMetric | string, el valor predeterminado es "".|estática| El nombre de métrica asociado de esta limitación. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, el valor predeterminado es 0. |Dinámica| El número máximo de réplicas principales de intercambio que se permiten a nivel global. |
|PlacementConstraintPriority | Int, el valor predeterminado es 0. | Dinámica|Determina la prioridad de la restricción de ubicación: 0: máxima; 1: mínima; negativo: ignorar. |
|PreferredLocationConstraintPriority | Int, el valor predeterminado es 2.| Dinámica|Determina la prioridad de la restricción de ubicación preferida: 0 para máxima; 1 para mínima; 2 para optimización; negativo para ignorar |
|CapacityConstraintPriority | Int, el valor predeterminado es 0. | Dinámica|Determina la prioridad de la restricción de capacidad: 0: máxima; 1: mínima; negativo: ignorar. |
|AffinityConstraintPriority | Int, el valor predeterminado es 0. | Dinámica|Determina la prioridad de la restricción de afinidad: 0: máxima; 1: mínima; negativo: ignorar. |
|FaultDomainConstraintPriority | Int, el valor predeterminado es 0. |Dinámica| Determina la prioridad de la restricción del dominio de error: 0: máxima; 1: mínima; negativo: ignorar. |
|UpgradeDomainConstraintPriority | Int, el valor predeterminado es 1.| Dinámica|Determina la prioridad de la restricción del dominio de actualización: 0: máxima; 1: mínima; negativo: ignorar. |
|ScaleoutCountConstraintPriority | Int, el valor predeterminado es 0. |Dinámica| Determina la prioridad de la restricción de recuento de escalado horizontal: 0: máxima; 1: mínima; negativo: ignorar. |
|ApplicationCapacityConstraintPriority | Int, el valor predeterminado es 0. | Dinámica|Determina la prioridad de la restricción de capacidad: 0: máxima; 1: mínima; negativo: ignorar. |
|MoveParentToFixAffinityViolation | Bool, el valor predeterminado es false. |Dinámica| Valor que determina si se pueden mover las réplicas primarias para corregir restricciones de afinidad.|
|MoveExistingReplicaForPlacement | Bool, el valor predeterminado es true. |Dinámica|Valor que determina si se deben mover las réplicas existentes durante la selección de ubicación. |
|UseSeparateSecondaryLoad | Bool, el valor predeterminado es true. | Dinámica|Valor que determina si se deben usar cargas secundarias diferentes. |
|PlaceChildWithoutParent | Bool, el valor predeterminado es true. | Dinámica|Valor que determina si puede colocarse la réplica de servicio secundaria si no hay ninguna réplica principal activa. |
|PartiallyPlaceServices | Bool, el valor predeterminado es true. |Dinámica| Determina si todas las réplicas de servicio del clúster se colocarán "todo o nada" dados los nodos adecuados limitados para ellas.|
|InterruptBalancingForAllFailoverUnitUpdates | Bool, el valor predeterminado es false. | Dinámica|Determina si cualquier tipo de actualización de las unidades de conmutación por error debe interrumpir la ejecución rápida o lenta del equilibrio. Si se especifica "false", la ejecución del equilibrio se interrumpirá si FailoverUnit: se crea o elimina, tiene réplicas que faltan, ha cambiado la ubicación de la réplica principal o ha cambiado el número de réplicas. La ejecución del equilibrio NO se interrumpirá en otros casos: si FailoverUnit: tiene réplicas adicionales, ha cambiado cualquier marca de réplica, ha cambiado solo la versión de partición o en cualquier otro caso. |
|GlobalMovementThrottleThresholdPercentage|double, el valor predeterminado es 0|Dinámica|Número máximo de movimientos totales permitido en las fases de equilibrio y ubicación (expresado como porcentaje del número total de réplicas del clúster) en el último intervalo indicado por GlobalMovementThrottleCountingInterval. 0 indica sin límite. Si se especifican esta opción y GlobalMovementThrottleThreshold, se usa el límite más conservador.|
|GlobalMovementThrottleThresholdPercentageForBalancing|double, el valor predeterminado es 0|Dinámica|Número máximo de movimientos permitido en la fase de equilibrio (expresado como porcentaje del número total de réplicas en PLB) en el último intervalo indicado por GlobalMovementThrottleCountingInterval. 0 indica sin límite. Si se especifican esta opción y GlobalMovementThrottleThresholdForBalancing, se usa el límite más conservador.|
|AutoDetectAvailableResources|bool, el valor predeterminado es TRUE|estática|Esta configuración desencadenará la detección automática de los recursos disponibles en el nodo (CPU y memoria). Cuando esta configuración se establece en true, se leen las capacidades reales y se corrigen si el usuario especificó unas capacidades erróneas de nodo o no las definió. Si esta configuración se establece en false, se hará el seguimiento de una advertencia en que el usuario especificó capacidades incorrectas para el nodo, pero no se corregirán. Esto significa que el usuario quiere especificar capacidades superiores a las reales del nodo o, si no se define ninguna capacidad, se asumirá una capacidad ilimitada. |

### <a name="section-name-hosting"></a>Nombre de sección: Hospedaje
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| ServiceTypeRegistrationTimeout |Tiempo en segundos, el valor predeterminado es 300. |Dinámica|Tiempo máximo permitido para que se registre ServiceType con Fabric. |
| ServiceTypeDisableFailureThreshold |Número entero, el valor predeterminado es 1. |Dinámica|Este es el umbral del recuento de errores después del cual se notifica a FailoverManager (FM) que deshabilite el tipo de servicio en ese nodo y pruebe un nodo diferente para la ubicación. |
| ActivationRetryBackoffInterval |Tiempo en segundos, el valor predeterminado es 5. |Dinámica|Intervalo de retroceso en cada error de activación; con cada error de activación continuo, el sistema vuelve a intentar la activación hasta el valor de MaxActivationFailureCount. El intervalo de reintento en cada intento es un producto del error de activación continua y el intervalo de retroceso de activación. |
| ActivationMaxRetryInterval |Tiempo en segundos, el valor predeterminado es 300. |Dinámica|Con cada error de activación continuo, el sistema vuelve a intentar la activación hasta el valor de ActivationMaxFailureCount. ActivationMaxRetryInterval especifica el intervalo de tiempo de espera antes de un reintento después de cada error de activación. |
| ActivationMaxFailureCount |Número entero, el valor predeterminado es 10. |Dinámica|Número de veces que los reintentos del sistema no lograron la activación antes de desistir. |
|ActivationTimeout| TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(180)|Dinámica| Especifique el intervalo de tiempo en segundos. El tiempo de espera para la activación, desactivación y actualización de la aplicación. |
|ApplicationHostCloseTimeout| TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(120)|Dinámica| Especifique el intervalo de tiempo en segundos. Cuando se detecta el cierre de Fabric en procesos autoactivados, FabricRuntime cierra todas las réplicas en el proceso de host (applicationhost) del usuario. Se trata del tiempo de espera para la operación de cierre. |
|ApplicationUpgradeTimeout| TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(360)|Dinámica| Especifique el intervalo de tiempo en segundos. Tiempo de espera de la actualización de la aplicación. Si el tiempo de espera es menor que el tiempo de "ActivationTimeout", se producirá un error en el implementador. |
|CreateFabricRuntimeTimeout|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(120)|Dinámica| Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la llamada de sincronización FabricCreateRuntime. |
|DeploymentMaxFailureCount|int, el valor predeterminado es 20| Dinámica|La implementación de la aplicación se reintentará las veces especificadas en DeploymentMaxFailureCount antes de que genere un error en el nodo.| 
|DeploymentMaxRetryInterval| TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(3600)|Dinámica| Especifique el intervalo de tiempo en segundos. Intervalo de reintento máximo para la implementación. Con cada error continuo, el intervalo de reintento se calcula como Min( DeploymentMaxRetryInterval; Recuento continuo de errores * DeploymentRetryBackoffInterval). |
|DeploymentRetryBackoffInterval| TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(10)|Dinámica|Especifique el intervalo de tiempo en segundos. Intervalo de espera para el error de implementación. En cada caso de error de implementación continuo, el sistema reintentará la implementación hasta las veces especificadas en MaxDeploymentFailureCount. El intervalo de reintento es producto de un error de implementación continua y el intervalo de espera de la implementación. |
|EnableActivateNoWindow| bool, el valor predeterminado es FALSE|Dinámica| El proceso de activación se crea en segundo plano sin ninguna consola. |
|EnableProcessDebugging|bool, el valor predeterminado es FALSE|Dinámica| Permite iniciar hosts de aplicaciones en el depurador. |
|EndpointProviderEnabled| bool, el valor predeterminado es FALSE|estática| Permite la administración de recursos de punto de conexión con Fabric. Requiere la especificación del inicio y del fin del intervalo de puertos de la aplicación en FabricNode. |
|FabricContainerAppsEnabled| bool, el valor predeterminado es FALSE|estática| |
|FirewallPolicyEnabled|bool, el valor predeterminado es FALSE|estática| Permite la apertura de puertos de firewall para los recursos del punto de conexión con puertos explícitos especificados en ServiceManifest. |
|GetCodePackageActivationContextTimeout|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(120)|Dinámica|Especifique el intervalo de tiempo en segundos. El valor de tiempo de espera para las llamadas de CodePackageActivationContext. Esto no es aplicable a los servicios ad-hoc. |
|IPProviderEnabled|bool, el valor predeterminado es FALSE|estática|Habilita la administración de direcciones IP. |
|NTLMAuthenticationEnabled|bool, el valor predeterminado es FALSE|estática| Proporciona compatibilidad para el uso de NTLM por parte de los paquetes de código que se están ejecutando como otros usuarios para que los procesos entre máquinas puedan comunicarse de forma segura. |
|NTLMAuthenticationPasswordSecret|SecureString, el valor predeterminado es Common::SecureString(L"")|estática|Hash cifrado que se usa para generar la contraseña para los usuarios de NTLM. Debe establecerse si NTLMAuthenticationEnabled es true. El implementador se encarga de validarlo. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, el valor predeterminado es Common::TimeSpan::FromMinutes(3)|Dinámica|Especifique el intervalo de tiempo en segundos. Configuración específica del entorno. Intervalo periódico en el que el hospedaje busca nuevos certificados que se usarán para la configuración de NTLM FileStoreService. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, el valor predeterminado es Common::TimeSpan::FromMinutes(4)|Dinámica| Especifique el intervalo de tiempo en segundos. Tiempo de espera para configurar usuarios de NTLM con nombres comunes de certificado. Se necesitan usuarios de NTLM para los recursos compartidos de FileStoreService. |
|RegisterCodePackageHostTimeout|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(120)|Dinámica| Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la llamada de sincronización de FabricRegisterCodePackageHost. Esto es aplicable solo para hosts de aplicación de paquete de código múltiple como FWP. |
|RequestTimeout|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(30)|Dinámica| Especifique el intervalo de tiempo en segundos. Esto representa el tiempo de espera para la comunicación entre el host de aplicación del usuario y el proceso de Fabric para realizar diversas operaciones relacionadas con el hospedaje, como el registro de fábrica o registro en tiempo de ejecución. |
|RunAsPolicyEnabled| bool, el valor predeterminado es FALSE|estática| Habilita la ejecución de paquetes de código como un usuario local que no es el usuario que está ejecutando el proceso de Fabric. Para habilitar esta directiva, Fabric se debe ejecutar como SYSTEM o como usuario con SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(120)|Dinámica|Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la llamada de sincronización de Register(Stateless/Stateful)ServiceFactory. |
|ServiceTypeDisableGraceInterval|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(30)|Dinámica|Especifique el intervalo de tiempo en segundos. Intervalo de tiempo después del cual se puede deshabilitar el tipo de servicio. |
|EnableDockerHealthCheckIntegration|bool, el valor predeterminado es TRUE|estática|Habilita la integración de eventos HEALTHCHECK de Docker con el informe de mantenimiento del sistema de Service Fabric. |

### <a name="section-name-federation"></a>Nombre de sección: Federación
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| LeaseDuration |Tiempo en segundos, el valor predeterminado es 30. |Dinámica|Duración de una concesión entre un nodo y sus vecinos. |
| LeaseDurationAcrossFaultDomain |Tiempo en segundos, el valor predeterminado es 30. |Dinámica|Duración de una concesión entre un nodo y sus vecinos entre dominios de error. |

### <a name="section-name-clustermanager"></a>Nombre de sección: ClusterManager
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| UpgradeStatusPollInterval |Tiempo en segundos, el valor predeterminado es 60. |Dinámica|La frecuencia de sondeo del estado de actualización de la aplicación. Este valor determina la frecuencia de actualización de cualquier llamada a GetApplicationUpgradeProgress. |
| UpgradeHealthCheckInterval |Tiempo en segundos, el valor predeterminado es 60. |Dinámica|Se comprueba la frecuencia del estado de mantenimiento durante las actualizaciones de una aplicación supervisada. |
| FabricUpgradeStatusPollInterval |Tiempo en segundos, el valor predeterminado es 60. |Dinámica|La frecuencia de sondeo del estado de Service Fabric. Este valor determina la frecuencia de actualización de cualquier llamada a GetFabricUpgradeProgress. |
| FabricUpgradeHealthCheckInterval |Tiempo en segundos, el valor predeterminado es 60. |Dinámica|Se comprueba la frecuencia del estado de mantenimiento durante una actualización de Fabric supervisada. |
|InfrastructureTaskProcessingInterval | Tiempo en segundos, el valor predeterminado es 10 |Dinámica|Especifique el intervalo de tiempo en segundos. El intervalo de procesamiento usado por la máquina de estado de procesamiento de tareas de infraestructura. |
|TargetReplicaSetSize |Int, el valor predeterminado es 7. |No permitida|TargetReplicaSetSize para ClusterManager. |
|MinReplicaSetSize |Int, el valor predeterminado es 3. |No permitida|MinReplicaSetSize para ClusterManager. |
|ReplicaRestartWaitDuration |Tiempo en segundos, el valor predeterminado es (60.0 * 30).|No permitida|Especifique el intervalo de tiempo en segundos. ReplicaRestartWaitDuration para ClusterManager. |
|QuorumLossWaitDuration |Tiempo en segundos, el valor predeterminado es MaxValue. |No permitida| Especifique el intervalo de tiempo en segundos. QuorumLossWaitDuration para ClusterManager. |
|StandByReplicaKeepDuration | Tiempo en segundos, el valor predeterminado es (3600.0 * 2).|No permitida|Especifique el intervalo de tiempo en segundos. StandByReplicaKeepDuration para ClusterManager. |
|PlacementConstraints | string, el valor predeterminado es "". |No permitida|PlacementConstraints para ClusterManager. |
|SkipRollbackUpdateDefaultService | Bool, el valor predeterminado es false. |Dinámica|El CM omitirá la reversión de los servicios predeterminados actualizados durante la reversión de la actualización de aplicaciones. |
|EnableDefaultServicesUpgrade | Bool, el valor predeterminado es false. |Dinámica|Habilita la actualización de servicios predeterminados durante la actualización de aplicaciones. Las descripciones de servicios predeterminados se deben sobrescribir después de la actualización. |
|InfrastructureTaskHealthCheckWaitDuration |Tiempo en segundos, el valor predeterminado es 0.|Dinámica| Especifique el intervalo de tiempo en segundos. La cantidad de tiempo de espera antes de iniciar las comprobaciones de mantenimiento después del posprocesamiento de una tarea de infraestructura. |
|InfrastructureTaskHealthCheckStableDuration | Tiempo en segundos, el valor predeterminado es 0.|Dinámica| Especifique el intervalo de tiempo en segundos. La cantidad de tiempo que se observarán comprobaciones de mantenimiento pasadas consecutivas antes de que el posprocesamiento de una tarea de infraestructura finalice correctamente. Al observarse una comprobación de mantenimiento con error, se restablecerá este temporizador. |
|InfrastructureTaskHealthCheckRetryTimeout | Tiempo en segundos, el valor predeterminado es 60. |Dinámica|Especifique el intervalo de tiempo en segundos. La cantidad de tiempo que se dedicará a reintentar las comprobaciones de mantenimiento con error durante el posprocesamiento de una tarea de infraestructura. Al observarse una comprobación de mantenimiento pasada, se restablecerá este temporizador. |
|ImageBuilderTimeoutBuffer |Tiempo en segundos, el valor predeterminado es 3. |Dinámica|Especifique el intervalo de tiempo en segundos. La cantidad de tiempo que se permitirá que se devuelvan errores de tiempo de espera específicos de Image Builder al cliente. Si este búfer es demasiado pequeño, el cliente agota el tiempo de espera antes que el servidor y obtiene un error genérico de tiempo de espera. |
|MinOperationTimeout | Tiempo en segundos, el valor predeterminado es 60. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera mínimo global para procesar internamente las operaciones en ClusterManager. |
|MaxOperationTimeout |Tiempo en segundos, el valor predeterminado es MaxValue. |Dinámica| Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo global para procesar internamente las operaciones en ClusterManager. |
|MaxTimeoutRetryBuffer | Tiempo en segundos, el valor predeterminado es 600. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo de funcionamiento cuando se realizan reintentos internos debido a que el tiempo de espera es <Original Time out>  +  <MaxTimeoutRetryBuffer>. El tiempo de espera adicional se agrega en incrementos de MinOperationTimeout. |
|MaxCommunicationTimeout |Tiempo en segundos, el valor predeterminado es 600. |Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo de espera máximo para la comunicación interna entre ClusterManager y otros servicios del sistema (es decir, el Servicio de nomenclatura, el Administrador de conmutación por error, etc.). Este tiempo de espera debe ser inferior al valor global de MaxOperationTimeout (ya que podría haber muchas comunicaciones entre los componentes del sistema para cada operación de cliente). |
|MaxDataMigrationTimeout |Tiempo en segundos, el valor predeterminado es 600. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo para operaciones de recuperación de migración de datos después de que ha tenido lugar una actualización de Fabric. |
|MaxOperationRetryDelay |Tiempo en segundos, el valor predeterminado es 5.|Dinámica| Especifique el intervalo de tiempo en segundos. El retraso máximo de reintentos internos cuando se producen errores. |
|ReplicaSetCheckTimeoutRollbackOverride |Tiempo en segundos, el valor predeterminado es 1200. |Dinámica| Especifique el intervalo de tiempo en segundos. Si ReplicaSetCheckTimeout se establece en el valor máximo de DWORD, entonces se reemplaza por el valor de esta configuración de cara a la reversión. El valor usado para la puesta al día nunca se reemplaza. |
|ImageBuilderJobQueueThrottle |Int, el valor predeterminado es 10. |Dinámica|Limitación de recuento de subprocesos para la cola de trabajo del proxy de Image Builder en solicitudes de aplicación. |
|MaxExponentialOperationRetryDelay|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(30)|Dinámica|Especifique el intervalo de tiempo en segundos. Retraso máximo exponencial de reintentos internos cuando se producen errores repetidamente. |

### <a name="section-name-defragmentationemptynodedistributionpolicy"></a>Nombre de sección: DefragmentationEmptyNodeDistributionPolicy
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, el valor predeterminado es None|Dinámica|Especifica que la desfragmentación de directiva continúa al vaciar los nodos. Para una métrica 0, indica que SF debe intentar desfragmentar nodos uniformemente entre UD y FD; 1 solo indica que los nodos deben desfragmentarse. |

### <a name="section-name-defragmentationmetrics"></a>Nombre de sección: DefragmentationMetrics
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, el valor predeterminado es None|Dinámica|Determina el conjunto de métricas que deben usarse para la desfragmentación y no para el equilibrio de carga. |

### <a name="section-name-defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>Nombre de sección: DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, el valor predeterminado es None|Dinámica|Determina el número de nodos libres que se necesitan para considerar desfragmentado el clúster al especificar el porcentaje en rango [0.0 - 1.0] o el número de nodos vacíos como número >= 1.0 |

### <a name="section-name-dnsservice"></a>Nombre de sección: DnsService
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|IsEnabled|bool, el valor predeterminado es FALSE|estática| |
|InstanceCount|int, el valor predeterminado es -1|estática|  |

### <a name="section-name-metricactivitythresholds"></a>Nombre de sección: MetricActivityThresholds
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, el valor predeterminado es None|Dinámica|Determina el conjunto de MetricActivityThresholds para las métricas del clúster. El equilibrio funcionará si maxNodeLoad es mayor que MetricActivityThresholds. Para las métricas de desfragmentación, define la cantidad de carga igual o menor que hará que Service Fabric considere el nodo vacío. |

### <a name="section-name-metricbalancingthresholds"></a>Nombre de sección: MetricBalancingThresholds
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, el valor predeterminado es None|Dinámica|Determina el conjunto de MetricBalancingThresholds para las métricas del clúster. El equilibrio funcionará si maxNodeLoad/minNodeLoad es mayor que MetricBalancingThresholds. La desfragmentación funcionará si maxNodeLoad/minNodeLoad en al menos un FD o UD es menor que MetricBalancingThresholds. |

### <a name="section-name-nodebufferpercentage"></a>Nombre de sección: NodeBufferPercentage
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, el valor predeterminado es None|Dinámica|Porcentaje de la capacidad de nodo por nombre de métrica; se usa como un búfer con el fin de dejar algo de espacio libre en un nodo en caso de conmutación por error. |

### <a name="section-name-replication"></a>Nombre de sección: Replication
| **Parámetro** | **Valores permitidos** | **Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|MaxCopyQueueSize|uint, el valor predeterminado es 1024|estática|Es el valor máximo y define el tamaño inicial de la cola que mantiene operaciones de replicación.  Tenga en cuenta que debe ser una potencia de 2.  Si, durante el tiempo de ejecución, la cola crece y alcanza este tamaño, se limitará la operación entre los replicadores principal y secundario.|
|BatchAcknowledgementInterval|TimeSpan, el valor predeterminado es Common::TimeSpan::FromMilliseconds(15)|estática|Especifique el intervalo de tiempo en segundos. Determina la cantidad de tiempo que el replicador espera después de recibir una operación, antes de devolver una confirmación. Las confirmaciones de otras operaciones recibidas durante este período de tiempo se devolverán en un solo mensaje-> de forma que se reduce el tráfico de red pero también posiblemente el rendimiento del replicador.|
|MaxReplicationMessageSize|uint, el valor predeterminado es 52428800|estática|Tamaño máximo de mensaje de las operaciones de replicación. El valor predeterminado es 50 MB.|
|ReplicatorAddress|string, el valor predeterminado es L"localhost:0"|estática|El punto de conexión en forma de cadena -'IP:Port' que usa el replicador de Windows Fabric para establecer conexiones con otras réplicas para enviar o recibir operaciones.|
|ReplicatorListenAddress|string, el valor predeterminado es L"localhost:0"|estática|El punto de conexión en forma de cadena -'IP:Port' que usa el replicador de Windows Fabric para recibir operaciones de otras réplicas.|
|ReplicatorPublishAddress|string, el valor predeterminado es L"localhost:0"|estática|El punto de conexión en forma de cadena -'IP:Port' que usa el replicador de Windows Fabric para enviar operaciones a otras réplicas.|
|MaxPrimaryReplicationQueueSize|uint, el valor predeterminado es 1024|estática|Es el número máximo de operaciones que podrían existir en la cola de replicación principal. Tenga en cuenta que debe ser una potencia de 2.|
|MaxPrimaryReplicationQueueMemorySize|uint, el valor predeterminado es 0|estática|Es el valor máximo de la cola de replicación principal en bytes.|
|MaxSecondaryReplicationQueueSize|uint, el valor predeterminado es 2048|estática|Es el número máximo de operaciones que podrían existir en la cola de replicación secundaria. Tenga en cuenta que debe ser una potencia de 2.|
|MaxSecondaryReplicationQueueMemorySize|uint, el valor predeterminado es 0|estática|Es el valor máximo de la cola de replicación secundaria en bytes.|
|QueueHealthMonitoringInterval|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(30)|estática|Especifique el intervalo de tiempo en segundos. Este valor determina el período de tiempo que usa el replicador para supervisar los eventos de estado de advertencia o error de las colas de la operación de replicación. Un valor "0" deshabilita la supervisión del estado. |
|QueueHealthWarningAtUsagePercent|uint, el valor predeterminado es 80|estática|Este valor determina el uso de la cola de replicación (en porcentaje) a partir del cual se advierte acerca de un alto uso de la cola. Esto se hace después de un intervalo de gracia de QueueHealthMonitoringInterval. Si el uso de la cola cae por debajo de este porcentaje en el intervalo de gracia.|
|RetryInterval|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(5)|estática|Especifique el intervalo de tiempo en segundos. Cuando una operación se pierde o rechaza, este temporizador determina con qué frecuencia el replicador volverá a intentar enviar la operación.|

### <a name="section-name-transport"></a>Nombre de sección: Transport
| **Parámetro** | **Valores permitidos** |**Directiva de actualización** |**Orientación o breve descripción** |
| --- | --- | --- | --- |
|ResolveOption|string, el valor predeterminado es L"unspecified"|estática|Determina cómo se resuelve el FQDN.  Los valores válidos son "sin especificar/ipv4/ipv6". |
|ConnectionOpenTimeout|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(60)|estática|Especifique el intervalo de tiempo en segundos. Tiempo de espera para la configuración de la conexión en los lados de aceptación y entrada (incluida la negociación de seguridad en el modo seguro). |

## <a name="next-steps"></a>pasos siguientes
Lea estos artículos para más información sobre la administración de clúster:

[Agregar o quitar certificados del clúster de Azure ](service-fabric-cluster-security-update-certs-azure.md) 

