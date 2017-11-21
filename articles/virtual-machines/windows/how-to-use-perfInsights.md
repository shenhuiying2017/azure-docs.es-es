---
title: "Cómo usar PerfInsights en Microsoft Azure | Microsoft Docs"
description: "Obtenga información sobre cómo usar PerfInsights para solucionar problemas de rendimiento de máquinas virtuales con Windows."
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 36e204c73e62e950c3f40eab7e1ce6bccd7abd83
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2017
---
# <a name="how-to-use-perfinsights"></a>Cómo usar PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) es un script automatizado que recopila información de diagnóstico útil, ejecuta cargas de esfuerzo de E/S y proporciona un informe de análisis para ayudar a solucionar problemas de rendimiento de máquinas virtuales con Windows en Microsoft Azure. Esto puede ejecutarse en máquinas virtuales como un script independiente o directamente desde el portal instando la [extensión de máquina virtual de diagnóstico de rendimiento de Azure](performance-diagnostics-vm-extension.md).

Se recomienda ejecutar este script antes de abrir una incidencia de soporte técnico con Microsoft para problemas de rendimiento de la máquina virtual.

## <a name="supported-troubleshooting-scenarios"></a>Escenarios admitidos de solución de problemas

PerfInsights puede recopilar y analizar varios tipos de información que se agrupan en escenarios únicos.

### <a name="collect-disk-configuration"></a>Recopilar configuración de disco 

En este escenario se recopila la configuración de disco y otra información importante, incluidos los siguientes elementos:

-   Registros de eventos

-   Estado de la red para todas las conexiones entrantes y salientes

-   Opciones de configuración de red y de firewall

-   Lista de tareas para todas las aplicaciones que se ejecutan actualmente en el sistema

-   Archivo de información creado por msinfo32 para la máquina virtual (VM)

-   Opciones de configuración de base de datos de Microsoft SQL Server (si la máquina virtual se identifica como un servidor que ejecuta SQL Server)

-   Contadores de confiabilidad de almacenamiento

-   Revisiones importantes de Windows

-   Controladores de filtro instalados

Se trata de un conjunto de información pasiva que no debería afectar al sistema. 

>[!Note]
>Este escenario se incluye automáticamente en cada uno de los escenarios siguientes.

### <a name="benchmarkstorage-performance-test"></a>Banco de pruebas y prueba de rendimiento de almacenamiento

