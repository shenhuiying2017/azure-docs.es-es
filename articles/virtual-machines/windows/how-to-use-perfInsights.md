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
ms.openlocfilehash: f15875610e2035c6f4c10c36e19c02f3e045b3ea
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="how-to-use-perfinsights"></a>Cómo usar PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) es un script automatizado que recopila información de diagnóstico útil. También ejecuta cargas de esfuerzo de E/S y proporciona un informe tras un análisis que ayuda a solucionar problemas de rendimiento de las máquinas virtuales Windows en Azure. Se puede ejecutar en máquinas virtuales como script independiente o directamente desde el portal mediante la instalación de la [extensión de máquina virtual de diagnóstico de rendimiento de Azure para Windows](performance-diagnostics-vm-extension.md).

Si las máquinas virtuales tienen problemas de rendimiento, ejecute este script antes de ponerse en contacto con el departamento de soporte técnico.

## <a name="supported-troubleshooting-scenarios"></a>Escenarios admitidos de solución de problemas

PerfInsights puede recopilar y analizar varios tipos de información. En la siguientes secciones se tratan escenarios comunes.

### <a name="collect-basic-configuration"></a>Recopilación de la configuración básica 

En este escenario se recopila la configuración de disco y otra información importante, lo que incluye:

-   Registros de eventos

-   Estado de la red para todas las conexiones entrantes y salientes

-   Opciones de configuración de red y de firewall

-   Lista de tareas para todas las aplicaciones que se ejecutan actualmente en el sistema

-   Archivo de información creado por msinfo32 para la máquina virtual

-   Opciones de configuración de base de datos de Microsoft SQL Server (si la máquina virtual se identifica como un servidor que ejecuta SQL Server)

-   Contadores de confiabilidad de almacenamiento

-   Revisiones importantes de Windows

-   Controladores de filtro instalados

Se trata de un conjunto de información pasiva que no debería afectar al sistema. 

>[!Note]
>Este escenario se incluye automáticamente en cada uno de los escenarios siguientes.

### <a name="benchmarking"></a>Pruebas comparativas

