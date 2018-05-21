---
title: Cambio de la configuración de un clúster de Azure Service Fabric | Microsoft Docs
description: En este artículo se describe la configuración de Fabric y las directivas de actualización de Fabric que se pueden personalizar.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/09/2018
ms.author: aljo
ms.openlocfilehash: 29afb683b579d6b59d9a8002351a57dc6e42fad0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>Personalización de la configuración de un clúster de Service Fabric y una directiva de actualización de Fabric
En este documento se explica cómo personalizar las diversas opciones de configuración de Fabric y la directiva de actualización de Fabric para el clúster de Service Fabric. Puede personalizarlos en [Azure Portal](https://portal.azure.com) o mediante una plantilla de Azure Resource Manager.

> [!NOTE]
> No todas las opciones están disponibles en el portal. En caso de que un ajuste que se muestra a continuación no esté disponible a través del portal, personalícelo mediante una plantilla de Azure Resource Manager.
> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personalización de la configuración de clústeres mediante plantillas de Resource Manager
Los pasos siguientes muestran cómo agregar un nuevo ajuste *MaxDiskQuotaInMB* a la sección *Diagnostics*.

1. Vaya a https://resources.azure.com.
2. Para ir a la suscripción, expanda **subscriptions** -> **\<Su suscripción>** -> **resourceGroups** -> **\<Su grupo de recursos>** -> **providers** -> **Microsoft.ServiceFabric** -> **clusters** -> **\<El nombre del clúster>**
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

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|string, el valor predeterminado es L"None"|estática| ApplicationCertificateValidationPolicy: None: no se valida el certificado de servidor; la solicitud se realiza correctamente. ServiceCertificateThumbprints: hace referencia a la configuración ServiceCertificateThumbprints para la lista separada por comas de las huellas digitales de los certificados remotos en los que puede confiar el proxy inverso. ServiceCommonNameAndIssuer: hace referencia a la configuración ServiceCommonNameAndIssuer para el nombre del firmante y la huella digital del emisor de los certificados remotos en los que puede confiar el proxy inverso. |
|BodyChunkSize |Uint, el valor predeterminado es 16384. |Dinámica| Proporciona el tamaño del fragmento en bytes usado para leer el cuerpo. |
|CrlCheckingFlag|uint, el valor predeterminado es 0x40000000 |Dinámica| Marcas para la validación de la cadena de certificados del servicio o aplicación; p. ej., comprobación de CRL 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY. Establecer la configuración en 0 deshabilita la comprobación de CRL. dwFlags de CertGetCertificateChain proporciona una lista completa de valores admitidos: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Tiempo en segundos. El valor predeterminado es 120 |Dinámica|Especifique el intervalo de tiempo en segundos.  Proporciona el tiempo de espera de solicitud predeterminado para las solicitudes HTTP que se van a procesar en la puerta de enlace de aplicaciones HTTP. |
|ForwardClientCertificate|bool, el valor predeterminado es FALSE|Dinámica| |
|GatewayAuthCredentialType |string, el valor predeterminado es "None" |estática| Indica el tipo de credenciales de seguridad que se usarán en el punto de conexión de la puerta de enlace de aplicaciones HTTP. Valores válidos son "None/X509. |
|GatewayX509CertificateFindType |string, el valor predeterminado es "FindByThumbprint". |Dinámica| Indica cómo buscar el certificado en el almacén especificado mediante el valor admitido GatewayX509CertificateStoreName: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | string, el valor predeterminado es "". |Dinámica| Valor del filtro de búsqueda usado para encontrar el certificado de puerta de enlace de aplicaciones HTTP. Este certificado se configura en el punto de conexión HTTP y también puede usarse para comprobar la identidad de la aplicación en caso de que la necesiten los servicios. Primero se busca FindValue y, si no existe, se busca FindValueSecondary. |
|GatewayX509CertificateFindValueSecondary | string, el valor predeterminado es "". |Dinámica|Valor del filtro de búsqueda usado para encontrar el certificado de puerta de enlace de aplicaciones HTTP. Este certificado se configura en el punto de conexión HTTP y también puede usarse para comprobar la identidad de la aplicación en caso de que la necesiten los servicios. Primero se busca FindValue y, si no existe, se busca FindValueSecondary.|
|GatewayX509CertificateStoreName |string, el valor predeterminado es "My". |Dinámica| Nombre del almacén de certificados X.509 que contiene el certificado de puerta de enlace de aplicaciones HTTP. |
|HttpRequestConnectTimeout|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(5)|Dinámica|Especifique el intervalo de tiempo en segundos.  Proporciona el tiempo de espera de conexión para las solicitudes HTTP que se envían desde la puerta de enlace de aplicaciones HTTP.  |
|IgnoreCrlOfflineError|bool, el valor predeterminado es TRUE|Dinámica|Especifica si se ignoran los errores de CRL sin conexión de la verificación del certificado del servicio o la aplicación. |
|IsEnabled |Bool, el valor predeterminado es false. |estática| Habilita o deshabilita HttpApplicationGateway. HttpApplicationGateway está deshabilitado de forma predeterminada y para habilitarlo es necesario establecer esta configuración. |
|NumberOfParallelOperations | Uint, el valor predeterminado es 5000 |estática|Número de lecturas para enviar a la cola del servidor HTTP. Controla el número de solicitudes simultáneas que se pueden satisfacer mediante HttpGateway. |
|RemoveServiceResponseHeaders|string, el valor predeterminado es L"Date; Server"|estática|Lista de encabezados de respuesta separados por punto y coma o comas que se eliminarán de la respuesta del servicio antes de enviarla al cliente. Si se establece en una cadena vacía, se pasarán todos los encabezados que devuelva el servicio como estén. es decir, no se sobrescribirá la fecha ni el servidor. |
|ResolveServiceBackoffInterval |Tiempo en segundos, el valor predeterminado es 5. |Dinámica|Especifique el intervalo de tiempo en segundos.  Proporciona el intervalo de retroceso predeterminado antes de reintentar una operación errónea del servicio de resolución. |
|SecureOnlyMode|bool, el valor predeterminado es FALSE|Dinámica| SecureOnlyMode: true. El proxy inverso solo reenviará a servicios que publican puntos de conexión seguros. false: el proxy inverso puede reenviar solicitudes a puntos de conexión seguros o no seguros.  |
|ServiceCertificateThumbprints|string, el valor predeterminado es L""|Dinámica| |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, el valor predeterminado es None|Dinámica|  |

## <a name="clustermanager"></a>ClusterManager
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|EnableDefaultServicesUpgrade | Bool, el valor predeterminado es false. |Dinámica|Habilita la actualización de servicios predeterminados durante la actualización de aplicaciones. Las descripciones de servicios predeterminados se deben sobrescribir después de la actualización. |
|FabricUpgradeHealthCheckInterval |Tiempo en segundos, el valor predeterminado es 60. |Dinámica|Se comprueba la frecuencia del estado de mantenimiento durante una actualización de Fabric supervisada. |
|FabricUpgradeStatusPollInterval |Tiempo en segundos, el valor predeterminado es 60. |Dinámica|La frecuencia de sondeo del estado de Service Fabric. Este valor determina la frecuencia de actualización de cualquier llamada a GetFabricUpgradeProgress. |
|ImageBuilderTimeoutBuffer |Tiempo en segundos, el valor predeterminado es 3. |Dinámica|Especifique el intervalo de tiempo en segundos. La cantidad de tiempo que se permitirá que se devuelvan errores de tiempo de espera específicos de Image Builder al cliente. Si este búfer es demasiado pequeño, el cliente agota el tiempo de espera antes que el servidor y obtiene un error genérico de tiempo de espera. |
|InfrastructureTaskHealthCheckRetryTimeout | Tiempo en segundos, el valor predeterminado es 60. |Dinámica|Especifique el intervalo de tiempo en segundos. La cantidad de tiempo que se dedicará a reintentar las comprobaciones de mantenimiento con error durante el posprocesamiento de una tarea de infraestructura. Al observarse una comprobación de mantenimiento pasada, se restablecerá este temporizador. |
|InfrastructureTaskHealthCheckStableDuration | Tiempo en segundos, el valor predeterminado es 0.|Dinámica| Especifique el intervalo de tiempo en segundos. La cantidad de tiempo que se observarán comprobaciones de mantenimiento pasadas consecutivas antes de que el posprocesamiento de una tarea de infraestructura finalice correctamente. Al observarse una comprobación de mantenimiento con error, se restablecerá este temporizador. |
|InfrastructureTaskHealthCheckWaitDuration |Tiempo en segundos, el valor predeterminado es 0.|Dinámica| Especifique el intervalo de tiempo en segundos. La cantidad de tiempo de espera antes de iniciar las comprobaciones de mantenimiento después del posprocesamiento de una tarea de infraestructura. |
|InfrastructureTaskProcessingInterval | Tiempo en segundos, el valor predeterminado es 10 |Dinámica|Especifique el intervalo de tiempo en segundos. El intervalo de procesamiento usado por la máquina de estado de procesamiento de tareas de infraestructura. |
|MaxCommunicationTimeout |Tiempo en segundos, el valor predeterminado es 600. |Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo de espera máximo para la comunicación interna entre ClusterManager y otros servicios del sistema (es decir, el Servicio de nomenclatura, el Administrador de conmutación por error, etc.). Este tiempo de espera debe ser inferior al valor global de MaxOperationTimeout (ya que podría haber muchas comunicaciones entre los componentes del sistema para cada operación de cliente). |
|MaxDataMigrationTimeout |Tiempo en segundos, el valor predeterminado es 600. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo para operaciones de recuperación de migración de datos después de que ha tenido lugar una actualización de Fabric. |
|MaxOperationRetryDelay |Tiempo en segundos, el valor predeterminado es 5.|Dinámica| Especifique el intervalo de tiempo en segundos. El retraso máximo de reintentos internos cuando se producen errores. |
|MaxOperationTimeout |Tiempo en segundos, el valor predeterminado es MaxValue. |Dinámica| Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo global para procesar internamente las operaciones en ClusterManager. |
|MaxTimeoutRetryBuffer | Tiempo en segundos, el valor predeterminado es 600. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo de funcionamiento cuando se realizan reintentos internos debido a que el tiempo de espera es <Original Time out>  +  <MaxTimeoutRetryBuffer>. El tiempo de espera adicional se agrega en incrementos de MinOperationTimeout. |
|MinOperationTimeout | Tiempo en segundos, el valor predeterminado es 60. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera mínimo global para procesar internamente las operaciones en ClusterManager. |
|MinReplicaSetSize |Int, el valor predeterminado es 3. |No permitida|MinReplicaSetSize para ClusterManager. |
|PlacementConstraints | string, el valor predeterminado es "". |No permitida|PlacementConstraints para ClusterManager. |
|QuorumLossWaitDuration |Tiempo en segundos, el valor predeterminado es MaxValue. |No permitida| Especifique el intervalo de tiempo en segundos. QuorumLossWaitDuration para ClusterManager. |
|ReplicaRestartWaitDuration |Tiempo en segundos, el valor predeterminado es (60.0 * 30).|No permitida|Especifique el intervalo de tiempo en segundos. ReplicaRestartWaitDuration para ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Tiempo en segundos, el valor predeterminado es 1200. |Dinámica| Especifique el intervalo de tiempo en segundos. Si ReplicaSetCheckTimeout se establece en el valor máximo de DWORD, entonces se reemplaza por el valor de esta configuración de cara a la reversión. El valor usado para la puesta al día nunca se reemplaza. |
|SkipRollbackUpdateDefaultService | Bool, el valor predeterminado es false. |Dinámica|El CM omitirá la reversión de los servicios predeterminados actualizados durante la reversión de la actualización de aplicaciones. |
|StandByReplicaKeepDuration | Tiempo en segundos, el valor predeterminado es (3600.0 * 2).|No permitida|Especifique el intervalo de tiempo en segundos. StandByReplicaKeepDuration para ClusterManager. |
|TargetReplicaSetSize |Int, el valor predeterminado es 7. |No permitida|TargetReplicaSetSize para ClusterManager. |
|UpgradeHealthCheckInterval |Tiempo en segundos, el valor predeterminado es 60. |Dinámica|Se comprueba la frecuencia del estado de mantenimiento durante las actualizaciones de una aplicación supervisada. |
|UpgradeStatusPollInterval |Tiempo en segundos, el valor predeterminado es 60. |Dinámica|La frecuencia de sondeo del estado de actualización de la aplicación. Este valor determina la frecuencia de actualización de cualquier llamada a GetApplicationUpgradeProgress. |

## <a name="common"></a>Común
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Tiempo en segundos, el valor predeterminado es 1. |Dinámica|Especifique el intervalo de tiempo en segundos. Intervalo de supervisión del rendimiento. La configuración en 0 o un valor negativo deshabilita la supervisión. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, el valor predeterminado es None|Dinámica|Especifica que la desfragmentación de directiva continúa al vaciar los nodos. Para una métrica 0, indica que SF debe intentar desfragmentar nodos uniformemente entre UD y FD; 1 solo indica que los nodos deben desfragmentarse. |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, el valor predeterminado es None|Dinámica|Determina el conjunto de métricas que deben usarse para la desfragmentación y no para el equilibrio de carga. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, el valor predeterminado es None|Dinámica|Determina el número de nodos libres que se necesitan para considerar desfragmentado el clúster al especificar el porcentaje en rango [0.0 - 1.0] o el número de nodos vacíos como número >= 1.0 |

## <a name="diagnostics"></a>Diagnóstico
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, el valor predeterminado es true. | Dinámica |Si se requerirá o no suplantación para acceder a los almacenes de diagnóstico en nombre de la aplicación. |
|AppEtwTraceDeletionAgeInDays |Int, el valor predeterminado es 3. | Dinámica |Número de días transcurridos los cuales se eliminarán los archivos ETL antiguos que contienen seguimientos de ETW de aplicación. |
|ApplicationLogsFormatVersion |Int, el valor predeterminado es 0. | Dinámica |Versión del formato de registros de aplicación. Los valores admitidos son 0 y 1. La versión 1 incluye más campos del registro de eventos de ETW que la versión 0. |
|ClusterId |string | Dinámica |El id. único del clúster. Se genera cuando se crea el clúster. |
|ConsumerInstances |string | Dinámica |La lista de instancias de consumidor de DCA. |
|DiskFullSafetySpaceInMB |Int, el valor predeterminado es 1024. | Dinámica |Espacio en disco restante en MB para proteger contra el uso por DCA. |
|EnableCircularTraceSession |Bool, el valor predeterminado es false. | estática |La marca indica si se deben usar sesiones de seguimiento circulares. |
|EnableTelemetry |Bool, el valor predeterminado es true. | Dinámica |Indica si se habilitará o deshabilitará la telemetría. |
|MaxDiskQuotaInMB |Int, el valor predeterminado es 65 536. | Dinámica |Cuota de disco en MB para archivos de registro de Windows Fabric. |
|ProducerInstances |string | Dinámica |La lista de instancias de productor de DCA. |

## <a name="dnsservice"></a>DnsService
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|IsEnabled|bool, el valor predeterminado es FALSE|estática| |
|InstanceCount|int, el valor predeterminado es -1|estática|  |

## <a name="fabricclient"></a>FabricClient
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Tiempo en segundos, el valor predeterminado es 2. |Dinámica|Especifique el intervalo de tiempo en segundos. El intervalo de tiempo de espera de conexión para cada vez que el cliente intenta abrir una conexión a la puerta de enlace.|
|HealthOperationTimeout. |Tiempo en segundos, el valor predeterminado es 120. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera de un mensaje de informe enviado al administrador de mantenimiento. |
|HealthReportRetrySendInterval |Tiempo en segundos, el valor predeterminado es 30. |Dinámica|Especifique el intervalo de tiempo en segundos. El intervalo dentro del cual el componente de informes reenvía informes de mantenimiento acumulados al administrador de mantenimiento. |
|HealthReportSendInterval. |Tiempo en segundos, el valor predeterminado es 30. |Dinámica|Especifique el intervalo de tiempo en segundos. El intervalo dentro del cual el componente de informes envía informes de mantenimiento acumulados al administrador de mantenimiento. |
|KeepAliveIntervalInSeconds |Int, el valor predeterminado es 20. |estática|El intervalo dentro del cual el transporte de FabricClient envía mensajes persistentes a la puerta de enlace. Para 0; keepAlive está deshabilitado. Debe ser un valor positivo. |
|MaxFileSenderThreads |Uint, el valor predeterminado es 10. |estática|El número máximo de archivos que se transfieren en paralelo. |
|NodeAddresses |string, el valor predeterminado es "". |estática|Una colección de direcciones (cadenas de conexión) en diferentes nodos que se pueden usar para comunicarse con el servicio de nomenclatura. Inicialmente el cliente se conecta mediante la selección de una de las direcciones de manera aleatoria. Si se proporciona más de una cadena de conexión y la conexión no se establece por un error de comunicación o de tiempo de espera, el cliente cambia al uso de la siguiente dirección de manera secuencial. Consulte la sección de reintentos de direcciones del servicio de nomenclatura para más información sobre la semántica de reintentos. |
|PartitionLocationCacheLimit |Int, el valor predeterminado es 100 000. |estática|Número de particiones almacenadas en caché para la resolución del servicio (se establece en 0 para un número ilimitado). |
|RetryBackoffInterval |Tiempo en segundos, el valor predeterminado es 3. |Dinámica|Especifique el intervalo de tiempo en segundos. El intervalo de interrupción antes de volver a intentar la operación. |
|ServiceChangePollInterval |Tiempo en segundos, el valor predeterminado es 120. |Dinámica|Especifique el intervalo de tiempo en segundos. El intervalo entre sondeos consecutivos del servicio cambia del cliente a la puerta de enlace para las devoluciones de llamada registradas de notificaciones de cambio de servicio. |

## <a name="fabrichost"></a>FabricHost
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, el valor predeterminado es 10. |Dinámica|Es el recuento máximo que el sistema reintentará la activación que ha generado error antes de desistir. |
|ActivationMaxRetryInterval |Tiempo en segundos, el valor predeterminado es 300. |Dinámica|Especifique el intervalo de tiempo en segundos. Intervalo de reintento máximo para la activación. Con cada error continuo, el intervalo de reintento se calcula como Min( ActivationMaxRetryInterval; Recuento continuo de errores * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Tiempo en segundos, el valor predeterminado es 5. |Dinámica|Especifique el intervalo de tiempo en segundos. Intervalo de retroceso en cada error de activación; con cada error de activación continuo, el sistema vuelve a intentar la activación hasta el valor de MaxActivationFailureCount. El intervalo de reintento en cada intento es un producto del error de activación continua y el intervalo de retroceso de activación. |
|EnableRestartManagement |Bool, el valor predeterminado es false. |Dinámica|Sirve para habilitar el reinicio del servidor. |
|EnableServiceFabricAutomaticUpdates |Bool, el valor predeterminado es false. |Dinámica|Sirve para habilitar la actualización automática de Fabric mediante Windows Update. |
|EnableServiceFabricBaseUpgrade |Bool, el valor predeterminado es false. |Dinámica|Sirve para habilitar la actualización básica en el servidor. |
|StartTimeout |Tiempo en segundos, el valor predeterminado es 300. |Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo de espera para el inicio de fabricactivationmanager. |
|StopTimeout |Tiempo en segundos, el valor predeterminado es 300. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera para la activación, desactivación y actualización del servicio hospedado. |

## <a name="fabricnode"></a>FabricNode
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |string, el valor predeterminado es "FindByThumbprint". |Dinámica|Indica cómo buscar el certificado en el almacén especificado mediante el valor admitido ClientAuthX509StoreName: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |string, el valor predeterminado es "". | Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado para el rol de administrador predeterminado FabricClient. |
|ClientAuthX509FindValueSecondary |string, el valor predeterminado es "". |Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado para el rol de administrador predeterminado FabricClient. |
|ClientAuthX509StoreName |string, el valor predeterminado es "My". |Dinámica|Nombre del almacén de certificados X.509 que contiene el certificado para el rol de administrador predeterminado FabricClient. |
|ClusterX509FindType |string, el valor predeterminado es "FindByThumbprint". |Dinámica|Indica cómo buscar certificados de clúster en el almacén especificado mediante los valores admitidos de ClusterX509StoreName: "FindByThumbprint"; "FindBySubjectName" con "FindBySubjectName"; cuando existen varias coincidencias, se usa la que tiene la expiración más lejana. |
|ClusterX509FindValue |string, el valor predeterminado es "". |Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado de clúster. |
|ClusterX509FindValueSecondary |string, el valor predeterminado es "". |Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado de clúster. |
|ClusterX509StoreName |string, el valor predeterminado es "My". |Dinámica|Nombre del almacén de certificados X.509 que contiene el certificado de clúster para proteger la comunicación dentro del clúster. |
|EndApplicationPortRange |Int, el valor predeterminado es 0. |estática|Fin (no inclusivo) de los puertos de aplicación administrados por el subsistema de hospedaje. Requerido si EndpointFilteringEnabled es true en Hosting. |
|ServerAuthX509FindType |string, el valor predeterminado es "FindByThumbprint". |Dinámica|Indica cómo buscar el certificado de servidor en el almacén especificado mediante el valor admitido ServerAuthX509StoreName: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |string, el valor predeterminado es "". |Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado de servidor. |
|ServerAuthX509FindValueSecondary |string, el valor predeterminado es "". |Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado de servidor. |
|ServerAuthX509StoreName |string, el valor predeterminado es "My". |Dinámica|Nombre del almacén de certificados X.509 que contiene el certificado de servidor para el servicio de entrada. |
|StartApplicationPortRange |Int, el valor predeterminado es 0. |estática|Inicio de los puertos de aplicación administrados por el subsistema de hospedaje. Requerido si EndpointFilteringEnabled es true en Hosting. |
|StateTraceInterval |Tiempo en segundos, el valor predeterminado es 300. |estática|Especifique el intervalo de tiempo en segundos. El intervalo del estado de nodo de seguimiento en cada nodo y en los nodos activos en FM/FMM. |
|UserRoleClientX509FindType |string, el valor predeterminado es "FindByThumbprint". |Dinámica|Indica cómo buscar el certificado en el almacén especificado mediante el valor admitido UserRoleClientX509StoreName: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |string, el valor predeterminado es "". |Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado para el rol de usuario predeterminado FabricClient. |
|UserRoleClientX509FindValueSecondary |string, el valor predeterminado es "". |Dinámica|Valor de filtro de búsqueda usado para encontrar el certificado para el rol de usuario predeterminado FabricClient. |
|UserRoleClientX509StoreName |string, el valor predeterminado es "My". |Dinámica|Nombre del almacén de certificados X.509 que contiene el certificado para el rol de usuario predeterminado FabricClient. |

## <a name="failovermanager"></a>FailoverManager
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|BuildReplicaTimeLimit|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(3600)|Dinámica|Especifique el intervalo de tiempo en segundos. Límite de tiempo para crear una réplica con estado. Una vez transcurrido, se iniciará un informe de estado de advertencia. |
|ClusterPauseThreshold|int, el valor predeterminado es 1|Dinámica|Si el número de nodos del sistema está por debajo de este valor, la ubicación, el equilibrio de carga y la conmutación por error se detendrán. |
|CreateInstanceTimeLimit|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(300)|Dinámica|Especifique el intervalo de tiempo en segundos. Límite de tiempo para crear una instancia sin estado. Una vez transcurrido, se iniciará un informe de estado de advertencia. |
|ExpectedClusterSize|int, el valor predeterminado es 1|Dinámica|Cuando el clúster se inicie por primera vez, el FM esperará a que estos nodos informen de su estado para empezar a colocar otros servicios, incluidos los servicios del sistema, como la nomenclatura. Al aumentar este valor, también aumenta el tiempo que tarda un clúster en iniciarse; pero se impide que los nodos anticipados se sobrecarguen, así como los movimientos adicionales necesarios a medida que haya más nodos conectados. Generalmente, este valor debe establecerse en una pequeña fracción del tamaño inicial del clúster. |
|ExpectedNodeDeactivationDuration|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(60.0 * 30)|Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo que se espera que tarde un nodo en completar la desactivación. |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(60.0 * 30)|Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo que se espera que tarde un nodo en actualizarse durante la actualización de Windows Fabric. |
|ExpectedReplicaUpgradeDuration|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(60.0 * 30)|Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo que se espera que tarden todas las réplicas en actualizarse en un nodo durante la actualización de la aplicación. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, el valor predeterminado es TRUE|Dinámica|Si se establece en true, se permitirá mover las réplicas con un tamaño de conjunto de réplicas de destino de 1 durante la actualización. |
|MinReplicaSetSize|int, el valor predeterminado es 3|No permitida|Este es el tamaño del conjunto de réplicas mínimo para FM. Si el número de réplicas activas de FM cae por debajo de este valor, el FM rechazará los cambios en el clúster hasta que se recupere al menos el número mínimo de réplicas. |
|PlacementConstraints|string, el valor predeterminado es L""|No permitida|Cualquier restricción de ubicación para las réplicas del Administrador de conmutación por error. |
|PlacementTimeLimit|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(600)|Dinámica|Especifique el intervalo de tiempo en segundos. Límite de tiempo para alcanzar el número de réplicas de destino. Una vez transcurrido, se iniciará un informe de estado de advertencia. |
|QuorumLossWaitDuration |Tiempo en segundos, el valor predeterminado es MaxValue. |Dinámica|Especifique el intervalo de tiempo en segundos. Es la duración máxima que se permite que una partición esté en estado de pérdida de cuórum. Si después de esta duración la partición sigue en pérdida de cuórum, se recupera de dicha pérdida considerando perdidas las réplicas inactivas. Tenga en cuenta que esto puede provocar una pérdida de datos. |
|ReconfigurationTimeLimit|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(300)|Dinámica|Especifique el intervalo de tiempo en segundos. Límite de tiempo para volver a configurar. Una vez transcurrido, se iniciará un informe de estado de advertencia. |
|ReplicaRestartWaitDuration|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(60.0 * 30)|No permitida|Especifique el intervalo de tiempo en segundos. Valor de ReplicaRestartWaitDuration para FMService. |
|StandByReplicaKeepDuration|Timespan, el valor predeterminado es Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|No permitida|Especifique el intervalo de tiempo en segundos. Valor StandByReplicaKeepDuration para FMService. |
|TargetReplicaSetSize|int, el valor predeterminado es 7|No permitida|Este es el número de réplicas de FM de destino que mantendrá Windows Fabric. Un número mayor da como resultado una mayor confiabilidad de los datos de FM, pero un menor equilibrio de rendimiento. |
|UserMaxStandByReplicaCount |Int, el valor predeterminado es 1. |Dinámica|El número máximo predeterminado de réplicas en espera que mantiene el sistema para los servicios de usuario. |
|UserReplicaRestartWaitDuration |Tiempo en segundos, el valor predeterminado es 60.0 * 30. |Dinámica|Especifique el intervalo de tiempo en segundos. Cuando una réplica persistente deja de funcionar, Windows Fabric espera esta cantidad de tiempo a que la réplica vuelva antes de crear nuevas réplicas de reemplazo (lo que requeriría una copia del estado). |
|UserStandByReplicaKeepDuration |Tiempo en segundos, el valor predeterminado es 3600,0 * 24 * 7 |Dinámica|Especifique el intervalo de tiempo en segundos. Cuando una réplica persistente vuelve de un estado inactivo, puede que ya se haya reemplazado. Este temporizador determina cuánto tiempo el FM mantendrá la réplica en espera antes de descartarla. |

## <a name="faultanalysisservice"></a>FaultAnalysisService
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, el valor predeterminado es 604 800. |estática| El tiempo aproximado que las acciones se mantienen en estado terminal. También depende de StoredActionCleanupIntervalInSeconds, dado que el trabajo para realizar la limpieza solo se realiza en ese intervalo. 604800 es 7 días. |
|DataLossCheckPollIntervalInSeconds|int, el valor predeterminado es 5|estática|Tiempo entre las comprobaciones que realiza el sistema mientras se espera a que se produzca la pérdida de datos. El número de veces que se comprobará el número de pérdida de datos por cada iteración interna es DataLossCheckWaitDurationInSeconds/this. |
|DataLossCheckWaitDurationInSeconds|int, el valor predeterminado es 25|estática|Tiempo total, en segundos, que el sistema esperará para que se produzca la pérdida de datos. Esto se usa internamente cuando se llama a la API StartPartitionDataLossAsync(). |
|MinReplicaSetSize |Int, el valor predeterminado es 0. |estática|MinReplicaSetSize para FaultAnalysisService. |
|PlacementConstraints | string, el valor predeterminado es "".|estática| PlacementConstraints para FaultAnalysisService. |
|QuorumLossWaitDuration | Tiempo en segundos, el valor predeterminado es MaxValue. |estática|Especifique el intervalo de tiempo en segundos. QuorumLossWaitDuration para FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, el valor predeterminado es 600|estática|Este parámetro se usa cuando se llama a la API de pérdida de datos. Controla cuánto tiempo esperará el sistema a que se elimine una réplica después de invocar la eliminación internamente. |
|ReplicaRestartWaitDuration |Tiempo en segundos, el valor predeterminado es 60 minutos.|estática|Especifique el intervalo de tiempo en segundos. ReplicaRestartWaitDuration para FaultAnalysisService. |
|StandByReplicaKeepDuration| Tiempo en segundos, el valor predeterminado es (60*24*7) minutos. |estática|Especifique el intervalo de tiempo en segundos. StandByReplicaKeepDuration para FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, el valor predeterminado es 3600. |estática|Es la frecuencia con la que se limpiará el almacén. Solo se quitarán las acciones en estado terminal y que se han completado como mínimo hace CompletedActionKeepDurationInSeconds. |
|StoredChaosEventCleanupIntervalInSeconds | Int, el valor predeterminado es 3600. |estática|La frecuencia con la que se auditará el almacén de cara a la limpieza; si el número de eventos es superior a 30 000, se activa la limpieza. |
|TargetReplicaSetSize |Int, el valor predeterminado es 0. |estática|NOT_PLATFORM_UNIX_START. El valor de TargetReplicaSetSize para FaultAnalysisService. |

## <a name="federation"></a>Federación
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|LeaseDuration |Tiempo en segundos, el valor predeterminado es 30. |Dinámica|Duración de una concesión entre un nodo y sus vecinos. |
|LeaseDurationAcrossFaultDomain |Tiempo en segundos, el valor predeterminado es 30. |Dinámica|Duración de una concesión entre un nodo y sus vecinos entre dominios de error. |

## <a name="filestoreservice"></a>FileStoreService
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|AnonymousAccessEnabled | Bool, el valor predeterminado es true. |estática|Habilita o deshabilita el acceso anónimo a los recursos compartidos de FileStoreService. |
|CommonName1Ntlmx509CommonName|string, el valor predeterminado es L""|estática| Nombre común del certificado X509 usado para generar HMAC en CommonName1NtlmPasswordSecret cuando se usa la autenticación NTLM. |
|CommonName1Ntlmx509StoreLocation|string, el valor predeterminado es L"LocalMachine"|estática|Ubicación del almacén del certificado X509 que se usa para generar HMAC en CommonName1NtlmPasswordSecret cuando se usa la autenticación NTLM. |
|CommonName1Ntlmx509StoreName|string, el valor predeterminado es L"MY"| estática|Nombre del almacén del certificado X509 que se usa para generar HMAC en CommonName1NtlmPasswordSecret cuando se usa la autenticación NTLM. |
|CommonName2Ntlmx509CommonName|string, el valor predeterminado es L""|estática|Nombre común del certificado X509 usado para generar HMAC en CommonName2NtlmPasswordSecret cuando se usa la autenticación NTLM. |
|CommonName2Ntlmx509StoreLocation|string, el valor predeterminado es L"LocalMachine"| estática|Ubicación del almacén del certificado X509 que se usa para generar HMAC en CommonName2NtlmPasswordSecret cuando se usa la autenticación NTLM. |
|CommonName2Ntlmx509StoreName|string, el valor predeterminado es L"MY"|estática| Nombre del almacén del certificado X509 que se usa para generar HMAC en CommonName2NtlmPasswordSecret cuando se usa la autenticación NTLM. |
|CommonNameNtlmPasswordSecret|SecureString, el valor predeterminado es Common::SecureString(L"")| estática|Secreto de contraseña que se usa como valor de inicialización para generar la misma contraseña cuando se usa la autenticación NTLM. |
|GenerateV1CommonNameAccount| bool, el valor predeterminado es TRUE|estática|Especifica si generar una cuenta con el algoritmo de generación de nombre de usuario V1. A partir de la versión 6.1 de Service Fabric, siempre se crea una cuenta con la generación V2. La cuenta V1 es necesaria para las actualizaciones desde y hacia las versiones que no admiten la generación V2 (anteriores a 6.1).|
|MaxCopyOperationThreads | Uint, el valor predeterminado es 0. |Dinámica| El número máximo de archivos paralelos que el replicador secundario puede copiar del principal. "0" == número de núcleos. |
|MaxFileOperationThreads | Uint, el valor predeterminado es 100. |estática| El número máximo de subprocesos paralelos que pueden realizar operaciones de archivos (copiar o mover) en el replicador principal. "0" == número de núcleos. |
|MaxRequestProcessingThreads | Uint, el valor predeterminado es 200. |estática|El número máximo de subprocesos paralelos que pueden procesar solicitudes en el replicador principal. "0" == número de núcleos. |
|MaxSecondaryFileCopyFailureThreshold | Uint, el valor predeterminado es 25.|Dinámica|El número máximo de reintentos de copia de archivos en el replicador secundario antes de desistir. |
|MaxStoreOperations | Uint, el valor predeterminado es 4096. |estática|Número máximo de operaciones paralelas de transacción de almacén que se permiten en el replicador principal. "0" == número de núcleos. |
|NamingOperationTimeout |Tiempo en segundos, el valor predeterminado es 60. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera para realizar la operación de nomenclatura. |
|PrimaryAccountNTLMPasswordSecret | SecureString, el valor predeterminado es empty. |estática| El secreto de contraseña que se usa como valor de inicialización para generar la misma contraseña cuando se usa la autenticación NTLM. |
|PrimaryAccountNTLMX509StoreLocation | string, el valor predeterminado es "LocalMachine".|estática| La ubicación del almacén del certificado X509 que se usa para generar HMAC en PrimaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
|PrimaryAccountNTLMX509StoreName | string, el valor predeterminado es "MY".|estática| El nombre del almacén del certificado X509 que se usa para generar HMAC en PrimaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
|PrimaryAccountNTLMX509Thumbprint | string, el valor predeterminado es "".|estática|La huella digital del certificado X509 que se usa para generar HMAC en PrimaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
|PrimaryAccountType | string, el valor predeterminado es "". |estática|El tipo de cuenta principal de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
|PrimaryAccountUserName | string, el valor predeterminado es "". |estática|El nombre de usuario de la cuenta principal de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
|PrimaryAccountUserPassword | SecureString, el valor predeterminado es empty. |estática|La contraseña de la cuenta principal de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
|QueryOperationTimeout | Tiempo en segundos, el valor predeterminado es 60. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera para realizar una operación de consulta. |
|SecondaryAccountNTLMPasswordSecret | SecureString, el valor predeterminado es empty. |estática| El secreto de contraseña que se usa como valor de inicialización para generar la misma contraseña cuando se usa la autenticación NTLM. |
|SecondaryAccountNTLMX509StoreLocation | string, el valor predeterminado es "LocalMachine". |estática|La ubicación del almacén del certificado X509 que se usa para generar HMAC en SecondaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
|SecondaryAccountNTLMX509StoreName | string, el valor predeterminado es "MY". |estática|El nombre del almacén del certificado X509 que se usa para generar HMAC en SecondaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
|SecondaryAccountNTLMX509Thumbprint | string, el valor predeterminado es "".| estática|La huella digital del certificado X509 que se usa para generar HMAC en SecondaryAccountNTLMPasswordSecret cuando se usa la autenticación NTLM. |
|SecondaryAccountType | string, el valor predeterminado es "".|estática| El tipo de cuenta secundario de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
|SecondaryAccountUserName | string, el valor predeterminado es "".| estática|El nombre de usuario de la cuenta secundaria de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |
|SecondaryAccountUserPassword | SecureString, el valor predeterminado es empty. |estática|La contraseña de la cuenta secundaria de la entidad de seguridad para los recursos compartidos de FileStoreService de la ACL. |

## <a name="healthmanager"></a>HealthManager
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Bool, el valor predeterminado es false. |estática|Directiva de evaluación de mantenimiento del clúster: habilita la evaluación de mantenimiento de tipos por aplicación. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Bool, el valor predeterminado es false. |estática|Directiva de evaluación de mantenimiento del clúster: las advertencias se tratan como errores. |
|MaxPercentUnhealthyApplications | Int, el valor predeterminado es 0. |estática|Directiva de evaluación de mantenimiento del clúster: porcentaje máximo permitido de aplicaciones incorrectas para que el estado del clúster sea correcto. |
|MaxPercentUnhealthyNodes | Int, el valor predeterminado es 0. |estática|Directiva de evaluación de mantenimiento del clúster: porcentaje máximo permitido de nodos incorrectos para que el estado del clúster sea correcto. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, el valor predeterminado es 10|estática|Directiva de evaluación de estado de actualización del clúster: porcentaje máximo permitido de nodos incorrectos delta para que el estado del clúster sea correcto. |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, el valor predeterminado es 15|estática|Directiva de evaluación de estado de actualización del clúster: porcentaje máximo permitido de nodos incorrectos delta en un dominio de actualización para que el estado del clúster sea correcto. |

## <a name="hosting"></a>Hospedaje
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Número entero, el valor predeterminado es 10. |Dinámica|Número de veces que los reintentos del sistema no lograron la activación antes de desistir. |
|ActivationMaxRetryInterval |Tiempo en segundos, el valor predeterminado es 300. |Dinámica|Con cada error de activación continuo, el sistema vuelve a intentar la activación hasta el valor de ActivationMaxFailureCount. ActivationMaxRetryInterval especifica el intervalo de tiempo de espera antes de un reintento después de cada error de activación. |
|ActivationRetryBackoffInterval |Tiempo en segundos, el valor predeterminado es 5. |Dinámica|Intervalo de retroceso en cada error de activación; con cada error de activación continuo, el sistema vuelve a intentar la activación hasta el valor de MaxActivationFailureCount. El intervalo de reintento en cada intento es un producto del error de activación continua y el intervalo de retroceso de activación. |
|ActivationTimeout| TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(180)|Dinámica| Especifique el intervalo de tiempo en segundos. El tiempo de espera para la activación, desactivación y actualización de la aplicación. |
|ApplicationHostCloseTimeout| TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(120)|Dinámica| Especifique el intervalo de tiempo en segundos. Cuando se detecta el cierre de Fabric en procesos autoactivados, FabricRuntime cierra todas las réplicas en el proceso de host (applicationhost) del usuario. Se trata del tiempo de espera para la operación de cierre. |
|ApplicationUpgradeTimeout| TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(360)|Dinámica| Especifique el intervalo de tiempo en segundos. Tiempo de espera de la actualización de la aplicación. Si el tiempo de espera es menor que el tiempo de "ActivationTimeout", se producirá un error en el implementador. |
|CreateFabricRuntimeTimeout|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(120)|Dinámica| Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la llamada de sincronización FabricCreateRuntime. |
|DeploymentMaxFailureCount|int, el valor predeterminado es 20| Dinámica|La implementación de la aplicación se reintentará las veces especificadas en DeploymentMaxFailureCount antes de que genere un error en el nodo.| 
|DeploymentMaxRetryInterval| TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(3600)|Dinámica| Especifique el intervalo de tiempo en segundos. Intervalo de reintento máximo para la implementación. Con cada error continuo, el intervalo de reintento se calcula como Min( DeploymentMaxRetryInterval; Recuento continuo de errores * DeploymentRetryBackoffInterval). |
|DeploymentRetryBackoffInterval| TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(10)|Dinámica|Especifique el intervalo de tiempo en segundos. Intervalo de espera para el error de implementación. En cada caso de error de implementación continuo, el sistema reintentará la implementación hasta las veces especificadas en MaxDeploymentFailureCount. El intervalo de reintento es producto de un error de implementación continua y el intervalo de espera de la implementación. |
|EnableActivateNoWindow| bool, el valor predeterminado es FALSE|Dinámica| El proceso de activación se crea en segundo plano sin ninguna consola. |
|EnableDockerHealthCheckIntegration|bool, el valor predeterminado es TRUE|estática|Habilita la integración de eventos HEALTHCHECK de Docker con el informe de mantenimiento del sistema de Service Fabric. |
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
|ServiceTypeDisableFailureThreshold |Número entero, el valor predeterminado es 1. |Dinámica|Este es el umbral del recuento de errores después del cual se notifica a FailoverManager (FM) que deshabilite el tipo de servicio en ese nodo y pruebe un nodo diferente para la ubicación. |
|ServiceTypeDisableGraceInterval|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(30)|Dinámica|Especifique el intervalo de tiempo en segundos. Intervalo de tiempo después del cual se puede deshabilitar el tipo de servicio. |
|ServiceTypeRegistrationTimeout |Tiempo en segundos, el valor predeterminado es 300. |Dinámica|Tiempo máximo permitido para que se registre ServiceType con Fabric. |

## <a name="httpgateway"></a>HttpGateway
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, el valor predeterminado es 50. |estática| Número de lecturas para enviar a la cola del servidor HTTP. Controla el número de solicitudes simultáneas que se pueden satisfacer mediante HttpGateway. |
|HttpGatewayHealthReportSendInterval |Tiempo en segundos, el valor predeterminado es 30. |estática|Especifique el intervalo de tiempo en segundos. El intervalo con el que la puerta de enlace HTTP envía los informes de mantenimiento acumulados a Health Manager. |
|IsEnabled|Bool, el valor predeterminado es false. |estática| Habilita o deshabilita HttpGateway. HttpGateway está deshabilitado de manera predeterminada. |
|MaxEntityBodySize |Uint, el valor predeterminado es 4 194 304. |Dinámica|Proporciona el tamaño máximo del cuerpo que se puede esperar de una solicitud HTTP. El valor predeterminado es 4 MB. Httpgateway no atenderá una solicitud si el tamaño del cuerpo es mayor que su valor. El tamaño mínimo del fragmento de lectura es 4096 bytes. Así que tiene que ser > = 4096. |

## <a name="imagestoreclient"></a>ImageStoreClient
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|ClientCopyTimeout | Tiempo en segundos, el valor predeterminado es 1800. |Dinámica| Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de copia de nivel superior para el servicio de almacén de imágenes. |
|ClientDefaultTimeout | Tiempo en segundos, el valor predeterminado es 180. |Dinámica| Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera para todas las solicitudes que no son de carga ni de descarga (por ejemplo, exists o delete) al servicio del almacén de imágenes. |
|ClientDownloadTimeout | Tiempo en segundos, el valor predeterminado es 1800. |Dinámica| Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de descarga de nivel superior para el servicio de almacén de imágenes. |
|ClientListTimeout | Tiempo en segundos, el valor predeterminado es 600. |Dinámica|Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de lista de nivel superior para el servicio de almacén de imágenes. |
|ClientUploadTimeout |Tiempo en segundos, el valor predeterminado es 1800. |Dinámica|Especifique el intervalo de tiempo en segundos. Valor de tiempo de espera de la solicitud de carga de nivel superior para el servicio de almacén de imágenes. |

## <a name="imagestoreservice"></a>ImageStoreService
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|habilitado |Bool, el valor predeterminado es false. |estática|La marca "Enabled" para ImageStoreService. Valor predeterminado: false |
|MinReplicaSetSize | Int, el valor predeterminado es 3. |estática|MinReplicaSetSize para ImageStoreService. |
|PlacementConstraints | string, el valor predeterminado es "". |estática| PlacementConstraints para ImageStoreService. |
|QuorumLossWaitDuration | Tiempo en segundos, el valor predeterminado es MaxValue. |estática| Especifique el intervalo de tiempo en segundos. QuorumLossWaitDuration para ImageStoreService. |
|ReplicaRestartWaitDuration | Tiempo en segundos, el valor predeterminado es 60.0 * 30. |estática|Especifique el intervalo de tiempo en segundos. ReplicaRestartWaitDuration para ImageStoreService. |
|StandByReplicaKeepDuration | Tiempo en segundos, el valor predeterminado es 3600.0 * 2. |estática| Especifique el intervalo de tiempo en segundos. StandByReplicaKeepDuration para ImageStoreService. |
|TargetReplicaSetSize | Int, el valor predeterminado es 7. |estática|TargetReplicaSetSize para ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, el valor predeterminado es 1. |Dinámica|Marca que indica si los valores de memoria se deben configurar automática y dinámicamente. Si es 0, la configuración de memoria se usa directamente y no cambia en función de las condiciones del sistema. Si es 1, la configuración de memoria se realiza automáticamente y puede cambiar según las condiciones del sistema. |
|MaximumDestagingWriteOutstandingInKB | Int, el valor predeterminado es 0. |Dinámica|El número de KB que se permite que vaya por delante el registro compartido con respecto al registro dedicado. Use 0 para indicar que no hay límite.
|SharedLogId |string, el valor predeterminado es "". |estática|GUID único del contenedor de registros compartidos. Use "" si utiliza la ruta de acceso predeterminada en la raíz de datos de Fabric. |
|SharedLogPath |string, el valor predeterminado es "". |estática|Nombre de archivo y ruta a la ubicación para colocar el contenedor de registros compartidos. Use "" para utilizar la ruta de acceso predeterminada en la raíz de datos de Fabric. |
|SharedLogSizeInMB |Int, el valor predeterminado es 8192. |estática|El número de MB para asignar en el contenedor de registros compartidos. |
|WriteBufferMemoryPoolMaximumInKB | Int, el valor predeterminado es 0. |Dinámica|El número de KB que se permite que crezca el bloque de memoria del búfer de escritura. Use 0 para indicar que no hay límite. |
|WriteBufferMemoryPoolMinimumInKB |Int, el valor predeterminado es 8 388 608. |Dinámica|El número de KB para asignar inicialmente al bloque de memoria del búfer de escritura. Use 0 para indicar que no hay límite. El valor predeterminado debe ser coherente con el valor de SharedLogSizeInMB siguiente. |

## <a name="management"></a>Administración
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|AzureStorageMaxConnections | Int, el valor predeterminado es 5000. |Dinámica|El número máximo de conexiones simultáneas a Azure Storage. |
|AzureStorageMaxWorkerThreads | Int, el valor predeterminado es 25. |Dinámica|El número máximo de subprocesos de trabajo en paralelo. |
|AzureStorageOperationTimeout | Tiempo en segundos, el valor predeterminado es 6000. |Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo de espera para que finalice la operación xstore. |
|DisableChecksumValidation | Bool, el valor predeterminado es false. |estática| Esta configuración permite habilitar o deshabilitar la validación de suma de comprobación durante el aprovisionamiento de aplicaciones. |
|DisableServerSideCopy | Bool, el valor predeterminado es false. |estática|Esta configuración habilita o deshabilita la copia del lado servidor del paquete de aplicación en ImageStore durante el aprovisionamiento de aplicaciones. |
|ImageCachingEnabled | Bool, el valor predeterminado es true. |estática|Esta configuración permite habilitar o deshabilitar el almacenamiento en caché. |
|ImageStoreConnectionString |SecureString |estática|Cadena de conexión a la raíz de ImageStore. |
|ImageStoreMinimumTransferBPS | Int, el valor predeterminado es 1024. |Dinámica|La velocidad mínima de transferencia entre el clúster e ImageStore. Este valor se usa para determinar el tiempo de espera al acceder al elemento ImageStore externo. Cambie este valor solo si la latencia entre el clúster e ImageStore es alta a fin de dar más tiempo al clúster para que se descargue del elemento ImageStore externo. |

## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, el valor predeterminado es None|Dinámica|Determina el conjunto de MetricActivityThresholds para las métricas del clúster. El equilibrio funcionará si maxNodeLoad es mayor que MetricActivityThresholds. Para las métricas de desfragmentación, define la cantidad de carga igual o menor que hará que Service Fabric considere el nodo vacío. |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, el valor predeterminado es None|Dinámica|Determina el conjunto de MetricBalancingThresholds para las métricas del clúster. El equilibrio funcionará si maxNodeLoad/minNodeLoad es mayor que MetricBalancingThresholds. La desfragmentación funcionará si maxNodeLoad/minNodeLoad en al menos un FD o UD es menor que MetricBalancingThresholds. |

## <a name="namingservice"></a>NamingService
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, el valor predeterminado es 0. |estática|El número máximo de entradas mantenidas en la caché de descripción del servicio LRU en la puerta de enlace de nomenclatura (se establece en 0 para un número ilimitado). |
|MaxClientConnections |Int, el valor predeterminado es 1000. |Dinámica|El número máximo permitido de conexiones de cliente por puerta de enlace. |
|MaxFileOperationTimeout |Tiempo en segundos, el valor predeterminado es 30. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo permitido para la operación del servicio de almacén de archivos. Las solicitudes que especifiquen un tiempo de espera mayor se rechazarán. |
|MaxIndexedEmptyPartitions |Int, el valor predeterminado es 1000. |Dinámica|El número máximo de particiones vacías que permanecerán indexadas en la caché de notificaciones para la sincronización de los clientes que se vuelven a conectar. Cualquier partición vacía por encima de este número se quitará del índice en orden ascendente de la versión de búsqueda. Durante la reconexión, los clientes se pueden seguir sincronizando y pueden recibir actualizaciones de particiones vacías que faltan, pero el protocolo de sincronización se encarece. |
|MaxMessageSize |Int, el valor predeterminado es 4\*1024\*1024. |estática|El tamaño de mensaje máximo para la comunicación del nodo cliente cuando se usa la nomenclatura. Mitigación de ataque de DOS; el valor predeterminado es 4 MB. |
|MaxNamingServiceHealthReports | Int, el valor predeterminado es 10. |Dinámica|El número máximo de operaciones lentas que el almacén del servicio de nomenclatura notifica como incorrectas al mismo tiempo. Si es 0, se envían todas las operaciones lentas. |
|MaxOperationTimeout |Tiempo en segundos, el valor predeterminado es 600. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera máximo permitido para las operaciones de cliente. Las solicitudes que especifiquen un tiempo de espera mayor se rechazarán. |
|MaxOutstandingNotificationsPerClient |Int, el valor predeterminado es 1000. |Dinámica|El número máximo de notificaciones pendientes antes de que la puerta de enlace cierre a la fuerza un registro de cliente. |
|MinReplicaSetSize | Int, el valor predeterminado es 3. |No permitida| El número mínimo de réplicas del servicio de nomenclatura en las que es necesario escribir para completar una actualización. Si hay un número menor de réplicas de las que están activas en el sistema, el sistema de confiabilidad rechaza las actualizaciones al almacén del servicio de nomenclatura hasta que las réplicas se restauran. Este valor nunca debe ser mayor que el de TargetReplicaSetSize. |
|PartitionCount |Int, el valor predeterminado es 3. |No permitida|El número de particiones del almacén del servicio de nomenclatura que se creará. Cada partición posee una única clave de partición que corresponde a su índice; de modo que existen [0; PartitionCount] claves de partición. Al aumentar el número de particiones del servicio de nomenclatura, aumenta la escala a la que puede ejecutarse el servicio de nomenclatura ya que se reduce la cantidad media de datos que contiene cualquier conjunto de réplicas de respaldo; a cambio, aumenta la utilización de recursos (dado que se deben mantener las réplicas de servicio PartitionCount*ReplicaSetSize).|
|PlacementConstraints | string, el valor predeterminado es "". |No permitida| Restricción de selección de ubicación para el servicio de nomenclatura. |
|QuorumLossWaitDuration | Tiempo en segundos, el valor predeterminado es MaxValue. |No permitida| Especifique el intervalo de tiempo en segundos. Cuando un servicio de nomenclatura entra en pérdida de cuórum, este temporizador se inicia. Cuando expira, el FM considera las réplicas inactivas perdidas e intenta recuperar el cuórum. Tenga en cuenta que esto puede dar lugar a la pérdida de datos. |
|RepairInterval | Tiempo en segundos, el valor predeterminado es 5. |estática| Especifique el intervalo de tiempo en segundos. Intervalo dentro del cual se inicia la reparación de la incoherencia de nomenclatura entre el propietario de la entidad y el propietario del nombre. |
|ReplicaRestartWaitDuration | Tiempo en segundos, el valor predeterminado es (60.0 * 30).|No permitida| Especifique el intervalo de tiempo en segundos. Cuando una réplica del servicio de nomenclatura deja de funcionar, el temporizador se inicia. Cuando expira, el FM comenzará a sustituir las réplicas inactivas (aún no se consideran perdidas). |
|ServiceDescriptionCacheLimit | Int, el valor predeterminado es 0. |estática| El número máximo de entradas mantenidas en la caché de descripción del servicio LRU en el almacén del servicio de nomenclatura (se establece en 0 para un número ilimitado). |
|ServiceNotificationTimeout |Tiempo en segundos, el valor predeterminado es 30. |Dinámica|Especifique el intervalo de tiempo en segundos. El tiempo de espera usado al entregar notificaciones de servicio al cliente. |
|StandByReplicaKeepDuration | Tiempo en segundos, el valor predeterminado es 3600.0 * 2. |No permitida| Especifique el intervalo de tiempo en segundos. Cuando una réplica del servicio de nomenclatura se reanuda después de un estado inactivo, puede que ya se haya reemplazado. Este temporizador determina cuánto tiempo el FM mantendrá la réplica en espera antes de descartarla. |
|TargetReplicaSetSize |Int, el valor predeterminado es 7. |No permitida|El número de conjuntos de réplicas para cada partición del almacén de servicio de nomenclatura. Aumentar el número de conjuntos de réplica aumenta el nivel de confiabilidad de la información del almacén del servicio de nomenclatura al reducirse la oportunidad de que la información se pierda como resultado de errores en los nodos. A cambio, aumenta la carga en Windows Fabric y la cantidad de tiempo que tarda en realizar actualizaciones en los datos de nomenclatura.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, el valor predeterminado es None|Dinámica|Porcentaje de la capacidad de nodo por nombre de métrica; se usa como un búfer con el fin de dejar algo de espacio libre en un nodo en caso de conmutación por error. |

## <a name="nodecapacities"></a>NodeCapacities
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |estática|Una colección de funcionalidades de nodo para diferentes métricas. |

## <a name="nodedomainids"></a>NodeDomainIds
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |estática|Describe los dominios de error a los que pertenece un nodo. El dominio de error se define mediante un URI que describe la ubicación del nodo en el centro de datos.  Los URI de dominio de error tienen el formato fd:/fd/ seguido de un segmento con la ruta de acceso al URI.|
|UpgradeDomainId |string, el valor predeterminado es "". |estática|Describe el dominio de actualización al que pertenece un nodo. |

## <a name="nodeproperties"></a>NodeProperties
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |estática|Una colección de pares de clave y valor de cadena para las propiedades de nodo. |

## <a name="paas"></a>Paas
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|ClusterId |string, el valor predeterminado es "". |No permitida|Almacén de certificados X509 usado por Fabric para la protección de la configuración. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|Counters |string | Dinámica |Lista separada por comas de los contadores de rendimiento que se recolectarán. |
|IsEnabled |Bool, el valor predeterminado es true. | Dinámica |La marca indica si está habilitada la colección de contadores de rendimiento en el nodo local. |
|MaxCounterBinaryFileSizeInMB |Int, el valor predeterminado es 1. | Dinámica |Tamaño máximo (en MB) de cada archivo binario de contador de rendimiento. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, el valor predeterminado es 10. | Dinámica |Intervalo máximo (en segundos) después del cual se crea un nuevo archivo binario de contador de rendimiento. |
|SamplingIntervalInSeconds |Int, el valor predeterminado es 60. | Dinámica |Intervalo de muestreo para los contadores de rendimiento que se recolectarán. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, el valor predeterminado es 0. | Dinámica|Determina la prioridad de la restricción de afinidad: 0: máxima; 1: mínima; negativo: ignorar. |
|ApplicationCapacityConstraintPriority | Int, el valor predeterminado es 0. | Dinámica|Determina la prioridad de la restricción de capacidad: 0: máxima; 1: mínima; negativo: ignorar. |
|AutoDetectAvailableResources|bool, el valor predeterminado es TRUE|estática|Esta configuración desencadenará la detección automática de los recursos disponibles en el nodo (CPU y memoria). Cuando esta configuración se establece en true, se leen las capacidades reales y se corrigen si el usuario especificó unas capacidades erróneas de nodo o no las definió. Si esta configuración se establece en false, se hará el seguimiento de una advertencia en que el usuario especificó capacidades incorrectas para el nodo, pero no se corregirán. Esto significa que el usuario quiere especificar capacidades superiores a las reales del nodo o, si no se define ninguna capacidad, se asumirá una capacidad ilimitada. |
|BalancingDelayAfterNewNode | Tiempo en segundos, el valor predeterminado es 120. |Dinámica|Especifique el intervalo de tiempo en segundos. No inicie actividades de equilibrio dentro de este período después de agregar un nuevo nodo. |
|BalancingDelayAfterNodeDown | Tiempo en segundos, el valor predeterminado es 120. |Dinámica|Especifique el intervalo de tiempo en segundos. No inicie actividades de equilibrio dentro de este período después de un evento de inactividad de nodos. |
|CapacityConstraintPriority | Int, el valor predeterminado es 0. | Dinámica|Determina la prioridad de la restricción de capacidad: 0: máxima; 1: mínima; negativo: ignorar. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, el valor predeterminado es 20. | Dinámica|Define el número de veces consecutivas que los movimientos emitidos por ResourceBalancer se descartan antes de que se realicen diagnósticos y se emitan advertencias de mantenimiento. Negativo: no se emiten advertencias bajo esta condición. |
|ConstraintFixPartialDelayAfterNewNode | Tiempo en segundos, el valor predeterminado es 120. |Dinámica| Especifique el intervalo de tiempo en segundos. No resuelva infracciones de restricciones FaultDomain y UpgradeDomain dentro de este período después de agregar un nuevo nodo. |
|ConstraintFixPartialDelayAfterNodeDown | Tiempo en segundos, el valor predeterminado es 120. |Dinámica| Especifique el intervalo de tiempo en segundos. No resuelva infracciones FaultDomain y UpgradeDomain dentro de este período después de un evento de inactividad de nodos. |
|ConstraintViolationHealthReportLimit | Int, el valor predeterminado es 50. |Dinámica| Define el número de veces que la réplica que infringe la restricción debe estar sin fijar de forma persistente antes de que se realicen diagnósticos y se emitan informes de mantenimiento. |
|DetailedConstraintViolationHealthReportLimit | Int, el valor predeterminado es 200. |Dinámica| Define el número de veces que la réplica que infringe la restricción debe estar sin fijar de forma persistente antes de que se realicen diagnósticos y se emitan informes de mantenimiento detallados. |
|DetailedDiagnosticsInfoListLimit | Int, el valor predeterminado es 15. |Dinámica| Define el número de entradas de diagnóstico (con información detallada) que se incluirán por restricción antes del truncamiento en Diagnostics.|
|DetailedNodeListLimit | Int, el valor predeterminado es 15. |Dinámica| Define el número de nodos por restricción que se incluirán antes del truncamiento en los informes de réplicas sin colocar. |
|DetailedPartitionListLimit | Int, el valor predeterminado es 15. |Dinámica| Define el número de particiones por entrada de diagnóstico que se incluirán para una restricción antes del truncamiento en Diagnostics. |
|DetailedVerboseHealthReportLimit | Int, el valor predeterminado es 200. | Dinámica|Define el número de veces que una réplica sin colocar tiene que estar sin colocar de forma persistente antes de que se emitan informes de mantenimiento detallados. |
|FaultDomainConstraintPriority | Int, el valor predeterminado es 0. |Dinámica| Determina la prioridad de la restricción del dominio de error: 0: máxima; 1: mínima; negativo: ignorar. |
|GlobalMovementThrottleCountingInterval | Tiempo en segundos, el valor predeterminado es 600. |estática| Especifique el intervalo de tiempo en segundos. Indique la longitud del intervalo pasado para rastrear movimientos de réplicas por dominio (se usa en combinación con GlobalMovementThrottleThreshold). Puede establecerse en 0 para omitir por completo la limitación global. |
|GlobalMovementThrottleThreshold | Uint, el valor predeterminado es 1000. |Dinámica| Número máximo de movimientos permitidos en la fase de equilibrio en el intervalo pasado indicado por GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint, el valor predeterminado es 0. | Dinámica|Número máximo de movimientos permitidos en la fase de equilibrio en el intervalo pasado indicado por GlobalMovementThrottleCountingInterval. 0 indica sin límite. |
|GlobalMovementThrottleThresholdForPlacement | Uint, el valor predeterminado es 0. |Dinámica| Número máximo de movimientos permitidos en la fase de selección de ubicación en el intervalo pasado indicado por GlobalMovementThrottleCountingInterval. 0 indica sin límite.|
|GlobalMovementThrottleThresholdPercentage|double, el valor predeterminado es 0|Dinámica|Número máximo de movimientos totales permitido en las fases de equilibrio y ubicación (expresado como porcentaje del número total de réplicas del clúster) en el último intervalo indicado por GlobalMovementThrottleCountingInterval. 0 indica sin límite. Si se especifican esta opción y GlobalMovementThrottleThreshold, se usa el límite más conservador.|
|GlobalMovementThrottleThresholdPercentageForBalancing|double, el valor predeterminado es 0|Dinámica|Número máximo de movimientos permitido en la fase de equilibrio (expresado como porcentaje del número total de réplicas en PLB) en el último intervalo indicado por GlobalMovementThrottleCountingInterval. 0 indica sin límite. Si se especifican esta opción y GlobalMovementThrottleThresholdForBalancing, se usa el límite más conservador.|
|InBuildThrottlingAssociatedMetric | string, el valor predeterminado es "". |estática| El nombre de métrica asociado de esta limitación. |
|InBuildThrottlingEnabled | Bool, el valor predeterminado es false. |Dinámica| Determina si está habilitada la limitación integrada. |
|InBuildThrottlingGlobalMaxValue | Int, el valor predeterminado es 0. |Dinámica|El número máximo de réplicas integradas que se permite a nivel global. |
|InterruptBalancingForAllFailoverUnitUpdates | Bool, el valor predeterminado es false. | Dinámica|Determina si cualquier tipo de actualización de las unidades de conmutación por error debe interrumpir la ejecución rápida o lenta del equilibrio. Si se especifica "false", la ejecución del equilibrio se interrumpirá si FailoverUnit: se crea o elimina, tiene réplicas que faltan, ha cambiado la ubicación de la réplica principal o ha cambiado el número de réplicas. La ejecución del equilibrio NO se interrumpirá en otros casos: si FailoverUnit: tiene réplicas adicionales, ha cambiado cualquier marca de réplica, ha cambiado solo la versión de partición o en cualquier otro caso. |
|MinConstraintCheckInterval | Tiempo en segundos, el valor predeterminado es 1. |Dinámica| Especifique el intervalo de tiempo en segundos. Define la cantidad mínima de tiempo que debe transcurrir antes de dos rondas de comprobación de restricciones consecutivas. |
|MinLoadBalancingInterval | Tiempo en segundos, el valor predeterminado es 5. |Dinámica| Especifique el intervalo de tiempo en segundos. Define la cantidad mínima de tiempo que debe transcurrir antes de dos rondas de equilibrio de carga consecutivas. |
|MinPlacementInterval | Tiempo en segundos, el valor predeterminado es 1. |Dinámica| Especifique el intervalo de tiempo en segundos. Define la cantidad mínima de tiempo que debe transcurrir antes de dos rondas de selección de ubicación consecutivas. |
|MoveExistingReplicaForPlacement | Bool, el valor predeterminado es true. |Dinámica|Valor que determina si se deben mover las réplicas existentes durante la selección de ubicación. |
|MovementPerPartitionThrottleCountingInterval | Tiempo en segundos, el valor predeterminado es 600. |estática| Especifique el intervalo de tiempo en segundos. Indique la longitud del intervalo pasado para rastrear movimientos de réplica para cada partición (se usa en combinación con MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, el valor predeterminado es 50. |Dinámica| No se producen movimientos relacionados con el equilibrio en una partición si el número de estos movimientos para réplicas de esa partición ha alcanzado o superado el valor de MovementPerFailoverUnitThrottleThreshold en el intervalo pasado indicado por MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Bool, el valor predeterminado es false. |Dinámica| Valor que determina si se pueden mover las réplicas primarias para corregir restricciones de afinidad.|
|PartiallyPlaceServices | Bool, el valor predeterminado es true. |Dinámica| Determina si todas las réplicas de servicio del clúster se colocarán "todo o nada" dados los nodos adecuados limitados para ellas.|
|PlaceChildWithoutParent | Bool, el valor predeterminado es true. | Dinámica|Valor que determina si puede colocarse la réplica de servicio secundaria si no hay ninguna réplica principal activa. |
|PlacementConstraintPriority | Int, el valor predeterminado es 0. | Dinámica|Determina la prioridad de la restricción de ubicación: 0: máxima; 1: mínima; negativo: ignorar. |
|PlacementConstraintValidationCacheSize | Int, el valor predeterminado es 10 000. |Dinámica| Limita el tamaño de la tabla usada para la validación rápida y el almacenamiento en caché de expresiones de restricciones de ubicación. |
|PlacementSearchTimeout | Tiempo en segundos, el valor predeterminado es 0.5. |Dinámica| Especifique el intervalo de tiempo en segundos. Al ubicar servicios, busque como máximo este período antes de devolver un resultado. |
|PLBRefreshGap | Tiempo en segundos, el valor predeterminado es 1. |Dinámica| Especifique el intervalo de tiempo en segundos. Define la cantidad mínima de tiempo que debe transcurrir antes de que PLB actualice el estado de nuevo. |
|PreferredLocationConstraintPriority | Int, el valor predeterminado es 2.| Dinámica|Determina la prioridad de la restricción de ubicación preferida: 0 para máxima; 1 para mínima; 2 para optimización; negativo para ignorar |
|PreventTransientOvercommit | Bool, el valor predeterminado es false. | Dinámica|Determina si PLB cuenta inmediatamente con los recursos que liberarán los movimientos iniciados. De forma predeterminada, PLB puede iniciar movimientos dentro y fuera en el mismo nodo, lo que puede generar confirmaciones en exceso transitorias. Si establece este parámetro en true, impedirá esta clase de confirmaciones en exceso y se deshabilitará la desfragmentación a petición (también conocida como placementWithMove). |
|ScaleoutCountConstraintPriority | Int, el valor predeterminado es 0. |Dinámica| Determina la prioridad de la restricción de recuento de escalado horizontal: 0: máxima; 1: mínima; negativo: ignorar. |
|SwapPrimaryThrottlingAssociatedMetric | string, el valor predeterminado es "".|estática| El nombre de métrica asociado de esta limitación. |
|SwapPrimaryThrottlingEnabled | Bool, el valor predeterminado es false.|Dinámica| Determina si está habilitada la limitación principal de intercambio. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, el valor predeterminado es 0. |Dinámica| El número máximo de réplicas principales de intercambio que se permiten a nivel global. |
|TraceCRMReasons |Bool, el valor predeterminado es true. |Dinámica|Especifica si se rastrearán los motivos de los movimientos emitidos por CRM al canal de eventos operativos. |
|UpgradeDomainConstraintPriority | Int, el valor predeterminado es 1.| Dinámica|Determina la prioridad de la restricción del dominio de actualización: 0: máxima; 1: mínima; negativo: ignorar. |
|UseMoveCostReports | Bool, el valor predeterminado es false. | Dinámica|Indica a LB que ignore el elemento de coste de la función de puntuación. Esto da lugar a un número posiblemente grande de movimientos para una ubicación mejor equilibrada. |
|UseSeparateSecondaryLoad | Bool, el valor predeterminado es true. | Dinámica|Valor que determina si se deben usar cargas secundarias diferentes. |
|ValidatePlacementConstraint | Bool, el valor predeterminado es true. |Dinámica| Especifica si la expresión PlacementConstraint de un servicio se valida o no cuando se actualiza la descripción de un servicio. |
|VerboseHealthReportLimit | Int, el valor predeterminado es 20. | Dinámica|Define el número de veces que una réplica tiene que ir sin colocar antes de que se notifique una advertencia de mantenimiento para ella (si está habilitado el informe de mantenimiento detallado). |

## <a name="reconfigurationagent"></a>ReconfigurationAgent
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Tiempo en segundos, el valor predeterminado es 900. |Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo que esperará el sistema antes de finalizar los hosts de servicio que tienen réplicas detenidas al cerrarse durante la actualización de la aplicación.|
|FabricUpgradeMaxReplicaCloseDuration | Tiempo en segundos, el valor predeterminado es 900. |Dinámica| Especifique el intervalo de tiempo en segundos. Tiempo que esperará el sistema antes de finalizar los hosts de servicio que tienen réplicas detenidas al cerrarse durante la actualización de Fabric. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(120)|Dinámica|Especifique el intervalo de tiempo en segundos. La duración que espera el sistema antes de terminar los hosts de servicio que tienen réplicas que están detenidas al cerrarse. Si este valor se establece en 0, no se indicará a las réplicas que se cierren.|
|NodeDeactivationMaxReplicaCloseDuration | Tiempo en segundos, el valor predeterminado es 900. |Dinámica|Especifique el intervalo de tiempo en segundos. Tiempo que esperará el sistema antes de finalizar los hosts de servicio que tienen réplicas detenidas al cerrarse durante la desactivación del nodo. |
|PeriodicApiSlowTraceInterval | Tiempo en segundos, el valor predeterminado es 5 minutos. |Dinámica| Especifique el intervalo de tiempo en segundos. PeriodicApiSlowTraceInterval define el intervalo por encima del cual el monitor de API vuelve a rastrear las llamadas de API lentas. |
|ReplicaChangeRoleFailureRestartThreshold|int, el valor predeterminado es 10|Dinámica| Entero. Especifica el número de errores de la API durante la promoción principal después del cual se aplicará la acción de mitigación automática (reiniciar réplica). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, el valor predeterminado es 2147483647|Dinámica| Entero. Especifica el número de errores de la API durante la promoción principal después del cual se generará un informe de mantenimiento de advertencia.|
|ServiceApiHealthDuration | Tiempo en segundos, el valor predeterminado es 30 minutos. |Dinámica| Especifique el intervalo de tiempo en segundos. ServiceApiHealthDuration define el tiempo que se espera a que una API se ejecute antes de que se notifique como incorrecta. |
|ServiceReconfigurationApiHealthDuration | Tiempo en segundos, el valor predeterminado es 30. |Dinámica| Especifique el intervalo de tiempo en segundos. ServiceReconfigurationApiHealthDuration define el tiempo que se espera que una API de servicio se ejecute antes de que se notifique como incorrecta. Esto se aplica a las llamadas API que afectan a la disponibilidad.|

## <a name="replication"></a>Replicación
| **Parámetro** | **Valores permitidos** | **Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan, el valor predeterminado es Common::TimeSpan::FromMilliseconds(15)|estática|Especifique el intervalo de tiempo en segundos. Determina la cantidad de tiempo que el replicador espera después de recibir una operación, antes de devolver una confirmación. Las confirmaciones de otras operaciones recibidas durante este período de tiempo se devolverán en un solo mensaje-> de forma que se reduce el tráfico de red pero también posiblemente el rendimiento del replicador.|
|MaxCopyQueueSize|uint, el valor predeterminado es 1024|estática|Es el valor máximo y define el tamaño inicial de la cola que mantiene operaciones de replicación. Tenga en cuenta que debe ser una potencia de 2. Si, durante el tiempo de ejecución, la cola crece y alcanza este tamaño, se limitará la operación entre los replicadores principal y secundario.|
|MaxPrimaryReplicationQueueMemorySize|uint, el valor predeterminado es 0|estática|Es el valor máximo de la cola de replicación principal en bytes.|
|MaxPrimaryReplicationQueueSize|uint, el valor predeterminado es 1024|estática|Es el número máximo de operaciones que podrían existir en la cola de replicación principal. Tenga en cuenta que debe ser una potencia de 2.|
|MaxReplicationMessageSize|uint, el valor predeterminado es 52428800|estática|Tamaño máximo de mensaje de las operaciones de replicación. El valor predeterminado es 50 MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, el valor predeterminado es 0|estática|Es el valor máximo de la cola de replicación secundaria en bytes.|
|MaxSecondaryReplicationQueueSize|uint, el valor predeterminado es 2048|estática|Es el número máximo de operaciones que podrían existir en la cola de replicación secundaria. Tenga en cuenta que debe ser una potencia de 2.|
|QueueHealthMonitoringInterval|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(30)|estática|Especifique el intervalo de tiempo en segundos. Este valor determina el período de tiempo que usa el replicador para supervisar los eventos de estado de advertencia o error de las colas de la operación de replicación. Un valor "0" deshabilita la supervisión del estado. |
|QueueHealthWarningAtUsagePercent|uint, el valor predeterminado es 80|estática|Este valor determina el uso de la cola de replicación (en porcentaje) a partir del cual se advierte acerca de un alto uso de la cola. Esto se hace después de un intervalo de gracia de QueueHealthMonitoringInterval. Si el uso de la cola cae por debajo de este porcentaje en el intervalo de gracia.|
|ReplicatorAddress|string, el valor predeterminado es L"localhost:0"|estática|El punto de conexión en forma de cadena -'IP:Port' que usa el replicador de Windows Fabric para establecer conexiones con otras réplicas para enviar o recibir operaciones.|
|ReplicatorListenAddress|string, el valor predeterminado es L"localhost:0"|estática|El punto de conexión en forma de cadena -'IP:Port' que usa el replicador de Windows Fabric para recibir operaciones de otras réplicas.|
|ReplicatorPublishAddress|string, el valor predeterminado es L"localhost:0"|estática|El punto de conexión en forma de cadena -'IP:Port' que usa el replicador de Windows Fabric para enviar operaciones a otras réplicas.|
|RetryInterval|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(5)|estática|Especifique el intervalo de tiempo en segundos. Cuando una operación se pierde o rechaza, este temporizador determina con qué frecuencia el replicador volverá a intentar enviar la operación.|

## <a name="runas"></a>RunAs
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|RunAsAccountName |string, el valor predeterminado es "". |Dinámica|Indica el nombre de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser" o "ManagedServiceAccount". Los valores válidos son "domain\user" o "user@domain". |
|RunAsAccountType|string, el valor predeterminado es "". |Dinámica|Indica el tipo de cuenta de ejecución. Es necesario para cualquier sección RunAs. Valores válidos son "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|string, el valor predeterminado es "". |Dinámica|Indica la contraseña de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser". |

## <a name="runasdca"></a>RunAs_DCA
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|RunAsAccountName |string, el valor predeterminado es "". |Dinámica|Indica el nombre de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser" o "ManagedServiceAccount". Los valores válidos son "domain\user" o "user@domain". |
|RunAsAccountType|string, el valor predeterminado es "". |Dinámica|Indica el tipo de cuenta de ejecución. Es necesario para cualquier sección RunAs. Valores válidos son "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|string, el valor predeterminado es "". |Dinámica|Indica la contraseña de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser". |

## <a name="runasfabric"></a>RunAs_Fabric
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|RunAsAccountName |string, el valor predeterminado es "". |Dinámica|Indica el nombre de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser" o "ManagedServiceAccount". Los valores válidos son "domain\user" o "user@domain". |
|RunAsAccountType|string, el valor predeterminado es "". |Dinámica|Indica el tipo de cuenta de ejecución. Es necesario para cualquier sección RunAs. Valores válidos son "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|string, el valor predeterminado es "". |Dinámica|Indica la contraseña de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser". |

## <a name="runashttpgateway"></a>RunAs_HttpGateway
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|RunAsAccountName |string, el valor predeterminado es "". |Dinámica|Indica el nombre de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser" o "ManagedServiceAccount". Los valores válidos son "domain\user" o "user@domain". |
|RunAsAccountType|string, el valor predeterminado es "". |Dinámica|Indica el tipo de cuenta de ejecución. Es necesario para cualquier sección RunAs. Valores válidos son "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|string, el valor predeterminado es "". |Dinámica|Indica la contraseña de la cuenta de ejecución. Solo es necesario para el tipo de cuenta "DomainUser". |

## <a name="security"></a>Seguridad
| **Parámetro** | **Valores permitidos** |**Directiva de actualización**| **Orientación o breve descripción** |
| --- | --- | --- | --- |
|AADClientApplication|string, el valor predeterminado es L""|estática|Nombre de la aplicación de cliente nativo o identificador que representa a los clientes de Fabric. |
|AADClusterApplication|string, el valor predeterminado es L""|estática|Nombre de la aplicación API web o identificador que representa el clúster. |
|AADTenantId|string, el valor predeterminado es L""|estática|Id. de inquilino (GUID) |
|AdminClientCertThumbprints|string, el valor predeterminado es L""|Dinámica|Huellas digitales de certificados que usan los clientes con el rol de administrador. Lista de nombres separados por comas. |
|AdminClientClaims|string, el valor predeterminado es L""|Dinámica|Todas las notificaciones posibles que se esperan de los clientes de administración. Tiene el mismo formato que ClientClaims. Esta lista se agrega internamente a ClientClaims, por lo que no es necesario agregar también las mismas entradas para ClientClaims. |
|AdminClientIdentities|string, el valor predeterminado es L""|Dinámica|Identidades de Windows de clientes de Fabric con el rol de administrador; se usa para autorizar las operaciones de Fabric con privilegios. Lista separada por comas; cada entrada es un nombre de grupo o de cuenta de dominio. Para mayor comodidad, a la cuenta que ejecuta fabric.exe se le asigna automáticamente un rol de administrador. Lo mismo sucede con ServiceFabricAdministrators. |
|CertificateExpirySafetyMargin|TimeSpan, el valor predeterminado es Common::TimeSpan::FromMinutes(43200)|estática|Especifique el intervalo de tiempo en segundos. Margen de seguridad para la expiración del certificado. El estado de informe de mantenimiento del certificado cambia de Correcto a Advertencia si el periodo de expiración es inferior a este valor. El valor predeterminado es 30 días. |
|CertificateHealthReportingInterval|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(3600 * 8)|estática|Especifique el intervalo de tiempo en segundos. Especifica el intervalo para los informes de mantenimiento del certificado. El valor predeterminado es 8 horas. Establecerlo en 0 deshabilita el informe de mantenimiento del certificado. |
|ClientCertThumbprints|string, el valor predeterminado es L""|Dinámica|Huellas digitales de certificados que usan los clientes para comunicarse con el clúster. El clúster las usa para autorizar la conexión entrante. Lista de nombres separados por comas. |
|ClientClaimAuthEnabled|bool, el valor predeterminado es FALSE|estática|Indica si la autenticación basada en notificaciones está habilitada en los clientes. Si se establece en true, se establece implícitamente ClientRoleEnabled. |
|ClientClaims|string, el valor predeterminado es L""|Dinámica|Todas las notificaciones posibles que se esperan de los clientes para conectarse a la puerta de enlace. Se trata de una lista "OR": ClaimsEntry \|\|ClaimsEntry \|\| ClaimsEntry... cada valor de ClaimsEntry es una lista "AND": ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue... |
|ClientIdentities|string, el valor predeterminado es L""|Dinámica|Identidades de Windows de FabricClient; la puerta de enlace de nomenclatura las usa para autorizar las conexiones entrantes. Lista separada por comas; cada entrada es un nombre de grupo o de cuenta de dominio. Para mayor comodidad; la cuenta que ejecuta fabric.exe se permite automáticamente. Lo mismo sucede con ServiceFabricAllowedUsers y ServiceFabricAdministrators. |
|ClientRoleEnabled|bool, el valor predeterminado es FALSE|estática|Indica si está habilitado el rol de cliente. Cuando se establece en true, se asignan roles a clientes en función de sus identidades. Para V2; habilitar esto significa que los clientes que no estén en AdminClientCommonNames/AdminClientIdentities solo pueden ejecutar operaciones de solo lectura. |
|ClusterCertThumbprints|string, el valor predeterminado es L""|Dinámica|Huellas digitales de certificados que pueden unirse al clúster; lista de nombres separados por comas. |
|ClusterCredentialType|string, el valor predeterminado es L"None"|No permitida|Indica el tipo de credenciales de seguridad que se usarán para proteger el clúster. Los valores válidos son "None/X509/Windows". |
|ClusterIdentities|string, el valor predeterminado es L""|Dinámica|Identidades de Windows de los nodos del clúster, se usan para la autorización de pertenencia del clúster. Lista separada por comas; cada entrada es un nombre de grupo o de cuenta de dominio. |
|ClusterSpn|string, el valor predeterminado es L""|No permitida|Nombre de entidad de seguridad de servicio del clúster cuando Fabric se ejecuta como un usuario de dominio único (cuenta de usuario de dominio o gMSA). Es el SPN de los agentes de escucha y de los agentes de escucha de concesión de fabric.exe: agentes de escucha de federación; agentes de escucha de replicación interna; agente de escucha del servicio en tiempo de ejecución y agente de escucha de la puerta de enlace de nomenclatura. Debe dejarse vacío cuando Fabric se ejecuta como cuentas de la máquina, en cuyo caso, se conecta el SPN del agente de escucha de proceso lateral desde la dirección de transporte del agente de escucha. |
|CrlCheckingFlag|uint, el valor predeterminado es 0x40000000|Dinámica|Marca predeterminada de validación de la cadena de certificados. Puede reemplazarse por la marca específica del componente, p. ej., Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY. Establecer la configuración en 0 deshabilita la comprobación de CRL. dwFlags de CertGetCertificateChain proporciona una lista completa de valores admitidos: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan, el valor predeterminado es Common::TimeSpan::FromMinutes(15)|Dinámica|Especifique el intervalo de tiempo en segundos. Durante cuánto tiempo se deshabilita la comprobación de CRL para un certificado especificado después de encontrar un error sin conexión. Especifica si se puede ignorar un error de CRL sin conexión. |
|CrlOfflineHealthReportTtl|TimeSpan, el valor predeterminado es Common::TimeSpan::FromMinutes(1440)|Dinámica|Especifique el intervalo de tiempo en segundos. |
|DisableFirewallRuleForDomainProfile| bool, el valor predeterminado es TRUE |estática| Indica si no se debe habilitar la regla de firewall para el perfil de dominio. |
|DisableFirewallRuleForPrivateProfile| bool, el valor predeterminado es TRUE |estática| Indica si no se debe habilitar la regla de firewall para el perfil privado. | 
|DisableFirewallRuleForPublicProfile| bool, el valor predeterminado es TRUE | estática|Indica si no se debe habilitar la regla de firewall para el perfil público. |
|FabricHostSpn| string, el valor predeterminado es L"" |estática| Nombre de entidad de seguridad de servicio de FabricHost cuando Fabric se ejecuta como usuario de dominio único (cuenta de usuario de dominio o gMSA) y FabricHost se ejecuta en la cuenta de la máquina. Es el SPN del agente de escucha de IPC para FabricHost, que, de forma predeterminada, se debe dejar vacío, ya que FabricHost se ejecuta con la cuenta de la máquina. |
|IgnoreCrlOfflineError|bool, el valor predeterminado es FALSE|Dinámica|Especifica si se omitirán los errores de CRL sin conexión cuando el lado servidor compruebe los certificados de cliente entrantes. |
|IgnoreSvrCrlOfflineError|bool, el valor predeterminado es TRUE|Dinámica|Especifica si se omitirán los errores de CRL sin conexión cuando el lado cliente compruebe los certificados de servidor entrantes; el valor predeterminado es true. Los ataques con certificados de servidor revocados requieren poner en peligro el DNS; más difícil que con certificados de cliente revocados. |
|ServerAuthCredentialType|string, el valor predeterminado es L"None"|estática|Indica el tipo de credenciales de seguridad que se usarán para proteger la comunicación entre FabricClient y el clúster. Los valores válidos son "None/X509/Windows". |
|ServerCertThumbprints|string, el valor predeterminado es L""|Dinámica|Huellas digitales de certificados de servidor que usa el clúster para comunicarse con los clientes. Los clientes las usan para autenticar el clúster. Lista de nombres separados por comas. |
|SettingsX509StoreName| string, el valor predeterminado es L"MY"| Dinámica|Almacén de certificados X509 que usa Fabric para la protección de la configuración. |
|X509Folder|string,el valor predeterminado es /var/lib/waagent|estática|Carpeta donde se encuentran los certificados X509 y las claves privadas. |

## <a name="securityadminclientx509names"></a>Security/AdminClientX509Names
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, el valor predeterminado es None|Dinámica| |

## <a name="securityclientaccess"></a>Security/ClientAccess
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|ActivateNode |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para activación de un nodo. |
|CancelTestCommand |string, el valor predeterminado es "Admin". |Dinámica| Cancela un comando de prueba específico, si se está ejecutando. |
|CodePackageControl |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para reiniciar paquetes de código. |
|CreateApplication |string, el valor predeterminado es "Admin". | Dinámica|Configuración de seguridad para creación de aplicaciones. |
|CreateComposeDeployment|string, el valor predeterminado es L"Admin"| Dinámica|Crea una implementación compuesta descrita por archivos compuestos. |
|CreateName |string, el valor predeterminado es "Admin". |Dinámica|Configuración de seguridad para la creación de URI de nomenclatura. |
|CreateService |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para la creación del servicio. |
|CreateServiceFromTemplate |string, el valor predeterminado es "Admin". |Dinámica|Configuración de seguridad para la creación de servicios a partir de una plantilla. |
|DeactivateNode |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para desactivación de un nodo. |
|DeactivateNodesBatch |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para desactivación de varios nodos. |
|Eliminar |string, el valor predeterminado es "Admin". |Dinámica| Configuraciones de seguridad para la operación de eliminación del cliente del almacén de imágenes. |
|DeleteApplication |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para eliminación de aplicaciones. |
|DeleteComposeDeployment|string, el valor predeterminado es L"Admin"| Dinámica|Elimina la implementación compuesta. |
|DeleteName |string, el valor predeterminado es "Admin". |Dinámica|Configuración de seguridad para la eliminación de URI de nomenclatura. |
|DeleteService |string, el valor predeterminado es "Admin". |Dinámica|Configuración de seguridad para eliminación de servicio. |
|EnumerateProperties |string, el valor predeterminado es "Admin\|\|User" | Dinámica|Configuración de seguridad para enumeración de propiedad de nomenclatura. |
|EnumerateSubnames |string, el valor predeterminado es "Admin\|\|User" |Dinámica| Configuración de seguridad para enumeración de URI de nomenclatura. |
|FileContent |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para transferencia de archivos de cliente del almacén de imágenes (externo al clúster). |
|FileDownload |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para inicio de la descarga de archivos de cliente del almacén de imágenes (externo al clúster). |
|FinishInfrastructureTask |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para finalizar tareas de infraestructura. |
|GetChaosReport | string, el valor predeterminado es "Admin\|\|User" |Dinámica| Captura el estado de Chaos dentro de un intervalo de tiempo determinado. |
|GetClusterConfiguration | string, el valor predeterminado es "Admin\|\|User" | Dinámica|Produce GetClusterConfiguration en una partición. |
|GetClusterConfigurationUpgradeStatus | string, el valor predeterminado es "Admin\|\|User" |Dinámica| Produce GetClusterConfigurationUpgradeStatus en una partición. |
|GetFabricUpgradeStatus |string, el valor predeterminado es "Admin\|\|User" |Dinámica| Configuración de seguridad para sondeo de estado de actualización del clúster. |
|GetNodeDeactivationStatus |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para comprobar el estado de desactivación. |
|GetNodeTransitionProgress | string, el valor predeterminado es "Admin\|\|User" |Dinámica| Configuración de seguridad para obtener el progreso en un comando de transición de nodo. |
|GetPartitionDataLossProgress | string, el valor predeterminado es "Admin\|\|User" | Dinámica|Captura el progreso de una llamada de API para invocar la pérdida de datos. |
|GetPartitionQuorumLossProgress | string, el valor predeterminado es "Admin\|\|User" |Dinámica| Captura el progreso de una llamada de API para invocar la pérdida de cuórum. |
|GetPartitionRestartProgress | string, el valor predeterminado es "Admin\|\|User" |Dinámica| Captura el progreso de una llamada de API para el reinicio de una partición. |
|GetServiceDescription |string, el valor predeterminado es "Admin\|\|User" |Dinámica| Configuración de seguridad para notificaciones de servicio de sondeo largo y descripciones de servicio de lectura. |
|GetStagingLocation |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para recuperación de la ubicación de almacenamiento temporal del cliente del almacén de imágenes. |
|GetStoreLocation |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para recuperación de la ubicación del almacén de clientes del almacén de imágenes. |
|GetUpgradeOrchestrationServiceState|string, el valor predeterminado es L"Admin"| Dinámica|Induce GetUpgradeOrchestrationServiceState en una partición. |
|GetUpgradesPendingApproval |string, el valor predeterminado es "Admin". |Dinámica| Produce GetUpgradesPendingApproval en una partición. |
|GetUpgradeStatus |string, el valor predeterminado es "Admin\|\|User" |Dinámica| Configuración de seguridad para sondeo de estado de actualización de aplicaciones. |
|InternalList |string, el valor predeterminado es "Admin". | Dinámica|Configuración de seguridad para operación de lista de archivos de cliente del almacén de imágenes (interno). |
|InvokeInfrastructureCommand |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para comandos de administración de tareas de infraestructura. |
|InvokeInfrastructureQuery |string, el valor predeterminado es "Admin\|\|User" | Dinámica|Configuración de seguridad para consultar tareas de infraestructura. |
|Enumerar |string, el valor predeterminado es "Admin\|\|User" | Dinámica|Configuración de seguridad para operación de lista de archivos de cliente del almacén de imágenes. |
|MoveNextFabricUpgradeDomain |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para reanudar actualizaciones del clúster con un dominio de actualización explícito. |
|MoveNextUpgradeDomain |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para reanudar actualizaciones de aplicaciones con un dominio de actualización explícito. |
|MoveReplicaControl |string, el valor predeterminado es "Admin". | Dinámica|Mueva la réplica. |
|NameExists |string, el valor predeterminado es "Admin\|\|User" | Dinámica|Configuración de seguridad para comprobaciones de existencia de URI de nomenclatura. |
|NodeControl |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para inicio, detención y reinicio de nodos. |
|NodeStateRemoved |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para notificar el estado quitado del nodo. |
|Ping |string, el valor predeterminado es "Admin\|\|User" |Dinámica| Configuración de seguridad para pings de cliente. |
|PredeployPackageToNode |string, el valor predeterminado es "Admin". |Dinámica| API previas a la implementación. |
|PrefixResolveService |string, el valor predeterminado es "Admin\|\|User" |Dinámica| Configuración de seguridad para resolución de prefijo de servicio basada en reclamaciones. |
|PropertyReadBatch |string, el valor predeterminado es "Admin\|\|User" |Dinámica| Configuración de seguridad para operaciones de lectura de propiedad de nomenclatura. |
|PropertyWriteBatch |string, el valor predeterminado es "Admin". |Dinámica|Configuraciones de seguridad para operaciones de escritura de la propiedad de nomenclatura. |
|ProvisionApplicationType |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para aprovisionamiento de tipos de aplicaciones. |
|ProvisionFabric |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para aprovisionamiento de MSI o del manifiesto de clúster. |
|Consultar |string, el valor predeterminado es "Admin\|\|User" |Dinámica| Configuración de seguridad para consultas. |
|RecoverPartition |string, el valor predeterminado es "Admin". | Dinámica|Configuración de seguridad recuperar una partición. |
|RecoverPartitions |string, el valor predeterminado es "Admin". | Dinámica|Configuración de seguridad para recuperar particiones. |
|RecoverServicePartitions |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para recuperar particiones de servicio. |
|RecoverSystemPartitions |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para recuperar particiones de servicio del sistema. |
|RemoveNodeDeactivations |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para revertir la desactivación en varios nodos. |
|ReportFabricUpgradeHealth |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para reanudar actualizaciones del clúster con el progreso de actualización actual. |
|ReportFault |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para informes de error. |
|ReportHealth |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para mantenimiento de informes. |
|ReportUpgradeHealth |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para reanudar actualizaciones de aplicaciones con el progreso de actualización actual. |
|ResetPartitionLoad |string, el valor predeterminado es "Admin\|\|User" |Dinámica| Configuración de seguridad para restablecer una carga para una unidad de conmutación por error. |
|ResolveNameOwner |string, el valor predeterminado es "Admin\|\|User" | Dinámica|Configuración de seguridad para resolver propietario de URI de nomenclatura. |
|ResolvePartition |string, el valor predeterminado es "Admin\|\|User" | Dinámica|Configuración de seguridad para resolver servicios del sistema. |
|ResolveService |string, el valor predeterminado es "Admin\|\|User" |Dinámica| Configuración de seguridad para resolución de servicio basada en reclamaciones. |
|ResolveSystemService|string, el valor predeterminado es L"Admin\|\|User"|Dinámica| Configuración de seguridad para resolver servicios del sistema. |
|RollbackApplicationUpgrade |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para revertir actualizaciones de aplicaciones. |
|RollbackFabricUpgrade |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para revertir actualizaciones del clúster. |
|ServiceNotifications |string, el valor predeterminado es "Admin\|\|User" |Dinámica| Configuración de seguridad para notificaciones del servicio basadas en eventos. |
|SetUpgradeOrchestrationServiceState|string, el valor predeterminado es L"Admin"| Dinámica|Induce SetUpgradeOrchestrationServiceState en una partición. |
|StartApprovedUpgrades |string, el valor predeterminado es "Admin". |Dinámica| Produce StartApprovedUpgrades en una partición. |
|StartChaos |string, el valor predeterminado es "Admin". |Dinámica| Inicia Chaos, si no se ha iniciado. |
|StartClusterConfigurationUpgrade |string, el valor predeterminado es "Admin". |Dinámica| Produce StartClusterConfigurationUpgrade en una partición. |
|StartInfrastructureTask |string, el valor predeterminado es "Admin". | Dinámica|Configuración de seguridad para iniciar tareas de infraestructura. |
|StartNodeTransition |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para iniciar una transición de nodo. |
|StartPartitionDataLoss |string, el valor predeterminado es "Admin". |Dinámica| Provoca la pérdida de datos en una partición. |
|StartPartitionQuorumLoss |string, el valor predeterminado es "Admin". |Dinámica| Provoca la pérdida de cuórum en una partición. |
|StartPartitionRestart |string, el valor predeterminado es "Admin". |Dinámica| Reinicia simultáneamente algunas o todas las réplicas de una partición. |
|StopChaos |string, el valor predeterminado es "Admin". |Dinámica| Detiene Chaos, si se ha iniciado. |
|ToggleVerboseServicePlacementHealthReporting | string, el valor predeterminado es "Admin\|\|User" |Dinámica| Configuración de seguridad para alternar informes de mantenimiento detallados de ubicación de servicio. |
|UnprovisionApplicationType |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para dejar de aprovisionar tipos de aplicación. |
|UnprovisionFabric |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para dejar de aprovisionar MSI o el manifiesto de clúster. |
|UnreliableTransportControl |string, el valor predeterminado es "Admin". |Dinámica| Transporte no confiable para agregar y quitar comportamientos. |
|UpdateService |string, el valor predeterminado es "Admin". |Dinámica|Configuración de seguridad para actualizaciones de servicio. |
|UpgradeApplication |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para iniciar o interrumpir actualizaciones de aplicaciones. |
|UpgradeComposeDeployment|string, el valor predeterminado es L"Admin"| Dinámica|Actualiza la implementación compuesta. |
|UpgradeFabric |string, el valor predeterminado es "Admin". |Dinámica| Configuración de seguridad para iniciar actualizaciones del clúster. |
|Cargar |string, el valor predeterminado es "Admin". | Dinámica|Configuración de seguridad para operación de carga del cliente del almacén de imágenes. |

## <a name="securityclientcertificateissuerstores"></a>Security/ClientCertificateIssuerStores
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, el valor predeterminado es None |Dinámica|Almacenes de certificados de emisor X509 para certificados de cliente; nombre = clientIssuerCN; valor = lista de almacenes separados por comas |

## <a name="securityclientx509names"></a>Security/ClientX509Names
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, el valor predeterminado es None|Dinámica| |

## <a name="securityclustercertificateissuerstores"></a>Security/ClusterCertificateIssuerStores
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, el valor predeterminado es None |Dinámica|Almacenes de certificados de emisor X509 para certificados de clúster; nombre = clusterIssuerCN; valor = lista de almacenes separados por comas |

## <a name="securityclusterx509names"></a>Security/ClusterX509Names
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, el valor predeterminado es None|Dinámica| |

## <a name="securityservercertificateissuerstores"></a>Security/ServerCertificateIssuerStores
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, el valor predeterminado es None |Dinámica|Almacenes de certificados de emisor X509 para certificados de servidor; nombre = serverIssuerCN; valor = lista de almacenes separados por comas |

## <a name="securityserverx509names"></a>Security/ServerX509Names
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, el valor predeterminado es None|Dinámica| |

## <a name="setup"></a>Configuración
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|ContainerNetworkName|string, el valor predeterminado es L""| estática |Nombre de red que se usará al configurar una red de contenedores.|
|ContainerNetworkSetup|bool, el valor predeterminado es FALSE| estática |Establece si se debe configurar una red de contenedores.|
|FabricDataRoot |string | No permitida |Directorio raíz de datos de Service Fabric. El valor predeterminado para Azure es d:\svcfab. |
|FabricLogRoot |string | No permitida |Directorio raíz del registro de Service Fabric. Aquí es donde se colocan los seguimientos y registros de SF. |
|NodesToBeRemoved|string, el valor predeterminado es "".| Dinámica |Nodos que deben quitarse como parte de la actualización de la configuración. (Solo para implementaciones independientes.)|
|ServiceRunAsAccountName |string | No permitida |El nombre de la cuenta con el que se ejecuta el servicio Fabric Host. |
|SkipFirewallConfiguration |Bool, el valor predeterminado es false. | No permitida |Especifica si el sistema debe establecer o no la configuración de firewall. Solo aplicable si usa Firewall de Windows. Si usa firewalls de terceros, debe abrir los puertos para que los usen el sistema y las aplicaciones. |

## <a name="tokenvalidationservice"></a>TokenValidationService
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|Proveedores |string, el valor predeterminado es "DSTS". |estática|Lista separada por comas de proveedores de validación de tokens para habilitar (proveedores válidos son: DSTS; AAD). Actualmente solo se puede habilitar un único proveedor cada vez. |

## <a name="traceetw"></a>Trace/Etw
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|Nivel |Int, el valor predeterminado es 4. | Dinámica |El nivel de seguimiento de eventos puede adoptar los valores 1, 2, 3, 4. Para que sea admitido, debe mantener el nivel de seguimiento en 4. |

## <a name="transactionalreplicator"></a>TransactionalReplicator
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Tiempo en segundos, el valor predeterminado es 0,015. | estática | Especifique el intervalo de tiempo en segundos. Determina la cantidad de tiempo que el replicador espera después de recibir una operación, antes de devolver una confirmación. Las confirmaciones de otras operaciones recibidas durante este período de tiempo se devolverán en un solo mensaje-> de forma que se reduce el tráfico de red pero también posiblemente el rendimiento del replicador. |
|CheckpointThresholdInMB |Int, el valor predeterminado es 50. |estática|Cuando el uso del registro supera este valor, se inicia un punto de control. |
|InitialPrimaryReplicationQueueSize |Uint, el valor predeterminado es 64. | estática |Este valor define el tamaño inicial de la cola que mantiene las operaciones de replicación en el replicador principal. Tenga en cuenta que debe ser una potencia de 2.|
|InitialSecondaryReplicationQueueSize |Uint, el valor predeterminado es 64. | estática |Este valor define el tamaño inicial de la cola que mantiene las operaciones de replicación en el replicador secundario. Tenga en cuenta que debe ser una potencia de 2. |
|MaxAccumulatedBackupLogSizeInMB |Int, el valor predeterminado es 800. |estática|El tamaño máximo acumulado (en MB) de los registros de copia de seguridad de una cadena de registros de copia de seguridad determinada. Las solicitudes de copia de seguridad incremental no se realizarán correctamente si esta genera un registro de copia de seguridad que provoque la acumulación de este tipo de elemento, ya que la copia de seguridad completa tendrá un tamaño superior. En tales casos, el usuario tiene que realizar una copia de seguridad completa. |
|MaxCopyQueueSize |Uint, el valor predeterminado es 16384. | estática |Es el valor máximo y define el tamaño inicial de la cola que mantiene operaciones de replicación. Tenga en cuenta que debe ser una potencia de 2. Si, durante el tiempo de ejecución, la cola crece y alcanza este tamaño, se limitará la operación entre los replicadores principal y secundario. |
|MaxMetadataSizeInKB |Int, el valor predeterminado es 4. |No permitida|Tamaño máximo de los metadatos de la secuencia de registro. |
|MaxPrimaryReplicationQueueMemorySize |Uint, el valor predeterminado es 0. | estática |Es el valor máximo de la cola de replicación principal en bytes. |
|MaxPrimaryReplicationQueueSize |Uint, el valor predeterminado es 8192. | estática |Es el número máximo de operaciones que podrían existir en la cola de replicación principal. Tenga en cuenta que debe ser una potencia de 2. |
|MaxRecordSizeInKB |Uint, el valor predeterminado es 1024. |No permitida| Tamaño máximo de un registro de secuencia de registro. |
|MaxReplicationMessageSize |Uint, el valor predeterminado es 52 428 800. | estática | Tamaño máximo de mensaje de las operaciones de replicación. El valor predeterminado es 50 MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, el valor predeterminado es 0. | estática |Es el valor máximo de la cola de replicación secundaria en bytes. |
|MaxSecondaryReplicationQueueSize |Uint, el valor predeterminado es 16384. | estática |Es el número máximo de operaciones que podrían existir en la cola de replicación secundaria. Tenga en cuenta que debe ser una potencia de 2. |
|MaxWriteQueueDepthInKB |Int, el valor predeterminado es 0. |No permitida| Valor entero para la profundidad máxima de la cola de escritura que puede usar el registrador de núcleos, especificado en kilobytes para el registro asociado con esta réplica. Este valor es el número máximo de bytes que pueden estar pendientes durante las actualizaciones del registrador de núcleos. Puede ser 0 para que el registrador de núcleos calcule un valor adecuado, o un múltiplo de 4. |
|MinLogSizeInMB |Int, el valor predeterminado es 0. |estática|Tamaño mínimo del registro transaccional. El registro no podrá truncarse a un tamaño inferior a este valor. 0 indica que el replicador determinará el tamaño de registro mínimo según otros valores de configuración. Al aumentar este valor, incrementa la posibilidad de hacer copias parciales y copias de seguridad incrementales, ya que se reducen las posibilidades de que se trunquen registros pertinentes. |
|ReplicatorAddress |string, el valor predeterminado es "localhost:0". | estática | El punto de conexión en forma de cadena -'IP:Port' que usa el replicador de Windows Fabric para establecer conexiones con otras réplicas para enviar o recibir operaciones. |
|SecondaryClearAcknowledgedOperations |Bool, el valor predeterminado es false. | estática |Controla si las operaciones en el replicador secundario se borran una vez que se confirman en el principal (se vacían en el disco). La configuración de este valor en TRUE puede dar lugar a lecturas de disco adicionales en el nuevo replicador, al tiempo que se actualizan las réplicas después de una conmutación por error. |
|SharedLogId |string |No permitida|Identificador de registro compartido. Es un GUID y debe ser único para cada registro compartido. |
|SharedLogPath |string |No permitida|Ruta de acceso al registro compartido. Si este valor está vacío, se usa el registro compartido predeterminado. |
|SlowApiMonitoringDuration |Tiempo en segundos, el valor predeterminado es 300. |estática| Especifique la duración de la API antes de que se desencadene el evento de mantenimiento de advertencia.|

## <a name="transport"></a>Transporte
| **Parámetro** | **Valores permitidos** |**Directiva de actualización** |**Orientación o breve descripción** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, el valor predeterminado es Common::TimeSpan::FromSeconds(60)|estática|Especifique el intervalo de tiempo en segundos. Tiempo de espera para la configuración de la conexión en los lados de aceptación y entrada (incluida la negociación de seguridad en el modo seguro). |
|ResolveOption|string, el valor predeterminado es L"unspecified"|estática|Determina cómo se resuelve el FQDN.  Los valores válidos son "sin especificar/ipv4/ipv6". |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Bool, el valor predeterminado es true. |estática| Sondeo automático y acción de actualización basados en un archivo de estado de objetivo. |
|MinReplicaSetSize |Int, el valor predeterminado es 0. |estática |MinReplicaSetSize para UpgradeOrchestrationService.
|PlacementConstraints | string, el valor predeterminado es "". |estática| PlacementConstraints para UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Tiempo en segundos, el valor predeterminado es MaxValue. |estática| Especifique el intervalo de tiempo en segundos. QuorumLossWaitDuration para UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Tiempo en segundos, el valor predeterminado es 60 minutos.|estática| Especifique el intervalo de tiempo en segundos. ReplicaRestartWaitDuration para UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Tiempo en segundos, el valor predeterminado es 60*24*7 minutos. |estática| Especifique el intervalo de tiempo en segundos. StandByReplicaKeepDuration para UpgradeOrchestrationService. |
|TargetReplicaSetSize |Int, el valor predeterminado es 0. |estática |TargetReplicaSetSize para UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Bool, el valor predeterminado es false. | estática|La configuración para crear actualizaciones de código requiere la aprobación del administrador antes de continuar. |

## <a name="upgradeservice"></a>UpgradeService
| **Parámetro** | **Valores permitidos** | **Directiva de actualización** | **Orientación o breve descripción** |
| --- | --- | --- | --- |
|BaseUrl | string, el valor predeterminado es "". |estática|BaseUrl para UpgradeService. |
|ClusterId | string, el valor predeterminado es "". |estática|ClusterId para UpgradeService. |
|CoordinatorType | string, el valor predeterminado es "WUTest".|No permitida|CoordinatorType para UpgradeService. |
|MinReplicaSetSize | Int, el valor predeterminado es 2. |No permitida| MinReplicaSetSize para UpgradeService. |
|OnlyBaseUpgrade | Bool, el valor predeterminado es false. |Dinámica|OnlyBaseUpgrade para UpgradeService. |
|PlacementConstraints |string, el valor predeterminado es "". |No permitida|PlacementConstraints para el servicio de actualización. |
|TargetReplicaSetSize | Int, el valor predeterminado es 3. |No permitida| TargetReplicaSetSize para UpgradeService. |
|TestCabFolder | string, el valor predeterminado es "". |estática| TestCabFolder para UpgradeService. |
|X509FindType | string, el valor predeterminado es "".|Dinámica| X509FindType para UpgradeService. |
|X509FindValue | string, el valor predeterminado es "". |Dinámica| X509FindValue para UpgradeService. |
|X509SecondaryFindValue | string, el valor predeterminado es "". |Dinámica| X509SecondaryFindValue para UpgradeService. |
|X509StoreLocation | string, el valor predeterminado es "". |Dinámica| X509StoreLocation para UpgradeService. |
|X509StoreName | string, el valor predeterminado es "My".|Dinámica|X509StoreName para UpgradeService. |

## <a name="next-steps"></a>Pasos siguientes
Lea estos artículos para más información sobre la administración de clúster:

[Agregar o quitar certificados del clúster de Azure ](service-fabric-cluster-security-update-certs-azure.md) 

