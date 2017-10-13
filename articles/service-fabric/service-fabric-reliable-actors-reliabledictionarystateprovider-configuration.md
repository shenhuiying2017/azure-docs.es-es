---
title: "Cambio de la configuración de ReliableDictionaryActorStateProvider en microservicios de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo configurar los actores con estado de Azure Service Fabric del tipo ReliableDictionaryActorStateProvider."
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: 
ms.assetid: 79b48ffa-2474-4f1c-a857-3471f9590ded
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 5dcd1b4f5a070e9a09b6f8338928d93d10227d38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Configuración de Reliable Actors: ReliableDictionaryActorStateProvider
Puede modificar configuración predeterminada de ReliableDictionaryActorStateProvider cambiando el archivo settings.xml que se genera en la raíz del paquete de Visual Studio en la carpeta Config del actor especificado.

El tiempo de ejecución de Azure Service Fabric busca los nombres de sección predefinidos en el archivo settings.xml y usa los valores de configuración mientras crea los componentes en tiempo de ejecución subyacentes.

> [!NOTE]
> **No** elimine o modifique los nombres de sección de las siguientes configuraciones en el archivo settings.xml que se genera en la solución de Visual Studio.
> 
> 

También hay configuraciones globales que afectan a la configuración de ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Configuración global
La configuración global se especifica en el manifiesto de clúster para el clúster de la sección KtlLogger. Permite la configuración del tamaño y la ubicación del registro compartido, así como los límites de memoria global usados por el registrador. Tenga en cuenta que los cambios en el manifiesto de clúster afectan a todos los servicios que usan ReliableDictionaryActorStateProvider y servicios con estado confiables.

El manifiesto de clúster es un único archivo XML que contiene los valores y las configuraciones que se aplican a todos los nodos y servicios del clúster. El archivo suele llamarse ClusterManifest.xml. Puede ver el manifiesto de clúster para su clúster usando el comando de PowerShell Get-ServiceFabricClusterManifest.

### <a name="configuration-names"></a>Nombres de configuración
| Nombre | Unidad | Valor predeterminado | Comentarios |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobytes |8388608 |Número mínimo de KB que se van a asignar en modo kernel al grupo de memoria del búfer de escritura del registrador. Este grupo de memoria se usa para almacenar en caché la información de estado antes de escribir en el disco. |
| WriteBufferMemoryPoolMaximumInKB |Kilobytes |Ilimitado |Tamaño máximo al que puede aumentar el grupo de memoria del búfer de escritura del registrador. |
| SharedLogId |GUID |"" |Especifica un GUID único que se usará para identificar el archivo de registro compartido predeterminado que usan todos los servicios de confianza en todos los nodos del clúster que no especifican SharedLogId en su configuración específica del servicio. Si se especifica SharedLogId, también se debe especificar SharedLogPath. |
| SharedLogPath |Nombre de ruta de acceso completo |"" |Especifica la ruta de acceso completa donde se encuentra el archivo de registro compartido que usan todos los servicios de confianza en todos los nodos del clúster que no especifican SharedLogPath en su configuración específica del servicio. Sin embargo, si se especifica SharedLogPath, también se debe especificar SharedLogId. |
| SharedLogSizeInMB |Megabytes |8192 |Especifica el número de MB de espacio en disco que se va a asignar estáticamente para el registro compartido. El valor deber ser 2048 o superior. |

