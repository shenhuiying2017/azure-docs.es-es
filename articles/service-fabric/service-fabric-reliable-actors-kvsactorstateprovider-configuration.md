---
title: "Cambio de la configuración de KVSActorStateProvider en microservicios de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo configurar los actores con estado de Azure Service Fabric de tipo KVSActorStateProvider."
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: 
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: d3424aa7a8e0f6011bbef4aa61274c1f598f5c86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Configuración de Reliable Actors: KVSActorStateProvider
Puede modificar la configuración predeterminada de KVSActorStateProvider cambiando el archivo settings.xml que se genera en la raíz del paquete de Microsoft Visual Studio en la carpeta Config del actor especificado.

El tiempo de ejecución de Azure Service Fabric busca los nombres de sección predefinidos en el archivo settings.xml y usa los valores de configuración mientras crea los componentes en tiempo de ejecución subyacentes.

> [!NOTE]
> **No** elimine o modifique los nombres de sección de las siguientes configuraciones en el archivo settings.xml que se genera en la solución de Visual Studio.
> 
> 

## <a name="replicator-security-configuration"></a>Configuración de seguridad del replicador
Las configuraciones de seguridad del replicador se utilizan para proteger el canal de comunicación que se usa durante la replicación. Esto significa que los servicios no ven el tráfico de replicación de unos y los otros, lo que garantiza que los datos de alta disponibilidad también están protegidos.
De forma predeterminada, una sección de configuración de seguridad vacía impide la seguridad de replicación.

### <a name="section-name"></a>Nombre de sección
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuración de replicador
Las configuraciones de replicador configuran el replicador que es responsable de hacer que el proveedor de estado del actor resulte altamente confiable.
La configuración predeterminada es generada por la plantilla de Visual Studio y debe ser suficiente. En esta sección se habla sobre las configuraciones adicionales que están disponibles para optimizar el replicador.

### <a name="section-name"></a>Nombre de sección
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nombres de configuración
| Nombre | Unidad | Valor predeterminado | Comentarios |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Segundos |0.015 |Período de tiempo durante el que el replicador del secundario espera después de recibir una operación antes de enviar una confirmación al principal. El resto de confirmaciones que se enviarán para las operaciones que se procesan dentro de este intervalo se envían como una respuesta. |
| ReplicatorEndpoint |N/D |Ningún valor predeterminado: parámetro obligatorio |Dirección IP y puerto que usará el replicador principal y secundario para comunicarse con otros replicadores del conjunto de réplicas. Esto debe hacer referencia a un punto de conexión de recursos de TCP en el manifiesto de servicio. Consulte [Service Manifest Resources](service-fabric-service-manifest-resources.md) (Recursos del manifiesto de servicio) para obtener más información sobre cómo definir recursos de punto de conexión en el manifiesto de servicio. |
| RetryInterval |Segundos |5 |Período de tiempo después del cual el replicador vuelve a transmitir un mensaje si no recibe una confirmación de una operación. |
| MaxReplicationMessageSize |Bytes |50 MB |Tamaño máximo de los datos de replicación que se puede transmitir en un único mensaje. |
| MaxPrimaryReplicationQueueSize |Número de operaciones |1024 |Número máximo de operaciones de la cola principal. Una operación se libera después de que el replicador principal reciba una confirmación de todos los replicadores secundarios. Este valor debe ser mayor que 64 y una potencia de 2. |
| MaxSecondaryReplicationQueueSize |Número de operaciones |2048 |Número máximo de operaciones de la cola secundaria. Una operación se libera después de que su estado pase a ser de alta disponibilidad mediante persistencia. Este valor debe ser mayor que 64 y una potencia de 2. |

## <a name="store-configuration"></a>Configuración de almacén
Las configuraciones de almacén se usan para configurar el almacén local que se usa para conservar el estado que se está replicando.
La configuración predeterminada es generada por la plantilla de Visual Studio y debe ser suficiente. En esta sección se habla sobre las configuraciones adicionales que están disponibles para optimizar el almacén local.

### <a name="section-name"></a>Nombre de sección
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Nombres de configuración
| Nombre | Unidad | Valor predeterminado | Comentarios |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Milisegundos |200 |Establece el intervalo máximo de procesamiento por lotes de las confirmaciones del almacén local duradero. |
| MaxVerPages |Número de páginas |16384 |El número máximo de páginas de versión en la base de datos del almacén local. Determina el número máximo de transacciones pendientes. |

## <a name="sample-configuration-file"></a>Archivo de configuración de ejemplo
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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

