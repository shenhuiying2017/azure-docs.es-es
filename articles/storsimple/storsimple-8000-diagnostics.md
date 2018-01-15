---
title: "Herramienta de diagnóstico para la solución de problemas de dispositivos de StorSimple 8000 | Microsoft Docs"
description: "Describe los modos de dispositivo StorSimple y explica cómo usar Windows PowerShell para StorSimple para cambiar el modo del dispositivo."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 7199009553eb7aae31db3f913fe4de87e03d74ba
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Use la herramienta de diagnóstico de StorSimple para solucionar los problemas de los dispositivos de la serie 8000.

## <a name="overview"></a>Información general

La herramienta de diagnóstico de StorSimple diagnostica problemas relacionados con el sistema, el rendimiento, la red y el estado de los componentes de hardware de un dispositivo StorSimple. Esta herramienta se puede usar en diversos escenarios. Estos escenarios incluyen el planeamiento de la carga de trabajo, la implementación de un dispositivo de StorSimple, la evaluación del entorno de red y la determinación del rendimiento de un dispositivo operativo. En este artículo se proporciona información general de la herramienta de diagnóstico y se describe cómo puede usarla con un dispositivo de StorSimple.

La herramienta de diagnóstico va dirigida principalmente a la serie dispositivos locales de StorSimple de la serie 8000 (8100 y 8600).

## <a name="run-diagnostics-tool"></a>Ejecución de la herramienta de diagnóstico

Esta herramienta se puede ejecutar mediante la interfaz de Windows PowerShell del dispositivo de StorSimple. Hay dos maneras de acceder a la interfaz local del dispositivo:

* [Uso de PuTTY para conectarse a la consola serie del dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Acceso remoto a la herramienta mediante Windows PowerShell para StorSimple](storsimple-8000-remote-connect.md).

En este artículo, se supone que se ha conectado a la consola serie del dispositivo mediante PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Para ejecutar la herramienta de diagnóstico