### <a name="sample-cluster-manifest-section"></a>Ejemplo de sección de manifiesto de clúster
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Comentarios
El registrador tiene un grupo global de memoria asignado desde la memoria del kernel no paginada que está disponible para todos los servicios de confianza en un nodo para almacenar en caché los datos de estado antes de que se escriban en el registro específico asociado con la réplica del servicio de confianza. El tamaño del grupo se controla mediante las opciones WriteBufferMemoryPoolMinimumInKB y WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB especifica el tamaño inicial de este grupo de memoria y el tamaño mínimo al que se puede reducir el grupo de memoria. WriteBufferMemoryPoolMaximumInKB es el tamaño máximo que puede alcanzar el grupo de memoria. Cada réplica de un servicio de confianza que está abierta puede aumentar el tamaño del grupo de memoria en una cantidad que determina el sistema hasta WriteBufferMemoryPoolMaximumInKB. Si el grupo de memoria demanda más memoria de la que hay disponible, las solicitudes de memoria se retrasarán hasta que haya memoria disponible. Por lo tanto, si el grupo de memoria del búfer de escritura es demasiado pequeño para una configuración concreta, el rendimiento se puede ver afectado.

Los parámetros SharedLogId y SharedLogPath siempre se usan juntos para definir el GUID y la ubicación del registro compartido predeterminado de todos los nodos del clúster. El registro compartido predeterminado se usa para todos los servicios de confianza que no especifican los valores de configuración en el archivo settings.xml para el servicio específico. Para obtener un mejor rendimiento, los archivos de registro compartidos deben colocarse en discos que se usen únicamente para el archivo de registro compartido, de modo que se reduzca la contención.

SharedLogSizeInMB especifica la cantidad de espacio en disco que se va a preasignar para el registro compartido predeterminado en todos los nodos.  No es necesario especificar SharedLogId y SharedLogPath para poder especificar SharedLogSizeInMB.

## <a name="replicator-security-configuration"></a>Configuración de seguridad del replicador
Las configuraciones de seguridad del replicador se usan para proteger el canal de comunicación que se usa durante la replicación. Esto significa que los servicios no ven el tráfico de replicación de unos y los otros, lo que garantiza que los datos de alta disponibilidad también están protegidos.
De forma predeterminada, una sección de configuración de seguridad vacía impide la seguridad de replicación.

### <a name="section-name"></a>Nombre de sección
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuración de replicador
Las configuraciones del replicador se usan para configurar el replicador que es responsable de hacer que el proveedor de estado del actor resulte altamente confiable mediante la replicación y la conservación del estado de forma local.
La configuración predeterminada es generada por la plantilla de Visual Studio y debe ser suficiente. En esta sección se habla sobre las configuraciones adicionales que están disponibles para optimizar el replicador.

### <a name="section-name"></a>Nombre de sección
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nombres de configuración
| Nombre | Unidad | Valor predeterminado | Comentarios |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Segundos |0.015 |Período de tiempo durante el que el replicador del secundario espera después de recibir una operación antes de enviar una confirmación al principal. El resto de confirmaciones que se enviarán para las operaciones que se procesan dentro de este intervalo se envían como una respuesta. |
| ReplicatorEndpoint |N/D |Ningún valor predeterminado: parámetro obligatorio |Dirección IP y puerto que usará el replicador principal y secundario para comunicarse con otros replicadores del conjunto de réplicas. Esto debe hacer referencia a un punto de conexión de recursos de TCP en el manifiesto de servicio. Consulte [Recursos del manifiesto de servicio](service-fabric-service-manifest-resources.md) para obtener más información sobre cómo definir recursos de punto de conexión en el manifiesto de servicio. |
| MaxReplicationMessageSize |Bytes |50 MB |Tamaño máximo de los datos de replicación que se puede transmitir en un único mensaje. |
| MaxPrimaryReplicationQueueSize |Número de operaciones |8192 |Número máximo de operaciones de la cola principal. Una operación se libera después de que el replicador principal reciba una confirmación de todos los replicadores secundarios. Este valor debe ser mayor que 64 y una potencia de 2. |
| MaxSecondaryReplicationQueueSize |Número de operaciones |16384 |Número máximo de operaciones de la cola secundaria. Una operación se libera después de que su estado pase a ser de alta disponibilidad mediante persistencia. Este valor debe ser mayor que 64 y una potencia de 2. |
| CheckpointThresholdInMB |MB |200 |Cantidad del espacio del archivo de registro después de que se compruebe el estado. |
| MaxRecordSizeInKB |KB |1024 |Tamaño del registro de mayor tamaño el replicador que puede escribir en el registro. Este valor debe ser un múltiplo de 4 y superior a 16. |
| OptimizeLogForLowerDiskUsage |Booleano |true |Cuando su valor es true, el registro se configura para que el archivo de registro específico de la réplica se cree usando un archivo disperso de NTFS. Esto reduce el uso del espacio en disco real del archivo. Cuando es false, el archivo se crea con asignaciones fijas, que ofrecen el mejor rendimiento de escritura. |
| SharedLogId |GUID |"" |Especifica un guid único que debe usarse para identificar el archivo de registro compartido que se usa con esta réplica. Normalmente, los servicios no deben usar esta opción de configuración. Sin embargo, si se especifica SharedLogId, también se debe especificar SharedLogPath. |
| SharedLogPath |Nombre de ruta de acceso completo |"" |Especifica la ruta de acceso completa donde se creará el archivo de registro compartido para esta réplica. Normalmente, los servicios no deben usar esta opción de configuración. Sin embargo, si se especifica SharedLogPath, también se debe especificar SharedLogId. |

