---
title: "Introducción a la solución de problemas de recursos en Azure Network Watcher | Microsoft Docs"
description: "En esta página se proporciona una introducción a las funcionalidades de solución de problemas de recursos en Network Watcher."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: a37c92e1aa58184ed29185742ec727c120fe593f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Introducción a la solución de problemas de recursos en Azure Network Watcher

Las puertas de enlace de red virtual proporcionan conectividad entre recursos locales y otras redes virtuales dentro de Azure. La supervisión de estas puertas de enlace y sus conexiones es esencial para garantizar la continuidad de la comunicación. Network Watcher proporciona la funcionalidad para solucionar problemas con las puertas de enlace de red virtual y las conexiones. Se puede llamar mediante el portal, PowerShell, la CLI o la API de REST. Cuando se llama, Network Watcher diagnostica el estado de la puerta de enlace de red virtual o la conexión y devuelve los resultados pertinentes. Esta solicitud es una transacción de larga duración; los resultados se devuelven cuando se completa el diagnóstico.

![portal][2]

## <a name="results"></a>Results

Los resultados preliminares devueltos proporcionan una perspectiva general del estado del recurso. Se puede proporcionar información más detallada para recursos, tal como se muestra en la sección siguiente:

En la siguiente lista, se presentan los valores devueltos con la API de solución de problemas:

* **startTime**: este valor es la hora de inicio de la llamada a la API de solución de problemas.
* **endTime**: este valor es la hora en que finalizó la solución de problemas.
* **code**: este valor es UnHealthy si se produce un único error de diagnóstico.
* **results**: es una colección de resultados devueltos para la conexión o la puerta de enlace de red virtual.
    * **id**: este valor es el tipo de error.
    * **summary**: este valor es un resumen del error.
    * **detailed**: este valor proporciona una descripción detallada del error.
    * **recommendedActions**: esta propiedad es una colección de acciones recomendadas que llevar a cabo.
      * **actionText**: este valor contiene el texto que describe qué acción realizar.
      * **actionUri**: este valor proporciona el URI de documentación sobre cómo actuar.
      * **actionUriText**: este valor es una breve descripción del texto de acción.

En las siguientes tablas se muestran los diferentes tipos de error (id bajo results en la lista anterior) que existen y si el error crea registros.

### <a name="gateway"></a>Puerta de enlace

| Tipo de error | Motivo | Registro|
|---|---|---|
| NoFault | Cuando no se detecta ningún error. |Sí|
| GatewayNotFound | No se encuentra la puerta de enlace o no está aprovisionada. |No|
| PlannedMaintenance |  La instancia de puerta de enlace está en mantenimiento.  |No|
| UserDrivenUpdate | Cuando hay una actualización del usuario en curso. Podría tratarse de una operación de cambio de tamaño. | No |
| VipUnResponsive | No se puede alcanzar la instancia principal de la puerta de enlace. Esto sucede cuando se produce un error en el sondeo de estado. | No |
| PlatformInActive | Hay un problema con la plataforma. | No|
| ServiceNotRunning | No se está ejecutando el servicio subyacente. | No|
| NoConnectionsFoundForGateway | No existe ninguna conexión en la puerta de enlace. Esto es solo una advertencia.| No|
| ConnectionsNotConnected | Las conexiones no están conectadas. Esto es solo una advertencia.| Sí|
| GatewayCPUUsageExceeded | El uso de CPU de la puerta de enlace actual es >95 %. | Sí |

### <a name="connection"></a>Conexión