En este escenario se ejecuta el banco de pruebas de [diskspd](https://github.com/Microsoft/diskspd) (IOPS y MBPS) para todas las unidades que estén conectadas a la máquina virtual. 

> [!Note]
> Este escenario puede afectar al sistema y no debe ejecutarse en un sistema de producción activo. Si es necesario, ejecute este escenario en una ventana de mantenimiento dedicada para evitar cualquier problema. Una carga de trabajo mayor que se deba a una prueba de seguimiento o un banco de pruebas podría afectar negativamente al rendimiento de la máquina virtual.
>

### <a name="general-vm-slow-analysis"></a>Análisis general de lentitud de la máquina virtual 

En este escenario se ejecuta un seguimiento de [contador de rendimiento](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) mediante el uso de los contadores que se especifican en el archivo Generalcounters.txt. Si la máquina virtual se identifica como un servidor que ejecuta SQL Server, ejecuta un seguimiento del contador de rendimiento mediante los contadores que se encuentran en el archivo Sqlcounters.txt. También se incluyen datos de diagnóstico de rendimiento.

### <a name="vm-slow-analysis-and-benchmark"></a>Análisis y banco de pruebas de lentitud de la máquina virtual

En este escenario se ejecuta un seguimiento del [contador de rendimiento](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) seguido de un banco de pruebas [diskspd](https://github.com/Microsoft/diskspd). 

> [!Note]
> Este escenario puede afectar al sistema y no debe ejecutarse en un sistema de producción activo. Si es necesario, ejecute este escenario en una ventana de mantenimiento dedicada para evitar cualquier problema. Una carga de trabajo mayor que se deba a una prueba de seguimiento o un banco de pruebas podría afectar negativamente al rendimiento de la máquina virtual.
>

### <a name="azure-files-analysis"></a>Análisis de Azure Files 

En este escenario se ejecuta una captura especial del contador de rendimiento junto con un seguimiento de red. La captura incluye todos los contadores de "Recursos compartidos de cliente de SMB". Los siguientes son algunos contadores de rendimiento de recursos compartidos de cliente de SMB clave que forman parte de la captura:

| **Tipo**     | **Contador de recursos compartidos de cliente de SMB** |
|--------------|-------------------------------|
| E/S         | Solicitudes de datos por segundo             |
|              | Solicitudes de lectura por segundo             |
|              | Solicitudes de escritura por segundo            |
| Latency      | Media de solicitud de datos por segundo         |
|              | Media de lectura por segundo                 |
|              | Media de escritura por segundo                |
| Tamaño de E/S      | Prom. de bytes/solicitud de datos       |
|              | Prom. de bytes/lectura               |
|              | Prom. de bytes/escritura              |
| Rendimiento   | Bytes de datos por segundo                |
|              | Bytes de lectura por segundo                |
|              | Bytes de escritura por segundo               |
| Longitud de la cola | Prom. Longitud de la cola de lectura        |
|              | Prom. Longitud de la cola de escritura       |
|              | Prom. Longitud de la cola de datos        |

### <a name="custom-configuration"></a>Configuración personalizada 

Al ejecutar una configuración personalizada, todos los seguimientos (diagnóstico de rendimiento, contador de rendimiento, xperf, red, storport) se ejecutan en paralelo, dependiendo de cuántos seguimientos diferentes se seleccionan. Una vez completado el seguimiento, la herramienta ejecuta el banco de pruebas de diskspd, si está seleccionado. 

> [!Note]
> Este escenario puede afectar al sistema y no debe ejecutarse en un sistema de producción activo. Si es necesario, ejecute este escenario en una ventana de mantenimiento dedicada para evitar cualquier problema. Una carga de trabajo mayor que se deba a una prueba de seguimiento o un banco de pruebas podría afectar negativamente al rendimiento de la máquina virtual.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>¿Qué tipo de información recopila el script?

Se recopila información sobre la máquina virtual de Windows, la configuración de discos o bloques de almacenamiento, contadores de rendimiento, registros y diferentes seguimientos, según el escenario de rendimiento que se use:

|Datos recopilados                              |  |  | Escenarios de rendimiento |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | Recopilar configuración de disco | Banco de pruebas y prueba de rendimiento de almacenamiento | Análisis general de lentitud de la máquina virtual | Análisis y banco de pruebas de lentitud de la máquina virtual | Análisis de Azure Files | Configuración personalizada |
| Información de los registros de eventos      | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Información del sistema               | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Asignación de volúmenes                       | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Asignación de discos                         | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Tareas en ejecución                    | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Contadores de confiabilidad de almacenamiento     | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Información de almacenamiento              | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Salida de fsutil                    | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Información de controlador de filtro               | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Salida de netstat                   | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Network configuration (Configuración de red)            | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Configuración del firewall           | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Configuración de SQL Server         | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Seguimientos de diagnóstico de rendimiento * |                            |                                    | Sí                      |                                |                      | Sí                  |
| Seguimiento de contadores de rendimiento **     |                            |                                    |                          |                                |                      | Sí                  |
| Seguimiento del contador de SMB **             |                            |                                    |                          |                                | Sí                  |                      |
| Seguimiento del contador de SQL Server **      |                            |                                    |                          |                                |                      | Sí                  |
| Seguimiento de XPerf                      |                            |                                    |                          |                                |                      | Sí                  |
| Seguimiento de StorPort                   |                            |                                    |                          |                                |                      | Sí                  |
| Seguimiento de la red                    |                            |                                    |                          |                                | Sí                  | Sí                  |
| Seguimiento del banco de pruebas de diskspd ***      |                            | Sí                                |                          | Sí                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Seguimientos de diagnóstico de rendimiento (*)

Se ejecuta un motor basado en reglas en segundo plano para recopilar datos y diagnosticar problemas de rendimiento en curso. Actualmente se admiten las reglas siguientes:

- Regla de HighCpuUsage: detecta períodos de uso de CPU elevados y muestra los principales consumidores de uso de CPU durante esos períodos.
- Regla de HighDiskUsage: detecta períodos de uso de disco elevados en discos físicos y muestra los principales consumidores de uso de disco durante esos períodos.
- Regla de HighResolutionDiskMetric: muestra métricas de IOPS, rendimiento y latencia de E/S por 50 milisegundos para cada disco físico. Ayuda a identificar rápidamente los períodos de limitación de disco.
- Regla de HighMemoryUsage: detecta períodos de uso de memoria elevados y muestra los principales consumidores de uso de memoria durante esos períodos.

> [!NOTE] 
> Actualmente, se admiten las versiones de Windows que incluyen .NET Framework 3.5 o versiones posteriores.

### <a name="performance-counter-trace-"></a>Seguimiento de contadores de rendimiento (**)

Recopila los siguientes contadores de rendimiento:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk, \LogicalDisk
- \Cache\Dirty Pages, \Cache\Lazy Write Flushes/sec, \Server\Pool Nonpaged, Failures, \Server\Pool Paged Failures
- Contadores seleccionados bajo \Network Interface, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments,  \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, \Per Processor Network Interface Card Activity, \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Para instancias de SQL Server
- \SQL Server:Buffer Manager, \SQLServer:Resource Pool Stats, \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Para Azure Files
\Recursos compartidos de cliente de SMB

### <a name="diskspd-benchmark-trace-"></a>Seguimiento del banco de pruebas de diskspd (***)
Pruebas de carga de trabajo de E/S de diskspd [disco del sistema operativo (escritura) y unidades de grupo (lectura/escritura)]

## <a name="run-the-perfinsights-on-your-vm"></a>Ejecutar PerfInsights en la máquina virtual

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>¿Qué es necesario saber antes de ejecutar el script? 

**Requisitos del script**

1.  Este script se debe ejecutar en la máquina virtual que tiene el problema de rendimiento. 

2.  Se admiten los sistemas operativos siguientes: Windows Server 2008 R2, 2012, 2012 R2, 2016; Windows 8.1 y Windows 10.

**Posibles problemas al ejecutar el script en máquinas virtuales de producción:**

1.  Es posible que el script afecte negativamente al rendimiento de la máquina virtual cuando se usa junto con el escenario de "Banco de pruebas" o "Personalizado" que se configura mediante XPerf o DiskSpd. Tenga cuidado cuando ejecute el script en un entorno de producción.

2.  Cuando use el script junto con el escenario de "Banco de pruebas" o "Personalizado" que se configura mediante DiskSpd, asegúrese de que ninguna otra actividad en segundo plano interfiere con la carga de trabajo de E/S en los discos probados.

3.  De forma predeterminada, el script usa la unidad de almacenamiento temporal para recopilar los datos. Si el seguimiento permanece habilitado durante más tiempo, es posible que la cantidad de datos que se recopilan sea relevante. Esto puede reducir la disponibilidad de espacio en el disco temporal, afectando por tanto a cualquier aplicación que se base en esta unidad.

### <a name="how-do-i-run-perfinsights"></a>¿Cómo se puede ejecutar PerfInsights? 

Puede ejecutar PerfInsights en una máquina virtual instalando la [extensión de máquina virtual de diagnóstico de rendimiento de Azure](performance-diagnostics-vm-extension.md) o ejecutarlo como un script independiente. 

**Instalación y ejecución de PerfInsights desde Azure Portal**

PerfInsights se puede ejecutar ahora con una extensión de máquina virtual denominada "extensión de diagnóstico de rendimiento de Azure". Para obtener más información, vea [Install Azure Performance Diagnostics extension](performance-diagnostics-vm-extension.md#install-the-extension) (Instalación de la extensión de diagnóstico de rendimiento de Azure).  

**Ejecución del script de PerfInsights en el modo independiente**

Para ejecutar el script de PerfInsights, siga estos pasos:


1. Descargue [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Desbloquee el archivo PerfInsights.zip. Para ello, haga clic con el botón derecho en el archivo PerfInsights.zip y seleccione **Propiedades**. En la pestaña **General**, seleccione **Desbloquear** y haga clic en **Aceptar**. De este modo se asegura de que el script se ejecuta sin advertencias de seguridad adicionales.  

    ![Desbloquee el archivo ZIP.](media/how-to-use-perfInsights/unlock-file.png)

3.  Expanda el archivo PerfInsights.zip comprimido en la unidad temporal (de forma predeterminada, normalmente es la unidad D). El archivo comprimido debe contener los siguientes archivos y carpetas:

    ![archivos de la carpeta zip](media/how-to-use-perfInsights/file-folder.png)

4.  Abra Windows PowerShell como administrador y, después, ejecute el script PerfInsights.ps1.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Es posible que tenga que escribir "y" si se le pide que confirme que quiere cambiar la directiva de ejecución.

    En el cuadro de diálogo de declinación de responsabilidades, tendrá la opción de compartir información de diagnóstico con el Soporte técnico de Microsoft. Para continuar también debe dar su consentimiento para el contrato de licencia. Realice sus selecciones y, después, haga clic en **Ejecutar script**.

    ![Cuadro de declinación de responsabilidades](media/how-to-use-perfInsights/disclaimer.png)

5.  Envíe el número de caso, si está disponible, al ejecutar el script (es para nuestras estadísticas). A continuación, haga clic en **Aceptar**.
    
    ![escribir el Id. de soporte](media/how-to-use-perfInsights/enter-support-number.png)

6.  Seleccione la unidad de almacenamiento temporal. El script puede detectar automáticamente la letra de unidad de la unidad. Si se produce algún problema en esta fase, es posible que se le pida que seleccione la unidad (la predeterminada es D). Los registros generados se almacenan aquí en la carpeta log\_collection. Después de escribir o aceptar la letra de unidad, haga clic en **Aceptar**.

    ![especificar la unidad](media/how-to-use-perfInsights/enter-drive.png)

7.  Seleccione un escenario de solución de problemas de la lista proporcionada.

       ![Seleccionar escenarios de soporte técnico](media/how-to-use-perfInsights/select-scenarios.png)

8.  También puede ejecutar PerfInsights sin la interfaz de usuario.

    El siguiente comando ejecuta el escenario de solución de problemas "Análisis general de lentitud de la máquina virtual" sin un mensaje de interfaz de usuario y sin capturar datos durante 30 segundos. Le pedirá que dé su consentimiento a la misma declinación de responsabilidades y términos de licencia que se mencionan en el paso 4.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

    Si quiere que PerfInsights se ejecute en modo silencioso, use el parámetro **-AcceptDisclaimerAndShareDiagnostics**. Por ejemplo, use el siguiente comando:

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>¿Cómo se pueden solucionar los problemas mientras se ejecuta el script?

Si el script finaliza de forma anómala, puede limpiar un estado incoherente si ejecuta el script junto con el modificador -Cleanup, como se indica a continuación:

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

Si se produce algún problema durante la detección automática de la unidad temporal, es posible que se le pida que seleccione la unidad (la predeterminada es D).

![especificar la unidad](media/how-to-use-perfInsights/enter-drive.png)

El script desinstala las herramientas de utilidad y quita las carpetas temporales.

### <a name="troubleshooting-other-script-issues"></a>Solución de problemas con otros scripts 

Si se produce algún problema al ejecutar el script, presione Ctrl+C para interrumpir la ejecución del script. Para quitar los objetos temporales, vea la sección "Realizar la limpieza después de una terminación anómala".

Si el script sigue produciendo errores incluso después de varios intentos, se recomienda ejecutar el script en "modo de depuración" con el parámetro "-Debug" durante el inicio.

Después de que se produzca el error, copie la salida completa de la consola de PowerShell y envíelo al agente de Soporte técnico de Microsoft que le está ayudando a solucionar el problema.

### <a name="how-do-i-run-the-script-in-custom-configuration-mode"></a>¿Cómo se ejecuta el script en modo de configuración personalizada?

Si selecciona la configuración **Personalizada**, puede habilitar varios seguimientos en paralelo (presione la tecla MAYÚS para seleccionar varios):

![seleccionar escenarios](media/how-to-use-perfInsights/select-scenario.png)

Cuando seleccione los escenarios Diagnóstico de rendimiento, Seguimiento del contador de rendimiento, Seguimiento de XPerf, Seguimiento de la red o Seguimiento de Storport, siga las instrucciones que aparecen en los cuadros de diálogo y trate de reproducir el problema de rendimiento lento después de iniciar los seguimientos.

El siguiente cuadro de diálogo permite iniciar un seguimiento:

![iniciar seguimiento](media/how-to-use-perfInsights/start-trace-message.png)

Para detener los seguimientos, tendrá que confirmar el comando en un segundo cuadro de diálogo.

![detener seguimiento](media/how-to-use-perfInsights/stop-trace-message.png)
![detener seguimiento](media/how-to-use-perfInsights/ok-trace-message.png)

Cuando se completen las operaciones o seguimientos, se genera un nuevo archivo en D:\\log\_collection (o la unidad temporal) con el nombre **CollectedData\_aaaa-MM-dd\_hh\_mm\_ss.zip.** Puede enviar este archivo al agente del servicio de soporte técnico para el análisis.

## <a name="review-the-diagnostics-report-created-by-perfinsights"></a>Revisar el informe de diagnóstico creado por PerfInsights

En el archivo **CollectedData\_aaaa-MM-dd\_hh\_mm\_ss.zip** generado por PerfInsights, encontrará un informe HTML que detalla las conclusiones de PerfInsights. Para revisar el informe, expanda el archivo **CollectedData\_aaaa-MM-dd\_hh\_mm\_ss.zip** y después abra el archivo **PerfInsights Report.html**.

Haga clic en la pestaña **Conclusiones**.

![pestaña Conclusiones](media/how-to-use-perfInsights/findingtab.png)

**Notas**

-   Los mensajes en rojo son problemas de configuración conocidos que pueden causar problemas de rendimiento.

-   Los mensajes en amarillo son advertencias que representan configuraciones inadecuadas que no necesariamente provocan problemas de rendimiento.

-   Los mensajes en azul son solo instrucciones informativas.

Revise los vínculos HTTP de todos los mensajes de error en rojo para obtener información detallada sobre las conclusiones y cómo pueden afectar al rendimiento o prácticas recomendadas para configuraciones con optimización para el rendimiento.

### <a name="disk-configuration-tab"></a>Pestaña Configuración del disco

En la sección **Información general** se muestran distintas vistas de la configuración de almacenamiento, incluida información de Diskpart y Espacios de almacenamiento.

En las secciones **DiskMap** y **VolumeMap** se describe desde una perspectiva dual cómo se relacionan los volúmenes lógicos y los discos físicos entre sí.

En la perspectiva PhysicalDisk (DiskMap), se muestran en la tabla todos los volúmenes lógicos que se ejecutan en el disco. En el ejemplo siguiente, PhysicalDrive2 ejecuta dos volúmenes lógicos creados en varias particiones (J y H):

![pestaña Datos](media/how-to-use-perfInsights/disktab.png)

En la perspectiva Volumen (*VolumeMap*), se muestran en las tablas todos los discos físicos en cada volumen lógico. Observe que para los discos RAID y dinámicos, puede ejecutar un volumen lógico en varios discos físicos. En el ejemplo siguiente *C:\\montaje* es un punto de montaje configurado como *SpannedDisk* en los discos físicos \#2 y \#3:

![pestaña Volumen](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-server-tab"></a>Pestaña SQL Server

Si la máquina virtual de destino hospeda alguna instancia de SQL Server, verá una pestaña adicional en el informe con el nombre **SQL Server**:

![ficha sql](media/how-to-use-perfInsights/sqltab.png)

En esta sección se incluye una pestaña "Información general" y subpestañas adicionales para cada una de las instancias de SQL Server hospedadas en la máquina virtual.

La sección "Información general" contiene una tabla útil en la que resumen todos los discos físicos (discos de datos y del sistema) que se están ejecutando y que contienen una combinación de archivos de datos y archivos de registro de transacciones.

En el ejemplo siguiente, se muestra *PhysicalDrive0* (que ejecuta la unidad C) porque los archivos *modeldev* y *modellog* se encuentran en la unidad C y son de tipos diferentes (Archivo de datos y Registro de transacciones, respectivamente):

![loginfo](media/how-to-use-perfInsights/loginfo.png)

Las pestañas específicas de la instancia de SQL Server contienen una sección general en la que se muestra información básica sobre la instancia seleccionada y secciones adicionales para información avanzada, incluidos los ajustes, configuraciones y opciones de usuario.

## <a name="references-to-the-external-tools-used"></a>Referencias a las herramientas externas usadas

### <a name="diskspd"></a>Diskspd

DISKSPD es un generador de cargas de almacenamiento y herramienta de prueba de rendimiento de los equipos de ingeniería de Windows y Windows Server e infraestructura de servidor de nube. Para más información, vea [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>XPerf

Xperf es una herramienta de línea de comandos para capturar seguimientos desde el kit de herramientas de rendimiento de Windows.

Para más información, vea [Windows Performance Toolkit -Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Pasos siguientes

### <a name="upload-diagnostics-logs-and-reports-to-microsoft-support-for-further-review"></a>Cargar registros e informes de diagnóstico a Soporte técnico de Microsoft para realizar una revisión adicional

Cuando se trabaja con el personal de Soporte técnico de Microsoft, puede que le soliciten enviar la salida generada por PerfInsights para ayudarle en el proceso de solución de problemas.

El agente de soporte técnico creará un área de trabajo DTM para usted y recibirá un mensaje de correo que incluye un vínculo al portal DTM (https://filetransfer.support.microsoft.com/EFTClient/Account/Login.htm) y un identificador de usuario único y una contraseña.

Este mensaje se enviará desde **CTS Automated Diagnostics Services** (ctsadiag@microsoft.com).

![Ejemplo del mensaje](media/how-to-use-perfInsights/supportemail.png)

Para mayor seguridad, tendrá que cambiar la contraseña la primera vez que la use.

Después de iniciar sesión en DTM, encontrará un cuadro de diálogo para cargar el archivo **CollectedData\_aaaa-MM-dd\_hh\_mm\_ss.zip** recopilado por PerfInsights.