## <a name="sample-configuration-file"></a>Archivo de configuración de muestra
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```

## <a name="remarks"></a>Comentarios
El parámetro BatchAcknowledgementInterval controla la latencia de replicación. Un valor de "0" ofrecerá la menor latencia posible, a costa del rendimiento (como deben enviarse y procesarse más mensajes de confirmación, cada uno con menos confirmaciones).
Cuanto mayor sea el valor de BatchAcknowledgementInterval, mayor será el rendimiento general de la replicación a costa de una mayor latencia de la operación. Esto se traduce directamente en la latencia de transacciones confirmadas.

El valor del parámetro CheckpointThresholdInMB controla la cantidad de espacio en disco que el replicador puede usar para almacenar información de estado en el archivo de registro específico de la réplica. Aumentar este valor a un valor mayor que el valor predeterminado puede provocar tiempos de reconfiguración cuando se agrega una nueva réplica para el conjunto. Esto se debe a la transferencia de estado parcial que tiene lugar debido a la disponibilidad de mayor cantidad de historial de operaciones en el registro. Esto podría aumentar el tiempo de recuperación de una réplica después de un error.

Si establece OptimizeForLowerDiskUsage en true, el espacio del archivo de registro se sobreaprovisionará para que las réplicas activas puedan almacenar más información de estado en sus archivos de registro, mientras que las réplicas inactivas usarán menos espacio en disco. Esto permite hospedar más réplicas en un nodo. Si establece OptimizeForLowerDiskUsage en false, la información de estado se escribe en los archivos de registro más rápidamente.

El parámetro MaxRecordSizeInKB define el tamaño máximo de un registro que el replicador puede escribir en el archivo de registro. En la mayoría de los casos, el tamaño predeterminado de 1024 KB del registro es óptimo. Sin embargo, si el servicio hace que elementos de datos de mayor tamaño formen parte de la información de estado, es posible que este valor se tenga que aumentar. Hay pocas ventajas en cambiar MaxRecordSizeInKB para que tenga un tamaño inferior a 1024, ya que los registros más pequeños solamente usan el espacio necesario para el registro más pequeño. Se espera que este valor solo tuviera que cambiarse en raras ocasiones.

Los parámetros SharedLogId y SharedLogPath siempre se usan en conjunto para obligar a un servicio a usar un registro compartido independiente del registro compartido predeterminado del nodo. Para obtener una mayor eficacia, todos los servicios posibles deben especificar el mismo registro compartido. Para reducir la contención del movimiento de encabezados, los archivos de registro compartido deben colocarse en discos que se usen únicamente para el archivo de registro compartido  Se espera que estos valores solo tuvieran que cambiarse en raras ocasiones.