Cuando se haya conectado a la interfaz de Windows PowerShell del dispositivo, realice los pasos siguientes para ejecutar el cmdlet.
1. Inicie sesión en la consola serie del dispositivo siguiendo los pasos detallados en [Uso de PuTTY para conectarse a la consola serie del dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Escriba el siguiente comando: 

    `Invoke-HcsDiagnostics`

    Si no se especifica el parámetro de ámbito, el cmdlet ejecuta todas las pruebas de diagnóstico. Estas pruebas incluyen el sistema, el estado de los componentes de hardware, la red y el rendimiento. 
    
    Para ejecutar solo una prueba concreta, especifique el parámetro de ámbito. Por ejemplo, para ejecutar solo la prueba de red, escriba

    `Invoke-HcsDiagnostics -Scope Network`

3. Seleccione y copie el resultado de la ventana PuTTY en un archivo de texto para analizarlo más a fondo.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Escenarios de uso de la herramienta de diagnóstico

Use la herramienta de diagnóstico para solucionar los problemas de estado de la red, el rendimiento, el sistema y el hardware. Algunos posibles escenarios son:

* **Dispositivo sin conexión**: su dispositivo de StorSimple de la serie 8000 está desconectado. Sin embargo, en la interfaz de Windows PowerShell, parece que ambos controladores están funcionando.
    * Puede usar esta herramienta para determinar el estado de la red.
         
         > [!NOTE]
         > No use esta herramienta para evaluar la configuración del rendimiento y de la red en un dispositivo antes del registro (o la configuración mediante el Asistente para la instalación). Durante el Asistente para la instalación y el registro se asigna una dirección IP válida. Puede ejecutar este cmdlet en un dispositivo que no esté registrado para conocer el estado del hardware y del sistema. Use el parámetro de ámbito, por ejemplo:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Problemas constantes en el dispositivo**: está experimentando problemas en el dispositivo que no desaparecen. Por ejemplo, error en el registro. Pueden tratarse también de problemas que aparecen después de que el dispositivo se ha registrado correctamente y lleva algún tiempo funcionando.
    * En este caso, puede usar esta herramienta para la solución de problemas preliminar antes de registrar una solicitud de servicio con el soporte técnico de Microsoft. Se recomienda ejecutar esta herramienta y capturar su salida. Así se la podrá proporcionar al equipo de soporte técnico para acelerar la solución de problemas.
    * Si existe algún error en el componente de hardware o el clúster, debe registrar una solicitud de soporte técnico.

* **Rendimiento bajo del dispositivo**: el dispositivo de StorSimple es lento.
    * En este caso, ejecute este cmdlet con el parámetro de ámbito establecido en rendimiento. Analice la salida. Obtenga las latencias de lectura y escritura en la nube. Use las latencias notificadas como el objetivo máximo que se puede conseguir, estime alguna sobrecarga en el procesamiento de datos interno y luego implemente las cargas de trabajo en el sistema. Para más información, vaya a [Uso de la prueba de red para solucionar los problemas de rendimiento del dispositivo](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Salidas de ejemplo y prueba de diagnóstico

### <a name="hardware-test"></a>Prueba de hardware

Esta prueba determina el estado de los componentes de hardware, el firmware de USM y el firmware de disco que se ejecutan en el sistema.

* Los componentes de hardware notificados son aquellos componentes que no superaron la prueba o no están presentes en el sistema.
* Las versiones de firmware de disco y firmware de USM se notifican para el Controlador 0, el Controlador 1 y los componentes compartidos del sistema. Para obtener una lista completa de los componentes de hardware, vaya a:

    * [Componentes de la caja principal](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Componentes de la caja EBOD](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Si la prueba de hardware informa de componentes con errores, [registre una solicitud de servicio con el soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>La salida de ejemplo de la prueba de hardware se ejecuta en un dispositivo 8100

Esta es una salida de ejemplo de un dispositivo de StorSimple 8100. En el dispositivo modelo 8100, la caja de EBOD no está presente. Por lo tanto, no se notifican los componentes del controlador de EBOD.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>Prueba del sistema

Esta prueba muestra la información del sistema, las actualizaciones disponibles, la información del clúster y la información de servicio del dispositivo.

* La información del sistema incluye el modelo, el número de serie del dispositivo, la zona horaria, el estado del controlador y la versión detallada del software que se ejecuta en el sistema. Para entender los distintos parámetros del sistema notificados en la salida, vaya a [Interpretación de la información del sistema](#appendix-interpreting-system-information).

* La disponibilidad de actualizaciones informa de si están disponibles los modos normal y de mantenimiento y sus nombres de paquete asociados. Si `RegularUpdates` y `MaintenanceModeUpdates` son `false`, significa que las actualizaciones no están disponibles. El dispositivo está actualizado.
* La información del clúster contiene la información sobre los diversos componentes lógicos de todos los grupos de clústeres de HCS y sus estados respectivos. Si observa un grupo de clústeres sin conexión en esta sección del informe, [póngase en contacto con el soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md).
* La información del servicio incluye los nombres y los estados de todos los servicios HCS y CiS que se ejecutan en el dispositivo. Esta información es útil para el soporte técnico de Microsoft en la solución de problemas del dispositivo.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Salida de ejemplo de la prueba del sistema ejecutada en un dispositivo 8100

Esta es una salida de ejemplo de la prueba del sistema que se ejecuta en un dispositivo 8100.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_servic         Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Prueba de red

Esta prueba valida el estado de las interfaces de red, los puertos, la conectividad del servidor DNS y NTP, el certificado SSL, las credenciales de cuenta de almacenamiento, la conectividad a los servidores de actualización y la conectividad del servidor proxy web en el dispositivo de StorSimple.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Salida de ejemplo de la prueba de red solo cuando está habilitado DATA0

Esta es la salida de ejemplo del dispositivo 8100. En la salida, puede apreciar que:
* Solo las interfaces de red DATA 0 y DATA 1 están habilitadas y configuradas.
* Las interfaces de DATA 2 a 5 no están habilitadas en el portal.
* La configuración del servidor DNS es válida y el dispositivo puede conectarse mediante el servidor DNS.
* La conectividad del servidor NTP también es correcta.
* Los puertos 80 y 443 están abiertos. Sin embargo, el puerto 9354 está bloqueado. En función de los [requisitos de red del sistema](storsimple-system-requirements.md), deberá abrir este puerto para la comunicación del bus de servicio.
* La certificación SSL es válida.
* El dispositivo puede conectarse a la cuenta de almacenamiento: _myss8000storageacct_.
* La conectividad a los servidores de actualización es válida.
* El proxy web no está configurado en este dispositivo.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Salida de ejemplo de la prueba de red cuando se habilitan DATA0 y DATA1

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Prueba de rendimiento

Esta prueba muestra el rendimiento en la nube mediante las latencias de lectura y escritura en la nube en el dispositivo. Esta herramienta puede usarse para establecer una línea de base del rendimiento en la nube que se puede alcanzar con StorSimple. La herramienta notifica el rendimiento máximo (escenario de mejor caso para latencias de lectura y escritura) que puede obtener para la conexión.

Como la herramienta notifica el rendimiento máximo que se puede obtener, podemos usar las latencias detectadas de lectura y escritura como objetivos al implementar las cargas de trabajo.

La prueba simula los tamaños de blob asociados con los diferentes tipos de volúmenes en el dispositivo. Los niveles normales y las copias de seguridad de volúmenes anclados localmente usan un tamaño de blob de 64 KB. Los volúmenes en capas con la opción de archivado marcada usan un tamaño de datos de blob de 512 KB. Si su dispositivo tiene configurados volúmenes anclados localmente y en capas, solo se ejecuta la prueba correspondiente al tamaño de datos de blob de 64 KB.

Para usar esta herramienta, realice los siguientes pasos:

1.  En primer lugar, cree una mezcla de volúmenes en capas y volúmenes anclados localmente con la opción de archivado marcada. Esta acción garantiza que la herramienta ejecuta las pruebas para tamaños de blob de 64 KB y 512 KB.

2. Ejecute el cmdlet cuando haya creado y configurado los volúmenes. Escriba: 

    `Invoke-HcsDiagnostics -Scope Performance`

3. Anote las latencias de lectura y escritura detectadas por la herramienta. Esta prueba puede tardar varios minutos en ejecutarse antes de notificar los resultados.

4. Si las latencias de conexión están todas en el intervalo esperado, las latencias detectadas por la herramienta se pueden usar como objetivo máximo alcanzable al implementar las cargas de trabajo. Estime cierta sobrecarga en el procesamiento de datos interno.

    Si las latencias de lectura y escritura detectadas por la herramienta de diagnóstico son demasiado elevadas:

    1. Configure Storage Analytics para los servicios de blob y analice la salida para comprender las latencias de la cuenta de Azure Storage. Para obtener instrucciones detalladas, vaya a [Habilitación y configuración de Storage Analytics](../storage/common/storage-enable-and-view-metrics.md). Si esas latencias también son altas y comparables a las cifras que recibió con la herramienta de diagnóstico de StorSimple, debe registrar una solicitud de servicio con Azure Storage.

    2. Si las latencias de la cuenta de almacenamiento son demasiado bajas, póngase en contacto con su administrador de red para que investigue los problemas de latencia de su red.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Salida de ejemplo de una prueba de rendimiento ejecutada en un dispositivo 8100

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Apéndice: interpretación de la información del sistema

Esta es una tabla que describe cuáles, de los diversos parámetros de Windows PowerShell de información del sistema, se deben asignar. 

| Parámetro de PowerShell    | DESCRIPCIÓN  |
|-------------------------|------------------|
| Id. de instancia             | Cada controlador lleva asociado un identificador único o un GUID.|
| NOMBRE                    | El nombre descriptivo del dispositivo, tal y como se configuró mediante el portal de Azure durante la implementación del dispositivo. El nombre descriptivo predeterminado es el número de serie del dispositivo. |
| Modelo                   | El modelo de su dispositivo StorSimple de la serie 8000. Puede ser 8100 o 8600.|
| SerialNumber            | El número de serie del dispositivo se asigna en la fábrica y tiene una longitud de 15 caracteres. Por ejemplo, 8600-SHX0991003G44HT indica:<br> 8600: es el modelo del dispositivo.<br>SHX: es el sitio de fabricación.<br> 0991003: indica un producto específico. <br> G44HT: los 5 últimos dígitos se incrementan para crear números de serie únicos. Es posible que no sea un conjunto secuencial.|
| TimeZone                | La zona horaria del dispositivo, tal y como se configuró en el portal de Azure durante la implementación del dispositivo.|
| CurrentController       | El controlador que está conectado mediante la interfaz de Windows PowerShell del dispositivo de StorSimple.|
| ActiveController        | El controlador que está activo en el dispositivo y controla todas las operaciones de red y disco. Puede ser el Controlador 0 o el Controlador 1.  |
| Controller0Status       | El estado del Controlador 0 en el dispositivo. El estado del controlador puede ser normal, en modo de recuperación o inaccesible.|
| Controller1Status       | El estado del Controlador 1 en el dispositivo.  El estado del controlador puede ser normal, en modo de recuperación o inaccesible.|
| SystemMode              | El estado general del dispositivo de StorSimple. El estado del dispositivo puede ser normal, mantenimiento o dado de baja (corresponde a desactivado en el portal de Azure).|
| FriendlySoftwareVersion | La cadena descriptiva que corresponde a la versión de software del dispositivo. Para un sistema que ejecuta Update 4, la versión de software descriptiva sería StorSimple 8000 Series Update 4.0.|
| HcsSoftwareVersion      | La versión de software HCS que se ejecuta en el dispositivo. Por ejemplo, la versión de software HCS correspondiente a StorSimple 8000 Series Update 4.0 es 6.3.9600.17820. |
| ApiVersion              | La versión de software de la API de Windows PowerShell del dispositivo HCS.|
| VhdVersion              | La versión de software de la imagen de fábrica con la que se envió el dispositivo. Si se restablece su dispositivo a los valores predeterminados de fábrica, se ejecuta esta versión de software.|
| OSVersion               | La versión de software del sistema operativo Windows Server que se ejecuta en el dispositivo. El dispositivo StorSimple se basa en Windows Server 2012 R2 que corresponde a 6.3.9600.|
| CisAgentVersion         | La versión de su agente Cis que se ejecuta en el dispositivo de StorSimple. Este agente ayuda a comunicarse con el servicio StorSimple Manager que se ejecuta en Azure.|
| MdsAgentVersion         | La versión correspondiente al agente Mds que se ejecuta en el dispositivo StorSimple. Este agente mueve los datos al servicio de supervisión y diagnóstico (MDS).|
| Lsisas2Version          | La versión correspondiente a los controladores LSI del dispositivo de StorSimple.|
| Capacity                | La capacidad total del dispositivo en bytes.|
| RemoteManagementMode    | Indica si el dispositivo puede administrarse de manera remota a través de su interfaz de Windows PowerShell. |
| FipsMode                | Indica si el modo FIPS (Federal Information Processing Standard) de Estados Unidos está habilitado en el dispositivo. El estándar FIPS 140 define algoritmos criptográficos aprobados para su uso por parte de los sistemas informáticos del Gobierno federal de los Estados Unidos a fin de garantizar la protección de los datos confidenciales. Para dispositivos que ejecutan Update 4 o posterior, el modo FIPS está habilitado de forma predeterminada. |

## <a name="next-steps"></a>pasos siguientes

* Conozca acerca de la [sintaxis del cmdlet Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Más información sobre cómo [solucionar problemas de implementación](storsimple-troubleshoot-deployment.md) en el dispositivo de StorSimple.