| Tipo de error | Motivo | Registro|
|---|---|---|
| NoFault | Cuando no se detecta ningún error. |Sí|
| GatewayNotFound | No se encuentra la puerta de enlace o no está aprovisionada. |No|
| PlannedMaintenance | La instancia de puerta de enlace está en mantenimiento.  |No|
| UserDrivenUpdate | Cuando hay una actualización del usuario en curso. Podría tratarse de una operación de cambio de tamaño.  | No |
| VipUnResponsive | No se puede alcanzar la instancia principal de la puerta de enlace. Sucede cuando se produce un error en el sondeo de estado. | No |
| ConnectionEntityNotFound | Falta una configuración de conexión. | No |
| ConnectionIsMarkedDisconnected | La conexión está marcada como "desconectada". |No|
| ConnectionNotConfiguredOnGateway | El servicio subyacente no tiene configurada la conexión. | Sí |
| ConnectionMarkedStandy | El servicio subyacente está marcado como en espera activa.| Sí|
| Autenticación | Error de coincidencia de clave previamente compartida. | Sí|
| PeerReachability | La puerta de enlace del mismo nivel no está accesible. | Sí|
| IkePolicyMismatch | La puerta de enlace del mismo nivel tiene directivas IKE que no son compatibles con Azure. | Sí|
| Error de WfpParse | Se produjo un error al analizar el registro de WFP. |Sí|

## <a name="supported-gateway-types"></a>Tipos de puerta de enlace admitidos

En la lista siguiente se muestra qué puertas de enlace y conexiones son compatibles con la solución de problemas de Network Watcher.
|  |  |
|---------|---------|
|**Tipos de puerta de enlace**   |         |
|VPN      | Compatible        |
|ExpressRoute | No compatible |
|HyperNet | No compatible|
|**Tipos de VPN** | |
|Basado en ruta | Compatible|
|Basado en directiva | No compatible|
|**Tipos de conexión**||
|IPSec| Compatible|
|VNet2Vnet| Compatible|
|ExpressRoute| No compatible|
|HyperNet| No compatible|
|VPNClient| No compatible|

## <a name="log-files"></a>Archivos de registro

Los archivos de registro de la solución de problemas de recursos se guardan en una cuenta de almacenamiento una vez finalizada dicha solución de problemas. En la siguiente imagen se muestra el contenido de ejemplo de una llamada que generó un error.

![Archivo ZIP][1]

> [!NOTE]
> En algunos casos, solo se escribe un subconjunto de los archivos de registro en el almacenamiento.

Para instrucciones sobre cómo descargar archivos desde cuentas de Azure Storage, consulte [Introducción a Azure Blob Storage mediante .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Otra herramienta que se puede utilizar es el Explorador de Storage. Encontrará más información acerca del Explorador de Storage en el siguiente vínculo: [Explorador de Storage](http://storageexplorer.com/).

### <a name="connectionstatstxt"></a>ConnectionStats.txt

El archivo **ConnectionStats.txt** contiene estadísticas generales de la conexión, incluidos los bytes de entrada y salida, el estado de la conexión y la hora en que se estableció esta.

> [!NOTE]
> Si la llamada a la API de solución de problemas devuelve un estado correcto, lo único que se devuelve en el archivo ZIP es un archivo **ConnectionStats.txt**.

El contenido de este archivo es similar al ejemplo siguiente:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

El archivo **CPUStats.txt** contiene el uso de la CPU y la memoria disponible en el momento de realizarse las pruebas.  El contenido de este archivo es similar al ejemplo siguiente:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

El archivo **IKEErrors.txt** contiene los errores de IKE que se encontraran durante la supervisión.

En el ejemplo siguiente se muestra el contenido de un archivo IKEErrors.txt. Los errores pueden variar según el problema.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

El archivo de registro **Scrubbed-wfpdiag.txt** contiene el registro de WFP. Este registro contiene el registro de errores de IKE y AuthIP, y de pérdida de paquetes.

En el ejemplo siguiente se muestra el contenido del archivo Scrubbed-wfpdiag.txt. En este ejemplo, la clave compartida de una conexión no era correcta, como se aprecia a partir de la tercera línea desde abajo. El ejemplo siguiente es un fragmento del código del registro completo, el cual puede ser extenso en función del problema.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.sum

El archivo **wfpdiag.txt.sum** es un registro que muestra los búferes y los eventos procesados.

El ejemplo siguiente es el contenido de un archivo wfpdiag.txt.sum.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>Pasos siguientes

Visite [Gateway troubleshooting - Azure portal](network-watcher-troubleshoot-manage-portal.md) (Solución de problemas de puerta de enlace: Azure Portal) para saber cómo diagnosticar las conexiones y las puertas de enlace de VPN a través del portal.
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