En este escenario se ejecuta el banco de pruebas [Diskspd](https://github.com/Microsoft/diskspd) (IOPS y MBPS) para todas las unidades conectadas a la máquina virtual. 

> [!Note]
> Este escenario puede afectar al sistema y no se debe ejecutar en un sistema de producción activo. Si es necesario, ejecute este escenario en una ventana de mantenimiento dedicada para evitar cualquier problema. Una aumento de la carga de trabajo debido a un prueba de seguimiento o a un banco de pruebas puede afectar negativamente al rendimiento de la máquina virtual.
>

### <a name="slow-vm-analysis"></a>Análisis de máquinas virtuales lentas 

En este escenario se ejecuta un seguimiento de [contador de rendimiento](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) mediante el uso de los contadores que se especifican en el archivo Generalcounters.txt. Si la máquina virtual se identifica como un servidor en el que se ejecuta SQL Server, ejecuta un seguimiento del contador de rendimiento. Para ello utiliza los contadores que se encuentran en el archivo Sqlcounters.txt e incluye datos de diagnóstico del rendimiento.

### <a name="slow-vm-analysis-and-benchmarking"></a>Análisis de máquinas virtuales lentas y pruebas comparativas

En este escenario se ejecuta el seguimiento de un [contador de rendimiento](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) e, inmediatamente después, un banco de pruebas [Diskspd](https://github.com/Microsoft/diskspd). 

> [!Note]
> Este escenario puede afectar al sistema y no se debe ejecutar en un sistema de producción activo. Si es necesario, ejecute este escenario en una ventana de mantenimiento dedicada para evitar cualquier problema. Una aumento de la carga de trabajo debido a un prueba de seguimiento o a un banco de pruebas puede afectar negativamente al rendimiento de la máquina virtual.
>

### <a name="azure-files-analysis"></a>Análisis de Azure Files 

En este escenario se ejecuta una captura especial del contador de rendimiento junto con un seguimiento de red. Dicha captura incluye todos los contadores de recursos compartidos de cliente de Bloque de mensajes del servidor (SMB). Los siguientes son algunos contadores de rendimiento de recursos compartidos de cliente de SMB clave que forman parte de la captura:

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
| Throughput   | Bytes de datos por segundo                |
|              | Bytes de lectura por segundo                |
|              | Bytes de escritura por segundo               |
| Longitud de la cola | Prom. Longitud de la cola de lectura        |
|              | Prom. Longitud de la cola de escritura       |
|              | Prom. Longitud de la cola de datos        |

### <a name="custom-slow-vm-analysis"></a>Análisis personalizado de máquinas virtuales lentas 

Cuando ejecute un análisis personalizado de máquinas virtuales lentas, seleccione seguimientos que se ejecuten en paralelo. Si lo desea, puede ejecutarlos todos (Performance Counter, Xperf, Network y StorPort). Una vez completado el seguimiento, la herramienta ejecuta el banco de pruebas Diskspd, siempre que esté seleccionado. 

> [!Note]
> Este escenario puede afectar al sistema y no se debe ejecutar en un sistema de producción activo. Si es necesario, ejecute este escenario en una ventana de mantenimiento dedicada para evitar cualquier problema. Una aumento de la carga de trabajo debido a un prueba de seguimiento o a un banco de pruebas puede afectar negativamente al rendimiento de la máquina virtual.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>¿Qué tipo de información recopila el script?

Se recopila información acerca de la máquina virtual de Windows, la configuración de discos o bloques de almacenamiento, contadores de rendimiento, registros y diferentes seguimientos. Depende del escenario de rendimiento que se use. En la tabla siguiente se dan los detalles:

|Datos recopilados                              |  |  | Escenarios de rendimiento |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | Recopilación de la configuración básica | Pruebas comparativas | Análisis de máquinas virtuales lentas | Análisis de máquinas virtuales lentas y pruebas comparativas | Análisis de Azure Files | Análisis personalizado de máquinas virtuales lentas |
| Información de los registros de eventos      | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Información del sistema               | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Asignación de volúmenes                       | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Asignación de discos                         | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Tareas en ejecución                    | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Contadores de confiabilidad de almacenamiento     | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Información de almacenamiento              | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Salida de fsutil                    | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Información del controlador del filtro               | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Salida de netstat                   | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Network configuration (Configuración de red)            | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Configuración del firewall           | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Configuración de SQL Server         | Sí                        | Sí                                | Sí                      | Sí                            | Sí                  | Sí                  |
| Seguimientos de diagnóstico de rendimiento * | Sí                        | Sí                                | Sí                      |                                | Sí                  | Sí                  |
| Seguimiento de contadores de rendimiento **     |                            |                                    |                          |                                |                      | Sí                  |
| Seguimiento del contador de SMB **             |                            |                                    |                          |                                | Sí                  |                      |
| Seguimiento del contador de SQL Server **      |                            |                                    |                          |                                |                      | Sí                  |
| Seguimiento de Xperf                      |                            |                                    |                          |                                |                      | Sí                  |
| Seguimiento de StorPort                   |                            |                                    |                          |                                |                      | Sí                  |
| Seguimiento de la red                    |                            |                                    |                          |                                | Sí                  | Sí                  |
| Seguimiento del banco de pruebas Diskspd ***      |                            | Sí                                |                          | Sí                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Seguimiento del diagnóstico de rendimiento (*)

Se ejecuta un motor basado en reglas en segundo plano para recopilar datos y diagnosticar problemas de rendimiento en curso. Actualmente se admiten las reglas siguientes:

- Regla de HighCpuUsage: detecta períodos de uso elevado de la CPU y muestra los principales consumidores de uso de la CPU durante dichos períodos.
- Regla de HighDiskUsage: detecta períodos de uso elevado de los discos físicos y muestra los principales consumidores de uso de los discos en dichos períodos.
- Regla de HighResolutionDiskMetric: muestra métricas de IOPS, rendimiento y latencia de E/S por 50 milisegundos de cada disco físico. Ayuda a identificar rápidamente los períodos de limitación de disco.
- Regla de HighMemoryUsage: detecta períodos de uso de memoria elevados y muestra los principales consumidores de uso de memoria durante esos períodos.

> [!NOTE] 
> Actualmente, se admiten las versiones de Windows que incluyen .NET Framework 3.5 o versiones posteriores.

### <a name="performance-counter-trace-"></a>Seguimiento de contadores de rendimiento (**)

Recopila los siguientes contadores de rendimiento:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk y \LogicalDisk
- \Cache\Dirty Pages, \Cache\Lazy Write Flushes/sec, \Server\Pool Nonpaged, Failures y \Server\Pool Paged Failures
- Varios contadores de \Network Interface, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments,  \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, \Per Processor Network Interface Card Activity, \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Para instancias de SQL Server
- \SQL Server:Buffer Manager, \SQLServer:Resource Pool Stats y \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Para Azure Files
\Recursos compartidos de cliente de SMB

### <a name="diskspd-benchmark-trace-"></a>Seguimiento del banco de pruebas Diskspd (***)
Pruebas de carga de trabajo de E/S de Diskspd [disco del sistema operativo (escritura) y unidades de grupo (lectura/escritura)]

## <a name="run-the-perfinsights-script-on-your-vm"></a>Ejecución del script PerfInsights en una máquina virtual

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>¿Qué es necesario saber antes de ejecutar el script? 

#### <a name="script-requirements"></a>Requisitos del script

-  Este script se debe ejecutar en la máquina virtual que tiene el problema de rendimiento. 

-  El script es compatible con los siguientes sistemas operativos: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 y Windows Server 2016; Windows 8.1 y Windows 10.

#### <a name="possible-problems-when-you-run-the-script-on-production-vms"></a>Posibles problemas al ejecutar el script en máquinas virtuales de producción

-  Tanto en los escenarios de pruebas comparativas como en el de "Análisis personalizado de máquinas virtuales lentas" que está configurado para usar Xperf o Diskspd, el script podría afectar de manera negativa al rendimiento de la máquina virtual. Estos escenarios no se deben ejecutar en un entorno de producción.

-  Tanto en los escenarios de pruebas comparativas como en el de "Análisis personalizado de máquinas virtuales lentas" que está configurado para usar Diskspd, asegúrese de que ninguna otra actividad en segundo plano interfier con la carga de trabajo de E/S.

-  De forma predeterminada, el script usa la unidad de almacenamiento temporal para recopilar los datos. Si el seguimiento permanece habilitado durante más tiempo, es posible que la cantidad de datos que se recopilan sea relevante. Esto puede reducir la disponibilidad de espacio en el disco temporal, lo que puede afectar a las aplicaciones que usen esta unidad.

### <a name="how-do-i-run-perfinsights"></a>¿Cómo se puede ejecutar PerfInsights? 

Para ejecutar PerfInsights en una máquina virtual es preciso instalar la [extensión de máquina virtual de diagnóstico de rendimiento de Azure para Windows](performance-diagnostics-vm-extension.md). Otra posibilidad es ejecutarlo como un script independiente. 

**Instalación y ejecución de PerfInsights desde Azure Portal**

Para más información acerca de esta opción, consulte [Install Azure Performance Diagnostics extension](performance-diagnostics-vm-extension.md#install-the-extension) (Instalación de la extensión de diagnóstico de rendimiento de Azure).  

**Ejecución del script de PerfInsights en el modo independiente**

Para ejecutar el script de PerfInsights, siga estos pasos:


1. Descargue [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Desbloquee el archivo PerfInsights.zip. Para ello, haga clic con el botón derecho en el archivo PerfInsights.zip y seleccione **Propiedades**. En la pestaña **General**, seleccione **Desbloquear** y haga clic en **Aceptar**. Así se asegura de que el script se ejecuta sin advertencias de seguridad adicionales.  

    ![Captura de pantalla de las propiedades de PerfInsights con Desbloquear resaltado](media/how-to-use-perfInsights/unlock-file.png)

3.  Expanda el archivo PerfInsights.zip comprimido en una unidad temporal (de forma predeterminada, suele ser la unidad D). El archivo comprimido debe contener los siguientes archivos y carpetas:

    ![Captura de pantalla de los archivos de la carpeta zip](media/how-to-use-perfInsights/file-folder.png)

4.  Abra Windows PowerShell como administrador y, después, ejecute el script PerfInsights.ps1.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Es posible que tenga que escribir "y" para confirmar que desea cambiar la directiva de ejecución.

    En el cuadro de diálogo **Notice and Consent** (Aviso y consentimiento), tiene la opción de compartir la información de diagnóstico con el Soporte técnico de Microsoft. Para continuar también debe dar su consentimiento para el contrato de licencia. Elija las opciones que desee y haga clic en **Run script** (Ejecutar script).

    ![Captura de pantalla del cuadro de diálogo de aviso y consentimiento](media/how-to-use-perfInsights/disclaimer.png)

5.  Envíe el número de caso, si está disponible, al ejecutar el script. Después seleccione **Aceptar**.
    
    ![Captura de pantalla del cuadro de diálogo del identificador de soporte técnico](media/how-to-use-perfInsights/enter-support-number.png)

6.  Seleccione la unidad de almacenamiento temporal. El script puede detectar automáticamente la letra de la unidad. Si se produce algún problema en esta fase, es posible que se le pida que seleccione la unidad (la predeterminada es D). Los registros generados se almacenan aquí, en la carpeta log\_collection. Después de escribir o aceptar la letra de la unidad, haga clic en **Aceptar**.

    ![Captura de pantalla del cuadro de diálogo de la unidad temporal](media/how-to-use-perfInsights/enter-drive.png)

7.  Seleccione un escenario de solución de problemas de la lista proporcionada.

       ![Captura de pantalla de la lista de escenarios de solución de problemas](media/how-to-use-perfInsights/select-scenarios.png)

También puede ejecutar PerfInsights sin la interfaz de usuario. El siguiente comando ejecuta el escenario de solución de problemas "Análisis de máquinas virtuales lentas" sin interfaz de usuario. Le pide que dé su consentimiento a la misma declinación de responsabilidades y términos de licencia que se mencionan en el paso 4.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

Si quiere que PerfInsights se ejecute en modo silencioso, use el parámetro **-AcceptDisclaimerAndShareDiagnostics**. Por ejemplo, use el siguiente comando:

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>¿Cómo se pueden solucionar los problemas mientras se ejecuta el script?

Si el script finaliza de forma anómala, para volver a un estado coherente ejecute el script junto con el modificador cleanup, como se indica a continuación:

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

Si se produce algún problema durante la detección automática de la unidad temporal, es posible que se le pida que seleccione la unidad (la predeterminada es D).

![Captura de pantalla del cuadro de diálogo de la unidad temporal](media/how-to-use-perfInsights/enter-drive.png)

El script desinstala las herramientas de utilidad y quita las carpetas temporales.

### <a name="troubleshoot-other-script-issues"></a>Solución de problemas con el script 

Si se produce algún problema al ejecutar el script, presione Ctrl+C para interrumpirlo. Si el script sigue sin funcionar correctamente después de varios intentos, se recomienda ejecutar el script en modo de depuración, para lo que debe usar el parámetro "-Debug" en el inicio.

Después de que se produzca el error, copie la salida completa de la consola de PowerShell y envíelo al agente de Soporte técnico de Microsoft que le está ayudando a solucionar el problema.

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>¿Cómo se ejecuta el script en modo "Análisis personalizado de máquinas virtuales lentas"?

Al seleccionar **Análisis personalizado de máquinas virtuales lentas**, puede habilitar varios seguimientos en paralelo (para seleccionarlos, use la tecla Mayús):

![Captura de pantalla de lista de escenarios](media/how-to-use-perfInsights/select-scenario.png)

Cuando seleccione los escenarios Seguimiento de contadores de rendimiento, Seguimiento de Xperf, Seguimiento de la red o Seguimiento de Storport, siga las instrucciones que aparecen en los cuadros de diálogo siguientes. Intente reproducir el problema de un rendimiento lento después de iniciar los seguimientos.

Los seguimientos se inician en el siguiente cuadro de diálogo:

![Captura de pantalla del cuadro de diálogo Start Performance Counter Trace (Iniciar Seguimiento de contadores de rendimiento)](media/how-to-use-perfInsights/start-trace-message.png)

Para detener los seguimientos, tendrá que confirmar el comando en un segundo cuadro de diálogo.

![Captura de pantalla del cuadro de diálogo Stopping Performance Counter Trace (Detención de Seguimiento de contadores de rendimiento)](media/how-to-use-perfInsights/stop-trace-message.png)
![Captura de pantalla del cuadro de diálogo Stopping All traces (Detención de todos los seguimientos)](media/how-to-use-perfInsights/ok-trace-message.png)

Cuando las operaciones o los seguimientos se completan, aparece un nuevo archivo en D:\\log\_collection (o la unidad temporal). El nombre del archivo es **CollectedData\_aaaa-MM-dd\_hh\_mm\_ss.zip.** Puede enviar este archivo al agente del servicio de soporte técnico para que lo analice.

## <a name="review-the-diagnostics-report"></a>Revisión del informe de diagnóstico

En el archivo **CollectedData\_aaaa-MM-dd\_hh\_mm\_ss.zip**, puede encontrar un informe HTML que detalla las conclusiones de PerfInsights. Para revisar el informe, expanda el archivo **CollectedData\_aaaa-MM-dd\_hh\_mm\_ss.zip** y después abra el archivo **PerfInsights Report.html**.

Haga clic en la pestaña **Conclusiones**.

![Captura de pantalla del informe de PerfInsights](media/how-to-use-perfInsights/findingtab.png)
![Captura de pantalla del informe de PerfInsights](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> Las conclusiones clasificadas como críticas son problemas conocidos que pueden provocar problemas de rendimiento. Las conclusiones clasificadas como importantes representan configuraciones no óptimas que no provocan necesariamente problemas de rendimiento. Las conclusiones clasificadas como informativas son solo declaraciones informativas.

Revise las recomendaciones y los vínculos de todas las conclusiones críticas e importantes. Aprenda cómo pueden afectar al rendimiento, así como los procedimientos recomendados para las configuraciones con optimización del rendimiento.

### <a name="storage-tab"></a>Pestaña Almacenamiento

La sección **Conclusiones** muestra diversas conclusiones y recomendaciones relacionadas con el almacenamiento.

En las secciones **Disk Map** (Mapa de discos) y **Volume Map** (Mapa de volúmenes) se describe la relación que los volúmenes lógicos y los discos físicos tienen entre sí.

En la perspectiva de los discos físicos (Disk Map), la tabla muestra todos los volúmenes lógicos que se ejecutan en el disco. En el ejemplo siguiente, **PhysicalDrive2** ejecuta dos volúmenes lógicos creados en varias particiones (J y H):

![Captura de la pestaña de discos](media/how-to-use-perfInsights/disktab.png)

En la perspectiva de los volúmenes (Volume Map), las tablas muestran todos los discos físicos en cada volumen lógico. Observe que en el caso de los discos RAID y dinámicos, un volumen lógico se puede ejecutar en varios discos físicos. En el ejemplo siguiente *C:\\mount* es un punto de montaje configurado como *SpannedDisk* en los discos físicos 2 y 3:

![Captura de pantalla de la ficha de volúmenes](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>Pestaña SQL

Si la máquina virtual de destino hospeda alguna instancia de SQL Server, en el informe verá una pestaña adicional denominada **SQL**:

![Captura de pantalla de pestaña SQL](media/how-to-use-perfInsights/sqltab.png)

Esta sección contiene una pestaña **Findings** (Conclusiones) y pestañas adicionales para cada una de las instancias de SQL Server hospedadas en la máquina virtual.

La pestaña **Conclusiones** contiene una lista de todos los problemas de rendimiento relacionados con SQL encontrados, junto con las recomendaciones.

En el ejemplo siguiente, se muestra **PhysicalDrive0** (que se ejecuta en la unidad C). En el ejemplo siguiente, los archivos **modeldev** y **modellog** se encuentran en la unidad C y son de tipos diferentes (archivo de datos y registro de transacciones, respectivamente).

![Captura de pantalla de información de registro](media/how-to-use-perfInsights/loginfo.png)

Las pestañas de las instancias concretas de SQL Server contienen una sección general que muestra información básica acerca de la instancia seleccionada. También contienen secciones adicionales para información avanzada, lo que incluye los valores, las configuraciones y las opciones de usuario.

### <a name="diagnostic-tab"></a>Pestaña Diagnóstico
La pestaña **Diagnostic** (Diagnóstico) contiene información acerca de los mayores consumidores de CPU, disco y memoria mientras se ejecuta PerfInsights. También puede encontrar información acerca de las revisiones críticas que podría necesitar el sistema, la lista de tareas y los eventos importantes del sistema. 

## <a name="references-to-the-external-tools-used"></a>Referencias a las herramientas externas usadas

### <a name="diskspd"></a>Diskspd

Diskspd es un generador de carga de almacenamiento y una herramienta de prueba de rendimiento de Microsoft. Para más información, consulte [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

Xperf es una herramienta de línea de comandos para capturar seguimientos desde Windows Performance Toolkit. Para más información, consulte [Windows Performance Toolkit -Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Pasos siguientes

Puede cargar los registros e informes de diagnóstico en el Soporte técnico de Microsoft para que examinen con mayor profundidad. En el servicio de soporte técnico pueden solicitarle que transmita el resultado que genera PerfInsights para facilitarles el proceso de solución de los problemas.

La siguiente captura de pantalla muestra un mensaje similar al que puede recibir:

![Captura de pantalla de mensaje de ejemplo del Soporte técnico de Microsoft](media/how-to-use-perfInsights/supportemail.png)

Siga las instrucciones del mensaje para acceder al área de trabajo de transferencia de archivos. Para mayor seguridad, tiene que cambiar la contraseña la primera vez que la use.

Después de iniciar sesión, encontrará un cuadro de diálogo para cargar el archivo **CollectedData\_aaaa-MM-dd\_hh\_mm\_ss.zip** que recopiló PerfInsights.
